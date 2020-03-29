---
title: Co je dávkový přepis - služba řeči
titleSuffix: Azure Cognitive Services
description: Dávkový přepis je ideální, pokud chcete přepsat velké množství zvuku v úložišti, jako jsou objekty Blobs Azure. Pomocí vyhrazeného rozhraní REST API můžete překážet na zvukové soubory pomocí identifikátoru URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat přepisy.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: ee7fbddade055c11f5870aa5a588a2fd02f10a23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131605"
---
# <a name="what-is-batch-transcription"></a>Co je přepis dávky?

Dávkový přepis je sada operací rozhraní REST API, která umožňuje přepsat velké množství zvuku v úložišti. Můžete překážet na zvukové soubory pomocí identifikátoru URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat výsledky přepisu.

Asynchronní přepis řeči na text je pouze jednou z funkcí. Pomocí dávkových možností rekripce REST API můžete volat následující metody:



|    Operace přepisu dávky                                             |    Metoda    |    Volání rozhraní API REST                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Vytvoří nový přepis.                                              |    POST      |    api/speechtotext/v2.0/přepisy            |
|    Načte seznam přepisů pro ověřené předplatné.    |    GET       |    api/speechtotext/v2.0/přepisy            |
|    Získá seznam podporovaných národních prostředí pro offline přepisy.              |    GET       |    api/speechtotext/v2.0/transkripce/národní prostředí    |
|    Aktualizuje proměnlivé podrobnosti o přepisu identifikovaném jeho ID.    |    Oprava     |    api/speechtotext/v2.0/transkripce/{id}       |
|    Odstraní zadanou úlohu přepisu.                                 |    DELETE    |    api/speechtotext/v2.0/transkripce/{id}       |
|    Získá přepis identifikován daným ID.                        |    GET       |    api/speechtotext/v2.0/transkripce/{id}       |




Podrobné rozhraní API, které je k dispozici jako [dokument Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A), můžete zkontrolovat a otestovat pod nadpisem `Custom Speech transcriptions`.

Dávkové transkripce úlohy jsou naplánovány na základě nejlepší úsilí. V současné době neexistuje žádný odhad, kdy se úloha změní do spuštěného stavu. Při normálním zatížení systému by se to mělo stát během několika minut. Jakmile je ve stavu spuštění, skutečný přepis je zpracován rychleji než zvuk v reálném čase.

Vedle snadno použitelnérozhraní API, není nutné nasadit vlastní koncové body a nemáte žádné požadavky souběžnosti sledovat.

## <a name="prerequisites"></a>Požadavky

### <a name="subscription-key"></a>Klíč předplatného

