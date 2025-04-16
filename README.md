from flask import Flask, request
import requests
import openAI

app = Flask(__name__)

TELEGRAM_TOKEN = '7660585319:AAEBN7PnIP1PK6VPGd1Af3ILXKpy4H3OaMY'
OPENAI_API_KEY = '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'

openAI.api_key = OPENAI_API_KEY

def send_message(chat_id, text):
    url = f"https://api.telegram.org/bot{TELEGRAM_TOKEN}/sendMessage"
    payload = {"chat_id": chat_id, "text": text}
    requests.post(url, json=payload)

@app.route("/webhook", methods=["POST"])
def webhook():
    data = request.json
    message = data["message"].get("text")
    chat_id = data["message"]["chat"]["id"]

    if not message:
        return "ok"

    response = openAI.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": message}]
    )

    reply = response.choices[0].message.content
    send_message(chat_id, reply)
    return "ok"

if __name__ == "__main__":
    app.run()
