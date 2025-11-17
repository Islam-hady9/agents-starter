# Local Setup Guide for Cloudflare Chat Agent

This guide will help you set up and run the Cloudflare AI Chat Agent on your local machine.

## Prerequisites

Before you begin, you'll need:
- A Cloudflare account
- An OpenAI API key (or you can configure a different AI provider)

## Step 1: Install Node.js and npm

Choose one of the following methods to install Node.js on your Linux system:

### Option A: Using NodeSource (Recommended)

This provides the latest LTS version of Node.js:

```bash
# Download and run the NodeSource setup script for Node.js 20.x (LTS)
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -

# Install Node.js and npm
sudo apt-get install -y nodejs

# Verify installation
node --version
npm --version
```

### Option B: Using nvm (Node Version Manager)

This allows you to manage multiple Node.js versions:

```bash
# Install nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash

# Restart your terminal or run:
source ~/.bashrc

# Install Node.js LTS version
nvm install --lts
nvm use --lts

# Verify installation
node --version
npm --version
```

### Option C: Using apt (System Package Manager)

This installs the version available in your system's repositories (may be older):

```bash
sudo apt update
sudo apt install nodejs npm

# Verify installation
node --version
npm --version
```

## Step 2: Install Project Dependencies

Navigate to the project directory and install dependencies:

```bash
cd /media/islam/F/Zedny/agents-starter

# Install all required packages
npm install
```

This will install all dependencies listed in `package.json`, including:
- Cloudflare agents framework
- React for the UI
- AI SDK and OpenAI provider
- Other required libraries

## Step 3: Configure Environment Variables

Create a `.dev.vars` file in the project root with your OpenAI API key:

```bash
# Create the .dev.vars file
touch .dev.vars
```

Edit the `.dev.vars` file and add your OpenAI API key:

```env
OPENAI_API_KEY=your_openai_api_key_here
```

**Where to get your OpenAI API key:**
1. Go to https://platform.openai.com/api-keys
2. Sign in or create an account
3. Click "Create new secret key"
4. Copy the key and paste it in your `.dev.vars` file

**Note:** Keep your API key secure and never commit it to version control.

## Step 4: Start the Local Development Server

Run the development server:

```bash
npm start
```

Or alternatively:

```bash
npm run dev
```

This will:
- Start the Vite development server
- Launch the chat agent locally
- Typically open at `http://localhost:5173` (check the terminal output for the exact URL)

## Step 5: Access the Chat Agent

Once the server is running:
1. Open your web browser
2. Navigate to the URL shown in the terminal (usually `http://localhost:5173`)
3. You should see the chat interface
4. Start chatting with your AI agent!

## Troubleshooting

### Port Already in Use

If port 5173 is already in use, Vite will automatically try the next available port. Check the terminal output for the actual port number.

### API Key Issues

If you get authentication errors:
- Make sure your OpenAI API key is correct in `.dev.vars`
- Verify you have credits/billing set up on your OpenAI account
- Check that the `.dev.vars` file is in the project root directory

### Dependencies Installation Issues

If `npm install` fails:
- Try clearing the npm cache: `npm cache clean --force`
- Delete `node_modules` and `package-lock.json`, then run `npm install` again
- Make sure you have sufficient disk space

## Next Steps

### Customizing Your Agent

Once running locally, you can customize:

1. **Add New Tools** - Edit `src/tools.ts` to add custom functionality
2. **Modify the UI** - Edit `src/app.tsx` and `src/styles.css` for UI changes
3. **Change AI Model** - Edit `src/server.ts` to use different AI providers (see README.md)
4. **Add Features** - Explore the project structure in `src/` directory

### Deploying to Cloudflare

When you're ready to deploy:

```bash
# Build and deploy to Cloudflare Workers
npm run deploy
```

Before deploying, make sure to:
1. Set up your Cloudflare account
2. Configure your OpenAI API key as a Cloudflare secret (not in `.dev.vars`)
3. Review the deployment settings in `wrangler.jsonc`

## Alternative AI Providers

You can use different AI providers instead of OpenAI:

### Using Cloudflare Workers AI

1. Install the Workers AI provider:
```bash
npm install workers-ai-provider
```

2. Update `src/server.ts` to use Workers AI instead of OpenAI
3. Add AI binding to `wrangler.jsonc`

See README.md for detailed instructions on alternative providers.

## Useful Commands

- `npm start` or `npm run dev` - Start development server
- `npm run deploy` - Build and deploy to Cloudflare
- `npm test` - Run tests
- `npm run format` - Format code with Prettier
- `npm run check` - Run type checking and linting

## Learn More

- [Cloudflare Agents Documentation](https://developers.cloudflare.com/agents/)
- [Cloudflare Workers Documentation](https://developers.cloudflare.com/workers/)
- [AI SDK Documentation](https://sdk.vercel.ai/docs)
- [Project README](./README.md)

## Support

If you encounter issues:
- Check the [project README](./README.md)
- Review the [Cloudflare Agents docs](https://developers.cloudflare.com/agents/)
- Open an issue on the project's GitHub repository
