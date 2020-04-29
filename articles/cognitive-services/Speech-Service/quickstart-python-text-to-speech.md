---
title: Převod textu na řeč, Python-Speech Service
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak převést převod textu na řeč pomocí Pythonu a REST API převodu textu na řeč. Vzorový text obsažený v této příručce je strukturovaný jako SSML (Speech syntézy Language). To vám umožní zvolit hlas a jazyk odpovědi na řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: trbye
ms.openlocfilehash: 171fdb033cba422d8ba580da3ab54db88ca20872
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400822"
---
# <a name="convert-text-to-speech-using-python"></a>Převod textu na řeč pomocí Pythonu

V tomto článku se dozvíte, jak převést převod textu na řeč pomocí Pythonu a REST API převodu textu na řeč. Text žádosti v této příručce je strukturovaný jako [SSML (Speech syntézy Language)](speech-synthesis-markup.md), který umožňuje zvolit hlas a jazyk odpovědi.

Tento článek vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

* Python 2.7.x nebo 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual Studio <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu pro rozpoznávání řeči

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Pak do svého projektu, do souboru s názvem `tts.py`, zkopírujte tento fragment kódu.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Pokud jste tyto moduly ještě nikdy nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests`.

Tyto moduly slouží k zápisu odezvy řeči do souboru s časovým razítkem, vytvoření požadavku HTTP a volání rozhraní API pro převod textu na mluvené slovo.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavení klíče předplatného a vytvoření výzvy pro TTS

V následujících částech vytvoříte metody pro zpracování autorizace, volání rozhraní API pro převod textu na mluvené slovo a ověření odpovědi. Pojďme začít přidáním kódu, který zajistí, že bude tato ukázka fungovat s Pythonem 2.7. x a 3. x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Nyní vytvoříme třídu. Tady budeme dávat naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na mluvené slovo.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

`subscription_key` Je jedinečný klíč z Azure Portal. `tts`vyzve uživatele k zadání textu, který se bude převádět na řeč. Tento vstup je řetězcový literál, takže znaky nemusejí být uvozeny řídicími znaky. Nakonec `timestr` získá aktuální čas, který použijeme k pojmenování souboru.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

REST API převodu textu na řeč vyžaduje přístupový token pro ověřování. Pro získání přístupového tokenu se vyžaduje Exchange. Tato ukázka vyměňuje klíč předplatného služby Speech pro přístupový token pomocí `issueToken` koncového bodu.

V této ukázce se předpokládá, že vaše předplatné služby Speech je v oblasti Západní USA. Pokud používáte jinou oblast, aktualizujte hodnotu pro `fetch_token_url`. Úplný seznam najdete v tématu [oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

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

## <a name="make-a-request-and-save-the-response"></a>Vytvoření žádosti a uložení odpovědi

Tady se chystáte vytvořit žádost a Uložit odpověď na řeč. Nejdřív je potřeba nastavit `base_url` a. `path` Tato ukázka předpokládá, že používáte koncový bod Západní USA. Pokud je prostředek zaregistrován v jiné oblasti, nezapomeňte aktualizovat `base_url`. Další informace najdete v tématu [oblasti služby Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je nutné přidat požadovaná záhlaví pro požadavek. Ujistěte se, že provedete aktualizaci `User-Agent` s názvem vašeho prostředku (umístěný v Azure Portal) a nastavíte `X-Microsoft-OutputFormat` na preferovaný zvukový výstup. Úplný seznam výstupních formátů najdete v tématu [zvukové výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Pak sestavte text žádosti pomocí jazyka Speech syntéz Markup Language (SSML). Tato ukázka definuje strukturu a používá `tts` vstup, který jste vytvořili dříve.

>[!NOTE]
> Tato ukázka používá písmo `Guy24kRUS` hlasu. Úplný seznam hlasů/jazyků poskytovaných společností Microsoft najdete v tématu [Podpora jazyků](language-support.md).
> Pokud vás zajímá vytváření jedinečného a rozpoznatelného hlasu pro vaši značku, přečtěte si téma [vytváření vlastních hlasových písem](how-to-customize-voice-font.md).

Nakonec vytvoříte požadavek na službu. Pokud je požadavek úspěšný a vrátí se stavový kód 200, odpověď na řeč se zapíše do souboru s časovým razítkem.

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
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Spojení všech součástí dohromady

Už jste téměř hotovi. Posledním krokem je vytvoření instance vaší třídy a volání funkcí.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Spuštění ukázkové aplikace

To je to, že jste připraveni spustit ukázkovou aplikaci pro převod textu na řeč. Z příkazového řádku (nebo relace Terminálové služby) přejděte do adresáře projektu a spusťte příkaz:

```console
python tts.py
```

Po zobrazení výzvy zadejte cokoli, co byste chtěli převést z převodu textu na řeč. V případě úspěchu se soubor řeči nachází ve složce projektu. Přehrajte ho pomocí oblíbeného Media Playeru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte ze zdrojového kódu ukázkové aplikace odebrat všechny důvěrné informace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Převod textu na řeč pomocí Pythonu a sady Speech SDK](quickstarts/speech-to-text-from-microphone.md)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam ukázek hlasu pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
