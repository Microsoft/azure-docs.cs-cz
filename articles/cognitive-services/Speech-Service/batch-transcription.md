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
ms.openlocfilehash: 9804992aee318fdc34815bdbe4187144704cd667
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85099762"
---
# <a name="what-is-batch-transcription"></a>Co je Batch přepis?

Batch přepis je sada operací REST API, která umožňuje přepisovat velké množství zvuků v úložišti. Můžete odkazovat na zvukové soubory pomocí identifikátoru URI sdíleného přístupového podpisu (SAS) a asynchronní příjem výsledků přepisu. S novým rozhraním API v 3.0 můžete zvolit zdlouhavého přepisování jeden nebo více zvukových souborů nebo zpracovat celý kontejner úložiště.

Přepis asynchronního převodu řeči na text je jenom jedna z funkcí. Pomocí rozhraní REST API pro dávkové přepisy můžete zavolat následující metody:



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

Úlohy dávkového přepisu jsou plánovány na základě optimálního úsilí. V současné době není k dispozici žádný odhad při změně úlohy do stavu spuštěno. Při normálním zatížení systému by se mělo provést během několika minut. Ve stavu spuštěno je skutečný přepis zpracován rychleji než v reálném čase.

Vedle snadno použitelného rozhraní API nemusíte nasazovat vlastní koncové body a nemusíte sledovat žádné požadavky na souběžnost.

## <a name="prerequisites"></a>Požadavky

### <a name="subscription-key"></a>Klíč předplatného

