---
title: Migrace z v2 na V3 REST API – Speech Service
titleSuffix: Azure Cognitive Services
description: V porovnání s verzí v2 nové rozhraní API verze 3 obsahuje sadu menších změn. Tento dokument vám v tuto chvíli pomůže migrovat na novou hlavní verzi.
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737972"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>Migrace z verze 2.0 na v 3.0 na text REST API

Verze v3 REST API řeči vylepšuje předchozí verzi rozhraní API z hlediska spolehlivosti a snadného použití. Rozložení rozhraní API se podrobněji zarovnává s ostatními Azure nebo rozhraní API služeb Cognitive Services. To vám pomůže při použití našeho rozhraní API pro rozpoznávání řeči při používání vašich stávajících dovedností.

Přehled rozhraní API je k dispozici jako [dokument Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0). To je ideální, když vám poskytneme Přehled rozhraní API a otestujete nové rozhraní API.

Poskytujeme ukázky pro C# a Python. V případě dávkových přepisů najdete ukázky v [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v `samples/batch` podadresáři.

## <a name="forward-compatibility"></a>Dopředná kompatibilita

Aby bylo zajištěno hladké migrace na verzi 3, mohou být v rozhraní V3 API v rámci stejné identity také nalezeny všechny entity z verze v2. Pokud dojde ke změně schématu výsledků (například přepisy), budou odpovědi pro rozhraní API pro GET ve verzi V3 ve schématu v3. Pokud provedete rozhraní API získat ve verzi v2, schéma výsledku bude ve formátu v2. Nově vytvořené entity na V3 **nejsou** ve verzi v2 k dispozici.

## <a name="breaking-changes"></a>Změny způsobující chyby

Seznam nejnovějších změn byl seřazen podle velikosti změn požadovaných k přizpůsobení. Existuje pouze několik změn, které vyžadují netriviální změnu v kódu volajícího. Většina změn vyžaduje jednoduchá přejmenování. Čas potřebný pro týmy, které se mají migrovat z verze V2 na v3, se v několika hodinách lišily na několik dní. Nicméně výhody zvýšené stability, jednoduššího kódu, rychlejší odezvy rychle posunou investice. 

### <a name="host-name-changes"></a>Změny názvu hostitele

Názvy hostitelů se změnily z {region}. položku CRI. AI na {region}. API. vnímání. Microsoft. com. V této změně cesty již neobsahují "API/", protože je součástí názvu hostitele. Úplný popis oblastí a cest najdete v [dokumentu Swagger](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) .

### <a name="identity-of-an-entity"></a>Identita entity

Vlastnost `id` byla nahrazena vlastností `self` . V v2 měl uživatel rozhraní API zjistit, jak se vytvářejí naše cesty k rozhraní API. To bylo nerozšiřitelné a vyžadovala to nepotřebnou práci od uživatele. Vlastnost `id` (UUID) je nahrazena `self` řetězcem (String), což je umístění entity (adresa URL). Hodnota je stále jedinečná mezi všemi vašimi entitami. Pokud `id` je uložen jako řetězec v kódu, je pro podporu nového schématu dostatek jednoduchého přejmenování. Tento obsah teď můžete použít `self` jako adresu URL pro všechna vaše volání REST pro vaši entitu (získat, opravit, odstranit).

Pokud má entita další funkce, které jsou k dispozici v rámci jiných cest, jsou uvedeny v části `links` . Dobrým příkladem je přepis, který má samostatnou metodu pro `GET` obsah přepisu.

V2 – přepis:

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

přepis V3:

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

V závislosti na implementaci klienta nemusí být vlastnost k přejmenování k dispozici. Doporučujeme využít využití vrácených hodnot `self` a `links` jako cílové adresy URL vašich volání REST, a ne generovat cesty ve vašem klientovi. Pomocí vrácených adres URL si můžete být jisti, že budoucí změny v cestách nebudou přerušovat váš klientský kód.

### <a name="working-with-collections-of-entities"></a>Práce s kolekcemi entit

Dřív rozhraní v2 API vrátilo všechny dostupné entity v odpovědi. Aby bylo možné podrobnější kontrolu nad očekávanou velikostí odpovědi, ve V3 jsou stránkování všech odpovědí na kolekce. Máte kontrolu nad počtem vrácených entit a posunem stránky. Díky tomuto chování je snadné předpovědět modul runtime procesoru odezvy a je konzistentní s jinými rozhraními API Azure.

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

