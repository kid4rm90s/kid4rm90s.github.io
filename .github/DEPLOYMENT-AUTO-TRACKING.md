# Deployment Tracking - Auto & Manual Setup

This GitHub Actions system automatically tracks deployments across your repositories and updates the portfolio with real-time status.

## 🚀 Quick Start: Three Ways to Track Deployments

| Method | Trigger | Setup | Latency |
| --- | --- | --- | --- |
| **Auto Release Check** | Every 6 hours | Edit repo list | ~6 hours |
| **Release Auto-Notify** | On release creation | Copy workflow | ~1 minute |
| **Manual Trigger** | You decide | None | Immediate |

---

## ✅ Method 1: Auto-Detect Releases (RECOMMENDED)

**Workflow:** `auto-detect-releases.yml`

Automatically checks your repositories for new releases every 6 hours and updates the portfolio.

### Setup

1. Open `.github/workflows/auto-detect-releases.yml`
2. Find the `repos_to_track` list:

   ```python
   repos_to_track = [
       "preeti2unicode",
       "WMESDK-KEYBOARD-SHORTCUT-IMPLEMENTATION-GUIDE",
       # Add your repo names here
   ]
   ```

3. Add your repository names
4. Commit and push

### How it works

- Runs automatically every 6 hours ⏰
- Fetches latest release from each repo
- Updates `deployments.json` and `index.html`
- Commits changes automatically

---

## 🔔 Method 2: Release Notification (FAST)

**Workflow:** `release-notifier.yml`

Triggers instantly when you create a release on any repository.

### Setup Option A: Main Portfolio Only

The workflow is already set up. Just create releases and it auto-triggers!

### Setup Option B: Copy to Other Repositories

To get instant notifications, copy this workflow to each of your repositories:

1. In each repo, create: `.github/workflows/release-notifier.yml`
2. Copy the content from your portfolio's `release-notifier.yml`
3. The workflow:
   - Detects the release
   - Checks out the portfolio repo
   - Updates deployment metadata
   - Pushes changes

**No configuration needed!** The workflow auto-detects repo name and version.

### How it works

- You create a GitHub release 📦
- Workflow triggers automatically ⚡
- Portfolio updates within 1 minute ✅
- Deployment card appears immediately

---

## 👁️ Method 3: Monitor GitHub Pages Status

**Workflow:** `monitor-github-pages.yml`

Automatically detects GitHub Pages deployments on your repositories.


1. Enable GitHub Pages on your repositories:
   - Go to Settings → Pages
   - Select source branch (usually `main` or `gh-pages`)

2. Edit `.github/workflows/monitor-github-pages.yml`:

   ```python
   pages_enabled_repos = [
       "kid4rm90s.github.io",
       "preeti2unicode",
       "NepaliBStoAD",
       # Add repos with Pages enabled
   ]
   ```

3. Commit and push

### How it works

- Checks every 12 hours ⏰
- Detects GitHub Pages deployments
- Updates portfolio with Pages status
- Shows which repos have deployed websites

---

## 🎯 Method 4: Manual Trigger (Still Available)

**Workflow:** `track-deployments.yml`

Manually trigger anytime from the Actions tab.

### Usage

1. Go to **Actions** tab
2. Select **"Track Deployments"**
3. Click **"Run workflow"**
4. Fill in:
   - `repo_name`: Repository name
   - `version`: Version tag (e.g., `v1.0.0`)
   - `branch`: Branch name (optional)
   - `status`: `success` or `failed`
5. Click **"Run workflow"****

---

## 📊 Recommended Setup for Best Results

### For Maximum Automation

1. **Enable Release Auto-Detect:**
   - Edit `auto-detect-releases.yml`
   - Add your repo names
   - ✅ Done! Updates every 6 hours

2. **Enable Fast Release Notification:**
   - Copy `release-notifier.yml` to your repos
   - Instant updates when you release
   - ✅ Done!

3. **Enable GitHub Pages Monitoring:**
   - Enable Pages on your repos
   - Edit `monitor-github-pages.yml` to add repo names
   - ✅ Done! Checks every 12 hours

