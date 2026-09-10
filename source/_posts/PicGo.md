---
title: PicGo安装使用
date: 2026-07-31 15:32:00
tags:
- PicGo
---

# 安装

[官网]: https://picgo.app/

```bash
npm install picgo -g

picgo set uploader

cat ~/.picgo/config.json
```

## GitHub图床

1. 新建一个公共仓库，例如：md-bed
2. 进入 https://github.com/settings/tokens 创建一个Tokens（classic），勾选repo所有，Expiration选择No expiration
3. 输入token
4. ![image-20260731100454433](https://cdn.jsdelivr.net/gh/XJLZ/md-bed/blog/image-20260731100454433.png)

```
{
  "picBed": {
    "uploader": "github",
    "current": "github",
    "github": {
      "repo": "XJLZ/md-bed",
      "branch": "main",
      "token": "ghp_xxxxxxxxxxxxx",
      "path": "blog/",
      "customUrl": "https://cdn.jsdelivr.net/gh/XJLZ/md-bed",
      "_id": "a5ea4e0e-0abc-48da-92b3-c4d4e7e82551",
      "_configName": "md-bed",
      "_createdAt": 1784029994336,
      "_updatedAt": 1784031434867
    }
  },
  "picgoPlugins": {},
  "uploader": {
    "github": {
      "configList": [
        {
          "repo": "XJLZ/md-bed",
          "branch": "main",
          "token": "ghp_xxxxxxxxxxxxx",
          "path": "blog/",
          "customUrl": "https://cdn.jsdelivr.net/gh/XJLZ/md-bed",
          "_id": "a5ea4e0e-0abc-48da-92b3-c4d4e7e82551",
          "_configName": "md-bed",
          "_createdAt": 1784029994336,
          "_updatedAt": 1784031434867
        }
      ],
      "defaultId": "a5ea4e0e-0abc-48da-92b3-c4d4e7e82551"
    }
  }
}
```

