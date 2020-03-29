---
title: 'Úvodní příručka: Asynchronní syntéza pro zvuk s dlouhou formou (náhled) - služba řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí rozhraní LONG Audio API můžete asynchronně převést text na řeč a načíst zvukový výstup z identifikátoru URI poskytovaného službou. Toto rozhraní REST API je ideální pro poskytovatele obsahu, kteří potřebují převést textové soubory větší než 10 000 znaků nebo 50 odstavců na syntetizované řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: d3cd330001bcf53e7bd4fb9e6955c76a9ef20511
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331072"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>Úvodní příručka: Asynchronní syntéza pro zvuk s dlouhou formou v Pythonu (preview)

V tomto rychlém startu použijete rozhraní LONG Audio API k asynchronnímu převodu textu na řeč a načtení zvukového výstupu z identifikátoru URI poskytovaného službou. Toto rozhraní REST API je ideální pro poskytovatele obsahu, kteří potřebují syntetizovat zvuk z textu větší ho 5 000 znaků (nebo více než 10 minut na délku). Další informace naleznete v [tématu Dlouhé zvukové rozhraní API](../../long-audio-api.md).

> [!NOTE]
> Asynchronní syntézu pro zvuk s dlouhou formou lze použít pouze s [vlastními neurálními hlasy](../../how-to-custom-voice.md#custom-neural-voices).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* Python 2.7.x nebo 3.x.
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download)nebo váš oblíbený textový editor.
* Předplatné Azure a klíč předplatného služby Speech. [Vytvořte účet Azure](../../get-started.md#new-resource) a [vytvořte prostředek řeči,](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-resource) abyste získali klíč. Při vytváření prostředku řeči zkontrolujte, zda je cenová úroveň nastavena na **S0**a umístění je nastaveno na [podporovanou oblast](../../regions.md#standard-and-neural-voices).

## <a name="create-a-project-and-import-required-modules"></a>Vytvoření projektu a import požadovaných modulů

Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru. Potom zkopírujte tento fragment kódu do `voice_synthesis_client.py`souboru s názvem .

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
> Pokud jste tyto moduly nepoužili, budete je muset před spuštěním programu nainstalovat. Tyto balíčky nainstalujete spuštěním příkazu `pip install requests urllib3`.

Tyto moduly se používají k analýzě argumentů, konstrukci požadavku HTTP a volání rozhraní REST API pro převod textu na řeč.

## <a name="get-a-list-of-supported-voices"></a>Získání seznamu podporovaných hlasů

Tento kód získá seznam dostupných hlasů, které můžete použít k převodu převodu převodu textu na řeč. Přidejte kód `voice_synthesis_client.py`do :

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

Pojďme otestovat, co jsi zatím udělal. V následující žádosti budete muset aktualizovat několik věcí:

* Nahraďte `<your_key>` pomocí klíče předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které byl váš `eastus` `westus`prostředek řeči vytvořen (například: nebo ). Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).

Spusťte tento příkaz:

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

Zobrazí se výstup, který vypadá takto:

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="prepare-input-files"></a>Příprava vstupních souborů

Připravte vstupní textový soubor. Může to být buď prostý text nebo text SSML. Požadavky na vstupní soubor naleznete v tématu [jak připravit obsah pro syntézu](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#prepare-content-for-synthesis).

## <a name="convert-text-to-speech"></a>Převod textu na řeč

Po přípravě vstupního textového souboru `voice_synthesis_client.py`přidejte tento kód pro syntézu řeči do aplikace :

> [!NOTE]
> 'concatenateResult' je volitelný parametr. Pokud tento parametr není nastaven, zvukové výstupy budou generovány na odstavec. Můžete také zřetězit zvuky do 1 výstupu nastavením parametru. Ve výchozím nastavení je zvukový výstup nastaven na riff-16khz-16bit-mono-pcm. Další informace o podporovaných zvukových výstupech naleznete [v tématu Formáty zvukového výstupu](https://docs.microsoft.com/azure/cognitive-services/speech-service/long-audio-api#audio-output-formats).

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

Pojďme požádat o syntézu textu pomocí vstupního souboru jako zdroje. V následující žádosti budete muset aktualizovat několik věcí:

* Nahraďte `<your_key>` pomocí klíče předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které byl váš `eastus` `westus`prostředek řeči vytvořen (například: nebo ). Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<input>` cestou k textovému souboru, který jste připravili pro převod textu na řeč.
* Nahraďte `<locale>` požadované výstupní národní prostředí. Další informace naleznete v [tématu podpora jazyků](../../language-support.md#neural-voices).
* Nahraďte `<voice_guid>` požadovaným výstupním hlasem. Použijte jeden z hlasů vrácených [získat seznam podporovaných hlasů](#get-a-list-of-supported-voices).

Převést text na řeč pomocí tohoto příkazu:

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> Pokud máte více než 1 vstupní soubory, budete muset odeslat více žádostí. Existují určitá omezení, která je třeba si uvědomit. 
> * Klient může odesílat až **5** požadavků na server za sekundu pro každý účet předplatného Azure. Pokud překročí omezení, klient získá kód chyby 429 (příliš mnoho požadavků). Prosím, snižte částku žádosti za sekundu
> * Server může spouštět a zařadit do fronty až **120** požadavků pro každý účet předplatného Azure. Pokud překročí omezení, server vrátí kód chyby 429 (příliš mnoho požadavků). Počkejte a vyhněte se odesílání nové žádosti, dokud nebudou některé požadavky dokončeny.

Zobrazí se výstup, který vypadá takto:

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

Výsledek obsahuje vstupní text a zvukové výstupní soubory, které jsou generovány službou. Tyto soubory si můžete stáhnout v zip.

## <a name="remove-previous-requests"></a>Odebrání předchozích požadavků

Server bude mít až **20 000** požadavků pro každý účet předplatného Azure. Pokud částka žádosti toto omezení překročí, odstraňte předchozí požadavky před vytvořením nových požadavků. Pokud neodeberete existující požadavky, zobrazí se oznámení o chybě.

Přidejte kód `voice_synthesis_client.py`do :

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

Nyní se podíváme, jaké žádosti jste dříve odeslali. Než budete pokračovat, budete muset v tomto požadavku aktualizovat několik věcí:

* Nahraďte `<your_key>` pomocí klíče předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které byl váš `eastus` `westus`prostředek řeči vytvořen (například: nebo ). Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).

Spusťte tento příkaz:

```console
python voice_synthesis_client.py --syntheses -key <your_key> -region <Region>
```

Tím se vrátí seznam požadavků na syntézu, které jste provedli. Zobrazí se výstup, jako je tento:

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

Nyní odebereme dříve odeslanou žádost. V níže uvedeném kódu budete muset aktualizovat několik věcí:

* Nahraďte `<your_key>` pomocí klíče předplatného služby Speech. Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<region>` oblastí, ve které byl váš `eastus` `westus`prostředek řeči vytvořen (například: nebo ). Tyto informace jsou k dispozici na kartě **Přehled** pro váš prostředek na [webu Azure Portal](https://aka.ms/azureportal).
* Nahraďte `<synthesis_id>` hodnotou vrácenou v předchozím požadavku.

> [!NOTE]
> Požadavky se stavem "Spuštěno" /"Čekání" nelze odebrat ani odstranit.

Spusťte tento příkaz:

```console
python voice_synthesis_client.py --delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

Zobrazí se výstup, jako je tento:

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>Získejte úplného klienta

Dokončeno `voice_synthesis_client.py` je k dispozici ke stažení na [GitHubu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o rozhraní LONG Audio API](../../long-audio-api.md)
