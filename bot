import os

import requests

def fetch_json_field(url, field):
    try:
        response = requests.get(url)
        response.raise_for_status()  # Raise an exception for HTTP errors
        json_data = response.json()
        
        if field in json_data:
            return json_data[field]
        else:
            return f"Field '{field}' not found in the JSON data."
    except requests.exceptions.RequestException as e:
        return f"Error fetching JSON data: {e}"

def fetch_multiple_urls(addresses, field):
    results = {}
    scanner = "https://api.forta.network/stats/sla/scanner/"
    for address in addresses:
        results[address] = fetch_json_field(scanner + address, field)
    return results

def send_telegram_message(bot_token, chat_id, message):
    url = f"https://api.telegram.org/bot{bot_token}/sendMessage"
    params = {"chat_id": chat_id, "text": message}

    try:
        response = requests.post(url, params)
        response.raise_for_status()  # Raise an exception for HTTP errors
        print("Message sent successfully!")
    except requests.exceptions.RequestException as e:
        print(f"Error sending message: {e}")

addresses = os.getenv('NODE_ADDRESSES')

field_to_fetch = "statistics"
results = fetch_multiple_urls(addresses, field_to_fetch)

bot_token = os.getenv('BOT_TOKEN')
chat_id = os.getenv('CHAT_ID')

threshold = 0.9

for url, result in results.items():
    print(f"Field '{field_to_fetch}' from {url}: {result}")
    if result['avg'] < threshold:
        sla_message = f"{url}: {result}"
        print(sla_message)
        send_telegram_message(bot_token, chat_id, sla_message)
