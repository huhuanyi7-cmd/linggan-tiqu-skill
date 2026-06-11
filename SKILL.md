---
name: 灵感提取
description: Use when the user says 灵感提取 or wants to extract content inspiration, scripts, hooks, structure, comments, audience angles, or reusable ideas from explicitly provided Douyin or Xiaohongshu content. Route Douyin video/share text to the local 文案抓取 skill for spoken-copy extraction and transcript analysis. Route Xiaohongshu notes/search/comments to agent-reach/xhs for public note text, images metadata, comments, and search results; if a Xiaohongshu video needs spoken transcription, combine agent-reach public text reading with 文案抓取 media/ASR fallback. Produce concise Chinese insight briefs for content planning, benchmarking, and rewriting.
metadata:
  short-description: 抖音/小红书内容灵感、口播和评论提取总入口
---

# 灵感提取

Use this as the user's top-level入口 when they give a clear platform instruction such as 抖音, 小红书, xhs, 红书, or a platform link/share text and ask to提取灵感、拆解内容、抓口播、看评论、找选题、总结爆点.

## Route

- **抖音视频 / 抖音分享文本**: use `文案抓取`.
  - Goal: extract spoken copy, transcript, "这个视频在说什么", hook, structure, reusable lines.
  - Default: try Doubao first, then temporary media download + ASR fallback.
  - Keep the storage policy from `文案抓取`: do not keep downloaded media unless user asks.

- **小红书笔记 / 小红书搜索 / 小红书评论**: use `agent-reach` with xhs.
  - Search: `xhs search "query"`.
  - Read note: `xhs read NOTE_URL_OR_SEARCH_RESULT_ID`.
  - Comments: `xhs comments NOTE_URL_OR_SEARCH_RESULT_ID`.
  - Important: prefer URLs or IDs returned by `xhs search/feed`; avoid naked note_id when xsec_token is missing.

- **小红书视频口播**: combine both.
  - First use `agent-reach`/`xhs read` for title, body text, visible captions, image/video metadata, and comments.
  - If the user needs the actual spoken copy and it is not exposed by xhs, use `文案抓取` on the user-provided file/link fallback.

- **B站/YouTube/网页/RSS**: use `agent-reach` directly if the user explicitly asks under 灵感提取.

## Output

Return a compact Chinese brief:

```markdown
## 提取结果
- 平台：
- 来源：
- 是否拿到原文/口播：
- 准确度：

## 内容在讲什么
- 一句话总结：
- 核心结构：
- 关键素材：

## 可复用灵感
- 开头钩子：
- 选题角度：
- 话术/句式：
- 评论/用户洞察：
- 可改写方向：

## 风险和复核
- 需要核实的数据/功效/价格：
- 平台或版权限制：
```

## Rules

- If the user explicitly names a platform, trust that platform route.
- If a URL/share text is ambiguous, infer from domains: `douyin.com` -> 抖音, `xiaohongshu.com`/`xhslink.com` -> 小红书.
- Do not treat generated copy from Doubao or another model as original transcript.
- When using `agent-reach`, follow its detailed docs only as needed; do not paste bulky raw output unless the user asks.
- Preserve source wording when extracting口播/笔记正文; clearly label summaries, inferred insights, and rewrites.
