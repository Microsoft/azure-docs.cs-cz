---
title: Co je Batch přepis-Speech Service
titleSuffix: Azure Cognitive Services
description: Služba Batch přepis je ideální, pokud chcete přepisovat velké množství zvuků v úložišti, jako jsou objekty blob Azure. Pomocí vyhrazené REST API můžete odkazovat na zvukové soubory s identifikátorem URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat přepisy.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208614"
---
# <a name="what-is-batch-transcription"></a>Co je Batch přepis?

Batch přepis je sada operací REST API, která umožňuje přepisovat velké množství zvuků v úložišti. Můžete odkazovat na zvukové soubory pomocí identifikátoru URI sdíleného přístupového podpisu (SAS) a asynchronní příjem výsledků přepisu.

Přepis asynchronního převodu řeči na text je jenom jedna z funkcí. Pomocí rozhraní REST API pro dávkové přepisy můžete zavolat následující metody:



|    Operace dávkového přepisu                                             |    Metoda    |    REST API volání                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Vytvoří nový přepis.                                              |    POST      |    API/speechtotext/v – 2.0/Přepisy            |
|    Načte seznam přepisů pro ověřený odběr.    |    GET       |    API/speechtotext/v – 2.0/Přepisy            |
|    Načte seznam podporovaných národních prostředí pro přepisy offline.              |    GET       |    API/speechtotext/v 2.0/přepisy/národní prostředí    |
|    Aktualizuje proměnlivé podrobnosti přepisu identifikovaného jeho ID.    |    POUŽITA     |    API/speechtotext/v 2.0/přepisy/{ID}       |
|    Odstraní zadanou úlohu přepisu.                                 |    DELETE    |    API/speechtotext/v 2.0/přepisy/{ID}       |
|    Získá přepis identifikovaný daným ID.                        |    GET       |    API/speechtotext/v 2.0/přepisy/{ID}       |




Můžete zkontrolovat a otestovat podrobné rozhraní API, které je k dispozici jako [dokument Swagger](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)pod hlavičkou `Custom Speech transcriptions`.

Úlohy dávkového přepisu jsou plánovány na základě optimálního úsilí. V současné době není k dispozici žádný odhad při změně úlohy do stavu spuštěno. Při normálním zatížení systému by se mělo provést během několika minut. Ve stavu spuštěno je skutečný přepis zpracován rychleji než v reálném čase.

Vedle snadno použitelného rozhraní API nemusíte nasazovat vlastní koncové body a nemusíte sledovat žádné požadavky na souběžnost.

## <a name="prerequisites"></a>Požadavky

### <a name="subscription-key"></a>Klíč předplatného

