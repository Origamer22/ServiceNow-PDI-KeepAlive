# ServiceNow PDI Keep-Alive

<div align="center">
  <h2>🇮🇱 We stand with Israel 🇮🇱</h2>
</div>

This repository contains a GitHub Actions workflow that automatically pings your ServiceNow Personal Developer Instance (PDI) every 3 hours to prevent it from hibernating due to inactivity (with a pause for Shabbat - from Friday 13:00 to Saturday 21:00 Israel Time).

Additionally, it features a self-sustaining **auto-commit** mechanism that prevents GitHub from pausing the workflow after 60 days of repository inactivity. 

## Folder Structure

`	ext
ServiceNow-PDI-KeepAlive/
├── .github/
│   └── workflows/
│       └── keep_alive.yml     # The GitHub Actions workflow file that does all the work
├── .gitignore                 # Specifies intentionally untracked files to ignore
├── LICENSE                    # The license for this project
└── README.md                  # This file
`

## Features
- **3-Hourly Ping:** A lightweight curl request is sent to your PDI every 3 hours to keep it awake.
- **Shabbat Observance:** The workflow automatically pauses from Friday 13:00 to Saturday 21:00 Israel Time.
- **Set It and Forget It:** GitHub automatically disables scheduled workflows on repositories that have had no commit activity for 60 days. To solve this, this workflow checks the date of your last commit every time it runs. If more than 50 days have passed, it will automatically push a small, empty dummy commit to the repository. This resets GitHub's 60-day timer, ensuring the workflow runs forever without any manual intervention from you!

## How to make it work

1. **Fork or clone this repository** to your own GitHub account.
2. Go to your repository's **Settings** > **Secrets and variables** > **Actions**.
3. Add the following **Repository secrets**:
   - SN_INSTANCE_URL: The base URL of your instance (e.g., https://dev12345.service-now.com)
   - SN_USERNAME: Your ServiceNow username (e.g., dmin)
   - SN_PASSWORD: Your ServiceNow password
4. **Important**: Go to your repository's **Settings** > **Actions** > **General**. Scroll down to **Workflow permissions** and ensure that **Read and write permissions** is selected. This allows the auto-commit feature to push the keep-alive commit!
5. Go to the **Actions** tab in your repository and enable workflows if prompted.
6. Click on **ServiceNow PDI Keep-Alive** on the left, click **Run workflow**, and run it once manually to verify it connects successfully.

That's it! GitHub Actions will now run the workflow automatically every 3 hours (excluding Shabbat) to keep your instance awake forever.
