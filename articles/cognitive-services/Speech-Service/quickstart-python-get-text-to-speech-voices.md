---
title: Seznam hlasů pro převod textu na řeč, Python-Speech Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Pythonu. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 02/10/2020
ms.author: dapine
ms.openlocfilehash: 51fe6cea80e097f34432ab8dc7293c758bd8d720
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77119809"
---
# <a name="get-the-list-of-text-to-speech-voices-using-python"></a>Získání seznamu hlasů pro převod textu na řeč pomocí Pythonu

V tomto článku se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Pythonu. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší. Seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

Tento článek vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

* Python 2.7.x nebo 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu pro rozpoznávání řeči

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.py`, zkopírujte tento fragment kódu.

```python
import requests
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Požadavky se používají pro požadavky HTTP na službu převodu textu na řeč.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavte klíč předplatného a vytvoření výzvu pro převod textu na ŘEČ

V následujících částech vytvoříte metody pro autorizaci, volání rozhraní API pro převod textu na řeč a ověřit odpověď. Pojďme začít vytvořením třídy. Je to, kde dáme naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na řeč.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` je jedinečný klíč z Azure Portal.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Tento koncový bod vyžaduje přístupový token pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken`ho koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jinou oblast, aktualizujte hodnotu pro `fetch_token_url`. Úplný seznam najdete v tématu [oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte tento kód do třídy `GetVoices`:

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

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady se chystáte vytvořit žádost a uložit seznam vrácených hlasů. Nejdřív je potřeba nastavit `base_url` a `path`. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud je prostředek zaregistrován v jiné oblasti, nezapomeňte aktualizovat `base_url`. Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadovaná záhlaví pro požadavek. A konečně provede požadavek do služby. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď se zapíše do souboru.

Zkopírujte tento kód do třídy `GetVoices`:

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

Už jste téměř hotovi. Posledním krokem je vytvoření instance své třídy a volání funkce.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = GetVoices(subscription_key)
    app.get_token()
    app.get_voices()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je to, že jste připraveni spustit ukázku. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam ukázek hlasu pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