Vlastnost `values` obsahuje podmnožinu dostupných entit kolekce. Počet a posun lze ovládat pomocí parametrů dotazu `skip` a `top` . Pokud není `@nextLink` null, je k dispozici více dat a další dávku dat lze načíst pomocí Get `$.@nextLink` .

Tato změna vyžaduje volání metody `GET` pro kolekci ve smyčce, dokud nebudou vráceny všechny prvky.

### <a name="creating-transcriptions"></a>Vytváření přepisů

Podrobný popis postupu vytvoření přepisu najdete v tématu [postup v dávkovém přepisu](./batch-transcription.md).

Vytváření přepisů bylo ve verzi V3 změněno, aby bylo možné explicitně nastavit konkrétní možnosti přepisu. Ve vlastnosti se teď dají nastavit všechny (volitelné) vlastnosti konfigurace `properties` .
Verze v3 teď podporuje několik vstupních souborů, proto vyžaduje seznam adres URL a ne jednu adresu URL, jakou vyžaduje v2. Název vlastnosti byl přejmenován z `recordingsUrl` na `contentUrls` . Funkce analýzy mínění v přepisech byla v systému V3 odstraněna. Místo toho doporučujeme použít [analýzu textu](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) služby Microsoft devnímání.

Nová vlastnost `timeToLive` v části `properties` může pomáhat při vyřazování stávajících dokončených entit. `timeToLive`Určuje dobu trvání, po jejímž uplynutí bude dokončená entita automaticky odstraněna. Nastavte ji na vysokou hodnotu (například `PT12H` ), pokud jsou entity nepřetržitě sledovány, spotřebovány a odstraněny, a jsou proto obvykle zpracovány dlouho před uplynutím 12 hodin.

V2 přepis textu žádosti POST:

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

text požadavku verze V3 přepis:

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

### <a name="format-of-v3-transcription-results"></a>Formát V3 přepisu výsledků

