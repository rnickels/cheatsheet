# Upgrade NPM on Windows

[Github Source](https://github.com/felixrieseberg/npm-windows-upgrade)

1. run in PowerShell as Administrator

~~~bash
$ Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force
$ npm install -g npm-windows-upgrade
$ npm-windows-upgrade
~~~