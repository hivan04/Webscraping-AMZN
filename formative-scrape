import os
import requests
import pandas as pd
from bs4 import BeautifulSoup
import time

# Detect File Type and Read ASINs 
def read_asins(file_path):
    if file_path.lower().endswith(".txt"):
        with open(file_path, "r") as file:
            return [line.strip() for line in file.readlines() if line.strip()]
    elif file_path.lower().endswith(".xlsx"):
        df = pd.read_excel(file_path)
        return df.iloc[:, 0].dropna().astype(str).tolist()  # Read ASINs from first column
    else:
        raise ValueError("Unsupported file format. Use .txt or .xlsx")

#Scrape products using the ASINs provided from imported .xlsx file, where the ASINs were scraped using the VBA script provided from seminars 
def scrape_amazon_product(asin):
    url = f"https://www.amazon.co.uk/dp/{asin}"
    headers = {
        "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36",
        "Accept-Language": "en-US,en;q=0.9", #Opens an Internet Explorer to Scrape
    }

    ##If in doubt there is no software the script can open:
    response = requests.get(url, headers=headers)
    if response.status_code != 200:
        print(f"Failed to fetch {asin}, status code: {response.status_code}")
        return {"ASIN": asin, "Error": "Failed to retrieve"}

    soup = BeautifulSoup(response.text, "html.parser")

    #Extract Product Title
    title_element = soup.find("span", {"id": "productTitle"})
    product_title = title_element.get_text(strip=True) if title_element else "Title Not Found"

    #Initialize Product Details that can be customised by including additionl items in the dictionary
    details_dict = {
        "ASIN": asin,
        "Title": product_title,
        "Price (£)": "Not Found",
        "Number of Reviews": "Not Found",
        "Product Dimensions": "Not Found",
        "Manufacturer": "Not Found",
        "Item model number": "Not Found",
        "Rating (out of 5)": "Not Found",
    }

    #Extract Price
    price_element = soup.find("span", {"class": "a-price-whole"})
    if price_element:
        decimal_element = soup.find("span", {"class": "a-price-fraction"})
        price = price_element.get_text(strip=True)
        decimal = decimal_element.get_text(strip=True) if decimal_element else "00"
        details_dict["Price (£)"] = f"{price}.{decimal}"

    #Extract Number of Reviews
    reviews_element = soup.find("span", {"id": "acrCustomerReviewText"})
    if reviews_element:
        details_dict["Number of Reviews"] = reviews_element.get_text(strip=True).replace(" ratings", "").replace(",", "")

    #Extract Rating
    rating_element = soup.find("span", {"class": "a-icon-alt"})
    if rating_element:
        rating_text = rating_element.get_text(strip=True)
        rating_value = rating_text.split(" ")[0] if " " in rating_text else rating_text
        details_dict["Rating (out of 5)"] = rating_value

    #Extract Product Details from 'Product Information' Table 
    product_info_table = soup.find("table", {"id": "productDetails_techSpec_section_1"})
    if product_info_table:
        for row in product_info_table.find_all("tr"):
            key_element = row.find("th")
            value_element = row.find("td")
            if key_element and value_element:
                key = key_element.get_text(strip=True)
                value = value_element.get_text(strip=True)
                if key in details_dict:
                    details_dict[key] = value

    #Extract Product Details from 'Detail Bullets' Section 
    product_details = soup.find("div", {"id": "detailBulletsWrapper_feature_div"})
    if product_details:
        for li in product_details.find_all("li"):
            text = li.get_text(separator=" ").strip()
            if "‏ :" in text:
                key, value = text.split("‏ :", 1)
                key, value = key.strip(), value.strip()
                if key in details_dict:
                    details_dict[key] = value

    return details_dict

#Importing Scraped Data into a .csv file
def main():
    # Define input and output file paths
    input_file = r"C:\Users\Ivan\Desktop\Python Scraping Script\asins.xlsx"
    output_file = r"C:\Users\Ivan\Desktop\Python Scraping Script\scraped.xlsx"

    # Ensure input file exists
    if not os.path.exists(input_file):
        print(f"Error: Input file '{input_file}' not found.")
        return

    # Read ASINs from the detected file format
    asins = read_asins(input_file)
    print(f"Found {len(asins)} ASINs to scrape...")

    all_products = []
    for asin in asins:
        print(f"Started Scraping ASIN: {asin}...")
        product_data = scrape_amazon_product(asin)
        all_products.append(product_data)
        print(f"Finished Scraping ASIN: {asin}...")
        time.sleep(2)  # Delay to prevent blocking

    # Convert to DataFrame and save to Excel
    df = pd.DataFrame(all_products)
    df.to_excel(output_file, index=False, engine="openpyxl")
    print(f"Scraping complete. Data saved to {output_file}")

#Execute Script
if __name__ == "__main__":
    main()

#Once process has been completed
print("Web Scraping has been completed!")