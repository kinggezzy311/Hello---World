#!/usr/bin/env python
# encoding: UTF-8

import sys
from smtplib import SMTP_SSL as SMTP
import requests
from time import sleep

priceRate 		= 75 #USD - change bitcoinData to get other currencies
fileName        = sys.argv[1]
emailAddress	= sys.argv[2]
paymentAddress 	= sys.argv[3] # 37zFquAR8Aknr9EiTHTBd5p3E6PwPM1zmD
productSample  	= sys.argv[4] # http://g.recordit.co/7yes2i5gUa.gif 
host 			= "{SMTP Server Address}"
usrnme 			= "{SMTP User Name}"
pswd 			= "{SMTP Password}"
subject 		= "Payment Requested for %s" % (fileName)
to_addr 		= emailAddress
from_addr   	= "{From Address}"
letterName		= "Frankenmint"
website 		= "Honeybadgerofmoney.com"
f 				= open(fileName, 'r' )
file       		= f.readlines()
address    		= paymentAddress
sampleLink 		= productSample
fileData   		= ""
	



req = requests.get('http://api.bitcoincharts.com/v1/weighted_prices.json')
bitcoinData = req.json()
# print bitcoinData
btcPrice = bitcoinData['USD']['24h']
print "Bitcoin Price: $"+btcPrice
amountDue =  round(priceRate/float(btcPrice), 8)
print "AmountDue ฿"+str(amountDue)


for lines in file:
	if lines.startswith((address, sampleLink)):
		continue
	fileData += lines
#print fileData
fileData = fileData.replace('\n', '<br />')

def send_email(host, subject, to_addr, from_addr, body_text):
		"""
		Send an Email
		"""
		msg_body = ""
		parts = ["From: %s" % from_address,
				"To: %s" % to_address,
				"MIME-Version: 1.0",
				"Content-type: text/html",
				"Subject: %s" % subject,
				"",
				body_text
				, "\r\n"]
		msg_body = "\r\n".join(parts)
		#print msg_body
		server = SMTP(host)
		server.login(usrnme,pswd)
		server.sendmail(from_addr, [to_addr], msg_body)
		server.quit()


body_text = 'Hey there! <br/><br/> Here is a sample of \
the content: %s <br/><br/> Please pay <a href=\"bitcoin:%s?amount=%s&label=Payment for %s\">฿%s</a> \
BTC to %s for this content. <br/> The file is emailed automatically upon  \
confirmation of funds.<br/><br/>Pleasure doing business with you!<br/><br/><br/> \
Sincerely,<br/><br/> %s <br/> %s' % (sampleLink, address, str(amountDue), fileName, str(amountDue), address, letterName, website)
send_email(host, subject, to_addr, from_addr, body_text)


#now we wait and watch for payments...

while True:
	print "Checking %s for Payments" % (address)
	req = requests.get("https://blockexplorer.com/api/addr/"+address)
	try:
		pmts = req.json()
	except ValueError:
		print "Connection Timed out"
		sleep(5)
		pmts = {}
		pmts["totalReceived"] = 0
	balance =  pmts['totalReceived']
	print balance

	if float(balance) >= float(amountDue):
		print "Payment Received!"
		print "Emailing File Data"
		body_text = "Your payment has been received!  Here is your \
		file:<br/><br/><br/> %s <br/><br/><br/> Sincerely,<br/><br/> %s <br/> %s" % (fileData, letterName, website)
		send_email(host, subject, to_addr, from_addr, body_text)
		break
	else:
		print "Still Waiting..."
		print "Will Try again in 3 Seconds..."
		sleep(3)
		

