# Sheldon's Eidetic Memory

自动采集你的 Gmail、Google Docs、Chrome 浏览记录、ChatGPT 和 Claude.ai 对话，通过 LLM 整理成结构化的个人 Wiki，支持本地全文搜索与原始链接溯源。

Inspired by [Andrej Karpathy's LLM Wiki idea](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

---

## 安装

**依赖：** Python 3.10+、Node.js 18+、Chrome

**1. 配置环境变量**

```bash
cp .env.example .env
```

编辑 `.env`，填写 LLM API Key 和 Google OAuth 凭据（见下方说明）。

**2. 启动**

```bash
chmod +x start.sh
./start.sh
```

后端运行在 `http://localhost:8765`，前端运行在 `http://localhost:5173`。

**3. 安装浏览器扩展（可选，用于同步 ChatGPT / Claude.ai 对话）**

1. Chrome 打开 `chrome://extensions`，开启开发者模式
2. 点击「加载已解压的扩展程序」，选择 `extension/` 目录

---

## 配置说明

### LLM 提供商

在 `.env` 中设置 `LLM_PROVIDER`，三选一：

| 提供商 | 配置项 |
|--------|--------|
| `claude`（默认） | `ANTHROPIC_API_KEY` |
| `openai` | `OPENAI_API_KEY` |
| `ollama` | `OLLAMA_BASE_URL`、`OLLAMA_MODEL` |

### Google OAuth（Gmail / Google Docs）

1. 在 [Google Cloud Console](https://console.cloud.google.com/) 创建项目，启用 Gmail API、Google Docs API、Google Drive API
2. 创建 OAuth 2.0 客户端 ID（Web 应用），添加重定向 URI：
   ```
   http://localhost:8765/connectors/gmail/callback
   http://localhost:8765/connectors/googledocs/callback
   ```
3. 将客户端 ID 和密钥填入 `.env`
4. 在 OAuth 同意屏幕的「测试用户」中添加自己的 Gmail 地址

---

## 使用

1. 打开 `http://localhost:5173` → **数据源**，完成各连接器授权并同步
2. 打开 **Dashboard**，点击「开始摄取」，LLM 将内容整理写入 `wiki/`
3. 打开 **搜索**，用自然语言查询知识库

生成的 Wiki 位于 `wiki/` 目录，可直接用 Obsidian 打开。

---

## 常见问题

**Gmail 授权报错 `redirect_uri_mismatch`**
检查 Google Cloud Console 中的重定向 URI 是否与上方一致，注意不能有末尾斜杠。

**Gmail 授权报错 `access_denied`**
在 OAuth 同意屏幕 → 测试用户中添加你的 Gmail 地址。

**Google Docs 同步失败（403）**
确认 Google Drive API 和 Google Docs API 都已启用，刚启用后需等待几分钟生效。

**Chrome 历史读取失败**
macOS 需在「系统设置 → 隐私与安全 → 完全磁盘访问」中授权运行本应用的终端。

**Ollama 只想在晚上跑**
在设置页开启「仅在低占用时段运行」并配置时间窗口（如 22:00–09:00），白天点击摄取会自动排队。
