---
title: 'Rychlý start: Seznam převod textu na řeč hlasů, Python – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu budete zjistěte, jak získat celý seznam standardní a neuronových sítí hlasy pro oblast nebo koncový bod pomocí Pythonu. V seznamu se vrátí jako JSON a hlasové dostupnosti se liší podle oblasti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: erhopf
ms.openlocfilehash: 66bda68b1313a7c172e273671bc3a03503d08e0d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876576"
---
# <a name="quickstart-get-the-list-of-text-to-speech-voices-using-python"></a>Rychlý start: Získání seznamu převod textu na řeč hlasy pomocí Pythonu

V tomto rychlém startu budete zjistěte, jak získat celý seznam standardní a neuronových sítí hlasy pro oblast nebo koncový bod pomocí Pythonu. V seznamu se vrátí jako JSON a hlasové dostupnosti se liší podle oblasti. Seznam podporovaných oblastí najdete v tématu [oblastech](regions.md).

Tento rychlý start vyžaduje [účtu služeb Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem hlasové služby. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč rozhraní předplatné Azure pro hlasové služby

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `get-voices.py`, zkopírujte tento fragment kódu.

```python
import requests
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Požadavky se používá pro požadavky HTTP na převod textu na řeč služby.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavte klíč předplatného a vytvoření výzvu pro převod textu na ŘEČ

V následujících částech vytvoříte metody pro autorizaci, volání rozhraní API pro převod textu na řeč a ověřit odpověď. Začněme tím, že vytvoření třídy. Je to, kde dáme naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na řeč.

```python
class GetVoices(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.access_token = None
```

`subscription_key` Je jedinečný klíč z portálu Azure portal.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Tento koncový bod vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vymění váš klíč předplatného hlasových služeb pro aplikace access token pomocí `issueToken` koncového bodu.

Tento příklad předpokládá, že vaše předplatné hlasové služby je v oblasti západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `fetch_token_url`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

Tady budete vytvářet žádosti a uložit seznam vrácených hlasy. Nejprve je nutné nastavit `base_url` a `path`. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `base_url`. Další informace najdete v tématu [hlasových služeb regiony](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

V dalším kroku přidejte požadované hlavičky pro dané žádosti. A konečně provede požadavek do služby. Pokud je žádost úspěšná a vrátí stavový kód 200 kód, odpověď zapsána do souboru.

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
            print("\nStatus code: " + str(response.status_code) + "\nvoices.json is ready to view.\n")
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

Je to, jste připraveni ke spuštění ukázky. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
python get-voices.py
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na Githubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Další informace najdete v tématech

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
