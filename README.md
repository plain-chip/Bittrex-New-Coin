//# Bittrex-New-Coin
//When run, emails users the new coins that have been added to the exchange, Bittrex


import requests
import json
import smtplib


def build_pair_string(pairs):
    pair_string = ""
    for pair in pairs:
        pair_string += pair + "\n"

    return pair_string

try:
    with open('markets.json', 'r') as current_markets:
        before = json.loads(current_markets.read())
except IOError:
    before = requests.get('https://bittrex.com/api/v1.1/public/getmarkets').json()
    with open('markets.json', 'w') as current_markets:
        current_markets.write(json.dumps(before))
    print("First run... Getting initial market data.")

after = requests.get('https://bittrex.com/api/v1.1/public/getmarkets').json()

before_set = set([market['MarketName'] for market in before['result']])
after_set = set([market['MarketName'] for market in after['result']])

new_set = after_set - before_set

if not new_set:
    with open('markets.json', 'w') as current_markets:
        current_markets.write(json.dumps(after))
    print('No pairs have been added')
    notnew_list = []
    for pair in new_set:
        notnew_list.append(pair)  # = [item for item in pair]
    print(notnew_list)
    
    sender = 'snally.343@gmail.com'
    receivers = ['mcgowanjack4@gmail.com']

    message = 'Subject: No New Coins\n\nBittrex has NOT added any new coins.\nCheck back later!'
    

    try:
        smtpObj = smtplib.SMTP_SSL('smtp.gmail.com:465')
        smtpObj.login('snally.343@gmail.com','**********')
        smtpObj.sendmail(sender, receivers, message)         
        print("\nSuccessfully sent email")
    except SMTPException:
       print("\nError: unable to send email")
if new_set:
    with open('markets.json', 'w') as current_markets:
        current_markets.write(json.dumps(after))
    print('Bittrex has added the following pairs:')
    new_list = []
    for pair in new_set:
        new_list.append(pair)  # = [item for item in pair]
    print(new_list)
    
    sender = 'snally.343@gmail.com'
    receivers = ['mcgowanjack4@gmail.com']

    message = """
Bittrex has added the following pairs:\n{}\n
Get 'em while they're hot!
    """.format(build_pair_string(new_list))

    try:
        smtpObj = smtplib.SMTP_SSL('smtp.gmail.com:465')
        smtpObj.login('example@example.com','password')
        smtpObj.sendmail(sender, receivers, message)         
        print("\nSuccessfully sent email")
    except SMTPException:
       print("\nError: unable to send email")
