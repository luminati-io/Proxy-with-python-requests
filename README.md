# Using a Proxy with Python Requests


[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

In this guide, you will learn how to use proxies with Python requests, particularly for [web scraping](https://brightdata.com/blog/how-tos/what-is-web-scraping), to [bypass website restrictions](https://brightdata.com/blog/proxy-101/how-to-bypass-an-ip-ban) by changing your IP and location:

- [Using a Proxy with a Python Request](#using-a-proxy-with-a-python-request)
- [Installing Packages](#installing-packages)
- [Components of Proxy IP Address](#components-of-proxy-ip-address)
- [Setting Proxies Directly in Requests](#setting-proxies-directly-in-requests)
- [Setting Proxies via Environment Variables](#setting-proxies-via-environment-variables)
- [Rotating Proxies Using a Custom Method and an Array of Proxies](#rotating-proxies-using-a-custom-method-and-an-array-of-proxies)
- [Using the Bright Data Proxy Service with Python](#using-the-bright-data-proxy-service-with-python)
- [Conclusion](#conclusion)

## Using a Proxy with a Python Request

## Installing Packages

Use `pip install` to install the following Python packages to send requests to the web page and collect the links:

- `requests`: sends HTTP requests to the website where you want to scrape the data.
- `beautifulsoup4`: parses HTML and XML documents to extract all the links.

## Components of Proxy IP Address

The three primary components of a proxy server are:

1.  **Protocol** is typically either HTTP or HTTPS.
2.  **Address** can be an IP address or a DNS hostname.
3.  **Port number** is anywhere between 0 and 65535, e.g. `2000`.

Thus, a proxy IP address would look like this: `https://192.167.0.1:2000` or
`https://proxyprovider.com:2000`.

## Setting Proxies Directly in Requests

This guide covers three ways to set proxies in requests. The first approach assumes doing that directly in the requests module.

Do as follows:

1. Import the Requests and Beautiful Soup packages in your Python script.
2. Create a directory called `proxies` that contains proxy server information.
3. In the `proxies` directory, define both the HTTP and HTTPS connections to the proxy URL.
4. Define the Python variable to set the URL of the web page you want to scrape the data from. Use `https://brightdata.com`.
5. Send a GET request to the web page using the `request.get()` method with two arguments: the URL of the website and proxies. The response will be stored in the `response` variable.
6. Pass `response.content` and `html.parser` as arguments to the `BeautifulSoup()` method to collect links.
7. Use the `find_all()` method with `a` as an argument to find all the links on the web page.
8. Extract the `href` attribute of each link using the `get()` method.

Here is the complete source code:

```
# import packages.  
import requests  
from bs4 import BeautifulSoup  
  
# Define proxies to use.  
proxies = {  
    'http': 'http://proxyprovider.com:2000',  
    'https': 'https://proxyprovider.com:2000',  
}  
  
# Define a link to the web page.  
url = "https://brightdata.com/"  
  
# Send a GET request to the website.  
response = requests.get(url, proxies=proxies)  
  
# Use BeautifulSoup to parse the HTML content of the website.  
soup = BeautifulSoup(response.content, "html.parser")  
  
# Find all the links on the website.  
links = soup.find_all("a")  
  
# Print all the links.  
for link in links:  
    print(link.get("href"))
```

Here is the output from running the script above:

![Scraped links](https://github.com/luminati-io/Proxy-with-python-requests/blob/main/link-to-webpage-2-1024x653.png)

## Setting Proxies via Environment Variables

To use the same proxy for all requests, it's best to set environment variables in the terminal window:

```powershell
export HTTP_PROXY='http://proxyprovider.com:2000'  
export HTTPS_PROXY='https://proxyprovider.com:2000'
```

You can remove the proxies definition from the script now:

```
# import packages.  
import requests  
from bs4 import BeautifulSoup  
  
# Define a link to the web page.  
url = "https://brightdata.com/"  
  
# Send a GET request to the website.  
response = requests.get(url)  
  
# Use BeautifulSoup to parse the HTML content of the website.  
soup = BeautifulSoup(response.content, "html.parser")  
  
# Find all the links on the website.  
links = soup.find_all("a")  
  
# Print all the links.  
for link in links:  
    print(link.get("href"))
```

## Rotating Proxies Using a Custom Method and an Array of Proxies

Rotating proxies helps work around the restrictions that websites put when they receive a large number of requests from the same IP address.

Do as follows:

1. Import the following Python libraries: Requests, Beautiful Soup, and Random.
2. Create a list of proxies to use during the rotation process. Use the `http://proxyserver.com:port` format:

```
# List of proxies  
proxies = [  
    "http://proxyprovider1.com:2010", "http://proxyprovider1.com:2020",  
    "http://proxyprovider1.com:2030", "http://proxyprovider2.com:2040",  
    "http://proxyprovider2.com:2050", "http://proxyprovider2.com:2060",  
    "http://proxyprovider3.com:2070", "http://proxyprovider3.com:2080",  
    "http://proxyprovider3.com:2090"  
]
```

3. Create a custom method called `get_proxy()`. It will randomly select a proxy from the list of proxies using the `random.choice()` method and return the selected proxy in dictionary format (both HTTP and HTTPS keys). You’ll use this method whenever you send a new request:

```
# Custom method to rotate proxies  
def get_proxy():  
    # Choose a random proxy from the list  
    proxy = random.choice(proxies)  
    # Return a dictionary with the proxy for both http and https protocols  
    return {'http': proxy, 'https': proxy}  
```

4. Create a loop that sends a certain number of GET requests using the rotated proxies. In each request, the `get()` method uses a randomly chosen proxy specified by the `get_proxy()` method.

5. Collect the links from the HTML content of the web page using the Beautiful Soup package, as explained previously.

6. Catch and print any exceptions that occur during the request process.

Here is the complete source code for this example:

```
# import packages  
import requests  
from bs4 import BeautifulSoup  
import random  
  
# List of proxies  
proxies = [  
    "http://proxyprovider1.com:2010", "http://proxyprovider1.com:2020",  
    "http://proxyprovider1.com:2030", "http://proxyprovider2.com:2040",  
    "http://proxyprovider2.com:2050", "http://proxyprovider2.com:2060",  
    "http://proxyprovider3.com:2070", "http://proxyprovider3.com:2080",  
    "http://proxyprovider3.com:2090"  
]

# Custom method to rotate proxies  
def get_proxy():  
    # Choose a random proxy from the list  
    proxy = random.choice(proxies)  
    # Return a dictionary with the proxy for both http and https protocols  
    return {'http': proxy, 'https': proxy}  
  
  
# Send requests using rotated proxies  
for i in range(10):  
    # Set the URL to scrape  
    url = 'https://brightdata.com/'  
    try:  
        # Send a GET request with a randomly chosen proxy  
        response = requests.get(url, proxies=get_proxy())  
  
        # Use BeautifulSoup to parse the HTML content of the website.  
        soup = BeautifulSoup(response.content, "html.parser")  
  
        # Find all the links on the website.  
        links = soup.find_all("a")  
  
        # Print all the links.  
        for link in links:  
            print(link.get("href"))  
    except requests.exceptions.RequestException as e:  
        # Handle any exceptions that may occur during the request  
        print(e)
```

## Using the Bright Data Proxy Service with Python

Bright Data has a large network of more than 72 million [residential proxy IPs](https://brightdata.com/proxy-types/residential-proxies) and more than 770,000 [datacenter proxies](https://brightdata.com/proxy-types/datacenter-proxies).

You can [integrate Bright Data’s datacenter proxies](https://brightdata.com/integration) into your Python requests. Once you have an account with Bright Data, follow these steps to create your first proxy:

1. Click **View proxy product** on the welcome page to view the different types of proxy offered by Bright Data:

![Bright Data proxy types](https://github.com/luminati-io/Proxy-with-python-requests/blob/main/bright-data-proxy-types-1024x464.png)

2. Select **Datacenter Proxies** to create a new proxy, and on the subsequent page, add your details, and save it:

![Datacenter proxies configuration](https://github.com/luminati-io/Proxy-with-python-requests/blob/main/datacenter-proxies-837x1024.png)

3. Once your proxy is created, the dashboard will show you parameters such as the host, the port, the username, and the password to use in your scripts:

![Datacenter proxy parameters](https://github.com/luminati-io/Proxy-with-python-requests/blob/main/datacenter-proxy-parameters-928x1024.png)

4. Copy-paste these parameters to your script and use the following format of the proxy URL: `username-(session-id)-password@host:port`.

> **Note:**\
> The `session-id` is a random number created by using a Python package called `random`.

Here is the code that uses a proxy from Bright Data in a Python request:

```
import requests  
from bs4 import BeautifulSoup  
import random  
  
# Define parameters provided by Brightdata  
host = 'brd.superproxy.io'  
port = 33335  
username = 'username'  
password = 'password'  
session_id = random.random()  
  
# format your proxy  
proxy_url = ('http://{}-session-{}:{}@{}:{}'.format(username, session_id,  
                                                     password, host, port))  
  
# define your proxies in dictionary  
proxies = {'http': proxy_url, 'https': proxy_url}  
  
# Send a GET request to the website  
url = "https://brightdata.com/"  
response = requests.get(url, proxies=proxies)  
  
# Use BeautifulSoup to parse the HTML content of the website  
soup = BeautifulSoup(response.content, "html.parser")  
  
# Find all the links on the website  
links = soup.find_all("a")  
  
# Print all the links  
for link in links:  
    print(link.get("href"))
```

Running this code will make a successful request using Bright Data’s [proxy service](https://brightdata.com/proxy-types).

## Conclusion

With Bright Data’s web platform, you can get reliable proxies for your project that cover any country or city in the world. Try Bright Data's [proxy services](https://brightdata.com/proxy-types) for free now!
