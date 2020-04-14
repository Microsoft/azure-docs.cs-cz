---
title: Převod převodu převodu textu na řeč, Python - služba řeči
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak převést převod převodu převodu převodu textu na řeč pomocí Pythonu a rozhraní REST API pro převod textu na řeč. Ukázkový text obsažený v této příručce je strukturován jako jazyk s označením syntézy řeči (SSML). To vám umožní zvolit hlas a jazyk odpovědi na řeč.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: how-to
ms.date: 04/13/2020
ms.author: dapine
ms.openlocfilehash: 6ea3f63bea0701dcd0f4b38f6584816d9a1bfbc7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81258424"
---
# <a name="convert-text-to-speech-using-python"></a>Převod převodu převodu textu na řeč pomocí Pythonu

V tomto článku se dozvíte, jak převést převod převodu převodu převodu textu na řeč pomocí Pythonu a rozhraní REST API pro převod textu na řeč. Tělo požadavku v této příručce je strukturováno jako [jazyk s poznámkami pro syntézu řeči (SSML),](speech-synthesis-markup.md)který umožňuje zvolit hlas a jazyk odpovědi.

Tento článek vyžaduje [účet Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s prostředkem služby Řeči. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](get-started.md).

## <a name="prerequisites"></a>Požadavky

* Python 2.7.x nebo 3.x
* <a href="https://visualstudio.microsoft.com/downloads/" target="_blank">Visual <span class="docon docon-navigate-external x-hidden-focus"> </span>Studio </a>, <a href="https://code.visualstudio.com/download" target="_blank">Visual Studio Code <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>nebo váš oblíbený textový editor
* Klíč předplatného Azure pro službu Řeč

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

Tyto moduly se používají k zápisu odpovědi řeči do souboru s časovým razítkem, vytvoření požadavku HTTP a volání rozhraní API pro převod textu na řeč.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Nastavení klíče předplatného a vytvoření výzvy pro tts

V několika následujících částech vytvoříte metody pro zpracování autorizace, volání rozhraní API pro převod textu na řeč a ověření odpovědi. Začněme přidáním nějakého kódu, který zajistí, že tato ukázka bude fungovat s Pythonem 2.7.x a 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Dále vytvoříme třídu. Toto je místo, kde budeme umístit naše metody pro výměnu tokenů a volání rozhraní API pro převod textu na řeč.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

Je `subscription_key` váš jedinečný klíč z portálu Azure. `tts`uživatele vyzve k zadání textu, který bude převeden na řeč. Tento vstup je literál řetězce, takže znaky není nutné uvozevat. Nakonec `timestr` získá aktuální čas, který použijeme k pojmenování souboru.

## <a name="get-an-access-token"></a>Získání přístupového tokenu

Rozhraní REST API pro převod textu na řeč vyžaduje přístupový token pro ověřování. Chcete-li získat přístupový token, je vyžadována výměna. Tato ukázka výměny klíče předplatného služby `issueToken` Speech pro přístupový token pomocí koncového bodu.

Tato ukázka předpokládá, že vaše předplatné služby Řeči je v oblasti USA – západ. Pokud používáte jinou oblast, aktualizujte `fetch_token_url`hodnotu pro aplikaci . Úplný seznam naleznete v tématu [Oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Zkopírujte tento `TextToSpeech` kód do třídy:

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

Zde budete stavět žádost a uložit odpověď na řeč. Nejprve je třeba `base_url` nastavit `path`a . Tato ukázka předpokládá, že používáte koncový bod V USA . Pokud je prostředek zaregistrován v jiné oblasti, `base_url`nezapomeňte aktualizovat . Další informace naleznete v tématu [Oblasti služby Rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Dále je třeba přidat požadované hlavičky pro požadavek. Ujistěte se, `User-Agent` že aktualizujete s názvem vašeho prostředku (umístěného na webu Azure Portal) a nastavte `X-Microsoft-OutputFormat` na upřednostňovaný zvukový výstup. Úplný seznam výstupních formátů naleznete [v tématu Zvukové výstupy](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Potom vytvořte tělo požadavku pomocí jazyka značek syntézy řeči (SSML). Tato ukázka definuje strukturu a `tts` používá vstup, který jste vytvořili dříve.

>[!NOTE]
> Tato ukázka `Guy24kRUS` používá hlasové písmo. Úplný seznam hlasů/jazyků poskytovaných společností Microsoft naleznete v [tématu Language support](language-support.md).
> Pokud máte zájem o vytvoření jedinečného, rozpoznatelného hlasu pro vaši značku, přečtěte si viz [Vytvoření vlastních hlasových písem](how-to-customize-voice-font.md).

Nakonec požádáte o službu. Pokud je požadavek úspěšný a je vrácen stavový kód 200, odpověď na řeč je zapsána do souboru s časovým razítkem.

Zkopírujte tento `TextToSpeech` kód do třídy:

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

To je ono, jste připraveni spustit ukázkovou aplikaci pro převod textu na řeč. Z příkazového řádku (nebo terminálové relace) přejděte do adresáře projektu a spusťte:

```console
python tts.py
```

Po zobrazení výzvy zadejte vše, co chcete převést z převodu z převodu na řeč. V případě úspěchu je soubor řeči umístěn ve složce projektu. Přehrajte si ji pomocí svého oblíbeného přehrávače médií.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Nezapomeňte odebrat všechny důvěrné informace ze zdrojového kódu ukázkové aplikace, jako jsou klíče předplatného.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte ukázky Pythonu na GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Viz také

* [Referenční informace k rozhraní API pro převod textu na řeč](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Převod převodu převodu převodu převodu převodu textu na řeč pomocí sady Python a sady Speech SDK](quickstarts/speech-to-text-from-microphone.md)
* [Vytváření vlastních hlasových písem](how-to-customize-voice-font.md)
* [Záznam hlasových vzorků pro vytvoření vlastního hlasu](record-custom-voice-samples.md)
