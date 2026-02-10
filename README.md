# pm2-meteor3

> **Modern fork of [pm2-meteor](https://github.com/andruschka/pm2-meteor)** - Deploy Node.js apps with PM2

This is a maintained fork of the original pm2-meteor project with the following improvements:
- ✅ **Multi-project support** - Deploy Express, Next.js, and Meteor apps
- ✅ **Meteor 3.x compatibility** - Works with latest Meteor versions
- ✅ **Modern Node.js SSH client** - Uses node-ssh instead of deprecated nodemiral
- ✅ **Removed CoffeeScript dependency** - Pure JavaScript implementation
- ✅ **Node.js 14+ support** - Compatible with modern Node.js versions
- ✅ **Active maintenance** - Regular updates and bug fixes

**Original project:** [andruschka/pm2-meteor](https://github.com/andruschka/pm2-meteor) (MIT License)

---

<div align="center">
  <img src="_logo.png" style="width: 50%; margin:0 auto; border-radius: 8px;" />
</div>
  

**pm2-meteor3** is a CLI tool that deploys your **Express**, **Next.js**, **Vite/React**, or **Meteor** apps to any server and runs them with PM2. No Docker, no complex CI/CD setup - just one command to deploy from your local machine.

## Supported Project Types

| Type | Build Process | Entry Point |
|------|--------------|-------------|
| **Express** | `npm install --production` | Your entry file (e.g., `src/index.js`) |
| **Next.js** | `npm install` + `npm run build` | `next start` |
| **Vite/React** | `npm run build` → `dist/` | `serve dist -s -l PORT` |
| **Meteor** | `meteor build` | `bundle/main.js` |

## Features
1. **Deploy Express, Next.js, Vite/React, and Meteor apps** with the same tool
2. Deploy from local directory or git repo  
3. Deploy to almost any server (Ubuntu, Debian, FreeBSD, etc.)  
4. Uses PM2 for process management with load balancing
5. Zero-downtime deployments with automatic backup
6. Scale your app in realtime with one command  
7. Revert to previous version if something goes wrong  

### Why PM2?
PM2 is a process manager, that will run/restart Nodejs apps, just like forever - but:
- PM2 has build in load balancing and scaling features
- PM2 also runs bash / python / ruby / coffee / php / perl
- We tested PM2 with some of our complex Meteor apps and it performed well (while forever crashed them without any notable reasons)
- more under [pm2.keymetrics.io](http://pm2.keymetrics.io/)

## Installation (on your dev machine)
```
$ npm i -g pm2-meteor3
```
You should have Nodejs, npm and PM2 installed on your host machine.  
pm2-meteor3 won't install global tools on your server.

## Quick Start

### Express API Example
```bash
cd my-express-api
pm2-meteor3 init        # Select "express" when asked
pm2-meteor3 deploy      # Build, upload, and start with PM2
```

### Next.js App Example
```bash
cd my-nextjs-app
pm2-meteor3 init        # Select "nextjs" when asked
pm2-meteor3 deploy      # Build, upload, and start with PM2
```

### Vite/React App Example
```bash
cd my-vite-app
pm2-meteor3 init        # Select "vite" when asked
pm2-meteor3 deploy      # Build, upload, and start with PM2
```

### Meteor App Example
```bash
cd my-meteor-app
pm2-meteor3 init        # Select "meteor" when asked
pm2-meteor3 deploy      # Build, upload, and start with PM2
```

---

## Usage

### 1. Init a pm2-meteor3.json config file
```bash
$ cd your-app
$ pm2-meteor3 init
```
The wizard will ask you:
1. **App name** - Name for PM2 process
2. **Project type** - express, nextjs, vite, or meteor
3. **App location** - Local path or git URL
4. **Server details** - Host, username, auth method
5. **Other options** - Port, instances, etc.

### 2. Review the generated pm2-meteor3.json file

#### Express Config Example
```json
{
  "appName": "my-api",
  "appType": "express",
  "appLocation": {
    "local": "./"
  },
  "entryPoint": "src/index.js",
  "env": {
    "NODE_ENV": "production",
    "PORT": 3000
  },
  "server": {
    "host": "my-server.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "cluster_mode",
    "instances": 2
  }
}
```

#### Next.js Config Example
```json
{
  "appName": "my-frontend",
  "appType": "nextjs",
  "appLocation": {
    "local": "./"
  },
  "nextBuildCommand": "npm run build",
  "env": {
    "NODE_ENV": "production",
    "PORT": 3000
  },
  "server": {
    "host": "my-server.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "cluster_mode",
    "instances": 2
  }
}
```

#### Vite/React Config Example
```json
{
  "appName": "my-vite-app",
  "appType": "vite",
  "appLocation": {
    "local": "./"
  },
  "viteBuildCommand": "npm run build",
  "env": {
    "NODE_ENV": "production",
    "PORT": 3000
  },
  "server": {
    "host": "my-server.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "fork_mode",
    "instances": 1
  }
}
```

#### Meteor Config Example
```json
{
  "appName": "my-meteor-app",
  "appType": "meteor",
  "appLocation": {
    "local": "./"
  },
  "meteorSettingsLocation": "./settings/production.json",
  "meteorBuildFlags": "--architecture os.linux.x86_64",
  "env": {
    "ROOT_URL": "https://my-app.com",
    "PORT": 3000,
    "MONGO_URL": "mongodb://localhost:27017/myapp"
  },
  "server": {
    "host": "my-server.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "cluster_mode",
    "instances": 2
  }
}
```

### Config Options Reference

| Option | Type | Description |
|--------|------|-------------|
| `appName` | string | Name of your app (used by PM2) |
| `appType` | string | `express`, `nextjs`, or `meteor` |
| `appLocation.local` | string | Local path to your app |
| `appLocation.git` | string | Git URL (alternative to local) |
| `appLocation.branch` | string | Git branch (default: master) |
| `entryPoint` | string | Entry file for Express (default: `src/index.js`) |
| `nextBuildCommand` | string | Build command for Next.js (default: `npm run build`) |
| `viteBuildCommand` | string | Build command for Vite (default: `npm run build`) |
| `meteorSettingsLocation` | string | Path to Meteor settings.json |
| `meteorBuildFlags` | string | Meteor build flags |
| `prebuildScript` | string | Script to run before build |
| `env` | object | Environment variables |
| `server.host` | string | Server hostname or IP |
| `server.username` | string | SSH username |
| `server.password` | string | SSH password (or use pem) |
| `server.pem` | string | Path to SSH private key |
| `server.port` | number | SSH port (default: 22) |
| `server.deploymentDir` | string | Where to deploy on server |
| `server.exec_mode` | string | `cluster_mode` or `fork_mode` |
| `server.instances` | number | Number of PM2 instances |
| `server.interpreter` | string | Custom Node.js path (for nvm) |
```

### 3. Deploy your app
```
$ pm2-meteor3 deploy
```
If you already have deployed this app before, the old app tar-bundle will be moved to a ./backup directory.  

##### If you want to only reconfigure settings / env changes
```
$ pm2-meteor3 reconfig
```
Will send new pm2-env file to server and hard-restart your app.

##### If something goes wrong: revert to previous version
```
$ pm2-meteor3 revert
```
Will unzip the old bundle.tar.gz and restart the app


### 4. Control your app
```
$ pm2-meteor3 start
$ pm2-meteor3 stop
$ pm2-meteor3 status
$ pm2-meteor3 logs
```

### 5. SCALE your app
Start 2 more instances:
```
$ pm2-meteor3 scale +2
```

Down/Upgrade to 4 instances
```
$ pm2-meteor3 scale 4
```

### 6. Undeploy your app (DANGEROUS)
To delete your app from the PM2 deamon and delete all app files add "allowUndeploy":true to your pm2-meteor3 setting, then:  
```
$ pm2-meteor3 undeploy
```

## If you want to deploy the bundle by yourself
```
$ pm2-meteor3 generateBundle
```
then transfer it to your machine, unzip it and run
```
$ pm2 start pm2-env.json
```
## Example Configs

### Express API with MongoDB
```json
{
  "appName": "api-server",
  "appType": "express",
  "appLocation": {
    "local": "./"
  },
  "entryPoint": "src/index.js",
  "env": {
    "NODE_ENV": "production",
    "PORT": 4000,
    "MONGODB_URI": "mongodb://localhost:27017/mydb",
    "JWT_SECRET": "your-secret-key"
  },
  "server": {
    "host": "api.example.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "cluster_mode",
    "instances": 4
  }
}
```

### Next.js Frontend with Custom Build
```json
{
  "appName": "frontend",
  "appType": "nextjs",
  "appLocation": {
    "local": "./"
  },
  "nextBuildCommand": "npm run build",
  "env": {
    "NODE_ENV": "production",
    "PORT": 3000,
    "NEXT_PUBLIC_API_URL": "https://api.example.com"
  },
  "server": {
    "host": "example.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "cluster_mode",
    "instances": 2
  }
}
```

### Meteor App from Git Repository
```json
{
  "appName": "meteor-app",
  "appType": "meteor",
  "appLocation": {
    "git": "https://user:token@github.com/user/repo.git",
    "branch": "production"
  },
  "meteorSettingsLocation": "settings/production.json",
  "meteorSettingsInRepo": true,
  "meteorBuildFlags": "--architecture os.linux.x86_64",
  "env": {
    "ROOT_URL": "https://app.example.com",
    "PORT": 3000,
    "MONGO_URL": "mongodb://localhost:27017/meteor"
  },
  "server": {
    "host": "app.example.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "exec_mode": "cluster_mode",
    "instances": 2
  }
}
```

### Using JavaScript Config (pm2-meteor3.js)
You can use a `.js` file instead of `.json` for dynamic configuration:

```javascript
// pm2-meteor3.js
const appName = "my-api";

module.exports = {
  appName,
  appType: "express",
  appLocation: {
    local: "./"
  },
  entryPoint: "src/index.js",
  env: {
    NODE_ENV: "production",
    PORT: process.env.PORT || 3000,
    DATABASE_URL: process.env.DATABASE_URL,
    API_KEY: process.env.API_KEY
  },
  server: {
    host: process.env.DEPLOY_HOST || "example.com",
    username: "deploy",
    pem: "~/.ssh/id_rsa",
    deploymentDir: "/opt/apps",
    exec_mode: "cluster_mode",
    instances: 2
  }
};
```

Then deploy with environment variables:
```bash
DATABASE_URL='postgres://...' API_KEY='xxx' pm2-meteor3 deploy
```

### Using nvm on Server
If you use nvm on your server, set the `loadProfile` and optionally `interpreter`:

```json
{
  "appName": "my-app",
  "appType": "express",
  "appLocation": { "local": "./" },
  "entryPoint": "src/index.js",
  "env": {
    "NODE_ENV": "production",
    "PORT": 3000
  },
  "server": {
    "host": "example.com",
    "username": "deploy",
    "pem": "~/.ssh/id_rsa",
    "deploymentDir": "/opt/apps",
    "loadProfile": "/home/deploy/.nvm/nvm.sh",
    "interpreter": "/home/deploy/.nvm/versions/node/v20.10.0/bin/node",
    "exec_mode": "fork_mode",
    "instances": 1
  }
}
```

---

## Server Requirements

Your target server needs:
- **Node.js** (14+ recommended)
- **npm**
- **PM2** (`npm install -g pm2`)
- SSH access (password or key-based)

pm2-meteor3 will NOT install these for you - make sure they're available before deploying.

## How It Works

1. **Build locally** - Your app is built on your machine
2. **Create tarball** - A `bundle.tar.gz` is created with your source code and config
3. **Upload via SSH** - The tarball is uploaded to your server
4. **Install dependencies** - `npm install --production` runs on the server
5. **Start with PM2** - PM2 starts your app with the configured settings

### Build Process by App Type

| Type | Local Build | Server Install | Entry Point |
|------|-------------|----------------|-------------|
| **Express** | Copy source files | `npm install --production --legacy-peer-deps` | Your entry file |
| **Next.js** | `npm run build` (if node_modules exists, skip npm install) | `npm install --production --legacy-peer-deps` | `next start` |
| **Vite** | `npm run build` → `dist/` (skip npm install if node_modules exists) | `npm install serve` | `serve dist -s -l PORT` |
| **Meteor** | `meteor build` | `npm install --production` (in programs/server) | `main.js` |

This means:
- No Docker required on server
- No Git required on server
- Native modules are compiled on target server (correct architecture)
- Small upload size (no node_modules in tarball for Express/Next.js)

### Windows Compatibility
pm2-meteor3 works on Windows, macOS, and Linux. All file operations use cross-platform Node.js APIs.

## Troubleshooting

### "Missing node/npm/pm2" error
Make sure Node.js, npm, and PM2 are installed on your server and accessible via SSH.

### Permission denied
Check your SSH credentials (password or pem file path).

### App not starting
Check PM2 logs on server:
```bash
ssh user@server "pm2 logs your-app-name"
```

### Next.js: Module not found
Make sure your `next.config.js` doesn't use features that require build-time only modules.

---

## License

MIT License - Based on [andruschka/pm2-meteor](https://github.com/andruschka/pm2-meteor)