Schéma výsledků přepisu bylo mírně změněno tak, aby bylo v souladu s přepisy vytvořenými koncovými body v reálném čase. Podrobný popis nového formátu najdete v tématu [postup v dávkovém přepisu](./batch-transcription.md). Schéma výsledku je Publikováno v našem [úložišti ukázek GitHub](https://aka.ms/csspeech/samples) v části `samples/batch/transcriptionresult_v3.schema.json` .

Názvy vlastností jsou nyní ve stylu CamelCase-použita a hodnoty pro kanál a mluvčí používají celočíselné typy. Pro zarovnání formátu trvání s ostatními rozhraními API Azure je teď ve formátu, jak je popsáno v ISO 8601.

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

### <a name="getting-the-content-of-entities-and-the-results"></a>Získání obsahu entit a výsledků

V v2 jsou odkazy na vstupní nebo výsledné soubory vložené s ostatními metadaty entit. V rámci vylepšení verze V3 existuje jasné oddělení metadat entit, které je vráceno funkcí GET on `$.self` a podrobností a přihlašovacích údajů pro přístup k souborům výsledků. Toto oddělení pomáhá chránit zákaznická data a umožňuje přesné řízení doby platnosti přihlašovacích údajů.

V v3 existuje vlastnost s názvem v `files` části odkazy pro případ, že entita zpřístupňuje data (datové sady, přepisy, koncové body, vyhodnocení). K získání `$.links.files` přístupu k obsahu jednotlivých souborů vrátí seznam souborů a adresu URL SAS. Chcete-li řídit dobu platnosti adres URL SAS, je `sasValidityInSeconds` možné použít parametr dotazu k určení doby života.

V2 – přepis:

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

přepis V3:

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

Výsledkem může být `$.links.files` :

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

`kind`Určuje formát obsahu souboru. V případě přepisů jsou soubory typu `TranscriptionReport` souhrnem úlohy a soubory typu `Transcription` jsou výsledkem samotné úlohy.

### <a name="customizing-models"></a>Přizpůsobení modelů

Před v3m bylo při školení modelu rozdíl mezi "akustickým modelem" a "jazykovým modelem". Výsledkem tohoto rozdílu je nutnost zadat více modelů při vytváření koncových bodů nebo přepisů. Pro zjednodušení tohoto procesu pro volající jsme odebrali rozdíly a provedli vše závislé na obsahu datových sad, které se používají pro školení modelů. Při této změně vytváření modelů teď podporuje smíšenou datovou sadu (data v jazyce a akustická data). Koncové body a Přepisy teď vyžadují jenom jeden model.

V důsledku této změny je potřeba, aby byl `kind` v příspěvku odebraný a `datasets[]` mohl by nyní obsahovat více datových sad stejného nebo smíšeného druhu.

Pro zlepšení výsledků poučeného modelu se akustická data automaticky používají interně pro jazykové školení. Obecně platí, že modely vytvořené prostřednictvím rozhraní V3 API poskytují přesnější výsledky než modely vytvořené pomocí rozhraní v2 API.

### <a name="retrieving-base-and-custom-models"></a>Načítají se základní a vlastní modely.

Aby bylo možné zjednodušit získání dostupných modelů, verze V3 oddělí kolekce "základních modelů" od zákazníka, který vlastní "přizpůsobené modely". Tyto dvě trasy jsou nyní `GET /speechtotext/v3.0/models/base` a `GET /speechtotext/v3.0/models/` .

Dříve byly vráceny všechny modely společně v rámci jedné odpovědi.

### <a name="name-of-an-entity"></a>Název entity

Název vlastnosti `name` je přejmenován na `displayName` . Tato volba je zarovnána na jiná rozhraní API Azure, aby neoznačovala vlastnosti identity. Hodnota této vlastnosti nesmí být jedinečná a může být změněna po vytvoření entity pomocí `PATCH` .

V2 – přepis:

```json
{
    "name": "Transcription using locale en-US"
}
```

přepis V3:

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>Přístup k odkazovaným entitám

V odkazovaných entitách v2 byly vždycky vloženy například používané modely koncového bodu. Vnořování entit vedlo k velkým odpovědím a spotřebitelé zřídka využili vnořený obsah. Pokud chcete zmenšit velikost odpovědi a zvýšit výkon pro všechny uživatele rozhraní API, odkazované entity už nejsou v odpovědi vložené. Místo toho se používá odkaz na jinou entitu, kterou lze použít přímo. Tento odkaz lze použít pro následné `GET` (Jedná se také o adresu URL), a to za stejným vzorem jako `self` odkaz.

V2 – přepis:

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

přepis V3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

V případě, že potřebujete spotřebovat podrobnosti odkazovaného modelu, jak je znázorněno v předchozím příkladu, Zjednodušte si vystavení GET `$.model.self` .

### <a name="retrieving-endpoint-logs"></a>Načítání protokolů koncových bodů

Verze V2 služby podporovala protokolování odpovědí koncových bodů. Aby bylo možné načíst výsledky koncového bodu s v2, jedna z nich musela vytvořit "Export dat", který představoval snímek výsledků definovaných časovým rozsahem. Proces exportu dávek dat se stane neflexibilní. Rozhraní V3 API poskytuje přístup ke každému jednotlivému souboru a umožňuje iteraci prostřednictvím nich.

Úspěšně se spustil koncový bod V3:

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

Odpověď pro GET `$.links.logs` :

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

Stránkování protokolů koncového bodu funguje podobně jako u všech ostatních kolekcí, s výjimkou toho, že není možné zadat žádný posun. Kvůli velkému množství dat, která jsou k dispozici, je nutné implementovat stránkování řízené serverem.

V systému V3 lze každý protokol koncového bodu odstranit jednotlivě vydáním odstranění na `self` souboru nebo pomocí příkazu Odstranit `$.links.logs` . Chcete-li zadat koncová data, `endDate` lze do žádosti přidat parametr dotazu.

### <a name="using-custom-properties"></a>Použití vlastních vlastností

Chcete-li oddělit vlastní vlastnosti z volitelných vlastností konfigurace, jsou nyní všechny explicitně pojmenované vlastnosti umístěny ve `properties` vlastnosti a všechny vlastnosti definované volajícími jsou nyní umístěny ve `customProperties` Vlastnosti.

V2 – přepis entity

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

V3 – přepis entity

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

Tato změna také povolila použití správných typů u všech explicitně pojmenovaných vlastností v `properties` (například bool namísto řetězce).

### <a name="response-headers"></a>Hlavičky odpovědi

hodnota V3 již nevrací hlavičku `Operation-Location` kromě záhlaví `Location` požadavků post. Hodnota obou hlaviček použitá pro stejné použití. Nyní `Location` se vrací pouze.

Vzhledem k tomu, že nová verze rozhraní API je teď spravovaná pomocí Azure API Management (APIM), hlavičky související s omezováním `X-RateLimit-Limit` `X-RateLimit-Remaining` a `X-RateLimit-Reset` nejsou obsažené v hlavičkách odpovědi.

### <a name="accuracy-tests"></a>Testy přesnosti

Testy přesnosti byly přejmenovány na hodnocení, protože nový název popisuje lepší význam, co představují. Cesty k příspěvkům jsou například "https://{region}. API. vnímání. Microsoft. com/speechtotext/v 3.0/vyhodnocení".
