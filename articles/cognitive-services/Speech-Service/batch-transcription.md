---
title: Jak používat službu Batch přepis-Speech Service
titleSuffix: Azure Cognitive Services
description: Přepis batch je ideální, pokud chcete přepisy velké množství zvuk v úložišti, jako jsou objekty BLOB Azure. Pomocí vyhrazené rozhraní REST API můžete odkazovat na zvukové soubory pomocí sdíleného přístupového podpisu (SAS) identifikátor URI a asynchronně přijímat přepisů.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: dc473c814cdd69204cddd976bc77f19b5db567b1
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77200074"
---
# <a name="how-to-use-batch-transcription"></a>Použití dávkového přepisu

Batch přepisu je ideální pro zdlouhavého přepisováníí velkého množství zvukového úložiště. Pomocí vyhrazeného REST API můžete odkazovat na zvukové soubory s identifikátorem URI sdíleného přístupového podpisu (SAS) a asynchronní příjem výsledků přepisu.

Rozhraní API nabízí funkce pro přepis asynchronního převodu řeči na text a další funkce. Pomocí REST API můžete vystavit metody pro:

- Vytvoření požadavků dávkového zpracování
- Dotaz na stav
- Stažení výsledků přepisu
- Odstranit přepisové informace ze služby

Podrobné rozhraní API je k dispozici jako [dokument Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)pod nadpisem `Custom Speech transcriptions`.

Úlohy dávkového přepisu jsou plánovány na základě optimálního úsilí. V současné době není k dispozici žádný odhad, kdy se úloha změní do stavu spuštěno. Při normálním zatížení systému by se mělo provést během několika minut. Ve stavu spuštěno je skutečný přepis zpracován rychleji než v reálném čase.

Vedle snadno použitelného rozhraní API nemusíte nasazovat vlastní koncové body a nemusíte sledovat žádné požadavky na souběžnost.

## <a name="prerequisites"></a>Předpoklady

### <a name="subscription-key"></a>Klíč předplatného

