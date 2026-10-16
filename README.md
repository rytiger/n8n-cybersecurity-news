Cybersecurity News Automation with n8n

A simple n8n workflow that collects cybersecurity news from different websites and sends it to a Telegram channel.
How it works
Collects news from the selected sources.
Filters articles by date.
Sends the results to Telegram.
News Sources
The repository includes rss_Sources.csv, which contains the news sources and their URLs.
You can edit the CSV file to add or remove websites. The sources don’t have to be cybersecurity-related; you can use it for other topics too.
After updating the CSV, make sure the records in your n8n Data Table are updated as well. The Get Row(s) node uses this data to retrieve the sources.


Before Running
Set up your Telegram bot and credentials in n8n.
Configure the correct Telegram chat ID for your channel.
Check the Filter node and set a recent date, such as today or yesterday.
Tip: Avoid starting with an old date. Retrieving too many articles at once can cause Too Many Requests errors and stop the workflow.
If this happens, try a more recent date or a smaller date range.
Notes
The workflow is intended to be customised.
Check your credentials and node settings before running it.
Keep your bot token and other secrets private. Don’t upload them to GitHub.

Author
rytiger
