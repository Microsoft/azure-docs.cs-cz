---
title: 'Rychlý Start: asynchronní Shrnutí pro dlouhý formát zvuku (Preview) – služba Speech'
titleSuffix: Azure Cognitive Services
description: K asynchronnímu převodu textu na řeč použijte rozhraní API pro dlouhé zvukové služby a načtěte zvukový výstup z identifikátoru URI, který poskytuje služba. Tato REST API je ideální pro poskytovatele obsahu, kteří potřebují převést textové soubory o více než 10 000 znaků nebo 50 odstavcích na syntetizované řeč.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331072"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Rychlý Start: asynchronní Shrnutí pro dlouhý formát zvuku v Pythonu (Preview)

V tomto rychlém startu použijete dlouhé zvukové rozhraní API k asynchronnímu převodu textu na řeč a načtete zvukový výstup z identifikátoru URI, který poskytuje služba. Tato REST API je ideální pro poskytovatele obsahu, kteří potřebují syntetizovat zvuk z textu delšího než 5 000 znaků (nebo delší než 10 minut). Další informace najdete v tématu [dlouhé zvukové rozhraní API](../../long-audio-api.md).

> [!NOTE]
> Asynchronní Shrnutí pro dlouhý formát zvuku se dá použít jenom s [vlastními hlasy neuronové](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* Python 2.7. x nebo 3. x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)nebo váš oblíbený textový editor.
* Předplatné Azure a klíč předplatného služby Speech. [Vytvořte účet Azure](../../get-started.md#new-resource) a [vytvořte prostředek pro rozpoznávání řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) , abyste získali klíč. Při vytváření prostředku řeči se ujistěte, že je cenová úroveň nastavená na **S0**a umístění je nastavené na [podporovanou oblast](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Potom tento fragment kódu zkopírujte do souboru s názvem `voice_synthesis_client.py`.

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

> [!NOTE]
> Pokud jste tyto moduly nepoužili, budete je muset nainstalovat před spuštěním programu. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests urllib3`.

Tyto moduly se používají k analýze argumentů, sestavení požadavku HTTP a volání dlouhého REST APIho textu na řeč.

## <a name="get-a-list-of-supported-voices"></a>Získat seznam podporovaných hlasů

Tento kód získá seznam dostupných hlasů, pomocí kterých můžete převést převod textu na řeč. Přidejte kód pro `voice_synthesis_client.py`:

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

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

### <a name="test-your-code"></a>Testování kódu

Pojďme vyzkoušet, co jste doposud provedli. V níže uvedené žádosti musíte aktualizovat několik věcí:

* Nahraďte `<your_key>` klíčem předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Položku `<region>` nahraďte oblastí, ve které byl vytvořen prostředek řeči (například `eastus` nebo `westus`). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).

Spusťte tento příkaz:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Zobrazí se výstup, který vypadá nějak takto:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Příprava vstupních souborů

Připravte vstupní textový soubor. Může to být prostý text nebo SSML text. Požadavky na vstupní soubor najdete v tématu [Příprava obsahu pro syntézu](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Převod textu na řeč

Po přípravě vstupního textového souboru přidejte tento kód pro syntézu řeči do `voice_synthesis_client.py`:

> [!NOTE]
> ' concatenateResult ' je volitelný parametr. Pokud tento parametr není nastaven, budou zvukové výstupy vygenerovány podle odstavce. Můžete také zřetězit zvuky do 1 výstupu nastavením parametru. Ve výchozím nastavení je zvukový výstup nastavený na RIFF-16khz-16bitový-mono-PCM. Další informace o podporovaných výstupech zvuku naleznete v tématu [formáty zvukového výstupu](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

### <a name="test-your-code"></a>Testování kódu

Pojďme vytvořit žádost o syntetizování textu pomocí vstupního souboru jako zdroje. V níže uvedené žádosti musíte aktualizovat několik věcí:

* Nahraďte `<your_key>` klíčem předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Položku `<region>` nahraďte oblastí, ve které byl vytvořen prostředek řeči (například `eastus` nebo `westus`). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<input>` cestou k textovému souboru, který jste připravili pro převod textu na řeč.
* Nahraďte `<locale>` požadovaným národním prostředím výstupu. Další informace najdete v tématu [Podpora jazyků](../../language-support.md#neural-voices).
* Nahraďte `<voice_guid>` požadovaným výstupem hlasu. Použijte jeden z hlasů vrácených funkcí [získat seznam podporovaných hlasů](#get-a-list-of-supported-voices).

Převod textu na řeč pomocí tohoto příkazu:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Pokud máte více než 1 vstupních souborů, budete muset odeslat více požadavků. Existují určitá omezení, která je potřeba znát. 
> * Klient může pro každý účet předplatného Azure odeslat až **5** požadavků na server za sekundu. Pokud překročí omezení, klient obdrží kód chyby 429 (příliš mnoho požadavků). Snižte prosím částku žádosti za sekundu.
> * Server může spouštět a zařadit do fronty až **120** požadavků pro každý účet předplatného Azure. Pokud se překročí omezení, server vrátí kód chyby 429 (příliš mnoho požadavků). Počkejte prosím a zabraňte odeslání nové žádosti, dokud nebudou dokončeny některé žádosti.

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

## <a name="remove-previous-requests"></a>Odebrat předchozí požadavky

Server bude pro každý účet předplatného Azure uchovávat až **20 000** požadavků. Pokud vaše žádost překračuje toto omezení, odeberte prosím předchozí požadavky, než začnete vytvářet nové. Pokud neodeberete stávající žádosti, obdržíte oznámení o chybě.

Přidejte kód pro `voice_synthesis_client.py`:

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

### <a name="test-your-code"></a>Testování kódu

Teď zkontrolujeme, jaké požadavky jste předtím odeslali. Než budete pokračovat, budete muset v této žádosti aktualizovat několik věcí:

* Nahraďte `<your_key>` klíčem předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Položku `<region>` nahraďte oblastí, ve které byl vytvořen prostředek řeči (například `eastus` nebo `westus`). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).

Spusťte tento příkaz:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Tím se vrátí seznam žádostí o shrnutí, které jste udělali. Zobrazí se výstup podobný tomuto:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Teď pojďme odebrat dřív odeslaný požadavek. V následujícím kódu budete muset aktualizovat několik věcí:

* Nahraďte `<your_key>` klíčem předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Položku `<region>` nahraďte oblastí, ve které byl vytvořen prostředek řeči (například `eastus` nebo `westus`). Tyto informace jsou k dispozici na kartě **Přehled** prostředku v [Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<synthesis_id>` hodnotou vrácenou v předchozí žádosti.

> [!NOTE]
> Žádosti se stavem "spuštěno"/"Wait" nelze odebrat ani odstranit.

Spusťte tento příkaz:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Zobrazí se výstup podobný tomuto:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Získat úplného klienta

Dokončenou `voice_synthesis_client.py` je k dispozici ke stažení na [GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o rozhraní API pro dlouhé zvukové rozhraní](../../long-audio-api.md)
