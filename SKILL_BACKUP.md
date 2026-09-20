---
name: linkedin-cdp-scraper
description: "CDP-based LinkedIn scraper via Edge — company pages, posts, profiles, jobs."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows]
metadata:
  hermes:
    tags: [linkedin, playwright, edge, cdp, scraper, b2b]
    category: devops
    related_skills: [fb-market]
    required: [Edge, Playwright]
---

# LinkedIn CDP Scraper

## When to use
- Scraping LinkedIn company pages, posts, profiles, job listings via CDP
- When APIs/MCP are unavailable or rate-limited
- Browser-based extraction when LinkedIn's WAF blocks direct requests

## Prerequisites
- Edge browser installed: `C:/Program Files (x86)/Microsoft/Edge/Application/msedge.exe`
- Playwright installed: `pip install playwright`
- PowerShell available (Git Bash `start` doesn't pass flags)
- User logged into LinkedIn in Edge
- Python 3.9+ (user has 3.11 ✓)
- Session file: `C:/Users/Tensaisan/linkedin_session.json` (from `save_linkedin_session.py`)

## Broken Approaches (do not use)
1. **curl/fetch LinkedIn** — Cloudflare/WAF blocked, returns login wall
2. **Jina Reader** — Blocked by LinkedIn
3. **Selenium with ChromeDriver** — Fingerprint detected faster than CDP
4. **Public LinkedIn APIs** — Require OAuth tokens, rate-limited
5. **Scraping without login** — LinkedIn shows login wall after 1-2 pages
6. **headless-only** — LinkedIn detects headless fingerprint (navigator.webdriver)
7. **Standard CSS selectors** — LinkedIn uses obfuscated CSS modules (e.g., `_1957d19a`, `_27506df7`). No `h1`, `.text-body-medium`, `[data-testid="inline-show-more-text"]` etc. exists. You MUST parse body text instead.

## Architecture

```
linkedin_cdp_config.yaml  ← Targets, selectors, paths
extract_linkedin.py       ← Extraction engine (config-driven)
linkedin_monitor.py       ← Daily runner
linkedin-cdp SKILL.md     ← This file
```

**How to add a company:** Edit `linkedin_cdp_config.yaml` → `companies` list.
**How to change output paths:** Edit `linkedin_cdp_config.yaml` → `output`.

## Workflow

### 1. Check CDP available
```
curl http://127.0.0.1:9222/json/version
```
If response → skip to step 3. If fails → step 2.

### 2. Start Edge with debug port
```
# Kill all Edge first, then start fresh (avoids msedgewebview2 orphan processes)
taskkill /F /IM msedge.exe /IM msedgewebview2.exe 2>&1
powershell.exe -Command "Start-Process 'C:/Program Files (x86)/Microsoft/Edge/Application/msedge.exe' -ArgumentList '--remote-debugging-port=9222', '--user-data-dir=C:/Users/Tensaisan/AppData/Local/Microsoft/Edge/User Data', '--no-first-run', '--no-default-browser-check'"
sleep 8
curl -s http://127.0.0.1:9222/json/version  # Verify
```

### 3. Connect Playwright to Edge via CDP
```python
async def connect_cdp():
    playwright = await async_playwright().start()
    browser = await playwright.chromium.connect_over_cdp("http://127.0.0.1:9222", timeout=15000)
    assert browser.is_connected(), "Browser disconnected"
    return browser, playwright
```

### 4. Create FRESH context (critical)
```python
# DO NOT reuse existing pages — they're tied to contexts that may close
browser, playwright = await connect_cdp()
ctx = await browser.new_context(
    user_agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
    viewport={"width": 1280, "height": 800},
)

# Inject saved cookies from linkedin_session.json
with open("C:/Users/Tensaisan/linkedin_session.json") as f:
    cookies = json.load(f).get("cookies", [])
for c in cookies:
    cookie = {
        "name": c["name"], "value": c["value"],
        "domain": c.get("domain", ".linkedin.com"),
        "path": c.get("/", "/"),
        "httpOnly": c.get("httpOnly", False),
        "secure": c.get("secure", True),
    }
    if c.get("expires"):
        cookie["expires"] = c["expires"]
    await ctx.add_cookies([cookie])

page = await ctx.new_page()
```

### 5. Verify login before scraping
```python
await page.goto("https://www.linkedin.com/feed/", timeout=60000, wait_until="domcontentloaded")
await asyncio.sleep(4)
title = await page.title()
if "login" in title.lower():
    raise Exception("Login wall - session expired")
print(f"Login valid: {title}")
```

### 6. Extract profile (BODY TEXT PARSING — correct approach)
LinkedIn uses obfuscated CSS modules. There are no `h1`, `.text-body-medium`, or `data-testid="inline-show-more-text"` elements. Parse `document.body.innerText` instead:
```python
async def extract_profile(page, profile_url):
    await page.goto(profile_url, timeout=60000, wait_until="domcontentloaded")
    await asyncio.sleep(5)
    
    # Scroll to load all content
    for _ in range(8):
        await page.keyboard.press("End")
        await asyncio.sleep(1)
    
    # Get raw body text
    body_text = await page.evaluate("() => document.body.innerText")
    
    # Parse sections from body text
    lines = [l.strip() for l in body_text.split('\n') if l.strip()]
    
    result = {"url": profile_url, "name": "", "headline": "", "location": "",
              "about": "", "experience": [], "education": [], "skills": [], "certifications": []}
    
    # Find name (first non-nav line after skip links)
    nav_words = {'home', 'my network', 'jobs', 'messaging', 'notifications', 'me', 'for business', 'skip to'}
    for line in lines:
        if any(nav in line.lower() for nav in nav_words) or len(line) < 5:
            continue
        result["name"] = line
        break
    
    # Find headline (next line with professional keywords after name)
    name_idx = next((i for i, l in enumerate(lines) if l == result["name"]), -1)
    if name_idx >= 0:
        for i in range(name_idx + 1, min(name_idx + 5, len(lines))):
            if any(kw in lines[i].lower() for kw in ['engineer', 'graduate', 'technician', 'manager', 'specialist']):
                result["headline"] = lines[i]
                break
    
    # Find location (geographic pattern)
    for line in lines:
        if 'Khartoum' in line or ', Sudan' in line:
            result["location"] = line
            break
    
    # Parse About/Experience/Education/Skills sections
    for section in ['About', 'Experience', 'Education', 'Skills']:
        for i, line in enumerate(lines):
            if line == section and i > 5:
                content = []
                for j in range(i + 1, min(i + 30, len(lines))):
                    if lines[j] in ['About', 'Experience', 'Education', 'Skills', 'Certifications']:
                        break
                    if lines[j].startswith('Show') or lines[j].startswith('… more'):
                        break
                    content.append(lines[j])
                if section == 'About':
                    result['about'] = '\n'.join(content)
                else:
                    result[section.lower()] = content
                break
    
    # Extract certifications from post content
    cert_keywords = ['certification', 'certified', 'completed', 'coursera', 'osha']
    for line in lines:
        if any(kw in line.lower() for kw in cert_keywords):
            result["certifications"].append(line)
    
    return result
```

### 7. Extract jobs
```python
async def extract_jobs(page, query, location):
    url = f"https://www.linkedin.com/jobs/search/?keywords={query.replace(' ', '%20')}&location={location.replace(' ', '%20')}&remote=true"
    result = {"query": query, "location": location, "jobs": []}
    
    await page.goto(url, timeout=60000, wait_until="domcontentloaded")
    await asyncio.sleep(5)
    
    for _ in range(8):
        await page.keyboard.press("End")
        await asyncio.sleep(2)
    
    jobs = await page.evaluate("""() => {
        const jobs = [];
        document.querySelectorAll('[data-testid="job-search-card"], .job-search-card, .jobs-search__results-list li').forEach(card => {
            const text = card.innerText.trim();
            if (text.length > 30) jobs.push(text);
        });
        if (jobs.length === 0) {
            document.querySelectorAll('div[aria-label]').forEach(d => {
                const text = d.innerText.trim();
                if (text.length > 40 && text.length < 1000) {
                    const hasJob = ['apply','senior','junior','years','experience','engineer','technician'].some(w => text.toLowerCase().includes(w));
                    if (hasJob) jobs.push(text);
                }
            });
        }
        return jobs.slice(0, 25);
    }""")
    
    result["jobs"] = jobs[:20]
    return result
```

## Error Handling
- **ConnectionError / ECONNREFUSED**: CDP not available → kill all Edge, start fresh via PowerShell
- **Login wall**: Session expired → run `save_linkedin_session.py` to re-login
- **TargetClosedError**: Reusing existing pages → create fresh context with `browser.new_context()`
- **Timeout on page load**: LinkedIn is slow — increase to 60000ms, use `domcontentloaded`
- **0 posts extracted**: Scroll more (15x), check selectors, LinkedIn UI changes frequently
- **Browser disconnected**: Kill all Edge, relaunch (don't reuse stale WS URL)
- **Profile lock**: Kill all Edge, relaunch
- **Rate limit (429)**: Wait 60s, reduce scroll count, increase delays between pages
- **Empty profile fields**: CSS selectors won't work — parse body text instead

## Observability
- Log fields: timestamp, company_slug, posts_found, error_code, duration_ms
- Alert thresholds: 0 posts for 3 consecutive runs → notify user
- What NOT to log: LinkedIn session tokens, cookies, personal identifiers

## Resilient Workflow (production pattern)
1. Check CDP → `curl http://127.0.0.1:9222/json/version`
2. Start Edge (if needed) → `edge_cdp_launcher.py`
3. Connect with retry → `connect_cdp(max_retries=3)` with exponential backoff
4. Health check → `browser.is_connected()` before each operation
5. Scroll → 10-15 End presses to load posts
6. Extract → `div[class*="feed-shared-update-v2"]` selector + post text
7. Login wall check → detect login state, prompt re-login
8. On disconnect → re-fetch WS URL, reconnect (don't reuse stale URL)
9. Log everything → timestamp, company_slug, posts_found, error_code, duration_ms

## Validation / Test Checklist
- [ ] Pre-flight: Edge running, CDP reachable, logged in
- [ ] Edge CDP started: `curl http://127.0.0.1:9222/json/version`
- [ ] Smoke test: Navigate to linkedin.com, verify login state
- [ ] Extraction test: Run extract_company on 1 company, verify posts returned
- [ ] Behavioral test: Scroll 10x, verify more posts loaded
- [ ] Error test: Simulate timeout, verify graceful fallback
- [ ] Output test: Verify result.json generated

## Troubleshooting
- **CDP connection fails**: Edge not running with `--remote-debugging-port=9222`. Run `edge_cdp_launcher.py` to auto-start
- **Git Bash `start msedge` fails**: Git Bash doesn't pass flags. Use `edge_cdp_launcher.py` (PowerShell-based)
- **Login wall**: Session expired → re-login in Edge, retry with exponential backoff
- **0 posts extracted**: LinkedIn UI changes selectors frequently — update `feed-shared-update-v2` to current class
- **Profile lock**: Kill all Edge, relaunch
- **Rate limited (429)**: Reduce scroll count, increase delays, wait 60s between companies
- **Stale WS URL**: Always re-fetch `/json/version` for fresh URL on reconnect
- **Selectors break**: LinkedIn updates class names weekly — monitor and update selectors in config

## Files
- Config: `C:/Users/Tensaisan/linkedin_cdp_config.yaml`
- Extraction: `C:/Users/Tensaisan/extract_linkedin.py`
- Monitor: `C:/Users/Tensaisan/linkedin_monitor.py`
- Output: `C:/Users/Tensaisan/linkedin_results/`

## Verification
- Edge CDP: `curl http://127.0.0.1:9222/json/version`
- Skill loads: `skill_view(name="devops\\linkedin-cdp-scraper")`