---
title: 'Rychlý start: Převést převod textu na řeč, Python – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí Pythonu a rozhraní REST API pro převod textu na řeč. Ukázkový text v tomto průvodci strukturovaná jako jazyk pro značky syntézu řeči (SSML). To umožňuje zvolit hlasu a jazyk odpovědi řeči.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 11/16/2018
ms.author: erhopf
ms.custom: seodec18
ms.openlocfilehash: 00f57ccc0e02c8805e9a41fc5bce8f0ca4a3303a
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53542522"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Rychlý start: Převést převod textu na řeč pomocí Pythonu

V tomto rychlém startu se dozvíte jak převést převod textu na řeč pomocí Pythonu a převod textu na řeč rozhraní REST API. Text požadavku v této příručce má strukturu [řeči syntézu Markup Language (SSML)](speech-synthesis-markup.md), která umožňuje výběr hlasu a jazyk odpovědi.

Tento rychlý start vyžaduje [účtu služeb Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem Speech Service. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download), nebo vašem oblíbeném textovém editoru
* Klíč rozhraní předplatné Azure pro službu rozpoznávání řeči

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `tts.py`, zkopírujte tento fragment kódu.

```python
import os, requests, time
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Tyto moduly se používají k zápisu odpovědi řeči do souboru s časovým razítkem, vytvořit požadavek HTTP a volání rozhraní API pro převod textu na řeč.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavte klíč předplatného a vytvoření výzvu pro převod textu na ŘEČ

V následujících částech vytvoříte metody pro autorizaci, volání rozhraní API pro převod textu na řeč a ověřit odpověď. Začněme přidáním kódu, který zajišťuje, že tento příklad bude fungovat s Python 2.7.x a 3.x.

```python
try: input = raw_input
except NameError: pass
```

V dalším kroku vytvoříme třídy. Je to, kde dáme naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na řeč.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` Je jedinečný klíč z portálu Azure portal. `tts` se zobrazí výzva k zadání textu, který bude převeden do mluvené řeči. Tento vstup je řetězcový literál, znaky se nemusí být uvozeny řídicími znaky. Nakonec `timestr` získá aktuální čas, který použijeme název souboru.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Převod textu na řeč rozhraní REST API vyžaduje přístupového tokenu pro ověřování. Chcete-li získat přístupový token, je potřeba systému exchange. Tato ukázka vymění váš klíč předplatného Speech Service token přístupu k použití `issueToken` koncového bodu.

Tento příklad předpokládá, že je vaše předplatné Speech Service v oblasti západní USA. Pokud používáte jiné oblasti, aktualizujte hodnotu pro `fetch_token_url`. Úplný seznam najdete v tématu [oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte tento kód do `TextToSpeech` třídy:

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

Tady budete vytvářet žádosti a uložení odpovědi řeči. Nejprve je nutné nastavit `base_url` a `path`. Tento příklad předpokládá, že používáte koncový bod západní USA. Pokud váš prostředek je registrován v jiné oblasti, ujistěte se, že aktualizujete `base_url`. Další informace najdete v tématu [Speech Service oblastech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je třeba přidat povinné hlavičky pro dané žádosti. Ujistěte se, že aktualizujete `User-Agent` názvem prostředku (nachází se na webu Azure Portal) a nastavte `X-Microsoft-OutputFormat` na preferovaný zvuku. Úplný seznam formátech výstupu najdete v tématu [zvuk výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#audio-outputs).

Potom vytvořte datovou část požadavku pomocí řeči syntézu Markup Language (SSML). Tato ukázka definuje strukturu a používá `tts` vstup jste vytvořili dříve.

>[!NOTE]
> Tento příklad používá `ZiraRUS` hlasového písma. Pro úplný seznam Microsoft poskytuje hlasy/jazyky, naleznete v tématu [jazykovou podporu](language-support.md).
> Pokud vás zajímá vytváření jedinečné, rozpoznat hlas pro hodnotu značky, přečtěte si téma [vytváření vlastního hlasového písma](how-to-customize-voice-font.md).

A konečně provede požadavek do služby. Pokud je žádost úspěšná a se vrátí stavový kód 200 kódu, odpověď řeči je zapsána do souboru časovým razítkem.

Zkopírujte tento kód do `TextToSpeech` třídy:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME',
        'cache-control': 'no-cache'
    }
    body = "<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female' name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" + self.tts + "</voice></speak>"

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) + "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) + "\nSomething went wrong. Check your subscription key and headers.\n")

```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je vytvoření instance své třídy a volání funkce.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

Je to, jste připraveni spustit převod textu na řeč ukázkovou aplikaci. Z příkazového řádku (nebo relaci Terminálové služby) přejděte do adresáře vašeho projektu a spusťte:

```console
python tts.py
```

Po zobrazení výzvy zadejte cokoli, co byste chtěli převést z převodu textu na řeč. V případě úspěšného ověření řeči soubor je umístěn ve složce vašeho projektu. Přehrávání pomocí vašeho oblíbeného přehrávač.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Ujistěte se, že zdrojový kód ukázkové aplikace, jako jsou klíče předplatného odebrání jakýchkoli důvěrných informací.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis#text-to-speech-api)

## <a name="see-also"></a>Další informace najdete v tématech

* [Vytvoření vlastního hlasového písma](how-to-customize-voice-font.md)
* [Záznam hlasu ukázky vytvořte vlastní hlasové](record-custom-voice-samples.md)
