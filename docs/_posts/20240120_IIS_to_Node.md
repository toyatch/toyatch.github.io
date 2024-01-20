---
layout: post
title:  "IISにNodeJSのWebアプリを配置する"
date:   2024-01-20 10:00:00 +0900
tags: node
---

IISはIIS側に設定したポート番号でリクエストを待ち受ける。
IISはリクエスト受信後、Nodeのアプリに転送する。
Nodeのアプリのポート番号は、IISに設定したポート番号とは異なり、HTTP_PLATFORM_PORTを設定しなければならない。

例:
1. IISのポート番号を3000に設定する
2. web.config内で起動するNodeアプリのポート番号は環境変数```HTTP_PLATFORM_PORT```と一致させる

web.confgig
```
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add
                name="httpPlatformHandler"
                path="*"
                verb="*"
                modules="httpPlatformHandler"
                resourceType="Unspecified" />
        </handlers>
        <httpPlatform
            processPath="node" arguments="index.js">
            <environmentVariables>
                <environmentVariable name="PORT" value="%HTTP_PLATFORM_PORT%" />
            </environmentVariables>
        </httpPlatform>
    </system.webServer>
</configuration>
```

index.ts
```
import express from "express";

const port = Number(process.env.PORT || 3000);
const app = express();

app.get("/", (_req, res) => {
  res.status(200).send();
});

app.listen(port);

```

以上。
