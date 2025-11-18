# 🚀 Groq Setup Guide - Kimi K2 Model Integration

This guide explains how to set up and run the Cloudflare Chat Agent with **Groq** using the **Moonshot AI Kimi K2** model instead of OpenAI.

## 🎯 What Changed?

This project has been migrated from OpenAI to Groq for faster inference and better performance:

| Aspect | Before | After |
|--------|--------|-------|
| **Provider** | OpenAI | Groq |
| **Model** | gpt-4o-2024-11-20 | moonshotai/kimi-k2-instruct-0905 |
| **API Key** | OPENAI_API_KEY | GROQ_API_KEY |
| **Package** | @ai-sdk/openai | @ai-sdk/groq |
| **Inference Speed** | Standard | Ultra-fast (Groq LPU™) |

---

## 📋 Prerequisites

Before you begin, you need:
- **Node.js** (v18 or later) and npm installed
- A **Groq account** and API key
- **Git** (optional, for version control)

---

## 🔑 Step 1: Get Your Groq API Key

### 1.1 Create a Groq Account

1. Go to **[Groq Console](https://console.groq.com/)**
2. Sign up or log in with your Google/GitHub account
3. Groq offers a **free tier** with generous rate limits

### 1.2 Generate an API Key

1. Once logged in, navigate to **[API Keys](https://console.groq.com/keys)**
2. Click **"Create API Key"**
3. Give it a name (e.g., "Cloudflare Agent")
4. Click **"Create"**
5. **Copy the API key immediately** - you won't see it again!
   - Format: `gsk_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

### 1.3 Verify Model Access

The Kimi K2 model (`moonshotai/kimi-k2-instruct-0905`) should be available by default. You can verify available models at the [Groq Models page](https://console.groq.com/docs/models).

---

## 🛠️ Step 2: Install Dependencies

### 2.1 Navigate to Project Directory

```bash
cd /media/islam/F/Zedny/agents-starter
```

### 2.2 Install Node Packages

```bash
# This will install the new @ai-sdk/groq package
npm install
```

This will install:
- `@ai-sdk/groq` - Groq provider for AI SDK
- All other required dependencies

### 2.3 Verify Installation

```bash
# Check if Groq package is installed
npm list @ai-sdk/groq
```

Expected output:
```
agents-starter@1.0.0
└── @ai-sdk/groq@1.0.9
```

---

## ⚙️ Step 3: Configure Environment Variables

### 3.1 Create `.dev.vars` File

This file stores your API key for local development:

```bash
# Create the file in the project root
touch .dev.vars
```

### 3.2 Add Your Groq API Key

Open `.dev.vars` in a text editor and add:

```env
GROQ_API_KEY=gsk_your_actual_groq_api_key_here
```

**Replace** `gsk_your_actual_groq_api_key_here` with the actual key you copied from Step 1.

### 3.3 Security Note

⚠️ **IMPORTANT:** Never commit `.dev.vars` to version control!

The `.gitignore` file already excludes it, but double-check:

```bash
# Verify .dev.vars is in .gitignore
cat .gitignore | grep ".dev.vars"
```

---

## 🚀 Step 4: Run the Agent Locally

### 4.1 Start the Development Server

```bash
npm start
```

Or alternatively:

```bash
npm run dev
```

### 4.2 What Happens Next

You should see output like:

```
VITE v7.1.12  ready in 437 ms

➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
➜  press h + enter to show help
```

### 4.3 Open the Chat Interface

1. Open your web browser
2. Navigate to **http://localhost:5173**
3. You should see the chat interface

### 4.4 Verify Groq Connection

If everything is configured correctly:
- ✅ You'll see the chat interface without errors
- ✅ The red "API Key Not Configured" banner should NOT appear

If you see the red error banner:
- ❌ Check that your `.dev.vars` file exists
- ❌ Verify the API key is correct and starts with `gsk_`
- ❌ Restart the development server (`npm start`)

---

## 🧪 Step 5: Test the Agent

### 5.1 Basic Chat Test

Type a simple message in the chat:

```
Hello! What model are you?
```

The agent should respond using the Kimi K2 model through Groq.

### 5.2 Test Tool Calling

Try these commands to test built-in tools:

#### Test Auto-Executing Tool:
```
What's the local time in New York?
```

Expected behavior: The agent calls `getLocalTime` tool automatically.

#### Test Confirmation-Required Tool:
```
What's the weather in London?
```

Expected behavior:
1. The agent shows a tool confirmation dialog
2. You see the parameters (city: "London")
3. You can **Approve** or **Deny** the action
4. After approval, the tool executes

### 5.3 Test Task Scheduling

Try scheduling tasks:

```
Schedule a reminder for me in 30 seconds to check the oven
```

Or:

```
Schedule a task for January 30, 2025 at 3:00 PM to send reports
```

### 5.4 Performance Check

**Groq is FAST!** You should notice:
- ⚡ Responses stream very quickly
- ⚡ Tool calls execute with minimal latency
- ⚡ Overall better performance than OpenAI

---

## 🔍 Step 6: Understanding the Configuration

### 6.1 Model Configuration

The model is configured in `src/server.ts`:

```typescript
import { groq } from "@ai-sdk/groq";

const model = groq("moonshotai/kimi-k2-instruct-0905");
```

**Available Groq Models:**
- `moonshotai/kimi-k2-instruct-0905` (Current)
- `llama-3.3-70b-versatile`
- `llama-3.1-8b-instant`
- `mixtral-8x7b-32768`
- `gemma2-9b-it`

To switch models, just change the model string.

### 6.2 API Key Location

The API key is read from environment variables:

**Local Development:**
- Stored in `.dev.vars` file
- Read as `process.env.GROQ_API_KEY`

**Production (Cloudflare Workers):**
- Set as a Cloudflare secret
- See Step 7 for deployment instructions

### 6.3 Tool Configuration

Tools are defined in `src/tools.ts`:

```typescript
export const tools = {
  getWeatherInformation,  // Requires confirmation
  getLocalTime,           // Auto-executes
  scheduleTask,           // Auto-executes
  getScheduledTasks,      // Auto-executes
  cancelScheduledTask     // Auto-executes
};
```

All tools work the same with Groq!

---

## 🌐 Step 7: Deploy to Cloudflare (Production)

### 7.1 Set Groq API Key as a Secret

For production, you need to set the API key as a Cloudflare secret:

```bash
# Set the secret using Wrangler CLI
echo "GROQ_API_KEY=gsk_your_actual_groq_api_key_here" | wrangler secret put GROQ_API_KEY
```

Or set it via the Cloudflare dashboard:
1. Go to **Workers & Pages**
2. Select your worker
3. Go to **Settings** → **Environment Variables**
4. Add `GROQ_API_KEY` as a secret

### 7.2 Deploy the Agent

```bash
# Build and deploy
npm run deploy
```

This will:
1. Build the Vite project
2. Bundle the worker
3. Deploy to Cloudflare Workers
4. Provide a production URL

### 7.3 Test Production Deployment

Visit the provided URL and test the chat interface.

---

## 🔧 Troubleshooting

### Problem: "Groq API Key Not Configured" Error

**Symptoms:** Red banner appears at the top of the page

**Solutions:**
1. Check `.dev.vars` file exists in project root
2. Verify the file contains: `GROQ_API_KEY=gsk_...`
3. Ensure no extra spaces or quotes around the key
4. Restart the dev server: `Ctrl+C` then `npm start`

**Verify your key:**
```bash
# Print the key (be careful!)
cat .dev.vars
```

### Problem: "Model not found" Error

**Symptoms:** Error message about model not available

**Solutions:**
1. Check the model name spelling in `src/server.ts`
2. Verify model is available at [Groq Models](https://console.groq.com/docs/models)
3. Try a different model like `llama-3.3-70b-versatile`

### Problem: Rate Limit Errors

**Symptoms:** Error messages about too many requests

**Solutions:**
1. Groq free tier has rate limits
2. Wait a few minutes before retrying
3. Consider upgrading to Groq Pro for higher limits
4. Check your usage at [Groq Console](https://console.groq.com/)

### Problem: API Key Invalid

**Symptoms:** Authentication errors, 401 responses

**Solutions:**
1. Verify your API key at [Groq Console](https://console.groq.com/keys)
2. Regenerate the key if needed
3. Update `.dev.vars` with the new key
4. Restart the server

### Problem: npm install Fails

**Symptoms:** Package installation errors

**Solutions:**
```bash
# Clear npm cache
npm cache clean --force

# Delete node_modules and package-lock.json
rm -rf node_modules package-lock.json

# Reinstall
npm install
```

### Problem: Port 5173 Already in Use

**Symptoms:** Error about port being in use

**Solutions:**
1. Vite will automatically try the next port (5174, 5175, etc.)
2. Check the terminal for the actual port number
3. Or manually kill the process using port 5173:
```bash
# Find process using port 5173
lsof -i :5173

# Kill it (replace PID with actual process ID)
kill -9 PID
```

---

## 📊 Groq vs OpenAI Comparison

### Performance

| Metric | OpenAI GPT-4 | Groq (Kimi K2) |
|--------|--------------|----------------|
| **Tokens/sec** | ~50-100 | ~300-800 |
| **First token** | ~500ms | ~50ms |
| **Latency** | Medium | Very Low |
| **Throughput** | Standard | High |

### Cost

Groq is generally more cost-effective than OpenAI:
- **Free tier:** Generous limits for testing
- **Pay-as-you-go:** Competitive pricing
- **No monthly minimums**

Check current pricing at [Groq Pricing](https://groq.com/pricing/)

### Features

| Feature | OpenAI | Groq |
|---------|--------|------|
| **Streaming** | ✅ | ✅ |
| **Tool Calling** | ✅ | ✅ |
| **Multiple Models** | ✅ | ✅ |
| **Image Input** | ✅ | ⚠️ Limited |
| **Inference Speed** | Good | Excellent |

---

## 🎯 Model-Specific Notes: Kimi K2

### About Kimi K2

The **Moonshot AI Kimi K2** model is optimized for:
- 📖 Long context understanding (200K+ tokens)
- 🇨🇳 Excellent Chinese language support
- 🧠 Strong reasoning capabilities
- 💬 Conversational tasks
- 🔧 Tool calling and function execution

### Context Window

Kimi K2 has a **massive context window** - you can include very long conversations or documents.

### Best Use Cases

- Customer support with long conversation history
- Document analysis and summarization
- Multi-turn reasoning tasks
- Bilingual applications (English/Chinese)

---

## 🔄 Switching Models

### Option 1: Use a Different Groq Model

Edit `src/server.ts`:

```typescript
// For faster, lighter responses:
const model = groq("llama-3.1-8b-instant");

// For more capable reasoning:
const model = groq("llama-3.3-70b-versatile");

// For long context (32K tokens):
const model = groq("mixtral-8x7b-32768");
```

### Option 2: Switch Back to OpenAI

If you want to revert to OpenAI:

```bash
# Uninstall Groq
npm uninstall @ai-sdk/groq

# Reinstall OpenAI
npm install @ai-sdk/openai
```

Then update `src/server.ts`:
```typescript
import { openai } from "@ai-sdk/openai";
const model = openai("gpt-4o-2024-11-20");
```

And update `.dev.vars`:
```env
OPENAI_API_KEY=sk-proj-...
```

### Option 3: Support Multiple Providers

You can make the provider configurable via environment variables. Let me know if you want help implementing this!

---

## 📚 Additional Resources

### Groq Documentation
- **Groq Docs:** https://console.groq.com/docs
- **API Reference:** https://console.groq.com/docs/api-reference
- **Model List:** https://console.groq.com/docs/models
- **Pricing:** https://groq.com/pricing/

### AI SDK Documentation
- **Vercel AI SDK:** https://sdk.vercel.ai/docs
- **Groq Provider:** https://sdk.vercel.ai/providers/ai-sdk-providers/groq
- **Tool Calling:** https://sdk.vercel.ai/docs/ai-sdk-core/tools-and-tool-calling

### Cloudflare Resources
- **Workers Docs:** https://developers.cloudflare.com/workers/
- **Agents Docs:** https://developers.cloudflare.com/agents/
- **Secrets Management:** https://developers.cloudflare.com/workers/configuration/secrets/

---

## 🆘 Getting Help

### Where to Get Support

1. **Groq Issues:**
   - Email: support@groq.com
   - Discord: [Groq Community](https://groq.com/discord)

2. **Cloudflare Issues:**
   - Forum: https://community.cloudflare.com/
   - Docs: https://developers.cloudflare.com/

3. **Project Issues:**
   - Check the original README.md
   - Review this guide again
   - Check `.dev.vars` configuration

---

## ✅ Quick Reference Checklist

Use this checklist to verify your setup:

- [ ] Node.js and npm installed (`node --version`)
- [ ] Groq account created
- [ ] Groq API key obtained from console
- [ ] Project dependencies installed (`npm install`)
- [ ] `.dev.vars` file created in project root
- [ ] `GROQ_API_KEY` added to `.dev.vars`
- [ ] Development server started (`npm start`)
- [ ] Chat interface accessible at http://localhost:5173
- [ ] No red error banner visible
- [ ] Basic chat message works
- [ ] Tool calling tested (weather, time, etc.)
- [ ] Task scheduling tested (optional)

---

## 🎉 You're All Set!

Your Cloudflare Chat Agent is now powered by **Groq** with the **Kimi K2 model**!

Enjoy **ultra-fast inference** and build amazing AI experiences! 🚀

---

**Last Updated:** November 17, 2025
**Agent Version:** 1.0.0
**Model:** moonshotai/kimi-k2-instruct-0905
**Provider:** Groq
