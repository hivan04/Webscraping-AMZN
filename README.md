# Webscraping-AMZN-
# Webscraping Script (Amazon)
## Purpose 🙌
Made for a Formative Assessment on Web-Scraping and wanted to see if there were any other effective methods for scraping Waterproof Dry Bags from Amazon as Power Query was unable to bypass Amazon's data security.

## Method 🔎
1. Firstly used a VBA provided in seminars in order to scrape the products' ASIN keys.
2. Proceeded to export it as an .xlsx from a .xlsm file, so that it would become readable using pandas (pd)
***All steps after were then done by the script***
3. Assigned columns to desired information (that we want to scrape). I chose: Product name/title, Price, Number of Reviews, Product Dimesnsions, Manufacturer, Item Model Number and Rating Value out of 5.
4. A section of code was written for each respective category, by identfing the id 'tag' the categories were assigned to (which was done by inspecting Amazon's HTML code).
5. The script was then commanded to extract the data and import the data it scraped into a new spreadsheet [called (location_name/\.../\scrape.xlsx); \ if on Windows, / if on MacOS/Linux].

-> Annotations were included in the terminal to let the user know where the script was in the process
-> Using the time.sleep(n) command [from the time library] mitigated the script being caught by Amazon's data defence 

## Disclaimer ❗
I do not take full credit for this script and this was powered by AI-generated code, with some moderations, de-bugging and final touch ups made by me.


