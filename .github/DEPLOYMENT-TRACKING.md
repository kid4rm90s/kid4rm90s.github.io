# Deployment Tracking Setup

This GitHub Actions workflow automatically tracks deployments across your repositories and updates the main `index.html` with deployment status.

## How It Works

1. **Trigger Methods**: The workflow can be triggered in two ways:
   - **Manual Dispatch** (recommended for GitHub Pages): Use the "Run workflow" button in the Actions tab
   - **Repository Dispatch**: From other repos, send a webhook event to trigger the update

2. **What Gets Updated**:
   - `deployments.json` - Stores all deployment metadata
   - `index.html` - Displays deployment status cards with version, timestamp, branch, and status

## Usage

### Method 1: Manual Trigger (Recommended)

1. Go to the **Actions** tab in your `kid4rm90s.github.io` repository
2. Select **"Track Deployments"** workflow
3. Click **"Run workflow"**
4. Fill in the inputs:
   - **repo_name**: The repository name (e.g., `my-awesome-project`)
   - **version**: The version tag (e.g., `v1.0.0`, `1.2.3`)
   - **branch**: The branch deployed (optional, defaults to `main`)
   - **status**: `success` or `failed`
5. Click **"Run workflow"**

The workflow will:
- Record the deployment in `deployments.json`
- Update `index.html` with the new deployment card
- Automatically commit and push changes

### Method 2: Trigger from Another Repository

Add this workflow to your deployed repository (`.github/workflows/notify-deployment.yml`):

```yaml
name: Notify Deployment

on:
  release:
    types: [published]
  workflow_run:
    workflows: ["Build and Deploy"]  # Change to your workflow name
    types: [completed]

jobs:
  notify:
    runs-on: ubuntu-latest
    if: github.event.workflow_run.conclusion == 'success'
    
    steps:
      - name: Trigger deployment update
        run: |
          curl -X POST https://api.github.com/repos/kid4rm90s/kid4rm90s.github.io/dispatches \
            -H "Authorization: token ${{ secrets.GITHUB_TOKEN }}" \
            -H "Accept: application/vnd.github.v3+raw" \
            -d '{
              "event_type": "repo-deployed",
              "client_payload": {
                "repo_name": "${{ github.repository }}",
                "version": "${{ github.ref_name }}",
                "branch": "${{ github.ref_name }}",
                "status": "success"
              }
            }'
```

## File Structure

```
kid4rm90s.github.io/
├── .github/
│   └── workflows/
│       └── track-deployments.yml      # Main workflow
├── deployments.json                   # Stores deployment records
├── index.html                         # Updated with deployment cards
└── .github/DEPLOYMENT-TRACKING.md     # This file
```

## deployments.json Format

```json
[
  {
    "repo": "my-project",
    "version": "v1.0.0",
    "branch": "main",
    "status": "success",
    "timestamp": "2026-02-19T15:30:00Z"
  },
  {
    "repo": "another-project",
    "version": "v2.1.0",
    "branch": "develop",
    "status": "failed",
    "timestamp": "2026-02-19T14:20:00Z"
  }
]
```

## Deployment Card Display

The workflow automatically generates styled cards that display:
- ✅ Repository name
- Version/tag
- Branch name
- Status (success ✅ or failed ❌)
- Exact timestamp of deployment

Cards are sorted by most recent deployment first.

## Customization

### Change Deployment Grid Layout

Edit `.deployment-grid` CSS in `index.html`:
```css
.deployment-grid {
    grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); /* Adjust minmax values */
}
```

### Add More Status Types

Modify the workflow in `.github/workflows/track-deployments.yml`:
```python
status_badge = '⚠️' if dep["status"] == "warning" else '✅' if dep["status"] == "success" else '❌'
```

### Modify Card Styling

Edit the `.deployment-card` CSS in `index.html` to change colors, hover effects, etc.

## Troubleshooting

### Workflow doesn't commit changes
- Ensure the repository has write permissions enabled
- Check that the workflow has `contents: write` permission (GitHub automatically enables this)

### Deployments not showing on index.html
- Verify `deployments.json` was created at the repository root
- Check workflow logs in the Actions tab for errors

### Rate limiting issues
- The workflow is optimized to avoid unnecessary API calls
- If using repository_dispatch frequently, add delays between triggers

## Security Notes

- The workflow uses GitHub's default `GITHUB_TOKEN` which is scoped to the repository
- No sensitive credentials are required
- All data is stored in public `deployments.json` file (adjust permissions if needed)

## Next Steps

1. Push this configuration to your GitHub Pages repository
2. Go to the **Actions** tab and enable workflows if needed
3. Test with a manual trigger using the prepared inputs
4. Set up deployment notifications in your other repositories

---

For more information, see the [GitHub Actions Documentation](https://docs.github.com/en/actions).
