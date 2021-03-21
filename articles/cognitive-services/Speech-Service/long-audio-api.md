---
title: Rozhraní API pro dlouhé zvukové služby – Speech
titleSuffix: Azure Cognitive Services
description: Naučte se, jak dlouhé zvukové rozhraní API je navržené pro asynchronní syntézu dlouhého textu na řeč.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102618402"
---
# <a name="long-audio-api"></a>Rozhraní API pro dlouhé zvukové zařízení

Rozhraní API pro dlouhé zvukové soubory je navržené pro asynchronní syntézu dlouhého textu na řeč (například zvukové knihy, články s novinkami a dokumenty). Toto rozhraní API nevrátí syntetizované zvuky v reálném čase, místo toho, abyste se mohli dotazovat na odpovědi a využívat výstupy, když jsou zpřístupněné ze služby. Na rozdíl od textu k rozhraní Speech API používaného sadou Speech SDK může dlouhé zvukové rozhraní API vytvářet syntetizované zvuky delší než 10 minut. díky tomu je ideální pro vydavatele a platformy zvukového obsahu a vytvořit tak dlouhý zvukový obsah, jako jsou zvukové knihy v dávce.

Další výhody pro dlouhé zvukové rozhraní API:

* Syntetizované rozpoznávání řeči vrácené službou používá nejlepší hlasy neuronové.
* Není potřeba nasazovat hlasový koncový bod, protože v něm nejsou žádné hlasy v režimu dávek v reálném čase.

