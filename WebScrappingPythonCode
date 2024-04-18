import requests
from bs4 import BeautifulSoup
import os
import time
from urllib.parse import urljoin
from multiprocessing import Process

# Function to extract and save content for a given URL with a delay
def extract_and_save_content(base_key, base_url, relative_url):
    # Skip links like "javascript:void(0);"
    if relative_url.startswith("javascript:"):
        return

    # Concatenate base_url and relative_url to get the full URL
    full_url = urljoin(base_url, relative_url)

    # Send an HTTP request to the URL
    try:
        response = requests.get(full_url)
        response.raise_for_status()  # Raise an HTTPError for bad responses
    except requests.exceptions.RequestException as e:
        print(f"Failed to retrieve the page {full_url}. Error: {e}")
        return

    # Parse the HTML content using BeautifulSoup
    soup = BeautifulSoup(response.text, 'html.parser')

    # Find and extract the desired content (adjust as needed)
    paragraphs = soup.find_all('p')
    table_cells = soup.find_all('td')
    Bullet_points = soup.find_all('li')

    # Create a directory to store output files (if not exists)
    output_directory = base_key
    os.makedirs(output_directory, exist_ok=True)

    # Save the output to a file with the link as the filename
    output_filename = os.path.join(output_directory, f"{relative_url.replace('/', '_')}.txt")
    with open(output_filename, "w", encoding="utf-8") as file:
        # Write the full URL before the content
        file.write(f"URL: {full_url}\n\n")

        # Extract and write text content from paragraphs to the file
        for paragraph in paragraphs:
            file.write(paragraph.get_text(strip=True) + '\n')

        # Extract and write text content from table cells to the file
        for cell in table_cells:
            file.write(cell.get_text(strip=True) + '\n')

        # Extract and write text content from bullet points to the file
        for points in Bullet_points:
            file.write(points.get_text(strip=True) + '\n')

    print(f"Content from {full_url} has been saved to {output_filename}")
    return full_url


if _name_ == "_main_":
    # Provide a dictionary of URLs with corresponding keys
    url_dict = {
        "https://google.com",
        #Urls for scrapping 
        # Add more key-value pairs as needed
    }

    # Create a list to store processes
    processes = []

    # Iterate through each URL and start a separate process for each
    for key, url in url_dict.items():
        try:
            response = requests.get(url)

            # Check if the request was successful (status code 200)
            if response.status_code == 200:
                # Parse the HTML content using BeautifulSoup
                soup = BeautifulSoup(response.text, 'html.parser')

                # Find all anchor tags (links)
                links = soup.find_all('a')

                # Iterate through each link and start a separate process for each
                for link in links:
                    relative_url = link.get('href')
                    if relative_url:
                        process = Process(target=extract_and_save_content, args=(key, url, relative_url))
                        processes.append(process)
                        process.start()

            else:
                print(f"Failed to retrieve the page for {key}. Status code: {response.status_code}")

        except Exception as e:
            print(f"An error occurred for {key}. Error: {e}")

    # Wait for all processes to finish
    for process in processes:
        process.join()

    time.sleep(0.3)
