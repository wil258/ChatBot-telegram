import requests

class SimpleTelegramBot:
    def __init__(self):
        # Token atualizado
        self.token = "7922182800:AAG9E87ptxQt_HOh1GKfVcbo5pmRO2Vf15k"
        self.url_base = f"https://api.telegram.org/bot{self.token}/"

    def obter_mensagens(self, update_id=None):
        url = f"{self.url_base}getUpdates?timeout=100"
        if update_id:
            url += f"&offset={update_id + 1}"
        resposta = requests.get(url)
        return resposta.json()

    def enviar_resposta(self, chat_id, mensagem):
        url = f"{self.url_base}sendMessage"
        payload = {"chat_id": chat_id, "text": mensagem}
        requests.post(url, json=payload)

    def iniciar(self):
        print("Bot iniciado...")
        update_id = None
        while True:
            mensagens = self.obter_mensagens(update_id)
            if mensagens and "result" in mensagens:
                for mensagem in mensagens["result"]:
                    update_id = mensagem["update_id"]
                    chat_id = mensagem["message"]["chat"]["id"]
                    texto = mensagem["message"].get("text", "")

                    # Mensagem personalizada baseada na entrada do usuário
                    resposta = self.criar_resposta(texto)
                    self.enviar_resposta(chat_id, resposta)

    def criar_resposta(self, texto):
        texto = texto.lower()  # Para tornar as comparações case-insensitive
        if texto in ["oi", "olá", "hello"]:
            return "Olá! Tudo bem? Como posso ajudar você hoje? 😊"
        elif texto == "tchau":
            return "Até logo! Espero te ver em breve. 👋"
        elif texto == "como você está?":
            return "Eu sou apenas um bot, mas estou funcionando perfeitamente! E você? 😄"
        elif texto == "ajuda":
            return "Claro! Posso ajudar com as seguintes opções:\n- Diga 'oi' para uma saudação\n- Pergunte 'como você está?'\n- Diga 'tchau' para se despedir\n- Ou apenas converse comigo!"
        else:
            return f"Desculpe, eu ainda não entendi: '{texto}'. Pode tentar algo como 'ajuda' para saber o que eu posso fazer!"

# Inicializando o bot com o novo token
if __name__ == "__main__":
    bot = SimpleTelegramBot()
    bot.iniciar()
