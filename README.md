# ServiceNow PDI Keep-Alive

This repository contains a GitHub Actions workflow that automatically pings your ServiceNow Personal Developer Instance (PDI) every hour to prevent it from hibernating due to inactivity. 

## How to make it work

1. **Fork or clone this repository** to your own GitHub account.
2. Go to your repository's **Settings** > **Secrets and variables** > **Actions**.
3. Add the following **Repository secrets**:
   - `SN_INSTANCE_URL`: The base URL of your instance (e.g., `https://dev12345.service-now.com`)
   - `SN_USERNAME`: Your ServiceNow username (e.g., `admin`)
   - `SN_PASSWORD`: Your ServiceNow password
4. Go to the **Actions** tab in your repository and enable workflows if prompted.
5. Click on **ServiceNow PDI Keep-Alive** on the left, click **Run workflow**, and run it once manually to verify it connects successfully.

That's it! GitHub Actions will now run the workflow automatically every hour to keep your instance awake. 

*Note: GitHub requires you to push a commit or manually run a workflow at least once every 60 days to keep scheduled actions active.*