> [!NOTE]
> Rozhraní API pro dlouhé zvukové rozhraní teď podporuje [neuronové hlasy](./language-support.md#neural-voices) a [vlastní neuronové hlasy](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Pracovní postup

Při použití rozhraní API dlouhého zvuku se obvykle odesílá textový soubor nebo soubory, které se mají syntetizovat, dotaz na jeho stav, a pokud je stav úspěšný, můžete si stáhnout zvukový výstup.

Tento diagram poskytuje přehled o pracovním postupu na nejvyšší úrovni.

![Diagram pracovního postupu dlouhého zvukového rozhraní API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Příprava obsahu pro syntézu

Při přípravě textového souboru se ujistěte, že:

* Je buď prostý text (. txt), nebo SSML text (. txt)
* Je kódovaný jako [UTF-8 s označením pořadí bajtů (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) .
* Je jeden soubor, ne zip.
* Obsahuje více než 400 znaků pro prostý text nebo 400 [fakturovatelných znaků](./text-to-speech.md#pricing-note) pro text SSML a méně než 10 000 odstavců.
  * U prostého textu je každý odstavec oddělený příkladem **zadání a návratového** [vstupu v prostém textu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) .
  * V případě textu SSML se každý SSMLový kus považuje za odstavec. SSML části musí být oddělené různými odstavci – [Příklad textového vstupu SSML textu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)

## <a name="sample-code"></a>Ukázka kódu
Zbývající část této stránky se soustředí na Python, ale vzorový kód pro rozhraní API pro dlouhé zvukové rozhraní je k dispozici na GitHubu pro následující programovací jazyky:

* [Vzorový kód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Vzorový kód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Vzorový kód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Příklad Pythonu

Tato část obsahuje příklady Pythonu, které znázorňují základní využití dlouhého zvukového rozhraní API. Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Potom tento fragment kódu zkopírujte do souboru s názvem `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Tyto knihovny se používají k vytvoření požadavku HTTP a volání REST API pro převod textu na řeč dlouhého zvuku.

### <a name="get-a-list-of-supported-voices"></a>Získat seznam podporovaných hlasů

Chcete-li získat seznam podporovaných hlasů, odešlete požadavek GET na `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Tento kód vám umožní získat úplný seznam hlasů pro konkrétní oblast nebo koncový bod, které můžete použít.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Nahraďte následující hodnoty:

* Nahraďte `<your_key>` klíčovým předplatným služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které se vytvořil prostředek řeči (například `eastus` nebo `westus` ). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).

Zobrazí se výstup, který vypadá nějak takto:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Pokud má **vlastnost. publicAvailable** **hodnotu true**, hlas je veřejný neuronové hlas. V opačném případě se jedná o vlastní neuronové hlas.

### <a name="convert-text-to-speech"></a>Převod textu na řeč

Připravte textový soubor, buď prostý text nebo SSML text, a přidejte následující kód do `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` je volitelný parametr. Pokud tento parametr není nastaven, budou zvukové výstupy vygenerovány podle odstavce. Můžete také zřetězit zvuky do 1 výstupu nastavením parametru. 
> `outputFormat` je také volitelné. Ve výchozím nastavení je zvukový výstup nastavený na RIFF-16khz-16bitový-mono-PCM. Další informace o podporovaných formátech zvukového výstupu najdete v tématu [formáty zvukového výstupu](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Nahraďte následující hodnoty:

* Nahraďte `<your_key>` klíčovým předplatným služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které se vytvořil prostředek řeči (například `eastus` nebo `westus` ). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<input_file_path>` cestou k textovému souboru, který jste připravili pro převod textu na řeč.
* Nahraďte `<locale>` požadovaným národním prostředím výstupu. Další informace najdete v tématu [Podpora jazyků](language-support.md#neural-voices).

Použijte jeden z hlasů vráceného předchozím voláním `/voices` koncového bodu.

* Pokud používáte veřejný neuronové hlas, nahraďte `<voice_name>` požadovaným výstupním hlasem.
* Pokud chcete použít vlastní neuronové hlas, nahraďte `voice_identities` proměnnou následujícím a nahraďte `<voice_id>` pomocí `id` vlastního hlasu neuronové.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Zobrazí se výstup, který vypadá nějak takto:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Pokud máte více než 1 vstupních souborů, budete muset odeslat více požadavků. Existují určitá omezení, která je potřeba znát.
> * Klient může pro každý účet předplatného Azure odeslat až **5** požadavků na server za sekundu. Pokud překročí omezení, klient obdrží kód chyby 429 (příliš mnoho požadavků). Snižte prosím částku žádosti za sekundu.
> * Server může spouštět a zařadit do fronty až **120** požadavků pro každý účet předplatného Azure. Pokud se překročí omezení, server vrátí kód chyby 429 (příliš mnoho požadavků). Počkejte prosím a zabraňte odeslání nové žádosti, dokud nebudou dokončeny některé žádosti.

Adresu URL ve výstupu lze použít k získání stavu žádosti.

### <a name="get-information-of-a-submitted-request"></a>Získání informací o odeslané žádosti

Chcete-li získat stav odeslaného požadavku na shrnutí, jednoduše odešlete požadavek GET na adresu URL vrácenou předchozím krokem.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
Výstup bude vypadat takto:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Z `status` vlastnosti můžete číst stav této žádosti. Požadavek se spustí ze `NotStarted` stavu a pak se změní na `Running` a nakonec se změní na `Succeeded` nebo `Failed` . K cyklickému dotazování tohoto rozhraní API můžete použít smyčku, dokud se stav nevrátí `Succeeded` .

### <a name="download-audio-result"></a>Stáhnout výsledek zvuku

Po úspěšném provedení žádosti o Shrnutí si můžete stáhnout výsledek zvuku voláním GET `/files` API.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Nahraďte `<request_id>` ID žádosti, která má výsledek stáhnout. Dá se najít v odpovědi na předchozí krok.

Výstup bude vypadat takto:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
Výstup obsahuje informace o 2 souborech. V rámci `"kind": "LongAudioSynthesisScript"` je vstupní skript odeslán. Druhá `"kind": "LongAudioSynthesisResult"` je výsledkem této žádosti.
Výsledkem je zip, který obsahuje vygenerované zvukové výstupní soubory spolu s kopií vstupního textu.

Oba soubory lze stáhnout z adresy URL ve své `links.contentUrl` Vlastnosti.

### <a name="get-all-synthesis-requests"></a>Získat všechny žádosti o Shrnutí

Seznam všech odeslaných žádostí můžete získat pomocí následujícího kódu:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

Výstup bude vypadat takto:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` vlastnost obsahuje seznam žádostí o Shrnutí. Seznam má stránkování s maximální velikostí stránky 100. Pokud je k dispozici více než 100 požadavků, `"@nextLink"` bude k dispozici vlastnost pro získání další stránky stránkovaného seznamu.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Můžete také přizpůsobit velikost stránky a přeskočit číslo zadáním `skip` a `top` v parametru URL.

### <a name="remove-previous-requests"></a>Odebrat předchozí požadavky

Služba bude pro každý účet předplatného Azure uchovávat až **20 000** požadavků. Pokud vaše žádost překračuje toto omezení, odeberte prosím předchozí požadavky, než začnete vytvářet nové. Pokud neodeberete stávající žádosti, obdržíte oznámení o chybě.

Následující kód ukazuje, jak odebrat konkrétní požadavek syntézy.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Pokud je žádost úspěšně odebrána, bude kód stavu odpovědi HTTP 204 (žádný obsah).

```console
response.status_code: 204
```

> [!NOTE]
> Žádosti se stavem `NotStarted` nebo `Running` nejde odebrat ani odstranit.

Dokončeno `long_audio_synthesis_client.py` je k dispozici na [GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Stavové kódy HTTP

Následující tabulka podrobně popisuje kódy a zprávy odpovědi HTTP z REST API.

| Rozhraní API | Stavový kód HTTP | Popis | Řešení |
|-----|------------------|-------------|----------|
| Vytvořit | 400 | Funkce Voice syntézy není v této oblasti povolena. | Změňte klíč předplatného řeči s podporovanou oblastí. |
|        | 400 | Platný je jenom **standardní** odběr řeči pro tuto oblast. | Změňte klíč předplatného řeči na cenovou úroveň Standard. |
|        | 400 | Překročil limit počtu požadavků 20 000 pro účet Azure. Před odesláním nových žádostí prosím odeberte nějaké žádosti. | Server bude pro každý účet Azure uchovávat až 20 000 požadavků. Před odesláním nových požadavků odstraňte nějaké žádosti. |
|        | 400 | Tento model se nedá použít ve shrnutí hlasu: {modelID}. | Ujistěte se, že stav {modelID} je správný. |
|        | 400 | Oblast pro požadavek se neshoduje s oblastí pro model: {modelID}. | Ujistěte se, že se oblast {modelID} shoduje s oblastí žádosti. |
|        | 400 | Funkce Voice syntézy podporuje pouze textový soubor v kódování UTF-8 se značkou pořadí bajtů. | Ujistěte se, že vstupní soubory jsou v kódování UTF-8 se značkou pořadí bajtů. |
|        | 400 | V požadavku na Shrnutí hlasu jsou povolené jenom platné vstupy SSML. | Ujistěte se, že vstupní výrazy SSML jsou správné. |
|        | 400 | Hlasový název {Voice} nebyl ve vstupním souboru nalezen. | Vstupní název vstupu SSML není zarovnán s identifikátorem ID modelu. |
|        | 400 | Počet odstavců ve vstupním souboru by měl být menší než 10 000. | Ujistěte se, že počet odstavců v souboru je menší než 10 000. |
|        | 400 | Vstupní soubor by měl být delší než 400 znaků. | Ujistěte se, že vstupní soubor překračuje 400 znaků. |
|        | 404 | Model deklarovaný v definici hlasové syntézy nejde najít: {modelID}. | Ujistěte se, že je {modelID} správné. |
|        | 429 | Překročil aktivní limit pro syntézu hlasu. Počkejte prosím, než se dokončí některé požadavky. | Server může spouštět a zařadit do fronty až 120 požadavků pro každý účet Azure. Počkejte prosím a vyhněte se odesílání nových požadavků do doby, než se dokončí některé žádosti. |
| Vše       | 429 | Existuje příliš mnoho požadavků. | Klient může pro každý účet Azure odeslat až 5 požadavků na server za sekundu. Snižte prosím částku žádosti za sekundu. |
| Odstranit    | 400 | Úkol syntézy hlasu se pořád používá. | Je možné odstranit pouze **dokončené** nebo **neúspěšné** požadavky. |
| GetByID   | 404 | Zadanou entitu nelze nalézt. | Ujistěte se, že je ID syntézy správné. |

## <a name="regions-and-endpoints"></a>Oblasti a koncové body

Rozhraní API pro dlouhé zvukové rozhraní je k dispozici ve více oblastech s jedinečnými koncovými body.

| Oblast | Koncový bod |
|--------|----------|
| East US | `https://eastus.customvoice.api.speech.microsoft.com` |
| Indie – střed | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Southeast Asia | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Spojené království – jih | `https://uksouth.customvoice.api.speech.microsoft.com` |
| West Europe | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formáty zvukového výstupu

Podporujeme flexibilní formáty zvukového výstupu. Nastavením parametru ' concatenateResult ' můžete vygenerovat zvukové výstupy na jeden odstavec nebo zřetězit výstup zvuku do jednoho výstupu. Rozhraní API pro dlouhé zvukové rozhraní podporuje následující formáty zvukového výstupu:

> [!NOTE]
> Výchozí formát zvuku je RIFF-16khz-16bitový-mono-PCM.

* RIFF-8KHz-16bitový-mono-PCM
* RIFF-16khz-16bitový-mono-PCM
* RIFF-24khz-16bitový-mono-PCM
* RIFF-48kHz-16bitový-mono-PCM
* Audio-16khz-32kbitrate-mono-MP3
* Audio-16khz-64kbitrate-mono-MP3
* Audio-16khz-128kbitrate-mono-MP3
* Audio-24khz-48kbitrate-mono-MP3
* Audio-24khz-96kbitrate-mono-MP3
* Audio-24khz-160kbitrate-mono-MP3
