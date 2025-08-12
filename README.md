# instagram
Not sure yet

## Notes

Integrating Instagram for automated messaging is possible, but it requires a bit more setup than a simple script. The Facebook Messenger Platform for Instagram is the correct API to use. It's designed for businesses to manage conversations at scale.

It's important to note that this is not a personal-use API. You need a Facebook Business Page and an Instagram Professional Account (either business or creator) linked to it. You will then create a Facebook Developer App to get the necessary access tokens and set up webhooks. The messages will be managed through this app, not your personal Instagram account.

Before the code will work, you must have a Facebook Developer account, a Facebook Page, and an Instagram Business account connected. You will also need to generate a Page Access Token with the correct permissions.


### Creating post
The process involves using the Messenger Platform API, which allows you to send various message types, including interactive ones with buttons that link to specific URLs.

To accomplish this, you'll need to use a message template that supports a call-to-action button, such as the Button Template. Your Python script will make a POST request to the API endpoint with a JSON payload that specifies the recipient, the message text, and the details of the button, including your custom URL.

```python
import requests
import json
import os

# --- Configuration ---
# You must obtain this from your Facebook Developer App settings.
# Do not hardcode this in a production environment. Use environment variables.
PAGE_ACCESS_TOKEN = os.environ.get("INSTAGRAM_PAGE_ACCESS_TOKEN", "YOUR_ACCESS_TOKEN_HERE")
# The ID of the recipient on Instagram.
# This could be a specific user or a conversation ID.
RECIPIENT_ID = "YOUR_RECIPIENT_ID_HERE"
# The URL you want to embed in the button.
CUSTOM_URL = "https://www.google.com"

# The API endpoint for sending messages.
API_URL = f"https://graph.facebook.com/v19.0/me/messages?access_token={PAGE_ACCESS_TOKEN}"

def send_url_message(recipient_id, message_text, button_url):
    """
    Sends a message with a custom URL button to a specified Instagram recipient.

    Args:
        recipient_id (str): The ID of the recipient (e.g., user ID, conversation ID).
        message_text (str): The main text of the message.
        button_url (str): The custom URL for the button.
    """
    # The JSON payload for the message.
    # This uses the 'template' type with a 'button' payload.
    payload = {
        "recipient": {
            "id": recipient_id
        },
        "message": {
            "attachment": {
                "type": "template",
                "payload": {
                    "template_type": "button",
                    "text": message_text,
                    "buttons": [
                        {
                            "type": "web_url",
                            "url": button_url,
                            "title": "Visit Custom URL"
                        }
                    ]
                }
            }
        }
    }

    headers = {
        "Content-Type": "application/json"
    }

    try:
        response = requests.post(API_URL, headers=headers, data=json.dumps(payload))
        response.raise_for_status()  # Raise an exception for bad status codes
        print("Message sent successfully!")
        print(f"Response: {response.json()}")

    except requests.exceptions.RequestException as e:
        print(f"Error sending message: {e}")
        print(f"Response body: {response.text}")

if __name__ == "__main__":
    message_to_send = "Hello! Click the button below to visit our custom page."
    send_url_message(RECIPIENT_ID, message_to_send, CUSTOM_URL)

```

### Sending DM
Here is a Python script that demonstrates how to send a simple text message as a DM using the requests library. This script provides the structure, but you'll need to fill in your specific credentials and recipient ID.

```python
import requests
import json
import time

# You must obtain these values from the Facebook Developer App setup.
# DO NOT hardcode these in a production application. Use environment variables.
PAGE_ID = "YOUR_FACEBOOK_PAGE_ID"
PAGE_ACCESS_TOKEN = "YOUR_PAGE_ACCESS_TOKEN"
# This is the ID of the user you want to send a message to.
# This ID is the Instagram-scoped ID, not their public username.
RECIPIENT_ID = "THE_INSTAGRAM_SCOPED_USER_ID"

# The base URL for the Facebook Graph API
GRAPH_API_URL = f"https://graph.facebook.com/v19.0/{PAGE_ID}/messages"

def send_instagram_dm(recipient_id, message_text):
    """
    Sends a text message to a specific Instagram user.

    Args:
        recipient_id (str): The Instagram-scoped user ID of the recipient.
        message_text (str): The text content of the message.
    """
    # The payload for the API request
    payload = {
        "messaging_type": "RESPONSE",
        "recipient": {"id": recipient_id},
        "message": {"text": message_text},
        "access_token": PAGE_ACCESS_TOKEN
    }

    print(f"Attempting to send message to recipient: {recipient_id}")
    try:
        # Make the POST request to the API
        response = requests.post(GRAPH_API_URL, json=payload)
        response.raise_for_status()  # Raise an exception for bad status codes
        
        # Parse the JSON response
        response_data = response.json()
        print("Message sent successfully!")
        print(f"Response: {json.dumps(response_data, indent=2)}")

        # You can add logic here to handle the response, e.g., logging message_id
        return response_data
    except requests.exceptions.HTTPError as errh:
        print(f"HTTP Error: {errh}")
    except requests.exceptions.ConnectionError as errc:
        print(f"Error Connecting: {errc}")
    except requests.exceptions.Timeout as errt:
        print(f"Timeout Error: {errt}")
    except requests.exceptions.RequestException as err:
        print(f"An unexpected error occurred: {err}")

if __name__ == "__main__":
    # Example usage
    dm_text = "Hello from a Python script! This is an automated message."
    send_instagram_dm(RECIPIENT_ID, dm_text)

    # To send a message with a URL, you would need to modify the `message` payload.
    # The API documentation specifies how to handle different message types.
    # A simple way to include a URL in a text message is to just put it in the string.
    # To include a more complex "link preview," you would use an `attachment` object.
    # Example of a URL in a text message:
    # dm_text_with_url = "Check out this cool website: https://developers.facebook.com/docs/messenger-platform/instagram/"
    # send_instagram_dm(RECIPIENT_ID, dm_text_with_url)

```