### Result

- ✅ New release created → Updates in ~1 minute
- ✅ No release, just Pages update → Updates every 12 hours
- ✅ Scheduled check → Updates every 6 hours
- ✅ Manual override anytime

---

## 🔧 Customization

### Change Check Frequencies

Edit `cron` schedules in workflows:

```yaml
schedule:
  - cron: '0 */6 * * *'   # Every 6 hours
```

Common examples:

```yaml
'0 * * * *'      # Every hour
'0 0 * * *'      # Daily at midnight UTC
'0 */4 * * *'    # Every 4 hours
'30 2 * * *'     # 2:30 AM UTC daily
```

### Add/Remove Repositories

- **Auto-Detect Releases:** Edit `repos_to_track`
- **Monitor Pages:** Edit `pages_enabled_repos`
- **Release Notifier:** Auto-detects (no config needed)

### Customize Display

Edit the Python scripts in workflows to change card display:

```python
lines.append(f'                <h3>{dep.get("repo")}</h3>')
lines.append(f'                <p><strong>Version:</strong> {dep.get("version")}</p>')
lines.append(f'                <p><strong>Branch:</strong> {dep.get("branch")}</p>')
```

---

## 📁 File Structure

```
.github/
├── workflows/
│   ├── track-deployments.yml       # Manual trigger
│   ├── auto-detect-releases.yml    # Auto: releases every 6h
│   ├── release-notifier.yml        # Auto: on release (instant)
│   └── monitor-github-pages.yml    # Auto: Pages every 12h
└── DEPLOYMENT-TRACKING.md          # This file

deployments.json                     # Auto-updated data
index.html                          # Auto-updated display
```

---

## 📋 deployments.json Format

```json
[
  {
    "repo": "preeti2unicode",
    "version": "v1.1.0",
    "branch": "main",
    "status": "success",
    "timestamp": "2026-02-19T15:30:00Z"
  },
  {
    "repo": "my-project",
    "version": "v2.0.0",
    "branch": "main",
    "status": "success",
    "timestamp": "2026-02-19T10:20:00Z"
  }
]
```

---

## ⚙️ Integration with CI/CD

### Trigger from GitHub Actions

```yaml
- name: Update Deployment Portfolio
  run: |
    gh workflow run track-deployments.yml \
      -f repo_name="${{ github.repository }}" \
      -f version="${{ github.ref_name }}" \
      -f branch="main" \
      -f status="success"
  env:
    GH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### Trigger from Shell Script

```bash
gh workflow run track-deployments.yml \
  -f repo_name="my-repo" \
  -f version="v1.0.0" \
  -f branch="main" \
  -f status="success"
```

---

## 🔍 Troubleshooting

### Workflows Not Running?

1. Check **Actions** tab → Enable if disabled
2. Verify org/user permissions allow workflows
3. Check workflow syntax (GitHub will show errors)

### Deployments Not Updating?

1. Check repo names match exactly (case-sensitive)
2. Check workflow logs for errors
3. Releases must be "Published" (not drafts)
4. Wait for scheduled check to run

### Manual Workflow Needed?

Yes! Keep `track-deployments.yml` for:
- Manual overrides
- Custom deployments
- Edge cases

---

## 📅 Workflow Schedule Examples

### Strict Release Tracking

- Auto-Detect: Every 6 hours
- Release-Notify: On new release (instant)

### Comprehensive Monitoring

- Release-Notify: On new release (instant)
- Auto-Detect: Every 4 hours
- Pages Monitor: Every 12 hours

### Minimal Setup

- Manual trigger only
- No scheduled tasks

---

## 🎓 Next Steps

1. ✅ Edit `auto-detect-releases.yml` - Add your repos
2. ✅ Edit `monitor-github-pages.yml` - Add Pages-enabled repos
3. ✅ (Optional) Copy `release-notifier.yml` to your repos
4. ✅ Commit and push all changes
5. ✅ Create a test release
6. ✅ Wait 1-6 minutes to see update

---

[GitHub Actions Documentation](https://docs.github.com/en/actions)
