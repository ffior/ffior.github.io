---
layout: post
title: Tauri 2.0 - Updater自动更新指南
date: 2025-10-27 10:30:00
categories: ["rust", "tauri"]
tags: [rust, tauri, 桌面端, 自动更新]
---

## Tauri 2.0 Updater自动更新指南

Tauri2.0 将原来的updater 单独封装成了插件plugins-workspace/plugins/updater
tauri.conf.json中的配置也从updater变成了plugins.updater，具体可以参考Upgrade from Tauri 1.0 | Tauri

### 安装插件
```shell
pnpm tauri add updater
pnpm tauri add process
```

### 通过官方的脚手架安装会自动帮我们在rust中注册插件和安装npm依赖
```javascript
import { check } from "@tauri-apps/plugin-updater";
import { relaunch } from "@tauri-apps/plugin-process";

const update = await check();

if (update?.available) {
    console.log(
        `found update ${update.version} from ${update.date} with notes ${update.body}`
    );
    let downloaded = 0;
    let contentLength: any = 0;
    // 也可以分开调用 update.download() 和 update.install()
    await update.downloadAndInstall((event) => {
        switch (event.event) {
            case "Started":
                contentLength = event.data.contentLength;
                console.log(
                    `started downloading ${event.data.contentLength} bytes`
                );
                break;
            case "Progress":
                downloaded += event.data.chunkLength;
                console.log(
                    `downloaded ${downloaded} from ${contentLength}`
                );
                break;
            case "Finished":
                console.log("download finished");
                break;
        }
    });

    console.log("update installed");
    // 此处 relaunch 前最好询问用户
    await relaunch();
}

```

### 生成signature的流程
#### 安装 tauri-cli
```shell
cargo install tauri-cli --locked
```

#### 生成密钥对
- 执行以下命令生成私钥（private-key.pem）和公钥（public-key.pem）
- 私钥（private-key.pem）：必须严格保密，用于对安装包签名（不要提交到代码仓库）
- 公钥（public-key.pem）：需要嵌入到应用中，用于客户端验证签名（需配置到 tauri.conf.json）
```shell
cargo tauri signer generate --write-keys tauri-signing-privatekey.pem
```

### 配置插件
```json
{
  "productName": "tauri-starter",
  "version": "0.1.0",
  // ...
  // 核心配置
  "plugins": {
    "updater": {
      "endpoints": [
        "https://releases.myapp.com/{{target}}/{{arch}}/{{current_version}}",
        // or a static github json file
        "https://github.com/user/repo/releases/latest/download/latest.json"
      ],
      "pubkey": "tauri-signing-privatekey.pem.pub文件中的内容"
    }
  },
  //...
}

```

#### latest.json文件产出
```json
{
    "version": "0.1.6",
    "notes": "我是更新说明第一次测试更新",
    "pub_date": "2025-10-27T10:33:50.626Z",
    "platforms": {
        "darwin-aarch64": {
            "url": "https://bj.7niu.com/v1/app/tauri/tauri-starter.app.tar.gz",
            "signature": "dW50cnVzdGVkIGNvbW1lbnQ6IHNpZ25hdHVyZSBmcm9tIHRhdXJpIHNlY3JldCBrZXkKUlVRK2gwQnk0RVMvdzI2UW5ybzNPUWZMcy9vbUFKOTRFbnVQaUJXYmw4a2ZrRUtuUW5memxMRlFrdUp0U2dDVlJvckEwdGtCcUdIRXl6NTFmUnhNQWdrRnRpb20ycU95THdjPQp0cnVzdGVkIGNvbW1lbnQ6IHRpbWVzdGFtcDoxNzYxNTQ2NjEwCWZpbGU6dGF1cmktc3RhcnRlci5hcHAudGFyLmd6CmdYeUVKT0ZKdUVPRGVPUlZoZG11b0FPZ1RQQXhQSGxvQTEyS1VKbUdHb1lxR3dBMGNER24rRXZMYis3N1NlcUJ5S2ZKNU5sZW5MMVpONW1EQUg3VEJBPT0K"
        }
    }
}
```

#### 签名
- macos下的签名文件，在tauri-starter.app.tar.gz同目录下，tauri-starter.app.tar.gz.sig中，将内容复制到上面latest.json的signature字段
- windows下的签名文件
- linux下的签名文件

#### 上传签名文件
- 上传tauri-starter.app.tar.gz到latest.json指定的url上
- 上传latest.json到指定的url上

#### 测试
- 打开应用，会发现自动更新，以及自定重启应用

## 推荐
- [Upgrade from Tauri 2.0](https://v2.tauri.app/start/migrate/from-tauri-2-beta/)