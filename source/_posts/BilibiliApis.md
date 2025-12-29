---
title: bilibiliApis
date: 2021-06-11 09:32:00
tags:
- bilibili
- Api
---



#### 1. 用户搜索

```
https://api.bilibili.com/x/web-interface/search/type?search_type=bili_user&page=1&changing=mid&__refresh__=true&highlight=1&single_column=0&keyword=新华社
```

param:

```
keyword=新华社
```

result：

```json
{
    "code": 0,
    "message": "0",
    "ttl": 1,
    "data": {
        "seid": "16508985117305632561",
         .....
        "result": [
            {
                "type": "bili_user",
                "mid": 473837611,
                "uname": "新华社",
                "usign": "我是稳中带皮皮中有稳稳得一皮的鲜花舍。本社专营各种新闻报道，欢迎各位选购。",
                "fans": 2165506,
                "videos": 1414,
                "upic": "//i2.hdslb.com/bfs/face/3e9def9e060a7654df1e65d4fe9d35c5e121e078.jpg",
                "verify_info": "",
                "level": 6,
                "gender": 3,
                "is_upuser": 1,
                "is_live": 1,
                "room_id": 22015508,
                "res": [
                    {
                        "aid": 546096095,
                        "bvid": "BV1Xq4y1576Q",
                        "title": "海军招生宣传片《踏浪扬帆 崭新征程》重磅发布",
                        "pubdate": 1623342182,
                        "arcurl": "http://www.bilibili.com/video/av546096095",
                        "pic": "//i2.hdslb.com/bfs/archive/cba9229c937b18f2bd7b4a60af10d885bd5ed774.jpg",
                        "play": "27772",
                        "dm": 67,
                        "coin": 844,
                        "fav": 486,
                        "desc": "这里有战舰乘风破浪，这里有潜艇潜行大洋，这里有战机巡航海天，这里有陆战精英全域出击，这里还有导弹剑指苍穹……报考海军院校，加入海军方阵，人民海军期待你的到来！",
                        "duration": "4:22",
                        "is_pay": 0,
                        "is_union_video": 1
                    }
                ],
                "official_verify": {
                    "type": 1,
                    "desc": "新华社官方账号"
                },
                "hit_columns": []
            }
        ],
        "show_column": 0
    }
}
```



#### 2. 用户视频

```
https://api.bilibili.com/x/space/arc/search?ps=10&tid=0&pn=1&order=pubdate&jsonp=jsonp&mid=473837611
```

param:

```
mid=473837611
```

result：

```
{
    "code": 0,
    "message": "0",
    "ttl": 1,
    "data": {
        "list": {
            "tlist": {...},
            "vlist": [
                {
                    "comment": 235,
                    "typeid": 204,
                    "play": 52226,
                    "pic": "http://i0.hdslb.com/bfs/archive/adc6d430745a30208de129183400ec23d78c937d.jpg",
                    "subtitle": "",
                    "description": "据法新社太子港7月9日报道，海地警方当地时间8日表示，对总统莫伊兹实施刺杀的至少有28人，并补充说其中26人是哥伦比亚人，另2人是出身海地的美国人。报道称，海地国家警察总局局长莱昂·夏尔在新闻发布会上表示：“我们已经逮捕15名哥伦比亚人和2名出身海地的美国人。3名哥伦比亚人被杀，其余8人在逃。”（参考消息）\n\n视频来源：今日俄罗斯       站内编辑：藤椒面",
                    "copyright": "1",
                    "title": "海地警方逮捕击毙多名暗杀总统嫌犯，其中两名美国人",
                    "review": 0,
                    "author": "新华社",
                    "mid": 473837611,
                    "created": 1625800679,
                    "length": "01:31",
                    "video_review": 51,
                    "aid": 334065170,
                    "bvid": "BV1Zw411R7s7",
                    "hide_click": false,
                    "is_pay": 0,
                    "is_union_video": 0,
                    "is_steins_gate": 0,
                    "is_live_playback": 0
                }
            ]
        },
        "page": {
            "pn": 1,
            "ps": 10,
            "count": 1622
        },
        "episodic_button": {
            "text": "播放全部",
            "uri": "//www.bilibili.com/medialist/play/473837611?from=space"
        }
    }
}
```

