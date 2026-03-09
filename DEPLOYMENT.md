# Deployment Guide for GitHub Pages

This game is built with Vite and React, and is now configured to work correctly on GitHub Pages.

## The Fix

The black screen issue was caused by incorrect asset paths. When deploying to GitHub Pages at `https://deshicart.github.io/Neonjump/`, all asset references need to be relative to `/Neonjump/` instead of the root `/`.

The fix was to add the `base` configuration to `vite.config.ts`:

```typescript
export default defineConfig({
  base: "/Neonjump/",
  // ... rest of config
});
```

## How to Deploy

### Option 1: Automated GitHub Actions (Recommended)

Create `.github/workflows/deploy.yml` with the following content:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches:
      - main  # or your default branch name

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install dependencies
        run: npm ci

      - name: Build
        run: npm run build

      - name: Setup Pages
        uses: actions/configure-pages@v4

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: './dist'

      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

Then:
1. Go to your repository settings
2. Navigate to Pages section
3. Under "Build and deployment", select "GitHub Actions" as the source
4. Push changes to your main branch, and the site will automatically deploy

### Option 2: Manual Deployment

1. Build the project locally:
   ```bash
   npm install
   npm run build
   ```

2. The built files will be in the `dist/` folder.

3. Deploy the contents of `dist/` to GitHub Pages manually:
   - You can use `gh-pages` npm package
   - Or push the `dist/` folder to the `gh-pages` branch

## Testing Locally

To test the build locally before deploying:

```bash
npm run build
npm run preview
```

The preview server will serve the built files with the correct base path.

## Important Notes

- The `vite.config.ts` file contains `base: "/Neonjump/"` which matches your repository name
- If you rename the repository, update the `base` path accordingly
- The `.gitignore` file excludes `node_modules/` and `dist/` from version control
- The game uses `vite-plugin-singlefile` to bundle everything into a single HTML file for easy deployment
