---
title: 'Rychlý Start: seznam hlasů pro převod textu na řeč, Python-Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Pythonu. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: c645d01dd37fd370911019446c99c76c9da6221c
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975971"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Rychlý Start: získání seznamu hlasů pro převod textu na řeč pomocí Pythonu

V tomto rychlém startu se dozvíte, jak získat úplný seznam standardních a neuronové hlasů pro oblast nebo koncový bod pomocí Pythonu. Seznam se vrátí jako JSON a dostupnost hlasu se v jednotlivých oblastech liší. Seznam podporovaných oblastí najdete v tématu [oblasti](regions.md).

Tento rychlý Start vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
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

`subscription_key` Je jedinečný klíč z portálu Azure portal.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Tento koncový bod vyžaduje přístupový token pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken`ho koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `fetch_token_url`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

## <a name="make-a-request-and-save-the-response"></a>Vytvořit žádost a uložit odpověď

Tady se chystáte vytvořit žádost a uložit seznam vrácených hlasů. Nejprve je nutné nastavit `base_url` a `path`. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `base_url`. Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále přidejte požadovaná záhlaví pro požadavek. A konečně provede požadavek do služby. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď se zapíše do souboru.

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

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
