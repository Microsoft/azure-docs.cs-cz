---
title: Seznam hlasů pro převod textu na řeč, Python-Speech Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Pythonu. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-python
ms.openlocfilehash: b19c7f92b38d68b1f072b1bf66f23f1ddeddc197
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87876270"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Získání seznamu hlasů pro převod textu na řeč pomocí Pythonu

V tomto článku se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Pythonu. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší. Seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

Tento článek vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

* Python 2.7.x nebo 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"></span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"></span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu pro rozpoznávání řeči

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.py`, zkopírujte tento fragment kódu.

```python
import requests
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Požadavky se používají pro požadavky HTTP na službu převodu textu na řeč.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavení klíče předplatného a vytvoření výzvy pro TTS

V následujících částech vytvoříte metody pro zpracování autorizace, volání rozhraní API pro převod textu na mluvené slovo a ověření odpovědi. Pojďme začít vytvořením třídy. Tady budeme dávat naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na mluvené slovo.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key`Je jedinečný klíč z Azure Portal.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Tento koncový bod vyžaduje přístupový token pro ověřování. Pro získání přístupového tokenu se vyžaduje Exchange. Tato ukázka vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken` koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jinou oblast, aktualizujte hodnotu pro `fetch_token_url` . Úplný seznam najdete v tématu [oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte tento kód do `GetVoices` třídy:

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
> Další informace o ověřování najdete v tématu [ověřování pomocí přístupového tokenu](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Vytvoření žádosti a uložení odpovědi

Tady se chystáte vytvořit žádost a uložit seznam vrácených hlasů. Nejdřív je potřeba nastavit `base_url` a `path` . Tato ukázka předpokládá, že používáte koncový bod Západní USA. Pokud je prostředek zaregistrován v jiné oblasti, nezapomeňte aktualizovat `base_url` . Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadovaná záhlaví pro požadavek. Nakonec vytvoříte požadavek na službu. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď se zapíše do souboru.

Zkopírujte tento kód do `GetVoices` třídy:

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

To je to, že jste připraveni spustit ukázku. Z příkazového řádku (nebo relace Terminálové služby) přejděte do adresáře projektu a spusťte příkaz:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte ze zdrojového kódu ukázkové aplikace odebrat všechny důvěrné informace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam ukázek hlasu pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
