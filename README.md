# gksales-image-downloader-anime
gksaled 批量下载网页图片，图片链接全部在html里面的img src里面

```
#https://github.com/copilot/c/18b064b4-29ed-4c99-86fd-a3a07c7b2378

"""
主要是以下三个网页
https://gksales.co.jp/anime_digital/
https://gksales.co.jp/anime_background/
https://gksales.co.jp/anime_linetest/
"""
import requests
from bs4 import BeautifulSoup
import os
import re
import concurrent.futures

proxy = "https://c.map987.us.kg/"
url = proxy + 'https://gksales.co.jp/anime_background/'

# Download HTML page
html_response = requests.get(url)
html_content = html_response.text

# Save HTML content to a file
with open('page.html', 'w', encoding='utf-8') as f:
    f.write(html_content)

# Parse HTML with BeautifulSoup
soup = BeautifulSoup(html_content, 'html.parser')

# Get the current script path
current_path = os.path.dirname(os.path.abspath(__file__))

# Find all img tags
img_tags = soup.find_all('img')

img_url = "-1."
pattern = r'-1.'

def download_image(img):
    img_url = img.get('src')
    
    # Remove "-scaled" suffix
    img_url = re.sub(r'-scaled(\.[a-zA-Z]+)?$', '', img_url)
    
    # Remove "三位数字x四位数字." or "四位数字x四位数字." pattern
    img_url = re.sub(r'\b\d{3}x\d{4}\.\b|\b\d{4}x\d{4}\.\b', '.', img_url)
    
    img_url = proxy + img_url
    print(img_url)
    
    if img_url:
        download(img_url)
    
    if re.match(pattern, img_url):
        img_url = re.sub(pattern, '.', img_url)
        download(img_url)

def download(img_url):      
        img_response = requests.get(img_url)
        img_name = img_url.split('/')[-1]
        with open(os.path.join(current_path, img_name), 'wb') as img_file:
            img_file.write(img_response.content)

# Use ThreadPoolExecutor to download images concurrently
with concurrent.futures.ThreadPoolExecutor() as executor:
    executor.map(download_image, img_tags)



"""

https://www.n.cn/search/64b80cd4344d4a89a34481eeaa223d4d?fr=none

import requests 
from bs4 import BeautifulSoup 
import os 
import re 
import threading 

def download_image(img_url): 
       proxy = "https://c.map987.us.kg/" 
       img_url = proxy + img_url 
       try: 
           img_response = requests.get(img_url) 
           img_name = img_url.split('/')[-1] 
           current_path = os.path.dirname(os.path.abspath(__file__)) 
           with open(os.path.join(current_path, img_name), 'wb') as img_file: 
               img_file.write(img_response.content) 
       except Exception as e: 
           print(f"下载图片 {img_url} 时出错: {e}") 


def main():
   proxy = "https://c.map987.us.kg/" 
   url = proxy + 'https://gksales.co.jp/anime_linetest/' 
   html_response = requests.get(url) 
   html_content = html_response.text 
 
   with open('page.html', 'w', encoding='utf-8') as f: 
       f.write(html_content) 
 
   soup = BeautifulSoup(html_content, 'html.parser') 
 
   img_tags = soup.find_all('img') 
   threads = [] 
   for img in img_tags: 
       img_url = img.get('src') 
       img_url = re.sub(r'-scaled(\.[a-zA-Z]+)?$', '', img_url) 
       img_url = re.sub(r'\b\d{{3}}x\d{{4}}\.\b|\b\d{{4}}x\d{{4}}\.\b', '.', img_url) 
       t = threading.Thread(target=download_image, args=(img_url)) 
       t.start() 
       threads.append(t) 
 
   for t in threads: 
       t.join() 
       
main()
"""

```
