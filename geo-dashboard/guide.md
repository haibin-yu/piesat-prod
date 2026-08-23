# 航天宏图 GEO 监测仪表盘 · 填写说明

## 这是什么

一个纯静态的监测仪表盘，追踪「航天宏图技术博客文章在搜索 / AI 引擎里的可见性」。零后端、零构建，3 个文件即可跑起来。

| 文件 | 作用 |
|------|------|
| `index.html` | 仪表盘页面（Chart.js 素雅框架，CDN 加载） |
| `query-list.json` | 10 条探测 query（用户会搜的问题） |
| `tracking-data.json` | 扫描数据（WebSearch 自动层 + AI 引擎人工层） |

## 怎么预览

页面用 `fetch` 读同目录 JSON，**直接双击 `index.html`（file:// 协议）会被浏览器拦截**。二选一：

```bash
# 方式一：本地起个临时服务
cd "/Users/haibinyu/Desktop/航天宏图/geo-监测仪表盘"
python3 -m http.server 8000
# 浏览器打开 http://localhost:8000
```

或者直接部署到任意静态托管（GitHub Pages / 客户服务器）后访问。

## 三层口径，怎么理解

- **引用命中率（WebSearch 自动层）**：每周扫描 10 条 query，结果里出现 `piesat.cn` 即算「已引用」。当前首期基线为 **0 引用**——注意这是**海外索引**，跟百度口径不同（见下）。
- **百度搜索（人工层）**：百度无开放排名 API，只能人工查。每周去百度搜核心 query，记录官网 `piesat.cn` 排第几（进前 10 算上榜）。**官网在百度的排名才是国内真实情况**——首期已确认「雷达卫星为什么能穿透云层」排第 1。
- **AI 引擎真实引用（人工层）**：豆包 / DeepSeek / Kimi 不开放引用溯源 API，只能人工去问。判定口径：官网 piesat.cn 被引用才算「已引用」，头条号等第三方不计入官网命中。

## 每周怎么更新 WebSearch 层

在 `tracking-data.json` 的 `scans` 数组末尾追加一次扫描（新日期 + 10 条 query 的 found / rank / competitorCount / contentTypes）。例如：

```json
{
  "date": "2026-08-30",
  "source": "websearch",
  "summary": "第二周扫描：……（一句话结论）",
  "queries": [
    {"id":1, "found":true, "rank":3, "competitorCount":10, "contentTypes":{"article":10,"video":0,"qa":0}},
    {"id":2, "found":false, "rank":null, "competitorCount":10, "contentTypes":{"article":10,"video":0,"qa":0}}
  ]
}
```

> 追加到 `scans` 数组里即可，页面会自动画趋势曲线。删除旧扫描不推荐（会丢趋势）。
> `rank` 是航天宏图在结果里的名次（第几名），未上榜填 `null`；上榜后「引用状态」列会显示「已引用 #名次」。

## 怎么填百度人工层

**推荐方式**：在仪表盘页面「百度搜索」区块下方，用「填写本次百度抽样」填写器——每条 query 下拉选百度排名（第1–10名）→ 填小结 → 生成 JSON → 复制 → 粘贴进 `tracking-data.json` 的 `baiduScans` 数组。

手动方式（结构参考）：

```json
{
  "date": "2026-08-30",
  "source": "baidu-manual",
  "summary": "本周百度抽样小结",
  "queries": [
    {"id":1, "found":true, "rank":1},
    {"id":2, "found":false, "rank":null}
  ]
}
```

- `found: true` = 官网进入百度前 10 名，`rank` = 排名；
- 没查到的 query 不必列出来，只记你实际查过的。

## 怎么填 AI 引擎人工层

**推荐方式**：在仪表盘页面「AI 引擎真实引用」区块下方，用「填写本次抽样」填写器——下拉勾选各引擎引用情况 → 点「生成 JSON」→ 复制 → 粘贴进 `tracking-data.json` 的 `aiScans` 数组。

手动方式（结构参考）：在 `tracking-data.json` 的 `aiScans` 数组里追加一次抽样。结构：

```json
{
  "date": "2026-08-30",
  "source": "ai-engine-manual",
  "summary": "本周抽样豆包/DeepSeek/Kimi，各问 3 条核心 query",
  "queries": [
    {
      "id": 1,
      "engines": {
        "doubao":   {"found": false, "rank": null},
        "deepseek": {"found": true,  "rank": 3},
        "kimi":     {"found": false, "rank": null}
      }
    }
  ]
}
```

- `found: true` 表示该引擎的答案里**引用了官网 piesat.cn**（头条号/知乎等第三方账号被引用、官网未上榜，仍记 `false`，并在 `summary` 里备注）；
- `rank` 表示官网出现在引用源列表里的序号（第几名，没有就填 `null`）；
- 只抽你关心的 query 就行，不必 10 条全跑。

## B3 / B4 上线后

把 `query-list.json` 里第 7、8 条的 `status` 从 `"待发布"` 改成 `"已上线"`，仪表盘的「待发布」标签会自动消失，这两条 query 也开始计入有效监测。

## 当前基线结论（2026-08-23 首期）

10 条 query 全部零引用。品牌词「航天宏图 女娲星座 SAR」也只见股票站/新闻站转载、未见官网博客。竞品内容几乎全是图文文章，视频与问答形式空白——既是现状，也是内容机会。
