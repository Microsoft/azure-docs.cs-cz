---
title: Seznam hlasů pro převod textu na řeč, Python - Služba řeči
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak získat úplný seznam standardních a neurálních hlasů pro oblast/koncový bod pomocí Pythonu. Seznam je vrácen jako JSON a dostupnost hlasu se liší podle oblasti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: b388c8d8b61e2fc638ae2bce5bc6d9eeb25ee0d4
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401018"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Získání seznamu hlasů pro převod textu na řeč pomocí Pythonu

V tomto článku se dozvíte, jak získat úplný seznam standardních a neurálních hlasů pro oblast/koncový bod pomocí Pythonu. Seznam je vrácen jako JSON a dostupnost hlasu se liší podle oblasti. Seznam podporovaných oblastí najdete v [tématu Oblasti](regions.md).

Tento článek vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

* Python 2.7.x nebo 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu Řeč

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.py`, zkopírujte tento fragment kódu.

```python
import requests
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Požadavky se používají pro požadavky HTTP pro službu převodu textu na řeč.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavení klíče předplatného a vytvoření výzvy pro tts

V několika následujících částech vytvoříte metody pro zpracování autorizace, volání rozhraní API pro převod textu na řeč a ověření odpovědi. Začněme vytvořením třídy. Toto je místo, kde budeme umístit naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na řeč.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

Je `subscription_key` váš jedinečný klíč z portálu Azure.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Tento koncový bod vyžaduje přístupový token pro ověřování. Chcete-li získat přístupový token, je vyžadována výměna. Tato ukázka výměny klíče předplatného služby `issueToken` Speech pro přístupový token pomocí koncového bodu.

Tato ukázka předpokládá, že vaše předplatné služby Řeči je v oblasti USA – západ. Pokud používáte jinou oblast, aktualizujte `fetch_token_url`hodnotu pro aplikaci . Úplný seznam naleznete v tématu [Oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte tento `GetVoices` kód do třídy:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Další informace o ověřování naleznete v tématu [Authenticate with an access token](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Žádost a uložení odpovědi

Zde budete stavět žádost a uložit seznam vrácených hlasů. Nejprve je třeba `base_url` nastavit `path`a . Tato ukázka předpokládá, že používáte koncový bod V USA . Pokud je prostředek zaregistrován v jiné oblasti, `base_url`nezapomeňte aktualizovat . Další informace naleznete v tématu [Oblasti služby Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadované hlavičky pro požadavek. Nakonec požádáte o službu. Pokud je požadavek úspěšný a je vrácen stavový kód 200, odpověď je zapsána do souboru.

Zkopírujte tento `GetVoices` kód do třídy:

```python
def get_voices(self):
    base_url = 'https://westus.tts.speech.microsoft.com'
    path = '/cognitiveservices/voices/list'
    get_voices_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token
    }
    response = requests.get(get_voices_url, headers=headers)
    if response.status_code == 200:
        with open('voices.json', 'wb') as voices:
            voices.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nvoices.json is ready to view.\n")
    else:
        print("\nStatus code: " + str(
            response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je vytvoření instance vaší třídy a volání funkcí.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je ono, jste připraveni spustit vzorek. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte odebrat všechny důvěrné informace ze zdrojového kódu ukázkové aplikace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam hlasových vzorků pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
