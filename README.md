# Python Automated Bulk WhatsApp Messages

It is a python script that sends WhatsApp message automatically from WhatsApp web application. It can be configured to send advertising messages to customers. It read data from an excel sheet and send a configured message to people.

## Prerequisites

In order to run the python script, your system must have the following programs/packages installed and the contact number should be saved in your phone (You can use bulk contact number saving procedure of email). There is a way without saving the contact number but has the limitation to send the attachment.
* Python 3.8: Download it from https://www.python.org/downloads
* Selenium Web Driver: Either you can use repo driver else you can download it https://chromedriver.chromium.org/downloads
* Google Chrome : Download it from https://www.google.com/chrome
* Pandas : Run in command prompt **pip install pandas**
* Xlrd : Run in command prompt **pip install xlrd**
* Selenium: Run in command prompt **pip install selenium** 
* PyAutoGUI: Run in command prompt **pip install PyAutoGUI** 

## Approach
* User scans web QR code to log in into the WhatsApp web application.
* The script reads a customized message from excel sheet.
* The script reads rows one by one and searches that contact number in the web search box if the contact number found on WhatsApp then it will send a configured message otherwise It reads next row. 
* Loop execute until and unless all rows complete.

Note: If you wish to send an image instead of text you can write attachment selection python code.

## Code
```
# Program to send bulk customized message through WhatsApp web application
# Original Author @inforkgodara
# Attachments by @acumpab

from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
from selenium.common.exceptions import NoSuchElementException
import pandas
import time
import pyautogui

# Load the chrome driver
driver = webdriver.Chrome()
count = 0

# Open WhatsApp URL in chrome browser
driver.get("https://web.whatsapp.com/")
wait = WebDriverWait(driver, 20)

# Read data from excel
excel_data = pandas.read_excel('Customer bulk email data.xlsx', sheet_name='Customers')
message = excel_data['Message'][0]

# Iterate excel rows till to finish
for column in excel_data['Name'].tolist():
    # Locate search box through x_path
    search_box = '//*[@id="side"]/div[1]/div/label/div/div[2]'
    person_title = wait.until(lambda driver:driver.find_element_by_xpath(search_box))

    # Clear search box if any contact number is written in it
    person_title.clear()

    # Send contact number in search box
    person_title.send_keys(str(excel_data['Contact'][count]))
    count = count + 1

    # Wait for 3 seconds to search contact number
    time.sleep(3)

    try:
        # Load error message in case unavailability of contact number
        element = driver.find_element_by_xpath('//*[@id="pane-side"]/div[1]/div/span')
    except NoSuchElementException:
        # Format the message from excel sheet
        message = message.replace('{customer_name}', column)
        person_title.send_keys(Keys.ENTER)
        actions = ActionChains(driver)
        actions.send_keys(message)
        actions.send_keys(Keys.ENTER)
        actions.perform()
        
        #OPTIONAL
        #This Code allows you to Paste some image in current clipboard
        mensajeBox = driver.find_element_by_css_selector('._2x4bz > div:nth-child(2)')
        mensajeBox.send_keys(Keys.CONTROL, 'v')
        time.sleep(2)
        botonSend = driver.find_element_by_css_selector('.SncVf')
        botonSend.click()
        time.sleep(2)
        
        
        #Attach Image or Video
        btnAttach = driver.find_element_by_xpath('/html/body/div/div[1]/div[1]/div[4]/div[1]/footer/div[1]/div[1]/div[2]/div/div')
        btnAttach.click()
        time.sleep(1)
        
        btnImageVideo = driver.find_element_by_xpath('/html/body/div/div[1]/div[1]/div[4]/div[1]/footer/div[1]/div[1]/div[2]/div/span/div[1]/div/ul/li[1]/button/span')
        btnImageVideo.click()
        time.sleep(5)
        
        pyautogui.write('<PATH_TO_FILE>')
        pyautogui.press('enter')
        time.sleep(10)
        botonSend = driver.find_element_by_css_selector('.SncVf')
        botonSend.click()
        time.sleep(2)

# Close Chrome browser
driver.quit()
```
Note: The script may not work in case if the HTML of web WhatsApp is changed.