Stejně jako u všech funkcí služby pro rozpoznávání řeči vytvoříte pomocí [příručky Začínáme](get-started.md)klíč předplatného z [Azure Portal](https://portal.azure.com) .

>[!NOTE]
> K použití dávkového přepisu se vyžaduje standardní předplatné (S0) pro službu Speech. Bezplatné předplatné klíče (F0) nebudou fungovat. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud máte v úmyslu přizpůsobit akustické nebo jazykové modely, postupujte podle kroků v tématu [přizpůsobení akustických modelů](how-to-customize-acoustic-models.md) a [Návrh jazykových modelů přizpůsobení](how-to-customize-language-model.md). Pokud chcete použít vytvořené modely v dávkovém přepisu, budete potřebovat jejich ID modelu. ID modelu můžete načíst při kontrole podrobností modelu. Pro službu Batch přepisu není potřebný nasazený vlastní koncový bod.

## <a name="the-batch-transcription-api"></a>Přepis rozhraní API služby Batch

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API služby Batch určené k transkripci podporuje následující formáty:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 8 kHz nebo 16 kHz, mono nebo stereo |
| MP3 | PCM | 16 bitů | 8 kHz nebo 16 kHz, mono nebo stereo |
| OGG | DÍLE | 16 bitů | 8 kHz nebo 16 kHz, mono nebo stereo |

Pro datové proudy stereofonních zvuků se při přepisu budou dělit kanály vlevo a vpravo. Pro každý kanál se vytváří soubor výsledků JSON. Časová razítka vygenerovaná na utterance umožňují vývojáři vytvořit objednaný finální přepis.

### <a name="configuration"></a>Konfigurace

Parametry konfigurace jsou zadány jako JSON:

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

K nakonfigurování přepisu použijte tyto volitelné vlastnosti:

| Parametr | Popis |
|-----------|-------------|
| `ProfanityFilterMode` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Přípustné hodnoty jsou `None`, které deaktivují filtrování vulgárních výrazů, `Masked`, které nahradí vulgární znaky hvězdičkami, `Removed` které odstraní všechny vulgární výrazy z výsledku nebo `Tags`, které přidávají značky "vulgární výrazy". Výchozí nastavení je `Masked`. |
| `PunctuationMode` | Určuje způsob zpracování interpunkce v výsledky rozpoznávání. Přípustné hodnoty jsou `None`, což zakáže interpunkci, `Dictated`, která implikuje explicitní interpunkci, `Automatic`, která umožňuje dekodéru zacházet s interpunkčním znaménkem nebo `DictatedAndAutomatic`, což znamená, že se automaticky zakazují interpunkční znaménka |
| `AddWordLevelTimestamps` | Určuje, zda mají být do výstupu přidány časová razítka na úrovni aplikace Word. Přípustné hodnoty jsou `true`, které umožňují, aby byla časová razítka na úrovni aplikace Word a `false` (výchozí hodnota) zakázána. |
| `AddSentiment` | Určuje mínění by měl být přidán do utterance. Přijaté hodnoty jsou `true`, které povolují mínění na utterance a `false` (výchozí hodnotu), která ho zakáže. |
| `AddDiarization` | Určuje, že by měla být provedena analýza diarization na vstupu, u kterého se očekává, že kanál mono obsahuje dvě hlasy. Přípustné hodnoty jsou `true`, které povolují diarization a `false` (výchozí hodnota), která ji zakáže. Také je nutné, aby bylo `AddWordLevelTimestamps` nastaveno na hodnotu true.|
|`TranscriptionResultsContainerUrl`|Volitelná adresa URL s [SAS služby](../../storage/common/storage-sas-overview.md) pro zapisovatelný kontejner v Azure. Výsledek bude uložen v tomto kontejneru.

### <a name="storage"></a>Úložiště

Služba Batch přepisu podporuje [úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pro čtení zvuku a psaní přepisů do úložiště.

## <a name="the-batch-transcription-result"></a>Výsledek přepisu dávky

U zvukového vstupu mono se vytváří jeden soubor výsledků přepisu. V případě zvukového vstupu stereo se vytváří dva soubory výsledků přepisu. Každá z nich má tuto strukturu:

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

Výsledek obsahuje tyto formuláře:

|Tvar|Obsah|
|-|-|
|`Lexical`|Skutečná slova byla rozpoznána.
|`ITN`|Inverzní text – normalizovaná forma rozpoznaného textu. Zkratky ("lékař Novák" na "Dr Smith"), telefonní čísla a další transformace jsou aplikovány.
|`MaskedITN`|Formulář vytvořené s aplikovaným maskou vulgárních výrazů
|`Display`|Formulář pro zobrazení rozpoznaného textu To zahrnuje interpunkci a používání velkých a malých písmen.

## <a name="speaker-separation-diarization"></a>Oddělení mluvčího (Diarization)

Diarization je proces oddělení mluvčích v rámci zvukového zařízení. Náš dávkový kanál podporuje diarization a dokáže rozpoznat dva reproduktory na záznamech kanálů mono. Tato funkce není k dispozici pro stereofonní nahrávky.

Všechen výstup přepisu obsahuje `SpeakerId`. Pokud se diarization nepoužívá, zobrazí se ve výstupu JSON `"SpeakerId": null`. Pro diarization podporujeme dva hlasy, takže reproduktory se identifikují jako `"1"` nebo `"2"`.

Pro vyžádání diarization stačí přidat relevantní parametr v požadavku HTTP, jak je znázorněno níže.

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

Časová razítka na úrovni aplikace by také musel být zapnutá, protože parametry výše uvedené žádosti ukazují.

## <a name="sentiment-analysis"></a>Analýza mínění

Funkce mínění odhadne mínění vyjádřenou ve zvukovém zařízení. Mínění se vyjadřuje hodnotou mezi 0 a 1 pro `Negative`, `Neutral`a `Positive` mínění. Například analýza mínění lze použít ve scénářích volání centra:

- Získejte přehled o spokojenosti zákazníků
- Získejte přehled o výkonu agentů (při volání tohoto týmu).
- Najde přesný bod v čase, kdy volání trvalo v nezáporném směru.
- Co je dobré při zapnutí negativního volání do kladného směru
- Určení toho, co se zákazníkům líbí a co se na produkt nebo službu nelíbí

Mínění je vyhodnoceno na segment zvuku na základě lexikálního formuláře. Celý text v tomto segmentu zvuku se používá k výpočtu mínění. Pro celý přepis se nepočítá žádné agregované mínění.

Ukázka výstupu JSON vypadá následovně:

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

Služba přepisu dokáže zvládnout velký počet odeslaných přepisů. Pomocí `GET` v [metodě přepisů](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)můžete zadat dotaz na stav vašich přepisů. Zachovejte informace vracené do rozumné velikosti zadáním parametru `take` (několik set). Po načtení výsledků [odstraňte přepisy](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) ze služby pravidelně. Tím se zaručí rychlé odpovědi z volání správy přepisu.

## <a name="sample-code"></a>Ukázka kódu

Kompletní ukázky jsou k dispozici v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples) v podadresáři `samples/batch`.

Je nutné upravit ukázkový kód s informacemi o předplatném, s oblastí služby, identifikátorem URI SAS odkazujícím na zvukový soubor na přepisovat a ID modelu pro případ, že chcete použít vlastní akustický nebo jazykový model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Vzorový kód nastaví klienta a odešle požadavek přepisu. Pak se zobrazí dotaz na informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Úplné podrobnosti o předchozích voláních najdete v našem [dokumentu Swagger](https://westus.cris.ai/swagger/ui/index). Úplný vzorek zobrazený tady najdete na [GitHubu](https://aka.ms/csspeech/samples) v podadresáři `samples/batch`.

Poznamenejte si nastavení asynchronní pro zvuk odesílání a příjem určené k transkripci stav. Klient, který vytvoříte je klienta .NET protokolu HTTP. K odeslání podrobností o zvukovém souboru a metodě `GetTranscriptions` pro příjem výsledků je `PostTranscriptions` metoda. `PostTranscriptions` vrátí popisovač a `GetTranscriptions` ho použije k vytvoření popisovače pro získání stavu přepisu.

Aktuální vzorový kód neurčuje vlastního modelu. Služba používá základní modely pro přepisování na soubor nebo soubory. K určení vzorů, můžete předat na stejné metodě jako ID modelu akustických a jazykový model.

> [!NOTE]
> Pro přepisy směrného plánu není nutné deklarovat ID pro základní modely. Pokud zadáte pouze ID jazykového modelu (bez ID akustického modelu), je automaticky vybrán shodný akustický model. Pokud zadáte pouze ID akustického modelu, je automaticky vybrán shodný jazykový model.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázku najdete v adresáři `samples/batch` v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
