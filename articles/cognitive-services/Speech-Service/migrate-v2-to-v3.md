---
title: Migrace z v2 na V3 REST API – Speech Service
titleSuffix: Azure Cognitive Services
description: Tento dokument pomáhá vývojářům migrovat kód z verze V2 na V3 ve službě Speech Services Speech-to-text REST API.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98569840"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>Migrace kódu z verze 2.0 do verze 3.0 REST API

V porovnání s verzí v2 je verze V3 služby Speech Services REST API pro převod řeči na text spolehlivější, jednodušší a spolehlivější s použitím rozhraní API pro podobné služby. Většina týmů může migrovat z verze V2 na V3 za jeden nebo dva dny.

## <a name="forward-compatibility"></a>Dopředná kompatibilita

Všechny entity z v2 lze také najít v rozhraní V3 API se stejnou identitou. Kde se změnilo schéma výsledku (například přepisy), výsledek GET v rozhraní API verze V3 používá schéma v3. Výsledek GET ve verzi v2 rozhraní API používá stejné schéma v2. Nově vytvořené entity na **V3 nejsou**   k dispozici v odpovědích z rozhraní API v2. 

## <a name="migration-steps"></a>Kroky migrace

Toto je souhrnný seznam položek, o kterých je potřeba vědět, když připravujete migraci. Podrobnosti najdete v jednotlivých odkazech. V závislosti na aktuálním použití rozhraní API se můžou použít všechny níže uvedené kroky. Pouze některé změny vyžadují netriviální změny v kódu volajícího. Většina změn vyžaduje pouze změnu názvů položek. 

Obecné změny: 

