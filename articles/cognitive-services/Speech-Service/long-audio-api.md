---
title: Dlouhé zvukové rozhraní API (Preview) – služba Speech
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
ms.openlocfilehash: 255cfe11f8601abc89a1d96f702f453c2af1ccbd
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533056"
---
# <a name="long-audio-api-preview"></a>Dlouhé zvukové rozhraní API (Preview)

Rozhraní API pro dlouhé zvukové soubory je navržené pro asynchronní syntézu dlouhého textu na řeč (například zvukové knihy, články s novinkami a dokumenty). Toto rozhraní API nevrátí syntetizované zvuky v reálném čase, místo toho, abyste se mohli dotazovat na odpovědi a využívat výstupy, když jsou zpřístupněné ze služby. Na rozdíl od rozhraní API pro rozpoznávání řeči používaného sadou Speech SDK může dlouhé zvukové rozhraní API vytvářet syntetizované zvuky delší než 10 minut, což je ideální pro vydavatele a platformy zvukového obsahu.

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
> [!NOTE]
> Pro čínštinu (kontinentální), čínština (Hongkong – zvláštní administrativní oblast), čínština (Tchaj-wan), japonština a korejština, se jedno slovo bude počítat jako dva znaky. 

## <a name="python-example"></a>Příklad Pythonu

Tato část obsahuje příklady Pythonu, které znázorňují základní využití dlouhého zvukového rozhraní API. Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Potom tento fragment kódu zkopírujte do souboru s názvem `voice_synthesis_client.py` .

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

Tyto knihovny se používají k analýze argumentů, sestavení požadavku HTTP a volání dlouhého REST APIho textu na řeč.

### <a name="get-a-list-of-supported-voices"></a>Získat seznam podporovaných hlasů

Tento kód vám umožní získat úplný seznam hlasů pro konkrétní oblast nebo koncový bod, které můžete použít. Přidejte kód do `voice_synthesis_client.py` :

```python
parser = argparse.ArgumentParser(description='Text-to-speech client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the speech subscription key, like fg1f763i01d94768bda32u7a******** ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

Spusťte skript pomocí příkazu `python voice_synthesis_client.py --voices -key <your_key> -region <region>` a nahraďte následující hodnoty:

* Nahraďte `<your_key>` klíčovým předplatným služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které se vytvořil prostředek řeči (například `eastus` nebo `westus` ). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).

Zobrazí se výstup, který vypadá nějak takto:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

Pokud **PublicVoice** má parametr PublicVoice **hodnotu true**, hlas je Public neuronové Voice. V opačném případě se jedná o vlastní neuronové hlas.

### <a name="convert-text-to-speech"></a>Převod textu na řeč

Připravte textový soubor, buď prostý text nebo SSML text, a přidejte následující kód do `voice_synthesis_client.py` :

> [!NOTE]
> ' concatenateResult ' je volitelný parametr. Pokud tento parametr není nastaven, budou zvukové výstupy vygenerovány podle odstavce. Můžete také zřetězit zvuky do 1 výstupu nastavením parametru. Ve výchozím nastavení je zvukový výstup nastavený na RIFF-16khz-16bitový-mono-PCM. Další informace o podporovaných výstupech zvuku naleznete v tématu [formáty zvukového výstupu](#audio-output-formats).

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

Spusťte skript pomocí příkazu `python voice_synthesis_client.py --submit -key <your_key> -region <region> -file <input> -locale <locale> -voiceId <voice_guid>` a nahraďte následující hodnoty:

* Nahraďte `<your_key>` klíčovým předplatným služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které se vytvořil prostředek řeči (například `eastus` nebo `westus` ). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<input>` cestou k textovému souboru, který jste připravili pro převod textu na řeč.
* Nahraďte `<locale>` požadovaným národním prostředím výstupu. Další informace najdete v tématu [Podpora jazyků](language-support.md#neural-voices).
* Nahraďte `<voice_guid>` požadovaným výstupním hlasem. Použijte jeden z hlasů vráceného předchozím voláním `/voicesynthesis/voices` koncového bodu.

Zobrazí se výstup, který vypadá nějak takto:

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

Výsledek obsahuje vstupní text a zvukové výstupní soubory, které jsou vygenerovány službou. Tyto soubory můžete stáhnout do souboru ZIP.

> [!NOTE]
> Pokud máte více než 1 vstupních souborů, budete muset odeslat více požadavků. Existují určitá omezení, která je potřeba znát. 
> * Klient může pro každý účet předplatného Azure odeslat až **5** požadavků na server za sekundu. Pokud překročí omezení, klient obdrží kód chyby 429 (příliš mnoho požadavků). Snižte prosím částku žádosti za sekundu.
> * Server může spouštět a zařadit do fronty až **120** požadavků pro každý účet předplatného Azure. Pokud se překročí omezení, server vrátí kód chyby 429 (příliš mnoho požadavků). Počkejte prosím a zabraňte odeslání nové žádosti, dokud nebudou dokončeny některé žádosti.

### <a name="remove-previous-requests"></a>Odebrat předchozí požadavky

Služba bude pro každý účet předplatného Azure uchovávat až **20 000** požadavků. Pokud vaše žádost překračuje toto omezení, odeberte prosím předchozí požadavky, než začnete vytvářet nové. Pokud neodeberete stávající žádosti, obdržíte oznámení o chybě.

Do souboru `voice_synthesis_client.py` přidejte následující kód:

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

Spusťte `python voice_synthesis_client.py --syntheses -key <your_key> -region <region>` , abyste získali seznam žádostí o shrnutí, které jste udělali. Zobrazí se výstup podobný tomuto:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Pokud chcete žádost odstranit, spusťte `python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>` a nahraďte `<synthesis_id>` hodnotou ID žádosti vrácenou z předchozího požadavku.

> [!NOTE]
> Žádosti se stavem "spuštěno"/"Wait" nelze odebrat ani odstranit.

Dokončeno `voice_synthesis_client.py` je k dispozici na [GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

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

## <a name="sample-code"></a>Ukázka kódu
Vzorový kód pro dlouhé zvukové rozhraní API je k dispozici na GitHubu.

* [Vzorový kód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Vzorový kód: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Vzorový kód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)