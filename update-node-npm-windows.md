The new best way to upgrade NPM on Windows:

https://github.com/felixrieseberg/npm-windows-upgrade

Run PowerShell as Administrator:

---
Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force
npm install -g npm-windows-upgrade
npm-windows-upgrade
---