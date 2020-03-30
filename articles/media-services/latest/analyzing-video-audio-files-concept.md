---
title: Analýza video souborů a zvukových souborů
titleSuffix: Azure Media Services
description: Zjistěte, jak analyzovat zvukový a video obsah pomocí AudioAnalyzerPreset a VideoAnalyzerPreset ve službě Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 01/30/2020
ms.author: juliako
ms.openlocfilehash: 1d28fc37b98493322b9e201ac899b7911dd1d705
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269884"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analýza video souborů a zvukových souborů pomocí Mediálníslužby Azure

Azure Media Services v3 umožňuje extrahovat přehledy z vašich video a audio souborů pomocí Video Indexer. Tento článek popisuje přednastavení analyzátoru Media Services v3, která slouží k extrahování těchto přehledů. Pokud chcete získat podrobnější přehledy, použijte přímo Video Indexer. Chcete-li zjistit, kdy použít přednastavení analyzátoru Video Indexer vs. Media Services, podívejte se na [srovnávací dokument](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Chcete-li analyzovat obsah pomocí přednastavení Media Services v3, vytvořte **transformaci** a odešlete **úlohu,** která používá jedno z těchto přednastavení: [VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset) nebo **AudioAnalyzerPreset**. Kurz, který ukazuje, jak používat **VideoAnalyzerPreset**, najdete [v tématu Analýza videí pomocí Mediálních služeb Azure](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Pokud použijete předvolby analyzátoru videa nebo zvuku, pomocí webu Azure Portal nastavte pro svůj účet 10 rezervovaných jednotek médií S3. Další informace najdete v tématu [Škálování zpracování médií](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Jako důležité připomenutí musíte při používání videoindexeru dodržovat všechny platné zákony a nesmíte používat Video Indexer ani žádnou jinou službu Azure způsobem, který porušuje práva ostatních nebo může být škodlivý pro ostatní. Před nahráním jakýchkoli videí, včetně biometrických údajů, do služby Video Indexer pro zpracování a ukládání, musíte mít všechna příslušná práva, včetně všech příslušných souhlasů, od jednotlivce (osob) ve videu. Informace o dodržování předpisů, ochraně osobních údajů a zabezpečení v programu Video Indexer naleznete ve [smluvních podmínkách služeb Microsoft Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Informace o povinnostech společnosti Microsoft týkající se ochrany osobních údajů a nakládání s vašimi údaji naleznete v Prohlášení společnosti Microsoft [o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement), [podmínkách online služeb](https://www.microsoft.com/licensing/product-licensing/products) ("OST") a [dodatku ke zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Další informace o ochraně osobních údajů, včetně informací o uchovávání, vymazání/zničení údajů, jsou k dispozici v [ost.](../video-indexer/faq.md) Používáním video indexeru souhlasíte s tím, že budete vázáni podmínkami služeb Cognitive Services, OST, DPA a Prohlášením o ochraně osobních údajů.

## <a name="built-in-presets"></a>Vestavěná přednastavení

Služba Media Services v současné době podporuje následující přednastavení přednastavení integrovaného analyzátoru:  

|**Název přednastavení**|**Scénář**|**Podrobnosti**|
|---|---|---|
|[AudioAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analýza zvuku|Přednastavení použije předdefinovanou sadu operací analýzy založených na AI, včetně přepisu řeči. V současné době přednastavení podporuje zpracování obsahu pomocí jediné zvukové stopy, která obsahuje řeč v jednom jazyce. Jazyk datové části zvuku můžete zadat ve vstupu pomocí formátu BCP-47 v oblasti jazykových značek. Podporované jazyky jsou angličtina ("en-US" a "en-GB"), španělština ("es-ES" a "es-MX"), francouzština ("fr-FR"), italština ("it-IT"), japonština ("ja-JP"), portugalština ("pt-BR"), čínština ("zh-CN"), němčina ("de-DE"), arabština ("ar-EG" a "ar-SY"), hindština ("hi-IN"), a korejština ('ko-KR').<br/><br/> Pokud jazyk není zadán nebo nastaven na hodnotu null, automatická detekce jazyka zvolí první zjištěný jazyk a pokračuje ve vybraném jazyce po dobu trvání souboru. Funkce automatického rozpoznávání jazyka aktuálně podporuje angličtinu, čínštinu, francouzštinu, němčinu, italštinu, japonštinu, španělštinu, ruštinu a portugalštinu. Nepodporuje dynamické přepínání mezi jazyky po zjištění prvního jazyka. Funkce automatické detekce jazyka funguje nejlépe u zvukových nahrávek s jasně rozpoznatelnou řečí. Pokud automatická detekce jazyka nenalezne jazyk, přepis se vrátí do angličtiny.|
|[VideoAnalyzerPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analýza zvuku a videa|Extrahuje přehledy (bohatá metadata) ze zvuku i videa a vypíše soubor ve formátu JSON. Můžete určit, zda chcete extrahovat pouze zvukové přehledy při zpracování souboru videa. Další informace naleznete v [tématu Analýza videa](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset)|Detekce tváří přítomných ve videu|Popisuje nastavení, která mají být použita při analýze videa pro detekci všech přítomných tváří.|

### <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

Přednastavení umožňuje extrahovat více zvukových poznatků ze zvukového souboru nebo videosouboru. Výstup obsahuje soubor JSON (se všemi poznatky) a VTT soubor pro audio přepis. Toto přednastavení přijímá vlastnost, která určuje jazyk vstupního souboru ve formě řetězce [BCP47.](https://tools.ietf.org/html/bcp47) Audio přehledy zahrnují:

* **Přepis zvuku**: Přepis mluvených slov s časovými razítky. Podporováno je více jazyků.
* **Indexování mluvčího**: Mapování reproduktorů a odpovídajících mluvených slov.
* **Analýza mínění řeči**: Výstup analýzy mínění provedené při přepisu zvuku.
* **Klíčová slova**: Klíčová slova, která jsou extrahována z přepisu zvuku.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Přednastavení umožňuje extrahovat více zvukových a video přehledů ze souboru videa. Výstup obsahuje soubor JSON (se všemi přehledy), soubor VTT pro přepis videa a kolekci miniatur. Toto přednastavení také přijímá řetězec [BCP47](https://tools.ietf.org/html/bcp47) (představující jazyk videa) jako vlastnost. Přehledy videa zahrnují všechny výše uvedené zvukové přehledy a následující další položky:

* **Sledování obličeje**: Doba, po kterou jsou ve videu přítomny tváře. Každá tvář má ID obličeje a odpovídající kolekci miniatur.
* **Vizuální text**: Text, který je detekován pomocí optického rozpoznávání znaků. Text je časově označen a také slouží k extrahování klíčových slov (kromě audio přepis).
* **Klíčové snímky**: Kolekce klíčových snímků extrahovaných z videa.
* **Moderování vizuálního obsahu**: Část videí označená jako dospělá nebo pikantní povahy.
* **Anotace**: Výsledek anotace videí na základě předdefinovaného objektového modelu

## <a name="insightsjson-elements"></a>insights.json prvky

Výstup obsahuje soubor JSON (insights.json) se všemi poznatky nalezenými ve videu nebo zvuku. JSON může obsahovat následující prvky:

### <a name="transcript"></a>Přepis

|Name (Název)|Popis|
|---|---|
|id|ID linky.|
|text|Přepis sám.|
|language|Jazyk přepisu. Určeno pro podporu přepisu, kde každý řádek může mít jiný jazyk.|
|Instance|Seznam časových rozsahů, kde se tento řádek objevil. Pokud je instance přepis, bude mít pouze 1 instance.|

Příklad:

```json
"transcript": [
{
    "id": 0,
    "text": "Hi I'm Doug from office.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    }
    ]
},
{
    "id": 1,
    "text": "I have a guest. It's Michelle.",
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:02.7200000",
        "end": "00:00:03.9600000"
    }
    ]
}
] 
```

### <a name="ocr"></a>Ocr

|Name (Název)|Popis|
|---|---|
|id|ID řádku OCR.|
|text|Text Rozpoznávání OCR.|
|spolehlivost|Uznání důvěry.|
|language|Jazyk OCR.|
|Instance|Seznam časových rozsahů, ve kterých se tento rozpoznávání OCR zobrazil (stejné rozpoznávání OCR se může zobrazit vícekrát).|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 0.91,
      "language": "en-US",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    },
    {
      "id": 1,
      "text": "NOTICIAS EN VIVO",
      "confidence": 0.9,
      "language": "es-ES",
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:28"
        },
        {
          "start": "00:00:32",
          "end": "00:00:38"
        }
      ]
    }
  ],
```

### <a name="faces"></a>Tváře

|Name (Název)|Popis|
|---|---|
|id|Id obličeje.|
|jméno|Jméno obličeje. Může to být "Neznámý #0", identifikovaná celebrita nebo osoba vyškolená zákazníkem.|
|spolehlivost|Důvěra v identifikaci obličeje.|
|description|Popis celebrity. |
|miniaturyId|Id miniatury této tváře.|
|knownPersonId|Interní ID (pokud se jedná o známou osobu).|
|referenceId|Bing ID (pokud se jedná o bing celebrity).|
|referenceTyp|V současné době jen Bing.|
|title|Název (pokud je to celebrita – například "generální ředitel společnosti Microsoft").|
|Imageurl|Adresa URL obrázku, pokud je to celebrita.|
|Instance|Případy, kdy se tvář objevila v daném časovém rozsahu. Každá instance má také thumbnailsId. |

```json
"faces": [{
    "id": 2002,
    "name": "Xam 007",
    "confidence": 0.93844,
    "description": null,
    "thumbnailId": "00000000-aee4-4be2-a4d5-d01817c07955",
    "knownPersonId": "8340004b-5cf5-4611-9cc4-3b13cca10634",
    "referenceId": null,
    "title": null,
    "imageUrl": null,
    "instances": [{
        "thumbnailsIds": ["00000000-9f68-4bb2-ab27-3b4d9f2d998e",
        "cef03f24-b0c7-4145-94d4-a84f81bb588c"],
        "adjustedStart": "00:00:07.2400000",
        "adjustedEnd": "00:00:45.6780000",
        "start": "00:00:07.2400000",
        "end": "00:00:45.6780000"
    },
    {
        "thumbnailsIds": ["00000000-51e5-4260-91a5-890fa05c68b0"],
        "adjustedStart": "00:10:23.9570000",
        "adjustedEnd": "00:10:39.2390000",
        "start": "00:10:23.9570000",
        "end": "00:10:39.2390000"
    }]
}]
```

### <a name="shots"></a>Záběry

|Name (Název)|Popis|
|---|---|
|id|Id výstřelu.|
|Klíčových snímků|Seznam klíčových snímků v rámci snímku (každý má ID a seznam časových rozsahů instancí). Instance klíčových snímků mají pole thumbnailId s ID miniatury klíčového prvku.|
|Instance|Seznam časových rozsahů tohoto snímku (snímky mají pouze 1 instanci).|

```json
"Shots": [
    {
      "id": 0,
      "keyFrames": [
        {
          "id": 0,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
            "thumbnailId": "00000000-0000-0000-0000-000000000000",  
          "start": "00: 00: 00.2000000",
          "end": "00: 00: 05.0330000"
        }
      ]
    },
    {
      "id": 1,
      "keyFrames": [
        {
          "id": 1,
          "instances": [
            {
                "thumbnailId": "00000000-0000-0000-0000-000000000000",      
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
      "thumbnailId": "00000000-0000-0000-0000-000000000000",
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```

### <a name="statistics"></a>statistiky

|Name (Název)|Popis|
|---|---|
|CorrespondenceCount|Počet korespondence ve videu.|
|Počet slov WordCount|Počet slov na řečníka.|
|SpeakerNumberOfFragments|Množství fragmentů, které má reproduktor ve videu.|
|ReproduktorLongestMonolog|Nejdelší monolog mluvčího. Pokud má reproduktor v monologu ticho, je součástí balení. Ticho na začátku a na konci monologu je odstraněno.|
|MluvčíTalkToListenRatio|Výpočet je založen na čase stráveném na monologu mluvčího (bez ticha mezi nimi) děleno celkovým časem videa. Čas se zaokrouhlí na třetí desetinnou čárku.|


### <a name="sentiments"></a>Pocity

Mínění jsou agregovány podle pole sentimentType (Kladné/Neutrální/Negativní). Například 0-0,1, 0,1-0,2.

|Name (Název)|Popis|
|---|---|
|id|ID sentimentu.|
|averageScore |Průměr všech skóre všech instancí tohoto typu mínění - Kladné/Neutrální/Negativní|
|Instance|Seznam časových rozsahů, kde se tento názor objevil.|
|sentimentTyp |Typ může být "Pozitivní", "Neutrální" nebo Negativní.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "sentimentType": "Positive",
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

### <a name="labels"></a>Popisky

|Name (Název)|Popis|
|---|---|
|id|ID popisku.|
|jméno|Název popisku (například "Počítač", "TV").|
|language|Jazyk názvu popisku (při překladu). BCP-47|
|Instance|Seznam časových rozsahů, ve kterých se tento popisek objevil (popisek se může zobrazit vícekrát). Každá instance má oblast spolehlivosti. |

```json
"labels": [
    {
      "id": 0,
      "name": "person",
      "language": "en-US",
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 00.0000000",
          "end": "00: 00: 25.6000000"
        },
        {
          "confidence": 1.0,
          "start": "00: 01: 33.8670000",
          "end": "00: 01: 39.2000000"
        }
      ]
    },
    {
      "name": "indoor",
      "language": "en-US",
      "id": 1,
      "instances": [
        {
          "confidence": 1.0,
          "start": "00: 00: 06.4000000",
          "end": "00: 00: 07.4670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 09.6000000",
          "end": "00: 00: 10.6670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 11.7330000",
          "end": "00: 00: 20.2670000"
        },
        {
          "confidence": 1.0,
          "start": "00: 00: 21.3330000",
          "end": "00: 00: 25.6000000"
        }
      ]
    }
  ] 
```

### <a name="keywords"></a>klíčová slova

|Name (Název)|Popis|
|---|---|
|id|ID klíčového slova.|
|text|Text klíčového slova.|
|spolehlivost|Jistota rozpoznávání klíčového slova.|
|language|Jazyk klíčového slova (při překladu).|
|Instance|Seznam časových rozsahů, ve kterých se toto klíčové slovo zobrazilo (klíčové slovo se může zobrazit vícekrát).|

```json
"keywords": [
{
    "id": 0,
    "text": "office",
    "confidence": 1.6666666666666667,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:00.5100000",
        "end": "00:00:02.7200000"
    },
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    }
    ]
},
{
    "id": 1,
    "text": "icons",
    "confidence": 1.4,
    "language": "en-US",
    "instances": [
    {
        "start": "00:00:03.9600000",
        "end": "00:00:12.2700000"
    },
    {
        "start": "00:00:13.9900000",
        "end": "00:00:15.6100000"
    }
    ]
}
] 
```

#### <a name="visualcontentmoderation"></a>visualContentModeon

VisualContentModeon blok obsahuje časové rozsahy, které Video Indexer zjištěno, že potenciálně obsah pro dospělé. Pokud visualContentModeration je prázdný, neexistuje žádný obsah pro dospělé, který byl identifikován.

Videa, u kterých se zjistí, že obsahují obsah pro dospělé nebo pikantní obsah, mohou být k dispozici pouze pro soukromé zobrazení. Uživatelé mohou podat žádost o lidské přezkoumání obsahu, `IsAdult` v takovém případě atribut bude obsahovat výsledek lidské kontroly.

|Name (Název)|Popis|
|---|---|
|id|ID moderování vizuálního obsahu.|
|adultScore|Skóre pro dospělé (od moderátora obsahu).|
|racyScore|Pikantní skóre (z moderování obsahu).|
|Instance|Seznam časových rozsahů, ve kterých se tato moderování vizuálního obsahu objevila.|

```json
"VisualContentModeration": [
{
    "id": 0,
    "adultScore": 0.00069,
    "racyScore": 0.91129,
    "instances": [
    {
        "start": "00:00:25.4840000",
        "end": "00:00:25.5260000"
    }
    ]
},
{
    "id": 1,
    "adultScore": 0.99231,
    "racyScore": 0.99912,
    "instances": [
    {
        "start": "00:00:35.5360000",
        "end": "00:00:35.5780000"
    }
    ]
}
] 
```
## <a name="next-steps"></a>Další kroky

[Kurz: Analýza videa pomocí služby Azure Media Services](analyze-videos-tutorial-with-api.md)