Stejně jako u všech funkcí služby Řeč vytvoříte klíč předplatného z [portálu Azure](https://portal.azure.com) podle našeho [průvodce Začínáme](get-started.md).

>[!NOTE]
> Pro použití dávkového přepisu je vyžadováno standardní předplatné (S0) pro službu Speech. Bezplatné klíče předplatného (F0) nefungují. Další informace naleznete v [tématu ceny a limity](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud plánujete přizpůsobit akustické nebo jazykové modely, postupujte podle pokynů v [části Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md) a [návrhu jazykových modelů vlastního nastavení](how-to-customize-language-model.md). Chcete-li použít vytvořené modely v dávkovém přepisu, potřebujete jejich ID modelu. ID modelu můžete načíst při kontrole podrobností modelu. Nasazený vlastní koncový bod není potřeba pro službu dávkového přepisu.

## <a name="the-batch-transcription-api"></a>Rozhraní API pro přepis dávky

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API pro batch transkripce podporuje následující formáty:

| Formát | Kodek | Datový tok | Vzorkovací frekvence                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |
| Mp3    | PCM   | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |
| Ogg    | Opus  | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |

U stereofonních zvukových proudů jsou během přepisu rozděleny levé a pravé kanály. Pro každý kanál se vytváří soubor výsledků JSON. Časová razítka vygenerovaná na utterance umožňují vývojáři vytvořit seřazený konečný přepis.

### <a name="configuration"></a>Konfigurace

Konfigurační parametry jsou k dispozici jako JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>Vlastnosti konfigurace

Pomocí těchto volitelných vlastností můžete nakonfigurovat přepis:

:::row:::
   :::column span="1":::
      **Parametr**
   :::column-end:::
   :::column span="2":::
      **Popis**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijaté hodnoty `None` jsou zakázat filtrování vulgárních výrazů, `Masked` nahradit vulgární výrazy hvězdičkami, `Removed` odstranit všechny vulgární `Tags` výrazy z výsledku nebo přidat značky "vulgárních výrazů". Výchozí hodnota je `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Určuje způsob zpracování interpunkce ve výsledcích rozpoznávání. Přijaté hodnoty `None` jsou zakázat interpunkci, `Dictated` znamenat explicitní (mluvené) interpunkce, `Automatic` nechat dekodér `DictatedAndAutomatic` vypořádat s interpunkcí, nebo používat diktované a automatické interpunkce. Výchozí hodnota je `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Určuje, zda mají být do výstupu přidána časová razítka na úrovni slova. Přijaté hodnoty `true` mají povolit časová `false` razítka na úrovni slov a (výchozí hodnota) zakázat.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Určuje, zda má být analýza mínění použita pro utterance. Přijaté hodnoty `true` jsou `false` povolit a (výchozí hodnota) zakázat.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Určuje, že diarization analýza by měla být provedena na vstupu, který se očekává, že mono kanál obsahující dva hlasy. Přijaté hodnoty `true` umožňují diarizaci a `false` (výchozí hodnota) ji zakázat. To také `AddWordLevelTimestamps` vyžaduje, aby byla nastavena na true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Volitelná adresa URL se [službou SAS](../../storage/common/storage-sas-overview.md) do zapisovatelného kontejneru v Azure. Výsledek je uložen v tomto kontejneru.
:::row-end:::

### <a name="storage"></a>Úložiště

Dávkový přepis podporuje [úložiště objektů Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pro čtení zvuku a zápis přepisů do úložiště.

## <a name="the-batch-transcription-result"></a>Výsledek přepisu dávky

Pro mono vstupní zvuk se vytváří jeden soubor výsledků transkripce. Pro stereofonní vstupní zvuk jsou vytvářeny dva soubory výsledků přepisu. Každý z nich má tuto strukturu:

```json
{
  "AudioFileResults":[
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

Výsledek obsahuje tyto formy:

| Formulář        | Obsah                                                                                                                                                  |
|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `Lexical`   | Skutečná slova rozpoznána.                                                                                                                             |
| `ITN`       | Inverzní text normalizovaná forma rozpoznaného textu. Používají se zkratky ("doctor smith" až "dr smith"), telefonní čísla a další transformace. |
| `MaskedITN` | Formulář ITN s vulgárním maskováním.                                                                                                             |
| `Display`   | Zobrazovaný formulář rozpoznaného textu. Zahrnuta jsou zahrnuta přidána interpunkce a velká písmena.                                                             |

## <a name="speaker-separation-diarization"></a>Oddělení reproduktorů (diarizace)

Diarizace je proces oddělení reproduktorů v kusu zvuku. Naše dávkové potrubí podporuje diarizaci a je schopno rozpoznat dva reproduktory na monokanálových nahrávkách. Tato funkce není k dispozici na stereofonních nahrávkách.

Veškerý výstup transkripce obsahuje `SpeakerId`. Pokud diarization není použit, `"SpeakerId": null` zobrazí se ve výstupu JSON. Pro diarizaci podporujeme dva hlasy, `"1"` takže `"2"`reproduktory jsou identifikovány jako nebo .

Chcete-li požádat o diarization, stačí přidat příslušný parametr v požadavku HTTP, jak je znázorněno níže.

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

Časová razítka na úrovni aplikace Word by také musela být zapnuta, jak naznačují parametry ve výše uvedeném požadavku.

## <a name="sentiment-analysis"></a>Analýza mínění

Funkce mínění odhaduje mínění vyjádřené ve zvuku. Sentiment je vyjádřen hodnotou mezi 0 `Negative`a `Neutral`1 `Positive` pro , a sentiment. Analýzu mínění lze například použít ve scénářích centra volání:

- Získejte přehled o spokojenosti zákazníků
- Získejte přehled o výkonu agentů (tým při volání)
- Najděte přesný okamžik, kdy se hovor otočil v negativním směru
- Co šlo dobře při přeměně negativního volání na pozitivní směr
- Zjištění, co se zákazníkům líbí a co se jim na produktu nebo službě nelíbí

Mínění se skóre skóre pro každý segment zvuku na základě lexikální formuláře. Celý text v tomto zvukovém segmentu se používá k výpočtu mínění. Pro celý přepis se nepočítá žádný souhrnný mínění. V současné době je analýza mínění k dispozici pouze pro anglický jazyk.

Ukázka výstupu JSON vypadá takto:

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>Osvědčené postupy

Transkripční služba zvládne velký počet odeslaných přepisů. Můžete dotaz na stav přepisů prostřednictvím `GET` metody [přepisů](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions). Uchovávejte informace vrácené do `take` přiměřené velikosti zadáním parametru (několik set). Po získání výsledků pravidelně [odstraňujte přepisy](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) ze služby. To zaručuje rychlé odpovědi z volání správy přepisu.

## <a name="sample-code"></a>Ukázka kódu

Kompletní ukázky jsou k dispozici v `samples/batch` [ukázkovém úložišti GitHub](https://aka.ms/csspeech/samples) uvnitř podadresáře.

> [!NOTE]
> Funkce dávkového přepisu je vystavena prostřednictvím rozhraní REST API popsaného výše. Tak Batch přepis lze použít z téměř jakéhokoli programovacího jazyka nebo prostředí, které podporuje REST. Níže uvedené příklady a ukázky v GitHubu jsou pouze reprezentativní a **nevyžadují** omezení, kde lze rozhraní API použít.

Je třeba přizpůsobit ukázkový kód s informacemi o předplatném, oblast služby, Identifikátor URI SAS směřující na zvukový soubor k přepsání a ID modelu v případě, že chcete použít vlastní akustický nebo jazykový model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Ukázkový kód nastaví klienta a odešle žádost o přepis. Poté se vyhodí informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Podrobné informace o předchozích hovorech najdete v našem [dokumentu Swagger](https://westus.cris.ai/swagger/ui/index). Pro celou ukázku zde uvedené, přejděte na [GitHub](https://aka.ms/csspeech/samples) v `samples/batch` podadresáři.

Poznamenejte si asynchronní nastavení pro vysílání zvuku a příjem stavu přepisu. Klient, který vytvoříte, je klient HTTP rozhraní .NET. Existuje `PostTranscriptions` metoda pro odeslání podrobností zvukového `GetTranscriptions` souboru a metoda pro příjem výsledků. `PostTranscriptions`vrátí popisovač `GetTranscriptions` a použije jej k vytvoření popisovače pro získání stavu přepisu.

Aktuální ukázkový kód neurčuje vlastní model. Služba používá základní modely pro přepis souboru nebo souborů. Chcete-li určit modely, můžete předat stejnou metodu jako ID modelu pro akustické a jazykový model.

> [!NOTE]
> Pro směrné přepisy není nutné deklarovat ID pro základní modely. Pokud zadáte pouze ID jazykového modelu (a žádné ID akustického modelu), bude automaticky vybrán odpovídající akustický model. Pokud zadáte pouze ID akustického modelu, bude automaticky vybrán odpovídající jazykový model.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázku najdete v `samples/batch` adresáři v [ukázkovém úložišti GitHubu](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
