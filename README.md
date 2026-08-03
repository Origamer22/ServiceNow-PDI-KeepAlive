# ServiceNow PDI Keep-Alive

ServiceNow Personal Developer Instances (PDIs) automatically hibernate after 2 hours of inactivity. This repository provides a scheduled GitHub Actions workflow that keeps your instance active 24/7 for free. It works by firing a lightweight REST API query every 90 minutes to prevent the instance from sleeping, without requiring any self-hosted servers.

## Setup Instructions

### 1. Create a New GitHub Repository
1. Go to GitHub and click **New Repository**.
2. Name your repository (e.g., `servicenow-pdi-keepalive`).
3. Keep it **Private** to protect your workflow logs.
4. Select **Add a README file** and click **Create repository**.
5. Clone the repository locally using your terminal or Git GUI:
   ```bash
   git clone https://github.com/YOUR_USERNAME/servicenow-pdi-keepalive.git
   cd servicenow-pdi-keepalive
   ```

### 2. Configure Encrypted Repository Secrets
Store your instance credentials safely as GitHub Repository Secrets. **Never hardcode credentials in your code.**
1. In your GitHub repository, navigate to **Settings** > **Secrets and variables** > **Actions**.
2. Click **New repository secret** and add the following three entries:

| Secret Name | Value Example | Description |
|---|---|---|
| `SN_INSTANCE_URL` | `https://dev12345.service-now.com` | Base URL of your PDI (no trailing slash) |
| `SN_USERNAME` | `admin` (or integration user) | Your instance login username |
| `SN_PASSWORD` | `YourSecretPassword` | Your instance user password |

### 3. Create the Keep-Alive Workflow File
Configure GitHub Actions to ping the REST API.
1. Inside your cloned repo folder, create the hidden `.github/workflows` directory structure:
   ```bash
   mkdir -p .github/workflows
   ```
2. Create a file named `keep_alive.yml` inside `.github/workflows/`:
   ```bash
   touch .github/workflows/keep_alive.yml
   ```
3. Paste the following configuration into `keep_alive.yml`:
   ```yaml
   name: ServiceNow PDI Keep-Alive

   on:
     schedule:
       # Runs every 90 minutes to beat the 2-hour hibernation timer
       - cron: '*/90 * * * *'
     workflow_dispatch: # Enables manual execution from GitHub UI

   jobs:
     ping-instance:
       runs-on: ubuntu-latest

       steps:
         - name: Ping ServiceNow Table API
           run: |
             # Strip trailing slashes from the instance URL if present
             URL="${{ secrets.SN_INSTANCE_URL }}"
             URL="${URL%/}"

             # Execute a lightweight GET request against the User table
             HTTP_CODE=$(curl -s -o /dev/null -w "%{http_code}" \
               -u "${{ secrets.SN_USERNAME }}:${{ secrets.SN_PASSWORD }}" \
               -H "Accept: application/json" \
               "$URL/api/now/table/sys_user?sysparm_limit=1")

             echo "Response Code: $HTTP_CODE"

             if [ "$HTTP_CODE" -eq 200 ] || [ "$HTTP_CODE" -eq 201 ]; then
               echo "Success: Instance is awake and active."
               exit 0
             else
               echo "Error: Ping failed with HTTP status code $HTTP_CODE."
               exit 1
             fi
   ```

### 4. Commit, Push, and Test the Automation
Trigger an initial run to verify connectivity.
1. Commit and push your workflow file to GitHub:
   ```bash
   git add .github/workflows/keep_alive.yml
   git commit -m "Add ServiceNow PDI keep-alive workflow"
   git push origin main
   ```
2. Go to your repository on GitHub and click the **Actions** tab.
3. Select **ServiceNow PDI Keep-Alive** from the left sidebar.
4. Click **Run workflow** > **Run workflow** to test it immediately.
5. Click on the triggered job run to view the logs and confirm a `200 OK` response.

## Important Maintenance Notes
- **GitHub Actions Inactivity Rule:** If no commits or activity occur in your repository for 60 consecutive days, GitHub automatically pauses scheduled cron workflows. Pushing a small commit or visiting the Actions tab once every two months keeps it running indefinitely.
- **Instance Reclaims:** API calls prevent hibernation (sleeping), but ServiceNow still requires an actual developer login to the instance management portal once every 10 days to prevent instance reclamation.
