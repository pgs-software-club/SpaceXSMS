# SpaceX SMS API

HTTP-based SMS gateway API supporting GET and POST requests. Use it to send Text, Flash, WAP-Push, vCard, and Unicode SMS, and to check credit balance, delivery reports, and transaction history.

## Base URL

```
https://sms.techhimalaya.com/smsapi/index.php
```

## Authentication

Every request requires your API key, passed as the `key` parameter.

```
key=YOUR_API_KEY
```

** Please contact Shibam Guragai at the Hackathon Venue for the API Key**

You can view and regenerate your API key from your account dashboard. Treat it like a password — anyone with the key can send SMS on your account.

## Available Endpoints

All endpoints share the same base URL and are differentiated by the `type` parameter (and other request fields). This document covers the **Text SMS API** in detail; the others follow the same request/response pattern unless noted.

| API | Description |
|---|---|
| Text SMS API | Send standard GSM text messages |
| Flash SMS API | Send flash (pop-up) SMS |
| WAP-Push API | Send WAP-Push SMS with a link |
| vCard API | Send contact card (vCard) SMS |
| Unicode API | Send Unicode SMS (for non-GSM scripts, e.g. Devanagari) |
| Credit Balance API | Check remaining SMS credit |
| DLR API | Fetch delivery reports for a sent shoot |
| Last Transaction API | Fetch recent transaction history |

---

## Text SMS API

**Endpoint:** `https://sms.techhimalaya.com/smsapi/index.php`
**Method:** HTTP GET or POST
**Auth:** API key required

Sends a standard text (GSM) SMS to one or more recipients. All parameters are passed as GET query strings or POST fields.

### Request Parameters

| Parameter | Required | Description |
|---|---|---|
| `key` | Required | Your API key, used to authenticate every request. |
| `campaign` | Optional | ID of the campaign to use, as defined in your account. Default Campaign ID: `9782`. |
| `routeid` | Optional | ID of the route to use, as defined in your account. TECH-SMSID: `195`. |
| `type` | Required | SMS type. Set to `text` for a standard SMS. |
| `responsetype` | Optional | Response format: `http`, `json`, or `xml`. Defaults to `http`. |
| `contacts` | Required | Recipient mobile numbers, comma-separated (e.g. `9841XXXXXX,9801XXXXXX`). |
| `senderid` | Required | Any sender ID approved for your account. |
| `msg` | Required | URL-encoded SMS text. Maximum length: 720 characters. |
| `time` | Optional | Scheduled delivery time, format `YYYY-MM-DD H:i` (e.g. `2025-12-01 14:30`). Leave blank to send immediately. |

### API Responses

| Response | Description |
|---|---|
| `ERR: {MESSAGE}` | An error occurred. The cause is described inside the braces (e.g. `ERR: INVALID API KEY` means the key is expired or invalid). Error messages are plain text and self-explanatory. |
| `SMS-SHOOT-ID/{alpha-numeric string}` | The SMS was submitted successfully. The returned shoot ID (after the slash) can be used to fetch delivery reports via the DLR API. |

### Sample API Calls

**Send a text SMS**

```
https://sms.techhimalaya.com/smsapi/index.php?key=YOUR_API_KEY&campaign=XXXXXX&routeid=XXXXXX&type=text&contacts=9841XXXXXX,9801XXXXXX&senderid=XXXXXX&msg=Hello%2C+this+is+a+test+SMS.
```

**Schedule a text SMS**

```
https://sms.techhimalaya.com/smsapi/index.php?key=YOUR_API_KEY&campaign=XXXXXX&routeid=XXXXXX&type=text&contacts=9841XXXXXX,9801XXXXXX&senderid=XXXXXX&msg=Hello%2C+this+is+a+test+SMS.&time=2026-09-17+14%3A27
```

**Send a text SMS to a single number**

```
https://sms.techhimalaya.com/smsapi/index.php?key=YOUR_API_KEY&campaign=XXXXXX&routeid=XXXXXX&type=text&contacts=9841XXXXXX&senderid=XXXXXX&msg=Hello%2C+this+is+a+test+SMS.
```

### Sample Code (Python)

**HTTP GET (`requests`)**

```python
import requests

api_key = "YOUR_API_KEY"
contacts = "9841XXXXXX"  # single number, or "9841XXXXXX,9801XXXXXX" for multiple
sender_id = "XXXXXX"
message = "Hello, this is a test SMS."

params = {
    "key": api_key,
    "campaign": "XXXXXX",
    "routeid": "XXXXXX",
    "type": "text",
    "contacts": contacts,
    "senderid": sender_id,
    "msg": message,
}

response = requests.get("https://sms.techhimalaya.com/smsapi/index.php", params=params)
print(response.text)
```

**HTTP POST (`requests`)**

```python
import requests

api_key = "YOUR_API_KEY"
contacts = "9841XXXXXX"
sender_id = "XXXXXX"
message = "Hello, this is a test SMS."

data = {
    "key": api_key,
    "campaign": "XXXXXX",
    "routeid": "XXXXXX",
    "type": "text",
    "contacts": contacts,
    "senderid": sender_id,
    "msg": message,
}

response = requests.post("https://sms.techhimalaya.com/smsapi/index.php", data=data)
print(response.text)
```

### Sample Code (PHP)

**HTTP GET**

```php
<?php
$api_key = 'YOUR_API_KEY';
$contacts = '9841XXXXXX,9801XXXXXX';
$sender_id = 'XXXXXX';
$message = urlencode('Hello, this is a test SMS.');

$api_url = "https://sms.techhimalaya.com/smsapi/index.php?key=".$api_key."&campaign=XXXXXX&routeid=XXXXXX&type=text&contacts=".$contacts."&senderid=".$sender_id."&msg=".$message;

$response = file_get_contents($api_url);
echo $response;
?>
```

**HTTP POST**

```php
<?php
$api_key = 'YOUR_API_KEY';
$contacts = '9841XXXXXX,9801XXXXXX';
$sender_id = 'XXXXXX';
$message = urlencode('Hello, this is a test SMS.');

$post_fields = "key=".$api_key."&campaign=XXXXXX&routeid=XXXXXX&type=text&contacts=".$contacts."&senderid=".$sender_id."&msg=".$message;

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, "https://sms.techhimalaya.com/smsapi/index.php");
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, $post_fields);
$response = curl_exec($ch);
curl_close($ch);
echo $response;
?>
```

---

## Other SMS Types

The Flash SMS, WAP-Push, vCard, and Unicode APIs use the same endpoint and core parameters (`key`, `campaign`, `routeid`, `contacts`, `senderid`, `msg`, `responsetype`, `time`), with `type` changed accordingly (e.g. `type=flash`, `type=unicode`, etc.) and, where relevant, extra parameters specific to that message type. Refer to your account's API documentation page for the exact parameter set of each.

## Utility APIs

- **Credit Balance API** — Check your remaining SMS credit.
- **DLR API** — Fetch the delivery status of a previously sent shoot using its Shoot ID.
- **Last Transaction API** — Retrieve your recent sending/transaction history.

---

## Notes

- Always URL-encode the `msg` parameter.
- Message length is capped at 720 characters for text SMS.
- Keep your API key secret; regenerate it immediately if it's ever exposed.
- Scheduled sends use local server time in `YYYY-MM-DD H:i` format.