#### 3. 用户动态

```
https://api.vc.bilibili.com/dynamic_svr/v1/dynamic_svr/space_history?offset_dynamic_id=0&need_top=1&platform=web&host_uid=14453048
```

param:

```
host_uid=14453048
```

result：

```
{
    "code": 0,
    "msg": "",
    "message": "",
    "data": {
        ...
        "cards": [
            {
                "desc": {
                    "uid": 14453048,
                    "type": 2,
                    "rid": 144211469,
                    "acl": 0,
                    "view": 3843,
                    "repost": 12,
                    "comment": 2,
                    "like": 203,
                    "is_liked": 0,
                    "dynamic_id": 544927493266101898,
                    "timestamp": 1625714219,
                    "pre_dy_id": 0,
                    "orig_dy_id": 0,
                    "orig_type": 0,
                    "user_profile": {...},
                    "uid_type": 1,
                    "stype": 0,
                    "r_type": 1,
                    "inner_id": 0,
                    "status": 1,
                    "dynamic_id_str": "544927493266101898",
                    "pre_dy_id_str": "0",
                    "orig_dy_id_str": "0",
                    "rid_str": "144211469"
                },
                "card": "{\"item\":{\"at_control\":\"\",\"category\":\"daily\",\"description\":\"竹行简&质量  动漫美图推荐【552】 \\n点击「转发」立刻永久保存美图[tv_点赞]\\n\\n动态内图片均为互联网整理收集，不会作为商业用途使用。侵权即删致歉（喜欢的作品ID可以通过https:\\/\\/saucenao.com查找作者）\\n#动漫美图##动漫壁纸##壁纸##二次元##动漫##萌妹子##二次元美图##美图##插画##P站搬运##FGO##明日方舟##原神#\",\"id\":144211469,\"is_fav\":0,\"pictures\":[{\"img_height\":3600,\"img_size\":7635.96,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/ae1be86697b3bd010ac931d9ed9a29f94c9a5959.png\",\"img_tags\":null,\"img_width\":2525},{\"img_height\":3285,\"img_size\":1494.98,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/86db901fc1e220b405666e527128ec0eb29dbdd8.jpg\",\"img_tags\":null,\"img_width\":2600},{\"img_height\":3720,\"img_size\":3704.7,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/5be972a75706793f00adfb0dbfcee5165ec3ed89.png\",\"img_tags\":null,\"img_width\":2381},{\"img_height\":3883,\"img_size\":6071.85,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/a6c6f84450fa480b118052759a626650516427ec.png\",\"img_tags\":null,\"img_width\":2533},{\"img_height\":6017,\"img_size\":15330.47,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/69ce25029248e63ff69df90acbca307f9fc1c400.png\",\"img_tags\":null,\"img_width\":4254},{\"img_height\":2902,\"img_size\":5212.84,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/8169463cb82f88d0a3c3e74749c4bcd7774a02d5.png\",\"img_tags\":null,\"img_width\":2246},{\"img_height\":2400,\"img_size\":6538.63,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/7751321725a2dbcdd4c8f372b3f3e5b7848a774d.png\",\"img_tags\":null,\"img_width\":3200},{\"img_height\":7016,\"img_size\":17883.63,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/1910a1f598fb7a19bf67a74bf2263ba914c84700.jpg\",\"img_tags\":null,\"img_width\":4961},{\"img_height\":1739,\"img_size\":775.91,\"img_src\":\"https:\\/\\/i0.hdslb.com\\/bfs\\/album\\/1688c404be149fce94d3330e85e4f9d6c0779096.jpg\",\"img_tags\":null,\"img_width\":1120}],\"pictures_count\":9,\"reply\":2,\"role\":[],\"settings\":{\"copy_forbidden\":\"0\"},\"source\":[],\"title\":\"\",\"upload_time\":1625714219},\"user\":{\"head_url\":\"https:\\/\\/i1.hdslb.com\\/bfs\\/face\\/1e68054ebba6bb6c511c1a25ea5ff05f33ca87b2.jpg\",\"name\":\"竹行简\",\"uid\":14453048,\"vip\":{\"avatar_subscript\":1,\"due_date\":1630080000000,\"label\":{\"label_theme\":\"annual_vip\",\"path\":\"\",\"text\":\"年度大会员\"},\"nickname_color\":\"#FB7299\",\"status\":1,\"theme_type\":0,\"type\":2,\"vip_pay_type\":1}}}",
                "extend_json": "{\"from\":{\"emoji_type\":1,\"from\":\"\",\"up_close_comment\":0,\"verify\":{\"asw\":{\"fl\":15,\"nv\":1},\"sw\":{\"fl\":15,\"nv\":1}}},\"like_icon\":{\"action\":\"\",\"action_url\":\"\",\"end\":\"\",\"end_url\":\"\",\"start\":\"\",\"start_url\":\"\"},\"topic\":{\"is_attach_topic\":1}}",
                "extra": {
                    "is_space_top": 0
                },
                "display": {...}
            }
        ],
        "next_offset": 543888523497252525,
        "_gt_": 0
    }
}
```

