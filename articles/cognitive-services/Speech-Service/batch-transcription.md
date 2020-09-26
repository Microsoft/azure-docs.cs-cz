---
title: Jak používat službu Batch přepis-Speech Service
titleSuffix: Azure Cognitive Services
description: Služba Batch přepis je ideální, pokud chcete přepisovat velké množství zvuků v úložišti, jako jsou objekty blob Azure. Pomocí vyhrazené REST API můžete odkazovat na zvukové soubory s identifikátorem URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat přepisy.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: fe864212eaccb67335586ef8b25049529ab36b81
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360748"
---
# <a name="how-to-use-batch-transcription"></a>Použití dávkového přepisu

Batch přepis je sada operací REST API, která umožňuje přepisovat velké množství zvuků v úložišti. Můžete odkazovat na zvukové soubory pomocí typického identifikátoru URI nebo identifikátoru URI sdíleného přístupového podpisu (SAS) a asynchronní příjem výsledků přepisu. V rozhraní API v 3.0 můžete přepisovat jeden nebo více zvukových souborů nebo zpracovat celý kontejner úložiště.

Pomocí rozhraní REST API pro dávkové přepisy můžete zavolat následující metody:

|    Operace dávkového přepisu                                             |    Metoda    |    REST API volání                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    Vytvoří nový přepis.                                              |    POST      |    speechtotext/v 3.0/Přepisy            |
|    Načte seznam přepisů pro ověřený odběr.    |    GET       |    speechtotext/v 3.0/Přepisy            |
|    Načte seznam podporovaných národních prostředí pro přepisy offline.              |    GET       |    speechtotext/v 3.0/přepisy/národní prostředí    |
|    Aktualizuje proměnlivé podrobnosti přepisu identifikovaného jeho ID.    |    POUŽITA     |    speechtotext/v 3.0/přepisy/{ID}       |
|    Odstraní zadanou úlohu přepisu.                                 |    DELETE    |    speechtotext/v 3.0/přepisy/{ID}       |
|    Získá přepis identifikovaný daným ID.                        |    GET       |    speechtotext/v 3.0/přepisy/{ID}       |
|    Načte soubory výsledků přepisu identifikované daným ID.    |    GET       |    speechtotext/v 3.0/přepisy/{ID}/soubory |

Můžete zkontrolovat a otestovat podrobné rozhraní API, které je k dispozici jako [dokument Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0).

Toto rozhraní API nevyžaduje vlastní koncové body a nemá žádné požadavky na souběžnost.

Úlohy dávkového přepisu jsou plánovány na základě optimálního úsilí.
Nemůžete odhadnout, kdy se úloha změní do běžícího stavu, ale v rámci normálního zatížení systému by se měla vyskytnout během několika minut. Ve spuštěném stavu dojde k přepisu rychleji než rychlost přehrávání zvukového běhu.

## <a name="prerequisites"></a>Požadavky

