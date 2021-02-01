```json
"build": {
    "appId": "cpy.com",
    "productName": "CPY",
    "asar": true,
    "compression": "normal",
    "directories": {
      "output": "packge"
    },
    "publish": [{
      "provider":"generic",
      "url": "http://172.18.79.225:8000/"
    }],
    "extraFiles": [
      {
        "from": "./clientDesktop",
        "to": "clientDesktop"
      }
    ],
    "mac": {
      "target": [
        "dmg",
        "zip"
      ]
    },
    "win": {
      "icon": "src/assets/img/windowFavicon.ico",
      "target": [
        "nsis"
      ]
    },
    "nsis": {
      "oneClick": false,
      "allowElevation": true,
      "allowToChangeInstallationDirectory": true,
      "installerIcon": "src/assets/img/favicon.ico",
      "uninstallerIcon": "src/assets/img/favicon.ico",
      "installerHeaderIcon": "src/assets/img/favicon.ico",
      "createDesktopShortcut": true,
      "createStartMenuShortcut": true
    }
  },
```