#### 4. 用户相簿

```
https://api.bilibili.com/x/dynamic/feed/draw/doc_list?page_num=0&page_size=30&biz=all&jsonp=jsonp&uid=14453048
```

param:

```
uid=14453048
```

result：

```
{
    "code": 0,
    "message": "0",
    "ttl": 1,
    "data": {
        "items": [
            {
                "doc_id": 144211469,
                "poster_uid": 14453048,
                "title": "",
                "description": "竹行简&质量  动漫美图推荐【552】 \n点击「转发」立刻永久保存美图[tv_点赞]\n\n动态内图片均为互联网整理收集，不会作为商业用途使用。侵权即删致歉（喜欢的作品ID可以通过https://saucenao.com查找作者）\n#动漫美图##动漫壁纸##壁纸##二次元##动漫##萌妹子##二次元美图##美图##插画##P站搬运##FGO##明日方舟##原神#",
                "pictures": [
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/ae1be86697b3bd010ac931d9ed9a29f94c9a5959.png",
                        "img_width": 2525,
                        "img_height": 3600,
                        "img_size": 7635.96,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/86db901fc1e220b405666e527128ec0eb29dbdd8.jpg",
                        "img_width": 2600,
                        "img_height": 3285,
                        "img_size": 1494.98,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/5be972a75706793f00adfb0dbfcee5165ec3ed89.png",
                        "img_width": 2381,
                        "img_height": 3720,
                        "img_size": 3704.7,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/a6c6f84450fa480b118052759a626650516427ec.png",
                        "img_width": 2533,
                        "img_height": 3883,
                        "img_size": 6071.85,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/69ce25029248e63ff69df90acbca307f9fc1c400.png",
                        "img_width": 4254,
                        "img_height": 6017,
                        "img_size": 15330.47,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/8169463cb82f88d0a3c3e74749c4bcd7774a02d5.png",
                        "img_width": 2246,
                        "img_height": 2902,
                        "img_size": 5212.84,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/7751321725a2dbcdd4c8f372b3f3e5b7848a774d.png",
                        "img_width": 3200,
                        "img_height": 2400,
                        "img_size": 6538.63,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/1910a1f598fb7a19bf67a74bf2263ba914c84700.jpg",
                        "img_width": 4961,
                        "img_height": 7016,
                        "img_size": 17883.63,
                        "img_tags": null
                    },
                    {
                        "img_src": "https://i0.hdslb.com/bfs/album/1688c404be149fce94d3330e85e4f9d6c0779096.jpg",
                        "img_width": 1120,
                        "img_height": 1739,
                        "img_size": 775.91,
                        "img_tags": null
                    }
                ],
                "count": 9,
                "ctime": 1625714219,
                "view": 3846,
                "like": 0,
                "dyn_id": "544927493266101898"
            } 
        ]
    }
}
```