Stejně jako u všech funkcí služby pro rozpoznávání řeči vytvoříte pomocí [příručky Začínáme](overview.md#try-the-speech-service-for-free)klíč předplatného z [Azure Portal](https://portal.azure.com) .

>[!NOTE]
> K použití dávkového přepisu se vyžaduje standardní předplatné (S0) pro službu Speech. Klíče bezplatného předplatného (F0) nefungují. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

Pokud plánujete přizpůsobovat modely, postupujte podle kroků v tématu [akustické přizpůsobení](how-to-customize-acoustic-models.md) a [přizpůsobení jazyka](how-to-customize-language-model.md). Pokud chcete v dávkovém přepisu použít vytvořené modely, budete potřebovat jejich umístění modelu. Umístění modelu lze načíst při kontrole podrobností modelu ( `self` vlastnost). Pro službu Batch přepisu není *potřebný* nasazený vlastní koncový bod.

## <a name="batch-transcription-api"></a>Rozhraní API pro dávkové Přepisy

Rozhraní API dávkového přepisu podporuje následující formáty:

| Formát | Kodek | Bity na ukázku | Vzorkovací frekvence             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |
| MP3    | PCM   | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |
| OGG    | OPUS  | 16bitový  | 8 kHz nebo 16 kHz, mono nebo stereo |

Pro datové proudy stereofonních zvuků se při přepisu budou dělit kanály vlevo a vpravo. Vytváří se soubor výsledků JSON pro každý kanál.
Chcete-li vytvořit seřazený finální přepis, použijte časová razítka vygenerovaná na utterance.

### <a name="configuration"></a>Konfigurace

Parametry konfigurace jsou zadány jako JSON (jeden nebo více jednotlivých souborů):

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Parametry konfigurace jsou zadány jako JSON (zpracování celého kontejneru úložiště):

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

Následující kód JSON určuje vlastní vyškolený model, který se má použít v přepisu Batch:

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>Vlastnosti konfigurace

K nakonfigurování přepisu použijte tyto volitelné vlastnosti:

:::row:::
   :::column span="1":::
      **Parametr**
   :::column-end:::
   :::column span="2":::
      **Popis**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      Volitelné, výchozí hodnota je `Masked` . Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijatelné hodnoty jsou `None` Zakázání filtrování vulgárních výrazů, `Masked` nahrazení vulgárních výrazů hvězdičkami, `Removed` Odebrání všech vulgárních výrazů z výsledku nebo `Tags` Přidání značek "vulgárních výrazů".
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Volitelné, výchozí hodnota je `DictatedAndAutomatic` . Určuje způsob zpracování interpunkce ve výsledcích rozpoznávání. Přijatelné hodnoty jsou `None` Zakázat interpunkci, `Dictated` která implikuje explicitní (hlasovou) interpunkci, `Automatic` aby dekodér mohl pracovat s interpunkčním znaménkem nebo `DictatedAndAutomatic` používat diktovánou a automatickou interpunkci.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Volitelné, `false` ve výchozím nastavení. Určuje, zda mají být do výstupu přidány časová razítka na úrovni aplikace Word.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Volitelné, `false` ve výchozím nastavení. Určuje, že by měla být provedena analýza diarization na vstupu, což se očekává, že kanál mono obsahuje dvě hlasy. Poznámka: vyžaduje, aby `wordLevelTimestampsEnabled` bylo nastavené na `true` .
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Volitelné `0` a `1` přepisu ve výchozím nastavení. Pole čísel kanálů, která se mají zpracovat. Tady můžete určit podmnožinu dostupných kanálů ve zvukovém souboru (například `0` jenom).
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Volitelné, ve výchozím nastavení se neodstraní. Doba, po kterou se přepisy po dokončení přepisu automaticky odstraní. `timeToLive`Je užitečné v rámci hromadného zpracování, které zajišťuje, aby se nakonec odstranily (např. `PT12H` 12 hodin).
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Volitelná adresa URL se [službou ad hoc SAS](../../storage/common/storage-sas-overview.md) k zapisovatelnému kontejneru v Azure. Výsledek je uložen v tomto kontejneru. SAS s uloženými zásadami přístupu **se nepodporuje** . Pokud tento parametr nezadáte, uloží Microsoft výsledky do kontejneru úložiště spravovaného Microsoftem. Když se přepis odstraní voláním [Odstranit přepisu](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), budou odstraněna také výsledná data.
:::row-end:::

### <a name="storage"></a>Storage

Batch přepis může číst zvuk z internetového identifikátoru URI, který je veřejně viditelný, a může číst zvuk nebo zapisovat přepisy pomocí identifikátoru URI SAS s [úložištěm objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

## <a name="batch-transcription-result"></a>Výsledek dávkového přepisu

U každého zvukového vstupu se vytvoří jeden přepisový soubor výsledků.
Operace [načíst přepisy souborů](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles) vrátí seznam souborů výsledků pro tento přepis. Chcete-li najít soubor přepisu pro konkrétní vstupní soubor, vyfiltrujte všechny vrácené soubory pomocí `kind`  ==  `Transcription` a `name`  ==  `{originalInputName.suffix}.json` .

Každý soubor výsledků přepisu má tento formát:

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "speaker": 1,                 // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]    
    }
  ]
}
```

Výsledek obsahuje následující pole:

:::row:::
   :::column span="1":::
      **Pole**
   :::column-end:::
   :::column span="2":::
      **Obsah**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      Skutečná slova byla rozpoznána.
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      Inverzní text – normalizovaná forma rozpoznaného textu. Zkratky ("lékař Novák" na "Dr Smith"), telefonní čísla a další transformace jsou aplikovány.
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      Formulář vytvořené s aplikovaným maskou vulgárních výrazů
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      Formulář pro zobrazení rozpoznaného textu Jsou zahrnutá interpunkční znaménka a malá písmena.
:::row-end:::

## <a name="speaker-separation-diarization"></a>Oddělení mluvčího (diarization)

Diarization je proces oddělení mluvčích v rámci zvukového zařízení. Kanál Batch podporuje diarization a dokáže rozpoznat dva reproduktory na záznamech kanálů mono. Tato funkce není k dispozici pro stereofonní nahrávky.

Výstup přepisu s povoleným diarization obsahuje `Speaker` záznam pro každou frázi přepisu. Pokud se diarization nepoužívá, `Speaker` vlastnost není přítomna ve výstupu JSON. Pro diarization podporujeme dva hlasy, takže reproduktory se identifikují jako `1` nebo `2` .

Chcete-li požádat o diarization, přidejte `diarizationEnabled` vlastnost nastavit na hodnotu `true` jako požadavek HTTP níže.

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

Časová razítka na úrovni aplikace musí být povolena, protože parametry výše uvedené žádosti ukazují.

## <a name="best-practices"></a>Osvědčené postupy

Služba Batch přepisu dokáže zvládnout velký počet odeslaných přepisů. Můžete se dotazovat na stav přepisu pomocí příkazu [získat přepisy](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions).
Po načtení výsledků volání odstraňte ze služby pravidelné [přepisy](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) . Případně nastavte `timeToLive` vlastnost, aby se zajistilo konečné odstranění výsledků.

## <a name="sample-code"></a>Ukázka kódu

Kompletní ukázky jsou k dispozici v [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v `samples/batch` podadresáři.

Pokud používáte vlastní model, aktualizujte vzorový kód s informacemi o předplatném, oblastí služby, identifikátorem URI ukazujícím na zvukový soubor na přepisovat a umístěním modelu.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Vzorový kód nastaví klienta a odešle požadavek přepisu. Pak se dotazuje na informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Úplné podrobnosti o předchozích voláních najdete v našem [dokumentu Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Úplný vzorek, který vidíte tady, najdete v podadresáři na [GitHubu](https://aka.ms/csspeech/samples) `samples/batch` .

Tato ukázka používá asynchronní instalaci k odeslání zvuku a získání přepisu stavu.
`PostTranscriptions`Metoda odešle podrobnosti o zvukovém souboru a `GetTranscriptions` metoda obdrží stavy.
`PostTranscriptions` Vrátí popisovač a `GetTranscriptions` použije ho k vytvoření popisovače pro získání stavu přepisu.

Tento ukázkový kód neurčuje vlastní model. Služba používá základní model pro zdlouhavého přepisování souborů nebo souborů. Chcete-li určit model, můžete předat stejné metodě odkaz na model pro vlastní model.

> [!NOTE]
> Pro přepisy směrného plánu není nutné deklarovat ID pro základní model.

## <a name="next-steps"></a>Další kroky

- [Referenční informace k rozhraní API pro převod řeči na text V3](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
