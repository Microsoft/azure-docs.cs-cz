---
title: Jak používat službu Batch přepis-Speech Service
titleSuffix: Azure Cognitive Services
description: Služba Batch přepis je ideální, pokud chcete přepisovat velké množství zvuků v úložišti, jako jsou objekty blob Azure. Pomocí vyhrazené REST API můžete odkazovat na zvukové soubory s identifikátorem URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat přepisy.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 158a99b1691e59fa58207f3c9291ca9d37a6679c
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538118"
---
# <a name="why-use-batch-transcription"></a>Proč používat Batch přepis?

Služba Batch přepis je ideální, pokud chcete přepisovat velké množství zvuků v úložišti, jako jsou objekty blob Azure. Pomocí vyhrazené REST API můžete odkazovat na zvukové soubory s identifikátorem URI sdíleného přístupového podpisu (SAS) a asynchronně přijímat přepisy.

## <a name="prerequisites"></a>Předpoklady

### <a name="subscription-key"></a>Klíč předplatného

Stejně jako u všech funkcí služby pro rozpoznávání řeči vytvoříte pomocí [příručky Začínáme](get-started.md)klíč předplatného z [Azure Portal](https://portal.azure.com) . Pokud máte v úmyslu získat z našich standardních modelů nějaké přepisy, musíte vytvořit klíč, který je potřeba udělat.

>[!NOTE]
> K použití dávkového přepisu se vyžaduje standardní předplatné (S0) pro služby řeči. Klíče bezplatného předplatného (F0) nebudou fungovat. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud máte v úmyslu přizpůsobit akustické nebo jazykové modely, postupujte podle kroků v části [přizpůsobení akustických modelů](how-to-customize-acoustic-models.md) a [přizpůsobení jazykových modelů](how-to-customize-language-model.md). Pokud chcete v rámci dávkového přepisu použít vytvořené modely, budete potřebovat jejich ID modelu. Toto ID není ID koncového bodu, které najdete v zobrazení podrobností koncového bodu, jedná se o ID modelu, které můžete načíst, když vyberete podrobnosti modelů.

## <a name="the-batch-transcription-api"></a>Rozhraní API pro dávkové Přepisy

Rozhraní API dávkového přepisu nabízí jako přepis asynchronního převodu řeči na text spolu s dalšími funkcemi. Je REST API, který zveřejňuje metody pro:

1. Vytváření požadavků dávkového zpracování
1. Dotaz na stav
1. Stahování přepisů

> [!NOTE]
> Rozhraní API služby Batch pro přepisy je ideální pro centra volání, která obvykle shromažďují tisíce hodin zvukového přenosu. Díky tomu se snadno přepisovat velké objemy zvukových nahrávek.

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API dávkového přepisu podporuje následující formáty:

| Formát | Kodek | Rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| BUĎ | Code | 16 bitů | 8 nebo 16 kHz, mono, stereo |
| MP3 | Code | 16 bitů | 8 nebo 16 kHz, mono, stereo |
| OGG | OPUS | 16 bitů | 8 nebo 16 kHz, mono, stereo |

V případě datových proudů v stereofonních zvukech rozhraní API dávkového přepisu během přepisu rozdělí levý a pravý kanál. Dva soubory JSON s výsledkem jsou jednotlivé vytvořené z jednoho kanálu. Časová razítka na utterance umožňují vývojáři vytvořit seřazený finální přepis. Tato ukázková žádost obsahuje vlastnosti pro filtrování vulgárních výrazů, interpunkční znaménka a časová razítka na úrovni slov.

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
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True",
    "AddSentiment" : "True"
  }
}
```

> [!NOTE]
> Rozhraní API služby Batch pro přepis používá službu REST pro vyžádání přepisů, jejich stavů a přidružených výsledků. Rozhraní API můžete použít v jakémkoli jazyce. Následující část popisuje, jak se používá rozhraní API.

### <a name="configuration-properties"></a>Vlastnosti konfigurace

K nakonfigurování přepisu použijte tyto volitelné vlastnosti:

| Parametr | Popis |
|-----------|-------------|
| `ProfanityFilterMode` | Určuje způsob zpracování vulgárních výrazů ve výsledcích rozpoznávání. Přípustné hodnoty jsou `None`, které deaktivují filtrování vulgárních výrazů, `masked`, které nahradí vulgární znaky hvězdičkami, `removed` které odstraní všechny vulgární výrazy z výsledku nebo `tags`, které přidávají značky "vulgární výrazy". Výchozí nastavení je `masked`. |
| `PunctuationMode` | Určuje způsob zpracování interpunkce ve výsledcích rozpoznávání. Přípustné hodnoty jsou `None`, což zakáže interpunkci, `dictated`, která implikuje explicitní interpunkci, `automatic`, která umožňuje dekodéru zacházet s interpunkčním znaménkem nebo `dictatedandautomatic`, což znamená, že se automaticky zakazují interpunkční znaménka |
 | `AddWordLevelTimestamps` | Určuje, zda mají být do výstupu přidány časová razítka na úrovni aplikace Word. Přípustné hodnoty jsou `true`, které umožňují, aby byla časová razítka na úrovni aplikace Word a `false` (výchozí hodnota) zakázána. |
 | `AddSentiment` | Určuje mínění by měl být přidán do utterance. Přijaté hodnoty jsou `true`, které povolují mínění na utterance a `false` (výchozí hodnotu), která ho zakáže. |
 | `AddDiarization` | Určuje, že by měla být provedena analýza diarization na vstupu, u kterého se očekává, že kanál mono obsahuje dvě hlasy. Přípustné hodnoty jsou `true`, které povolují diarization a `false` (výchozí hodnota), která ji zakáže. Také je nutné, aby bylo `AddWordLevelTimestamps` nastaveno na hodnotu true.|

### <a name="storage"></a>Úložiště

Služba Batch přepisu podporuje [úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pro čtení zvuku a psaní přepisů do úložiště.

## <a name="speaker-separation-diarization"></a>Oddělení mluvčího (Diarization)

Diarization je proces oddělení mluvčích v rámci zvukového zařízení. Náš dávkový kanál podporuje Diarization a dokáže rozpoznat dva reproduktory na záznamech kanálů mono.

Aby bylo možné požádat o zpracování požadavku na přepis zvuku pro diarization, stačí přidat příslušný parametr v požadavku HTTP, jak je znázorněno níže.

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

Časová razítka na úrovni slov by také musela být zapnutá, protože parametry výše uvedené žádosti ukazují. 

Odpovídající zvuk bude obsahovat reproduktory identifikované číslem (aktuálně podporujeme pouze dva hlasy, takže reproduktory budou identifikovány jako mluvčí 1 a mluvčí 2) následovaný výstupem přepisu.

Všimněte si také, že v stereofonních záznamech není Diarization k dispozici. Kromě toho všechen výstup JSON bude obsahovat značku mluvčího. Pokud se diarization nepoužívá, zobrazí se ve výstupu JSON "mluvčí: null".

> [!NOTE]
> Diarization je k dispozici ve všech oblastech a pro všechna národní prostředí!

## <a name="sentiment"></a>Zabarvení

Mínění je nová funkce v rozhraní API služby Batch přepisu a je důležitou funkcí v doméně centra volání. Zákazníci mohou použít parametry `AddSentiment` pro jejich požadavky na

1.  Získejte přehled o spokojenosti zákazníků
2.  Získejte přehled o výkonu agentů (při volání tohoto týmu).
3.  Přesné určení bodu v čase, kdy volání trvalo v nezáporném směru
4.  Určení toho, co je dobré při zapínání negativních volání na pozitivní
5.  Určení toho, co se zákazníkům líbí a co se na produkt nebo službu nelíbí

Mínění je vyhodnoceno na segment zvuku, kde je zvukový segment definován jako časový poměr mezi začátkem utterance (posun) a detekcí nečinnosti koncového datového proudu. K výpočtu mínění se použije celý text v tomto segmentu. Nepočítáme žádné agregované hodnoty mínění pro celé volání nebo celé rozpoznávání řeči každého kanálu. Tyto agregace jsou ponechány vlastníkovi domény, aby je bylo možné dále použít.

Mínění se používá na lexikálním formuláři.

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
Tato funkce používá model mínění, který je aktuálně ve verzi beta.

## <a name="sample-code"></a>Ukázka kódu

Kompletní ukázky jsou k dispozici v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples) v podadresáři `samples/batch`.

Je nutné upravit ukázkový kód s informacemi o předplatném, s oblastí služby, identifikátorem URI SAS odkazujícím na zvukový soubor na přepisovat a ID modelu pro případ, že chcete použít vlastní akustický nebo jazykový model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Vzorový kód nastaví klienta a odešle požadavek přepisu. Pak se zobrazí dotaz na informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Úplné podrobnosti o předchozích voláních najdete v našem [dokumentu Swagger](https://westus.cris.ai/swagger/ui/index). Úplný vzorek zobrazený tady najdete na [GitHubu](https://aka.ms/csspeech/samples) v podadresáři `samples/batch`.

Poznamenejte si asynchronní nastavení pro publikování zvuku a přijetí přepisu stavu. Vytvořeným klientem je klient .NET HTTP. K odeslání podrobností o zvukovém souboru a metodě `GetTranscriptions` pro příjem výsledků je `PostTranscriptions` metoda. `PostTranscriptions` vrátí popisovač a `GetTranscriptions` ho použije k vytvoření popisovače pro získání stavu přepisu.

Aktuální ukázkový kód neurčuje vlastní model. Služba používá základní modely pro zdlouhavého přepisování souborů nebo souborů. Chcete-li určit modely, můžete předat stejnou metodu jako ID modelu pro akustický a jazykový model.

> [!NOTE]
> Pro přepisy směrného plánu není nutné deklarovat ID pro základní modely. Pokud zadáte pouze ID jazykového modelu (bez ID akustického modelu), je automaticky vybrán shodný akustický model. Pokud zadáte pouze ID akustického modelu, je automaticky vybrán shodný jazykový model.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázku najdete v adresáři `samples/batch` v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples).

> [!NOTE]
> Úlohy dávkového přepisu jsou naplánovány na nejvyšší úsilí, nejedná se o časový odhad, kdy se úloha změní do stavu spuštěno. Po spuštění se skutečný přepis zpracovává rychleji než zvuk v reálném čase.

## <a name="next-steps"></a>Další kroky

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
