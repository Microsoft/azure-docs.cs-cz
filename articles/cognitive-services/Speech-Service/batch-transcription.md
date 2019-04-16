---
title: Jak používat jiné služby Batch – hlasové služby
titlesuffix: Azure Cognitive Services
description: Přepis batch je ideální, pokud chcete přepisy velké množství zvuk v úložišti, jako jsou objekty BLOB Azure. Pomocí vyhrazené rozhraní REST API můžete odkazovat na zvukové soubory pomocí sdíleného přístupového podpisu (SAS) identifikátor URI a asynchronně přijímat přepisů.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 2/20/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: b389d86fe4d23e3f4ee1c66e4270a74351098129
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59579355"
---
# <a name="why-use-batch-transcription"></a>Proč používat službu Batch určené k transkripci?

Přepis batch je ideální, pokud chcete přepisy velké množství zvuk v úložišti, jako jsou objekty BLOB Azure. Pomocí vyhrazené rozhraní REST API můžete odkazovat na zvukové soubory pomocí sdíleného přístupového podpisu (SAS) identifikátor URI a asynchronně přijímat přepisů.

## <a name="prerequisites"></a>Požadavky

### <a name="subscription-key"></a>Klíč předplatného

Se všemi funkcemi služby řeči, při vytváření odběru klíč z [webu Azure portal](https://portal.azure.com) podle našich [Příručka Začínáme](get-started.md). Pokud budete chtít získat přepisů z našich základní modely, vytváří se klíč je všechno, co musíte udělat.

>[!NOTE]
> Standardní předplatné (S0) pro hlasové služby je potřeba pomocí služby batch určené k transkripci. Bezplatné předplatné klíče (F0) nebudou fungovat. Další informace najdete v tématu [ceny a omezení](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

### <a name="custom-models"></a>Vlastní modely

Pokud máte v plánu pro přizpůsobení akustických nebo jazykové modely, postupujte podle kroků v [přizpůsobení akustických modelů](how-to-customize-acoustic-models.md) a [přizpůsobení jazykových modelů](how-to-customize-language-model.md). Používat modely vytvořené v určené k transkripci batch budete potřebovat své ID modelu. Toto ID není ID koncového bodu, které se nachází v zobrazení Podrobnosti o koncovém bodu, je ID modelu, který můžete načíst při výběru Podrobnosti modelů.

## <a name="the-batch-transcription-api"></a>Přepis rozhraní API služby Batch

Rozhraní API služby Batch určené k transkripci nabízí asynchronní přepis řeči na text, společně s další funkce. Je rozhraní REST API, který poskytuje metody pro:

1. Vytváření žádostí o zpracování služby batch
1. Stav dotazu
1. Přepisy stahování

> [!NOTE]
> Rozhraní API služby Batch určené k transkripci je ideální pro volání Center, která obvykle accumulate tisíce hodin zvukového záznamu. To usnadňuje přepisy velké objemy zvukových nahrávek.

### <a name="supported-formats"></a>Podporované formáty

Rozhraní API služby Batch určené k transkripci podporuje následující formáty:

| Formát | Kodek | S přenosovou rychlostí | Vzorkovací frekvence |
|--------|-------|---------|-------------|
| WAV | PCM | 16 bitů | 8 nebo 16 mono, stereo kHz, |
| MP3 | PCM | 16 bitů | 8 nebo 16 mono, stereo kHz, |
| OGG | DÍLE | 16 bitů | 8 nebo 16 mono, stereo kHz, |

Přepis rozhraní API služby Batch pro stereo zvukové datové proudy, rozdělí levého a pravého kanálu během přepis. Každé dva soubory JSON s výsledkem jsou vytvořeny z jednoho kanálu. Časová razítka na utterance umožňují vývojářům vytvořit seřazený konečné přepisu. Tento ukázkový požadavek obsahuje vlastnosti pro filtrování vulgárních výrazů, interpunkce a word úrovně časová razítka. 

### <a name="configuration"></a>Konfigurace

Parametry konfigurace jsou k dispozici jako dokumenty JSON:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> Rozhraní API určené k transkripci služby Batch používá službu REST pro požadování přepisů, jejich stav a přidružené výsledky. Můžete použít rozhraní API z jakéhokoli jazyka. Další část popisuje, jak se používá rozhraní API.

### <a name="configuration-properties"></a>Vlastnosti konfigurace

| Parametr | Popis | Povinné / volitelné |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Určuje způsob zpracování vulgárních výrazů v výsledky rozpoznávání. Platné hodnoty jsou `none` který zakáže filtrování vulgárních výrazů `masked` hvězdičky, která nahradí vulgárních výrazů `removed` výsledek, který zruší všechny vulgárních výrazů nebo `tags` které přidá značky "vulgárních výrazů". Ve výchozím nastavení je `masked`. | Nepovinné |
| `PunctuationMode` | Určuje způsob zpracování interpunkce v výsledky rozpoznávání. Platné hodnoty jsou `none` který zakáže interpunkční znaménka, `dictated` což naznačuje explicitní interpunkce, `automatic` které umožní dekodér řešit interpunkční znaménka, nebo `dictatedandautomatic` což naznačuje nařízeny interpunkční znaménka nebo automaticky. | Nepovinné |
 | `AddWordLevelTimestamps` | Určuje, pokud úroveň časová razítka slovo měla být přidána do výstupu. Platné hodnoty jsou `true` umožňující slovo úrovně časová razítka a `false` (výchozí hodnota) pro jeho zakázání. | Nepovinné |

### <a name="storage"></a>Úložiště

Služba batch podporuje určené k transkripci [úložiště objektů Blob v Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) pro čtení zvuk a přepisů zápis do úložiště.

## <a name="webhooks"></a>Webhooky 

Dotazování na stav určené k transkripci nemusí být většina výkonné a poskytují nejlepší uživatelské prostředí. Dotazování na stav, můžete zaregistrovat zpětná volání, které oznámí klient po dokončení dlouho běžící úlohy určené k transkripci.

Další podrobnosti najdete v tématu [Webhooky](webhooks.md).

## <a name="sample-code"></a>Ukázka kódu

Celá ukázka je k dispozici v [ukázkového úložiště Githubu](https://aka.ms/csspeech/samples) uvnitř `samples/batch` podadresáře.

Budete muset přizpůsobit vzorový kód s informace o vašem předplatném, oblasti služby SAS URI odkazující na zvukový soubor přepisy a modelování ID v případě, že chcete použít vlastní akustických nebo jazyka modelu. 

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

Vzorový kód bude instalace klienta a odeslat žádost o přepis. To bude potom dotazovat na informace o stavu a vytisknout podrobnosti o průběhu určené k transkripci.

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

Úplné podrobnosti o předchozí volání, najdete v našich [dokument Swagger](https://westus.cris.ai/swagger/ui/index). Úplnou ukázku je znázorněno zde, v části [Githubu](https://aka.ms/csspeech/samples) v `samples/batch` podadresáře.

Poznamenejte si nastavení asynchronní pro zvuk odesílání a příjem určené k transkripci stav. Klient, který vytvoříte je klienta .NET protokolu HTTP. Je `PostTranscriptions` metodu pro odesílání podrobnosti zvukový soubor a `GetTranscriptions` metodu pro příjem výsledků. `PostTranscriptions` Vrátí popisovač, a `GetTranscriptions` používá k vytvoření popisovače se získat stav určené k transkripci.

Aktuální vzorový kód neurčuje vlastního modelu. Služba používá základní modely pro přepisování na soubor nebo soubory. K určení vzorů, můžete předat na stejné metodě jako ID modelu akustických a jazykový model.

> [!NOTE]
> Pro základní přepisů není nutné deklarovat ID pro základní modely. Pokud zadáte pouze jazyk ID modelu (a žádné ID akustický model), je automaticky vybrána odpovídající akustický model. Pokud zadáte jenom ID akustický model, je automaticky vybrána odpovídající jazykový model.

## <a name="download-the-sample"></a>Stažení ukázky

Můžete najít ukázky v `samples/batch` v adresáři [ukázkového úložiště Githubu](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch určené k transkripci úlohy jsou naplánovány na jak kapacita systému dovolí, neexistuje žádný časový odhad pro když se změní úlohy do stavu spuštěno. Jednou v běžícím stavu, skutečné přepis, jsou zpracovávána rychleji než zvuku reálném čase.

## <a name="next-steps"></a>Další postup

* [Získání zkušebního předplatného služby Speech](https://azure.microsoft.com/try/cognitive-services/)