1. [Změnit název hostitele](#host-name-changes)

1. [Přejmenujte ID vlastnosti na sebe v klientském kódu.](#identity-of-an-entity) 

1. [Změna kódu pro iteraci přes kolekce entit](#working-with-collections-of-entities)

1. [Přejmenujte název vlastnosti na hodnotu DisplayName v kódu klienta.](#name-of-an-entity)

1. [Úprava načítání metadat odkazovaných entit](#accessing-referenced-entities)

1. Pokud používáte dávku přepisu: 

    * [Úprava kódu pro vytváření přepisů Batch](#creating-transcriptions) 

    * [Přizpůsobení kódu pro nové schéma výsledků přepisu](#format-of-v3-transcription-results)

    * [Upravit kód způsobu načítání výsledků](#getting-the-content-of-entities-and-the-results)

1. Pokud používáte rozhraní API pro školení a testování vlastních modelů: 

    * [Použít úpravy pro školení vlastního modelu](#customizing-models)

    * [Změna způsobu, jakým se načítají základní a vlastní modely](#retrieving-base-and-custom-models)

    * [Přejmenujte segment cesty accuracytests na vyhodnocení ve vašem klientském kódu.](#accuracy-tests)

1. Používáte-li rozhraní API koncových bodů:

    * [Změna způsobu načítání protokolů koncových bodů](#retrieving-endpoint-logs)

1. Další drobné změny: 

    * [Předání všech vlastních vlastností jako customProperties, nikoli vlastností ve vašich žádostech POST](#using-custom-properties)

    * [Přečtěte si umístění z umístění hlavičky odpovědi místo operace – umístění.](#response-headers)

## <a name="breaking-changes"></a>Změny způsobující chyby

### <a name="host-name-changes"></a>Změny názvu hostitele

Názvy hostitelů koncového bodu se změnily z `{region}.cris.ai` na `{region}.api.cognitive.microsoft.com` . Cesty k novým koncovým bodům už neobsahují `api/` , protože jsou součástí názvu hostitele. [Dokument Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) obsahuje seznam platných oblastí a cest.
>[!IMPORTANT]
>Změňte název hostitele z `{region}.cris.ai` na `{region}.api.cognitive.microsoft.com` , kde oblast je oblast vašeho předplatného pro rozpoznávání řeči. Odeberte také `api/` z jakékoli cesty v kódu klienta.

### <a name="identity-of-an-entity"></a>Identita entity

Vlastnost `id` je nyní `self` . V v2 měl uživatel rozhraní API zjistit, jak se vytvářejí naše cesty k rozhraní API. To bylo nerozšiřitelné a vyžadovala to nepotřebnou práci od uživatele. Vlastnost `id` (UUID) je nahrazena `self` řetězcem (String), což je umístění entity (adresa URL). Hodnota je stále jedinečná mezi všemi vašimi entitami. Pokud `id` je uložen jako řetězec ve vašem kódu, je pro podporu nového schématu dostatečně přejmenování. Tento obsah teď můžete použít `self` jako adresu URL pro `GET` `PATCH` volání, a `DELETE` REST pro vaši entitu.

Pokud má entita další funkce, které jsou k dispozici prostřednictvím jiných cest, jsou uvedeny v části `links` . Následující příklad pro přepis ukazuje samostatnou metodu `GET` obsahu přepisu:
>[!IMPORTANT]
>Přejmenujte vlastnost `id` na `self` v kódu klienta. V případě potřeby změňte typ z `uuid` na `string` . 

**V2 – přepis:**

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

**přepis V3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

V závislosti na implementaci vašeho kódu nemusí být vlastnost k přejmenování k dispozici. `self` `links` Místo generování cest ve vašem klientovi doporučujeme použít vrácenou a hodnotu jako cílové adresy URL volání REST. Pomocí vrácených adres URL si můžete být jisti, že budoucí změny v cestách nebudou přerušovat váš klientský kód.

### <a name="working-with-collections-of-entities"></a>Práce s kolekcemi entit

Dřív rozhraní API v2 vrátilo ve výsledku všechny dostupné entity. Aby bylo možné přesnější kontrolu nad očekávanou velikostí odpovědi v v3, jsou všechny výsledky kolekce na stránkování. Máte kontrolu nad počtem vrácených entit a počátečním posunem stránky. Toto chování usnadňuje předpověď modulu runtime procesoru odezvy.

Základní tvar odpovědi je stejný pro všechny kolekce:

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

`values`Vlastnost obsahuje podmnožinu dostupných entit kolekce. Počet a posun lze ovládat pomocí `skip` `top` parametrů dotazu a. `@nextLink`V opačném případě jsou `null` k dispozici další data a další dávku dat lze načíst pomocí získání `$.@nextLink` .

Tato změna vyžaduje volání metody `GET` pro kolekci ve smyčce, dokud nebudou vráceny všechny prvky.

>[!IMPORTANT]
>Pokud odpověď příkazu GET na `speechtotext/v3.0/{collection}` obsahuje hodnotu v `$.@nextLink` , pokračujte v vystavování `GETs` , `$.@nextLink` dokud `$.@nextLink` není nastavena pro načtení všech prvků kolekce.

### <a name="creating-transcriptions"></a>Vytváření přepisů

Podrobný popis postupu vytvoření dávek přepisů najdete v tématu [postup v dávkovém přepisu](./batch-transcription.md).

Rozhraní API pro přepis V3 umožňuje explicitně nastavit konkrétní možnosti přepisu. Ve vlastnosti se teď dají nastavit všechny (volitelné) vlastnosti konfigurace `properties` .
Verze v3 také podporuje více vstupních souborů, takže vyžaduje seznam adres URL, nikoli jenom jednu adresu URL jako v2. Název vlastnosti v2 `recordingsUrl` je nyní `contentUrls` ve verzi v3. Funkce analýzy mínění v přepisech se odebrala ve verzi v3. Možnosti analýzy mínění najdete v tématu [Analýza textu](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) služby společnosti Microsoft.

Nová vlastnost `timeToLive` v rámci `properties` může přispět k vyřazení stávajících dokončených entit. `timeToLive`Určuje dobu trvání, po jejímž uplynutí bude dokončená entita automaticky odstraněna. Nastavte ji na vysokou hodnotu (například `PT12H` ), pokud jsou entity nepřetržitě sledovány, spotřebovány a odstraněny, a jsou proto obvykle zpracovány dlouho před uplynutím 12 hodin.

**V2 přepis textu žádosti POST:**

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

**text požadavku verze V3 přepis:**

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```
>[!IMPORTANT]
>Přejmenujte vlastnost `recordingsUrl` na `contentUrls` a předejte pole adres URL místo jedné adresy URL. Předejte nastavení `diarizationEnabled` pro `wordLevelTimestampsEnabled` nebo `bool` místo `string` .

### <a name="format-of-v3-transcription-results"></a>Formát V3 přepisu výsledků

Schéma výsledků přepisu se mírně změnilo, aby se daly zarovnat s přepisy vytvořenými koncovými body v reálném čase. Podrobný popis nového formátu v [dávkovém přepisu](./batch-transcription.md)naleznete v tématu. Schéma výsledku je Publikováno v našem [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v části `samples/batch/transcriptionresult_v3.schema.json` .

Názvy vlastností jsou nyní ve stylu CamelCase-použita a hodnoty pro `channel` a `speaker` nyní používají celočíselné typy. Formát pro dobu trvání teď používá strukturu popsanou v normě ISO 8601, která odpovídá formátování doby trvání používané v jiných rozhraních API Azure.

Ukázka výsledků přepisu v3. Rozdíly jsou popsány v komentářích.

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

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
>[!IMPORTANT]
>Deserializovat výsledek přepisu do nového typu, jak je uvedeno výše. Místo jednoho souboru na zvukový kanál rozliší kanály kontrolou hodnoty vlastnosti `channel` pro každý prvek v `recognizedPhrases` . Pro každý vstupní soubor teď existuje jeden soubor výsledků.


### <a name="getting-the-content-of-entities-and-the-results"></a>Získání obsahu entit a výsledků

V v2 jsou odkazy na vstupní nebo výsledné soubory vložené s ostatními metadaty entit. V rámci vylepšení verze v3 je jasné oddělení metadat entit (které je vráceno funkcí GET on `$.self` ) a podrobností a přihlašovacích údajů pro přístup k souborům výsledků. Toto oddělení pomáhá chránit zákaznická data a umožňuje přesné řízení doby platnosti přihlašovacích údajů.

V části v3 `links` Zahrňte dílčí vlastnost volanou `files` pro případ, že entita zpřístupňuje data (datové sady, přepisy, koncové body nebo hodnocení). K získání `$.links.files` přístupu k obsahu jednotlivých souborů vrátí se seznam souborů a adresa URL SAS. Chcete-li řídit dobu platnosti adres URL SAS, je `sasValidityInSeconds` možné použít parametr dotazu k určení doby života.

**V2 – přepis:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

**přepis V3:**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**Výsledkem může být `$.links.files` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`Vlastnost určuje formát obsahu souboru. V případě přepisů jsou soubory typu `TranscriptionReport` souhrnem úlohy a soubory typu `Transcription` jsou výsledkem samotné úlohy.

>[!IMPORTANT]
>Chcete-li získat výsledky operací, použijte `GET` na `/speechtotext/v3.0/{collection}/{id}/files` , již nejsou obsaženy v odpovědích `GET` na `/speechtotext/v3.0/{collection}/{id}` nebo `/speechtotext/v3.0/{collection}` .

### <a name="customizing-models"></a>Přizpůsobení modelů

Před v3m bylo při školení modelu rozdíl mezi _akustickým modelem_ a _jazykovým modelem_ . Výsledkem tohoto rozdílu je nutnost zadat více modelů při vytváření koncových bodů nebo přepisů. Pro zjednodušení tohoto procesu pro volající jsme odebrali rozdíly a provedli vše na základě obsahu datových sad, které se používají pro školení modelů. Při této změně vytváření modelů teď podporuje smíšenou datovou sadu (data v jazyce a akustická data). Koncové body a Přepisy teď vyžadují jenom jeden model.

V důsledku této změny je potřeba `kind` v `POST` operaci odebrat a `datasets[]` pole může nyní obsahovat více datových sad stejného nebo smíšeného druhu.

Pro zlepšení výsledků trained model se akustická data automaticky používají interně během školení jazyka. Obecně platí, že modely vytvořené prostřednictvím rozhraní V3 API poskytují přesnější výsledky než modely vytvořené pomocí rozhraní v2 API.

>[!IMPORTANT]
>Chcete-li přizpůsobit akustický a jazykový model, předejte všechny požadované jazyky a akustické datové sady v `datasets[]` příspěvku do `/speechtotext/v3.0/models` . Tím se vytvoří jeden model s oběma částmi, které jsou přizpůsobené.

### <a name="retrieving-base-and-custom-models"></a>Načítají se základní a vlastní modely.

Aby bylo možné zjednodušit získání dostupných modelů, hodnota V3 oddělí kolekce "základních modelů" od zákazníka, který vlastní "přizpůsobené modely". Tyto dvě trasy jsou nyní `GET /speechtotext/v3.0/models/base` a `GET /speechtotext/v3.0/models/` .

V v2 všechny modely byly vráceny v rámci jedné odpovědi.

>[!IMPORTANT]
>Chcete-li získat seznam zadaných základních modelů pro přizpůsobení, použijte `GET` `/speechtotext/v3.0/models/base` . Vlastní přizpůsobené modely můžete najít `GET` v `/speechtotext/v3.0/models` .

### <a name="name-of-an-entity"></a>Název entity

`name`Vlastnost je nyní `displayName` . To je konzistentní s jinými rozhraními API Azure, aby neoznačovaly vlastnosti identity. Hodnota této vlastnosti nesmí být jedinečná a po vytvoření entity s operací je možné ji změnit `PATCH` .

**V2 – přepis:**

```json
{
    "name": "Transcription using locale en-US"
}
```

**přepis V3:**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>Přejmenujte vlastnost `name` na `displayName` v kódu klienta.

### <a name="accessing-referenced-entities"></a>Přístup k odkazovaným entitám

V v2 byly odkazované entity vždycky vložené, například používané modely koncového bodu. Vnořování entit vedlo k velkým odpovědím a spotřebitelé zřídka využili vnořený obsah. Pokud chcete zmenšit velikost odpovědi a zvýšit výkon, odkazované entity už nejsou v odpovědi vložené. Místo toho se zobrazí odkaz na jinou entitu a lze ji použít přímo pro následné `GET` (Jedná se také o adresu URL), a to za stejným vzorem jako `self` odkaz.

**V2 – přepis:**

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
        }
      ]
    },
  ]
}
```

**přepis V3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

Pokud potřebujete spotřebovat podrobnosti odkazovaného modelu, jak je znázorněno v předchozím příkladu, stačí, když vydáte příkaz získat na `$.model.self` .

>[!IMPORTANT]
>Pokud chcete načíst metadata odkazovaných entit, vydejte si `$.{referencedEntity}.self` například příkaz Get on, který načte model přepisu `GET` `$.model.self` .


### <a name="retrieving-endpoint-logs"></a>Načítání protokolů koncových bodů

Verze V2 služby podporovala výsledky koncového bodu protokolování. Chcete-li načíst výsledky koncového bodu s v2, vytvořte "Export dat", který představoval snímek výsledků definovaných časovým rozsahem. Proces exportu dávek dat byl neflexibilní. Rozhraní V3 API poskytuje přístup ke každému jednotlivému souboru a umožňuje iteraci prostřednictvím nich.

**Úspěšně se spustil koncový bod V3:**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**Odpověď pro GET `$.links.logs` :**

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

Stránkování protokolů koncového bodu funguje podobně jako u všech ostatních kolekcí, s výjimkou toho, že není možné zadat žádný posun. Vzhledem k velkému množství dat, která jsou k dispozici, je stránkování určováno serverem.

V systému V3 lze každý protokol koncového bodu odstranit jednotlivě vyvoláním `DELETE` operace na `self` soubor nebo pomocí příkazu `DELETE` zapnuto `$.links.logs` . K určení koncového data `endDate` lze do žádosti přidat parametr dotazu.

>[!IMPORTANT]
>Místo vytváření exportů protokolů při `/api/speechtotext/v2.0/endpoints/{id}/data` použití `/v3.0/endpoints/{id}/files/logs/` pro přístup k souborům protokolu jednotlivě. 

### <a name="using-custom-properties"></a>Použití vlastních vlastností

Chcete-li oddělit vlastní vlastnosti z volitelných vlastností konfigurace, jsou nyní všechny explicitně pojmenované vlastnosti umístěny ve `properties` vlastnosti a všechny vlastnosti definované volajícími jsou nyní umístěny ve `customProperties` Vlastnosti.

**V2 – entita přepisu:**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**V3 přepisující entitu:**

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

Tato změna také umožňuje používat správné typy pro všechny explicitně pojmenované vlastnosti v části `properties` (například Boolean namísto řetězce).

>[!IMPORTANT]
>Předejte všechny vlastní vlastnosti jako `customProperties` místo `properties` v rámci vašich `POST` požadavků.

### <a name="response-headers"></a>Hlavičky odpovědi

hodnota V3 již nevrací `Operation-Location` hlavičku kromě `Location` záhlaví `POST` požadavků. Hodnota obou hlaviček v v2 byla stejná. Nyní `Location` je vrácen pouze.

Vzhledem k tomu, že nová verze rozhraní API je teď spravovaná pomocí Azure API Management (APIM), hlavičky související s omezováním `X-RateLimit-Limit` `X-RateLimit-Remaining` a `X-RateLimit-Reset` nejsou obsažené v hlavičkách odpovědi.

>[!IMPORTANT]
>Přečtěte si umístění z hlavičky odpovědi `Location` místo `Operation-Location` . V případě kódu odpovědi 429 si přečtěte `Retry-After` hodnotu hlavičky místo `X-RateLimit-Limit` , `X-RateLimit-Remaining` nebo `X-RateLimit-Reset` .


### <a name="accuracy-tests"></a>Testy přesnosti

Testy přesnosti byly přejmenovány na hodnocení, protože nový název popisuje lepší význam, co představují. Nové cesty jsou: `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` .

>[!IMPORTANT]
>Přejmenujte segment cesty `accuracytests` na `evaluations` svůj klientský kód.


## <a name="next-steps"></a>Další kroky

Projděte si všechny funkce těchto běžně používaných rozhraní REST API poskytovaných službami Speech:

* [Rozhraní REST API pro převod řeči na text](rest-speech-to-text.md)
* [Dokument Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) pro v3 REST API
* Vzorový kód pro provedení přepisu služby Batch najdete v [úložišti ukázkového úložiště GitHub](https://aka.ms/csspeech/samples) v `samples/batch` podadresáři.
