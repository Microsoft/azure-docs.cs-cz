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
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: 088b6ef93631cb964979de3621453caa430c5b1e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559706"
---
# <a name="why-use-batch-transcription"></a>Proč používat službu Batch určené k transkripci?

Přepis batch je ideální, pokud chcete přepisy velké množství zvuk v úložišti, jako jsou objekty BLOB Azure. Pomocí vyhrazené rozhraní REST API můžete odkazovat na zvukové soubory pomocí sdíleného přístupového podpisu (SAS) identifikátor URI a asynchronně přijímat přepisů.

## <a name="prerequisites"></a>Požadavky

### <a name="subscription-key"></a>Klíč předplatného

Se všemi funkcemi služby řeči, při vytváření odběru klíč z [webu Azure portal](https://portal.azure.com) podle našich [Příručka Začínáme](get-started.md). Pokud budete chtít získat přepisů z našich základní modely, vytváří se klíč je všechno, co musíte udělat.

>[!NOTE]
> Standardní předplatné (S0) pro hlasové služby je potřeba pomocí služby batch určené k transkripci. Bezplatné předplatné klíče (F0) nebudou fungovat. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud máte v úmyslu přizpůsobit akustické nebo jazykové modely, postupujte podle kroků v části [přizpůsobení akustických modelů](how-to-customize-acoustic-models.md) a [přizpůsobení jazykových modelů](how-to-customize-language-model.md). Pokud chcete v rámci dávkového přepisu použít vytvořené modely, budete potřebovat jejich ID modelu. Toto ID není ID koncového bodu, které najdete v zobrazení podrobností koncového bodu, jedná se o ID modelu, které můžete načíst, když vyberete podrobnosti modelů.

## <a name="the-batch-transcription-api"></a>Přepis rozhraní API služby Batch

Rozhraní API služby Batch určené k transkripci nabízí asynchronní přepis řeči na text, společně s další funkce. Je rozhraní REST API, který poskytuje metody pro:

1. Vytváření žádostí o zpracování služby batch
1. Stav dotazu
1. Přepisy stahování

> [!NOTE]
> Rozhraní API služby Batch určené k transkripci je ideální pro volání Center, která obvykle accumulate tisíce hodin zvukového záznamu. Díky tomu se snadno přepisovat velké objemy zvukových nahrávek.

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API služby Batch určené k transkripci podporuje následující formáty:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 8 nebo 16 mono, stereo kHz, |
| MP3 | PCM | 16 bitů | 8 nebo 16 mono, stereo kHz, |
| OGG | DÍLE | 16 bitů | 8 nebo 16 mono, stereo kHz, |

Přepis rozhraní API služby Batch pro stereo zvukové datové proudy, rozdělí levého a pravého kanálu během přepis. Každé dva soubory JSON s výsledkem jsou vytvořeny z jednoho kanálu. Časová razítka na utterance umožňují vývojářům vytvořit seřazený konečné přepisu. Tato ukázková žádost obsahuje vlastnosti pro filtrování vulgárních výrazů, interpunkční znaménka a časová razítka na úrovni slov.

### <a name="configuration"></a>Konfiguraci

Parametry konfigurace jsou zadány jako JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
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
> Rozhraní API určené k transkripci služby Batch používá službu REST pro požadování přepisů, jejich stav a přidružené výsledky. Můžete použít rozhraní API z jakéhokoli jazyka. Další část popisuje, jak se používá rozhraní API.

### <a name="configuration-properties"></a>Vlastnosti konfigurace

K nakonfigurování přepisu použijte tyto volitelné vlastnosti:

| Parametr | Popis |
|-----------|-------------|
| `ProfanityFilterMode` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Platné hodnoty jsou `none` který zakáže filtrování vulgárních výrazů `masked` hvězdičky, která nahradí vulgárních výrazů `removed` výsledek, který zruší všechny vulgárních výrazů nebo `tags` které přidá značky "vulgárních výrazů". Ve výchozím nastavení je `masked`. |
| `PunctuationMode` | Určuje způsob zpracování interpunkce v výsledky rozpoznávání. Platné hodnoty jsou `none` který zakáže interpunkční znaménka, `dictated` což naznačuje explicitní interpunkce, `automatic` které umožní dekodér řešit interpunkční znaménka, nebo `dictatedandautomatic` což naznačuje nařízeny interpunkční znaménka nebo automaticky. |
 | `AddWordLevelTimestamps` | Určuje, zda mají být do výstupu přidány časová razítka na úrovni aplikace Word. Přijaté hodnoty `true` umožňují, aby se časová razítka `false` na úrovni aplikace Word a (výchozí hodnota) zakázala. |
 | `AddSentiment` | Určuje mínění by měl být přidán do utterance. Přijatelné hodnoty `true` umožňují mínění na utterance a `false` (výchozí hodnota), která ji zakáže. |
 | `AddDiarization` | Určuje, že by se mělo provést diarization alalysis na vstupu, u kterého se očekává, že kanál mono obsahuje dvě hlasy. Přijatelné hodnoty `true` umožňují diarization a `false` (výchozí hodnota), která ji zakáže. Také je nutné `AddWordLevelTimestamps` nastavit na hodnotu true.|

### <a name="storage"></a>Storage

Služba Batch přepisu podporuje [úložiště objektů BLOB v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pro čtení zvuku a psaní přepisů do úložiště.

## <a name="webhooks"></a>webhooks

Cyklické dotazování na stav přepisu nemusí být nejvýkonnější nebo poskytovat nejlepší uživatelské prostředí. Chcete-li provést dotaz na stav, můžete zaregistrovat zpětná volání, která upozorní klienta na dokončení dlouhotrvajících úloh přepisu.

Další podrobnosti najdete v tématu [](webhooks.md)Webhooky.

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

Všimněte si také, že v stereofonních záznamech není Diarization k dispozici. Kromě toho všechen výstup JSON bude obsahovat značku mluvčího. Pokud se diarization nepoužívá, zobrazí se mluvčí: Null ve výstupu JSON.

> [!NOTE]
> Diarization je k dispozici ve všech oblastech a pro všechna národní prostředí!

## <a name="sentiment"></a>Mínění

Mínění je nová funkce v rozhraní API služby Batch přepisu a je důležitou funkcí v doméně centra volání. Zákazníci mohou použít `AddSentiment` parametry pro své požadavky na

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

Kompletní ukázky jsou k dispozici v `samples/batch` [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v podadresáři.

Je nutné upravit ukázkový kód s informacemi o předplatném, s oblastí služby, identifikátorem URI SAS odkazujícím na zvukový soubor na přepisovat a ID modelu pro případ, že chcete použít vlastní akustický nebo jazykový model.

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Vzorový kód nastaví klienta a odešle požadavek přepisu. Pak se zobrazí dotaz na informace o stavu a vytiskne podrobnosti o průběhu přepisu.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Úplné podrobnosti o předchozích voláních najdete v našem [dokumentu Swagger](https://westus.cris.ai/swagger/ui/index). Úplný vzorek, který vidíte tady, najdete v [](https://aka.ms/csspeech/samples) `samples/batch` podadresáři na GitHubu.

Poznamenejte si nastavení asynchronní pro zvuk odesílání a příjem určené k transkripci stav. Klient, který vytvoříte je klienta .NET protokolu HTTP. Je `PostTranscriptions` metodu pro odesílání podrobnosti zvukový soubor a `GetTranscriptions` metodu pro příjem výsledků. `PostTranscriptions` Vrátí popisovač, a `GetTranscriptions` používá k vytvoření popisovače se získat stav určené k transkripci.

Aktuální vzorový kód neurčuje vlastního modelu. Služba používá základní modely pro přepisování na soubor nebo soubory. K určení vzorů, můžete předat na stejné metodě jako ID modelu akustických a jazykový model.

> [!NOTE]
> Pro přepisy směrného plánu není nutné deklarovat ID pro základní modely. Pokud zadáte pouze ID jazykového modelu (bez ID akustického modelu), je automaticky vybrán shodný akustický model. Pokud zadáte pouze ID akustického modelu, je automaticky vybrán shodný jazykový model.

## <a name="download-the-sample"></a>Stažení ukázky

Ukázku najdete v `samples/batch` adresáři v [úložišti ukázek GitHubu](https://aka.ms/csspeech/samples).

> [!NOTE]
> Úlohy dávkového přepisu jsou naplánovány na nejvyšší úsilí, nejedná se o časový odhad, kdy se úloha změní do stavu spuštěno. Po spuštění se skutečný přepis zpracovává rychleji než zvuk v reálném čase.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
