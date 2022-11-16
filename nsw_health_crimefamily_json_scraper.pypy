#-*- coding: utf-8 -*-
from importlib import reload
from seleniumwire import webdriver
from seleniumwire.utils import decode
import certifi
import cgi
import json
import locale
import os
import re
import ssl
import sys
reload(sys)



# search dict list for a specific keyname 
def findkeys(node, kv):
    if isinstance(node, list):
        for i in node:
            for x in findkeys(i, kv):
               yield x
    elif isinstance(node, dict):
        if kv in node:
            yield node[kv]
        for j in node.values():
            for x in findkeys(j, kv):
                yield x

# finds total number of policy documents published
url = 'https://www1.health.nsw.gov.au/pds/Pages/a-z.aspx'
driver = webdriver.Chrome()
driver.get(url)
driver.implicitly_wait(10)
text = driver.find_element('id', 'ResultCount').text

count = int(re.sub(r'\D+', '', text))
print(f"url: {url}, count: {count}")
driver.quit()


# iterates through every page of policy document search results
# parses json response containing info for each pd 

dataset = [] 	

for i in list(range(1, count, 25)):
	url = 'https://www1.health.nsw.gov.au/pds/Pages/date.aspx#Default={"k":"","o":[{"d":1,"p":"nswhpdsMPPublicationDateTime"}],"s":' + f"{i}" + '}'
	print(url)
	driver = webdriver.Chrome()
	driver.implicitly_wait(5) 
	driver.get(url)
	for request in driver.requests:
		if request.response:
		    if 'application/json' in request.response.headers['Content-Type'] and 'ProcessQuery' in request.url:
		    	body = json.loads(decode(request.response.body, request.response.headers.get('Content-Encoding', 'identity')))
		    	results = list(findkeys(body, 'ResultRows'))[0]
		    	dataset.extend(results)
		    	driver.quit()

		    	# writes to json file each loop in case script terminates
		    	with open('dataset.json', 'w') as outfile:
		    		json.dump(dataset, outfile, indent=4)

print(f"total: {len(dataset)}")
