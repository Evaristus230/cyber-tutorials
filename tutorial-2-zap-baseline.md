# 🛡️ Tutorial 2: ZAP Baseline Scan + Manual Verification — Finding What Automation Misses

> **Goal**: Run an automated ZAP baseline scan against Juice Shop, then manually verify and escalate findings.  
> **Lab**: [`cyber-lab-env`](https://github.com/Evaristus230/cyber-lab-env) (Juice Shop @ `http://localhost:3000`, ZAP API @ `http://localhost:8090`)  
> **Tools**: `curl`, ZAP API, browser DevTools

---

## 🚀 Step 1: Trigger a ZAP Baseline Scan via API

Since ZAP is running in **headless daemon mode** (thanks to your `docker-compose.yml`), we use its REST API:

```bash
# Start a new session (optional but clean)
curl "http://localhost:8090/JSON/core/action/newSession/"

# Spider the site (discover URLs)
curl "http://localhost:8090/JSON/spider/action/scan/?url=http://juice-shop:3000"

# Wait ~10 sec for spider to finish, then:
# Run an active scan
curl "http://localhost:8090/JSON/ascan/action/scan/?url=http://juice-shop:3000&recurse=true&inScopeOnly=false"

curl "http://localhost:8090/JSON/ascan/view/status/"
# Returns: {"status":"65"} → 65% complete
curl "http://localhost:8090/JSON/core/view/alerts/?baseurl=http://juice-shop:3000"

{
  "alert": "Cross Site Scripting (Reflected)",
  "risk": "Medium",
  "url": "http://juice-shop:3000/#/search",
  "param": "q",
  "evidence": "<script>alert(1)</script>"
}