Stejně jako u všech funkcí služby pro rozpoznávání řeči vytvoříte pomocí [příručky Začínáme](get-started.md)klíč předplatného z [Azure Portal](https://portal.azure.com) .

>[!NOTE]
> K použití dávkového přepisu se vyžaduje standardní předplatné (S0) pro službu Speech. Klíče bezplatného předplatného (F0) nefungují. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud máte v úmyslu přizpůsobit akustické nebo jazykové modely, postupujte podle kroků v tématu [přizpůsobení akustických modelů](how-to-customize-acoustic-models.md) a [Návrh jazykových modelů přizpůsobení](how-to-customize-language-model.md). Pokud chcete použít vytvořené modely v dávkovém přepisu, budete potřebovat jejich ID modelu. ID modelu můžete načíst při kontrole podrobností modelu. Pro službu Batch přepisu není potřebný nasazený vlastní koncový bod.

## <a name="the-batch-transcription-api"></a>Rozhraní API pro dávkové Přepisy

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API dávkového přepisu podporuje následující formáty:

| Formát | Kodek | Rychlostí | Vzorkovací frekvence                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |
| MP3    | PCM   | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |
| OGG    | OPUS  | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |

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

:::row:::
   :::column span="1":::
      **Ukazatele**
   :::column-end:::
   :::column span="2":::
      **Popis**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijatelné hodnoty jsou `None` zakázání filtrování vulgárních výrazů, `Masked` nahrazení vulgárních výrazů hvězdičkami, `Removed` odebrání všech vulgárních výrazů z výsledku nebo `Tags` Přidání značek "vulgárních výrazů". Výchozí hodnota je `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      Určuje způsob zpracování interpunkce ve výsledcích rozpoznávání. Přijatelné hodnoty jsou `None` zakázat interpunkci, `Dictated` která implikuje explicitní (hlasovou) `Automatic` interpunkci, aby dekodér mohl pracovat s interpunkčním `DictatedAndAutomatic` znaménkem nebo používat diktovánou a automatickou interpunkci. Výchozí hodnota je `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      Určuje, zda mají být do výstupu přidány časová razítka na úrovni aplikace Word. Přijatelné hodnoty jsou `true` povolit časová razítka na úrovni `false` aplikace Word a (výchozí hodnotu), která ji zakáže.
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      Určuje, zda má být pro utterance použita analýza mínění. Přijaté hodnoty jsou `true` povoleny a `false` (výchozí hodnota), která ji zakáže. Další podrobnosti najdete v tématu [Analýza mínění](#sentiment-analysis) .
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      Určuje, že by měla být provedena analýza diarization na vstupu, což se očekává, že kanál mono obsahuje dvě hlasy. Přijaté hodnoty `true` povolují diarization a `false` (výchozí hodnota) ji zakáže. Také je nutné `AddWordLevelTimestamps` nastavit na hodnotu true.
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      Volitelná adresa URL s [SAS služby](../../storage/common/storage-sas-overview.md) pro zapisovatelný kontejner v Azure. Výsledek je uložen v tomto kontejneru.
:::row-end:::

### <a name="storage"></a>Storage

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

:::row:::
   :::column span="1":::
      **Formulář**
   :::column-end:::
   :::column span="2":::
      **Obsah**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      Skutečná slova byla rozpoznána.
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      Inverzní text – normalizovaná forma rozpoznaného textu. Zkratky ("lékař Novák" na "Dr Smith"), telefonní čísla a další transformace jsou aplikovány.
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      Formulář vytvořené s aplikovaným maskou vulgárních výrazů
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      Formulář pro zobrazení rozpoznaného textu Jsou zahrnutá interpunkční znaménka a malá písmena.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Oddělení mluvčího (Diarization)

Diarization je proces oddělení mluvčích v rámci zvukového zařízení. Náš dávkový kanál podporuje diarization a dokáže rozpoznat dva reproduktory na záznamech kanálů mono. Tato funkce není k dispozici pro stereofonní nahrávky.

Všechen výstup přepisu obsahuje `SpeakerId`. Pokud se diarization nepoužívá, zobrazí `"SpeakerId": null` se ve výstupu JSON. Pro diarization podporujeme dva hlasy, takže reproduktory se identifikují jako `"1"` nebo `"2"`.

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

Mínění je vyhodnoceno na segment zvuku na základě lexikálního formuláře. Celý text v tomto segmentu zvuku se používá k výpočtu mínění. Pro celý přepis se nepočítá žádné agregované mínění. Analýza mínění je aktuálně dostupná jenom v anglickém jazyce.

> [!NOTE]
> Doporučujeme místo toho použít Microsoft rozhraní API pro analýzu textu. Nabízí pokročilejší funkce nad rámec analýzy mínění, jako je například extrakce klíčových frází, automatické zjišování jazyka a další. Informace a ukázky najdete v [dokumentaci k analýza textu](https://azure.microsoft.com/services/cognitive-services/text-analytics/).
>

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

Služba přepisu dokáže zvládnout velký počet odeslaných přepisů. Pomocí `GET` [metody přepisů](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)můžete zadat dotaz na stav přepisů. Zachovejte informace vracené do rozumné velikosti zadáním `take` parametru (několik set). Po načtení výsledků [odstraňte přepisy](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription) ze služby pravidelně. Tím se garantuje rychlé odpovědi z volání správy přepisu.

## <a name="sample-code"></a>Ukázka kódu

Kompletní ukázky jsou k dispozici v `samples/batch` [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v podadresáři.

Je nutné upravit ukázkový kód s informacemi o předplatném, s oblastí služby, identifikátorem URI SAS odkazujícím na zvukový soubor na přepisovat a ID modelu pro případ, že chcete použít vlastní akustický nebo jazykový model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Vzorový kód nastaví klienta a odešle požadavek přepisu. Pak se dotazuje na informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Úplné podrobnosti o předchozích voláních najdete v našem [dokumentu Swagger](https://westus.cris.ai/swagger/ui/index). Úplný vzorek, který vidíte tady, najdete v `samples/batch` podadresáři na [GitHubu](https://aka.ms/csspeech/samples) .

Poznamenejte si asynchronní nastavení pro publikování zvuku a přijetí přepisu stavu. Vytvořeným klientem je klient .NET HTTP. Existuje `PostTranscriptions` metoda pro odeslání podrobností o zvukovém souboru a `GetTranscriptions` metodě pro příjem výsledků. `PostTranscriptions`Vrátí popisovač a `GetTranscriptions` použije ho k vytvoření popisovače pro získání stavu přepisu.

Aktuální ukázkový kód neurčuje vlastní model. Služba používá základní modely pro zdlouhavého přepisování souborů nebo souborů. Chcete-li určit modely, můžete předat stejnou metodu jako ID modelu pro akustický a jazykový model.

> [!NOTE]
> Pro přepisy směrného plánu není nutné deklarovat ID pro základní modely. Pokud zadáte pouze ID jazykového modelu (bez ID akustického modelu), je automaticky vybrán shodný akustický model. Pokud zadáte pouze ID akustického modelu, je automaticky vybrán shodný jazykový model.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázku najdete v `samples/batch` adresáři v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