Stejně jako u všech funkcí služby pro rozpoznávání řeči vytvoříte pomocí [příručky Začínáme](get-started.md)klíč předplatného z [Azure Portal](https://portal.azure.com) .

>[!NOTE]
> K použití dávkového přepisu se vyžaduje standardní předplatné (S0) pro službu Speech. Klíče bezplatného předplatného (F0) nefungují. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud plánujete přizpůsobovat modely, postupujte podle kroků v tématu [akustické přizpůsobení](how-to-customize-acoustic-models.md) a [přizpůsobení jazyka](how-to-customize-language-model.md). Pokud chcete v dávkovém přepisu použít vytvořené modely, budete potřebovat jejich umístění modelu. Umístění modelu lze načíst při kontrole podrobností modelu ( `self` vlastnost). Pro službu Batch přepisu není *potřebný* nasazený vlastní koncový bod.

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

Pokud chcete používat vlastní školené modely v dávkových přepisech, můžete na ně odkazovat podobně jako na následujícím obrázku:

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
      Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přijatelné hodnoty jsou `None` Zakázání filtrování vulgárních výrazů, `Masked` nahrazení vulgárních výrazů hvězdičkami, `Removed` Odebrání všech vulgárních výrazů z výsledku nebo `Tags` Přidání značek "vulgárních výrazů". Výchozí hodnota je `Masked`.
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      Určuje způsob zpracování interpunkce ve výsledcích rozpoznávání. Přijatelné hodnoty jsou `None` Zakázat interpunkci, `Dictated` která implikuje explicitní (hlasovou) interpunkci, `Automatic` aby dekodér mohl pracovat s interpunkčním znaménkem nebo `DictatedAndAutomatic` používat diktovánou a automatickou interpunkci. Výchozí hodnota je `DictatedAndAutomatic`.
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      Určuje, zda mají být do výstupu přidány časová razítka na úrovni aplikace Word. Přijatelné hodnoty jsou `true` Povolit časová razítka na úrovni aplikace Word a `false` (výchozí hodnotu), která ji zakáže.
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      Určuje, že by měla být provedena analýza diarization na vstupu, což se očekává, že kanál mono obsahuje dvě hlasy. Přijaté hodnoty `true` povolují diarization a `false` (výchozí hodnota) ji zakáže. Také je nutné `wordLevelTimestampsEnabled` nastavit na hodnotu true.
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      Volitelné pole čísel kanálů ke zpracování. Tady můžete určit podmnožinu dostupných kanálů ve zvukovém souboru (například `0` jenom). Pokud není zadaný, kanály `0` a `1` jsou přepisu jako výchozí.
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      Volitelná doba trvání pro automatické odstranění přepisů po dokončení přepisu. `timeToLive`Je užitečné v rámci hromadného zpracování, které zajišťuje, aby se nakonec odstranily (např. `PT12H` ). Pokud není zadaný nebo nastavený na `PT0H` , přepis se neodstraní automaticky.
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      Volitelná adresa URL s [SAS služby](../../storage/common/storage-sas-overview.md) pro zapisovatelný kontejner v Azure. Výsledek je uložen v tomto kontejneru. Pokud tento parametr nezadáte, uloží Microsoft výsledky do kontejneru úložiště spravovaného Microsoftem. Když se přepis odstraní voláním [Odstranit přepisu](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription), budou odstraněna také výsledná data.
:::row-end:::

### <a name="storage"></a>Storage

Služba Batch přepisu podporuje [úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pro čtení zvuku a psaní přepisů do úložiště.

## <a name="the-batch-transcription-result"></a>Výsledek přepisu dávky

Pro každý vstupní zvuk se vytváří jeden soubor výsledků přepisu. Seznam výsledných souborů můžete získat voláním metody [Get přepiss](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles). Tato metoda vrátí seznam souborů výsledků pro tento přepis. Chcete-li najít soubor přepisu pro konkrétní vstupní soubor, vyfiltrujte všechny vrácené soubory pomocí `kind`  ==  `Transcription` a `name`  ==  `{originalInputName.suffix}.json` .

Každý soubor výsledků přepisu má tento formát:

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
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

## <a name="speaker-separation-diarization"></a>Oddělení mluvčího (Diarization)

Diarization je proces oddělení mluvčích v rámci zvukového zařízení. Náš dávkový kanál podporuje diarization a dokáže rozpoznat dva reproduktory na záznamech kanálů mono. Tato funkce není k dispozici pro stereofonní nahrávky.

Výstup přepisu s povoleným diarization obsahuje `Speaker` záznam pro každou frázi přepisu. Pokud se diarization nepoužívá, vlastnost není `Speaker` přítomna ve výstupu JSON. Pro diarization podporujeme dva hlasy, takže reproduktory se identifikují jako `1` nebo `2` .

Pro vyžádání diarization stačí přidat relevantní parametr v požadavku HTTP, jak je znázorněno níže.

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

Služba přepisu dokáže zvládnout velký počet odeslaných přepisů. Můžete zadat dotaz na stav vašich přepisů `GET` v části [získání přepisů](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions). Po načtení výsledků volání odstraňte ze služby pravidelné [přepisy](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) . Případně nastavte `timeToLive` vlastnost na rozumnou hodnotu, abyste zajistili případné odstranění výsledků.

## <a name="sample-code"></a>Ukázka kódu

Kompletní ukázky jsou k dispozici v [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v `samples/batch` podadresáři.

Aktualizujte prosím vzorový kód s informacemi o předplatném, s oblastí služby, identifikátorem URI SAS odkazujícím na zvukový soubor na přepisovat a umístěním modelu pro případ, že chcete použít vlastní model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

Vzorový kód nastaví klienta a odešle požadavek přepisu. Pak se dotazuje na informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

Úplné podrobnosti o předchozích voláních najdete v našem [dokumentu Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). Úplný vzorek, který vidíte tady, najdete v podadresáři na [GitHubu](https://aka.ms/csspeech/samples) `samples/batch` .

Poznamenejte si asynchronní nastavení pro publikování zvuku a přijetí přepisu stavu. Vytvořeným klientem je klient .NET HTTP. Existuje `PostTranscriptions` metoda pro odeslání podrobností o zvukovém souboru a `GetTranscriptions` metodě pro přijetí stavů. `PostTranscriptions`Vrátí popisovač a `GetTranscriptions` použije ho k vytvoření popisovače pro získání stavu přepisu.

Aktuální ukázkový kód neurčuje vlastní model. Služba používá základní model pro zdlouhavého přepisování souborů nebo souborů. Chcete-li určit model, můžete předat stejné metodě odkaz na model pro vlastní model.

> [!NOTE]
> Pro přepisy směrného plánu není nutné deklarovat ID pro základní model.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázku najdete v `samples/batch` adresáři v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Další kroky

- [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
