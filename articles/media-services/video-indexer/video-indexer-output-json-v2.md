---
title: Prozkoumejte výstup video indexeru vytvořený rozhraním API v2 – Azure
titleSuffix: Azure Media Services
description: Toto téma zkoumá výstup Azure Media Services Video Indexer vytvořený rozhraním API v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/09/2019
ms.author: juliako
ms.openlocfilehash: 2fac5e07f9646c4fc0fac7b1be53b5a5ac1ea803
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245925"
---
# <a name="examine-the-video-indexer-output-produced-by-api"></a>Prohlédněte si výstup videoindexeru vytvořený rozhraním API

Při volání **získat video index** ROZHRANÍ API a stav odpovědi je v pořádku, dostanete podrobný výstup JSON jako obsah odpovědi. Obsah JSON obsahuje podrobnosti o zadané video přehledy. Poznatky zahrnují: přepisy, ZOC, tváře, témata, bloky, atd. Každý typ přehledu zahrnuje instance časových rozsahů, které se zobrazují, když se přehled zobrazí ve videu. 

1. Chcete-li načíst soubor JSON, zavolejte [rozhraní Get Video Index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?)
1. Pokud vás také zajímají konkrétní artefakty, zavolejte [na adresu URL API url, která se vám podaří získat video artefakt](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?)

    Ve volání rozhraní API zadejte požadovaný typ artefaktu (OCR, Tváře, Klíčové rámce atd.)

Souhrnné přehledy videa můžete také vizuálně prozkoumat stisknutím tlačítka **Přehrát** na videu na webu [Video Indexer.](https://www.videoindexer.ai/) Další informace naleznete v [tématu Zobrazení a úpravy přehledů videa](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Tento článek zkoumá obsah JSON vrácené **získat video index** rozhraní API. 

> [!NOTE]
> Vypršení platnosti všech přístupových tokenů v video indexeru je jedna hodina.


## <a name="root-elements"></a>Kořenové prvky

|Name (Název)|Popis|
|---|---|
|accountId|ID účtu VI seznamu skladeb.|
|id|ID seznamu skladeb.|
|jméno|Název seznamu stop.|
|description|Popis seznamu skladeb.|
|userName|Jméno uživatele, který seznam stop vytvořil.|
|Vytvořen|Čas vytvoření seznamu stop.|
|soukromíRežim|Režim ochrany osobních údajů seznamu stop (Soukromé/Veřejné).|
|state|Seznam skladeb (nahrané, zpracovávané, zpracované, neúspěšné, v karanténě).|
|isOwned|Označuje, zda byl seznam stop vytvořen aktuálním uživatelem.|
|Iseditable|Označuje, zda je aktuální uživatel oprávněn upravovat seznam stop.|
|isBase|Označuje, zda je seznam stop základním seznamem stop (video) nebo seznamem videí z jiných videí (odvozených).|
|durationInSeconds|Celková doba trvání seznamu stop.|
|souhrnně přehled|Obsahuje jeden [summarizedInsights](#summarizedinsights).
|Videa|Seznam [videí,](#videos) která vytváří seznam videí.<br/>Pokud tento seznam videí vytvořených časovými rozsahy jiných videí (odvozených) budou videa v tomto seznamu obsahovat pouze data z zahrnutých časových rozsahů.|

```json
{
  "accountId": "bca61527-1221-bca6-1527-a90000002000",
  "id": "abc3454321",
  "name": "My first video",
  "description": "I am trying VI",
  "userName": "Some name",
  "created": "2018/2/2 18:00:00.000",
  "privacyMode": "Private",
  "state": "Processed",
  "isOwned": true,
  "isEditable": false,
  "isBase": false,
  "durationInSeconds": 120, 
  "summarizedInsights" : { . . . }
  "videos": [{ . . . }]
}
```

## <a name="summarizedinsights"></a>souhrnně přehled

Tato část zobrazuje souhrn přehledů.

|Atribut | Popis|
|---|---|
|jméno|Název videa. Například Azure Monitor.|
|id|ID videa. Například 63c6d532ff.|
|soukromíRežim|Rozdělení může mít jeden z následujících režimů: **Soukromé**, **Veřejné**. **Veřejné** – video je viditelné všem ve vašem účtu a všem, kdo mají odkaz na video. **Soukromé** – video je viditelné všem ve vašem účtu.|
|doba trvání|Obsahuje jednu dobu trvání, která popisuje čas, kdy došlo k pochopení. Doba trvání je v sekundách.|
|miniaturaVideoId|ID videa, ze kterého byla miniatura pořízena.
|miniaturyId|ID miniatury videa. Chcete-li získat skutečnou miniaturu, zavolejte [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) a předat ji thumbnailVideoId a thumbnailId.|
|Tváře|Může obsahovat nula nebo více ploch. Podrobnější informace naleznete v [tématu tváře](#faces).|
|klíčová slova|Může obsahovat nula nebo více klíčových slov. Podrobnější informace naleznete v [tématu klíčová slova](#keywords).|
|Pocity|Může obsahovat nula nebo více mínění. Podrobnější informace naleznete v [tématu mínění](#sentiments).|
|zvukové efekty| Může obsahovat nula nebo více audioEffects. Podrobnější informace naleznete v [tématu audioEffects](#audioEffects).|
|Popisky| Může obsahovat nulové nebo více popisků. Podrobné další informace naleznete v [tématu popisky](#labels).|
|Značky| Může obsahovat nula nebo více značek. Podrobnější informace naleznete v [tématu značky](#brands).|
|statistiky | Podrobnější informace naleznete ve [statistikách](#statistics).|
|Emoce| Může obsahovat nula nebo více emocí. Podrobnější informace naleznete v tématu [emoce](#emotions).|
|Témata|Může obsahovat nula nebo více témat. [Témata](#topics) vhled.|

## <a name="videos"></a>Videa

|Name (Název)|Popis|
|---|---|
|accountId|ID účtu VI videa.|
|id|Id videa.|
|jméno|Jméno toho videa.
|state|Stav videa (nahrané, zpracovávané, zpracované, neúspěšné, v karanténě).|
|zpracováníPrůběh|Průběh zpracování během zpracování (například 20 %).|
|failureCode|Kód selhání, pokud se nepodařilo zpracovat (například "UnsupportedFileType").|
|zpráva o chybě|Zpráva o selhání, pokud se nepodařilo zpracovat.|
|externalId|Externí ID videa (pokud je určeno uživatelem).|
|externalUrl|Externí adresa URL videa (pokud je zadána uživatelem).|
|zprostředkovatele identity|Externí metadata videa (pokud jsou zadána uživatelem).|
|isAdult|Označuje, zda bylo video ručně zkontrolováno a identifikováno jako video pro dospělé.|
|Postřehy|Objekt insights. Další informace naleznete v tématu [přehledy](#insights).|
|miniaturyId|ID miniatury videa. Chcete-li získat skutečné miniatury volání [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) a předat ji ID videa a thumbnailId.|
|publishedUrl|Url pro streamování videa.|
|publikovánoUrlProxy|Adresa URL pro streamování videa z (pro zařízení Apple).|
|viewToken|Krátkodobý zobrazení token pro streamování videa.|
|sourceLanguage|Zdrojový jazyk videa.|
|language|Skutečný jazyk videa (překlad).|
|indexingPreset|Přednastavení použité k indexování videa.|
|streamingPreset|Přednastavení použité k publikování videa.|
|jazykováModelId|Model CRIS použitý k přepisu videa.|
|statistiky | Další informace naleznete v [tématu statistiky](#statistics).|

```json
{
    "videos": [{
        "accountId": "2cbbed36-1972-4506-9bc7-55367912df2d",
        "id": "142a356aa6",
        "state": "Processed",
        "privacyMode": "Private",
        "processingProgress": "100%",
        "failureCode": "General",
        "failureMessage": "",
        "externalId": null,
        "externalUrl": null,
        "metadata": null,
        "insights": {. . . },
        "thumbnailId": "89d7192c-1dab-4377-9872-473eac723845",
        "publishedUrl": "https://videvmediaservices.streaming.mediaservices.windows.net:443/d88a652d-334b-4a66-a294-3826402100cd/Xamarine.ism/manifest",
        "publishedProxyUrl": null,
        "viewToken": "Bearer=<token>",
        "sourceLanguage": "En-US",
        "language": "En-US",
        "indexingPreset": "Default",
        "linguisticModelId": "00000000-0000-0000-0000-000000000000"
    }],
}
```
### <a name="insights"></a>Postřehy

Každý přehled (například řádky přepisu, tváře, značky atd.) obsahuje seznam jedinečných prvků (například face1, face2, face3) a každý prvek má vlastní metadata a seznam svých instancí (což jsou časové rozsahy s dalšími volitelnými metadaty).

Tvář může mít ID, jméno, miniaturu, další metadata a seznam jeho časových instancí (například: 00:00:05 – 00:00:10, 00:01:00 - 00:02:30 a 00:41:21 – 00:41:49.) Každá časová instance může mít další metadata. Například souřadnice obdélníku plochy (20 230,60,60).

|Version|Verze kódu|
|---|---|
|sourceLanguage|Zdrojový jazyk videa (za předpokladu jednoho hlavního jazyka). Ve formě řetězce [BCP-47.](https://tools.ietf.org/html/bcp47)|
|language|Jazyk postřehy (přeložený ze zdrojového jazyka). Ve formě řetězce [BCP-47.](https://tools.ietf.org/html/bcp47)|
|Přepis|Přepis [transcript](#transcript) vhled.|
|Ocr|Přehled [ocr.](#ocr)|
|klíčová slova|Přehled [klíčových slov.](#keywords)|
|blocks|Může obsahovat jeden nebo více [bloků.](#blocks)|
|Tváře|[Tváře](#faces) vhled.|
|Popisky|[Popisky](#labels) vhled.|
|Záběry|[Záběry](#shots) pohled.|
|Značky|[Značky](#brands) vhled.|
|zvukové efekty|[AudioEffects](#audioEffects) insight.|
|Pocity|[Pocity](#sentiments) vhled.|
|visualContentModeon|[VisualContentModeon](#visualcontentmoderation) insight.|
|moderování textalContentModeon|[TextalContentModeon](#textualcontentmoderation) insight.|
|Emoce| Emoce [emotions](#emotions) vhled.|
|Témata|[Témata](#topics) vhled.|

Příklad:

```json
{
  "version": "0.9.0.0",
  "sourceLanguage": "en-US",
  "language": "es-ES",
  "transcript": ...,
  "ocr": ...,
  "keywords": ...,
  "faces": ...,
  "labels": ...,
  "shots": ...,
  "brands": ...,
  "audioEffects": ...,
  "sentiments": ...,
  "visualContentModeration": ...,
  "textualContentModeration": ...
}
```

#### <a name="blocks"></a>blocks

Atribut | Popis
---|---
id|ID bloku.|
Instance|Seznam časových rozsahů tohoto bloku.|

#### <a name="transcript"></a>Přepis

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

#### <a name="ocr"></a>Ocr

|Name (Název)|Popis|
|---|---|
|id|ID řádku OCR.|
|text|Text Rozpoznávání OCR.|
|spolehlivost|Uznání důvěry.|
|language|Jazyk OCR.|
|Instance|Seznam časových rozsahů, ve kterých se tento rozpoznávání OCR zobrazil (stejné rozpoznávání OCR se může zobrazit vícekrát).|
|height|Výška obdélníku OCR|
|top|Horní umístění v px|
|left| Levé umístění v px|
|šířka|Šířka obdélníku OCR|

```json
"ocr": [
    {
      "id": 0,
      "text": "LIVE FROM NEW YORK",
      "confidence": 675.971,
      "height": 35,
      "language": "en-US",
      "left": 31,
      "top": 97,
      "width": 400,      
      "instances": [
        {
          "start": "00:00:26",
          "end": "00:00:52"
        }
      ]
    }
  ],
```

#### <a name="keywords"></a>klíčová slova

|Name (Název)|Popis|
|---|---|
|id|ID klíčového slova.|
|text|Text klíčového slova.|
|spolehlivost|Jistota rozpoznávání klíčového slova.|
|language|Jazyk klíčového slova (při překladu).|
|Instance|Seznam časových rozsahů, ve kterých se toto klíčové slovo zobrazilo (klíčové slovo se může zobrazit vícekrát).|

```json
{
    id: 0,
    text: "technology",
    confidence: 1,
    language: "en-US",
    instances: [{
            adjustedStart: "0:05:15.782",
            adjustedEnd: "0:05:16.249",
            start: "0:05:15.782",
            end: "0:05:16.249"
    },
    {
            adjustedStart: "0:04:54.761",
            adjustedEnd: "0:04:55.228",
            start: "0:04:54.761",
            end: "0:04:55.228"
    }]
}
```

#### <a name="faces"></a>Tváře

|Name (Název)|Popis|
|---|---|
|id|Id obličeje.|
|jméno|Jméno obličeje. Může to být "Neznámý #0, identifikovaná celebrita nebo zákazník vyškolený člověk.|
|spolehlivost|Důvěra v identifikaci obličeje.|
|description|Popis celebrity. |
|miniaturyId|Id miniatury této tváře.|
|knownPersonId|Pokud se jedná o známou osobu, její vnitřní ID.|
|referenceId|Pokud se jedná o celebritu Bing, její ID Bingu.|
|referenceTyp|V současné době jen Bing.|
|title|Pokud se jedná o celebritu, její název (například "Generální ředitel společnosti Microsoft").|
|Imageurl|Pokud se jedná o celebritu, její obraz url.|
|Instance|Jedná se o případy, kdy se tvář objevila v daném časovém rozsahu. Každá instance má také thumbnailsId. |

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

#### <a name="labels"></a>Popisky

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

#### <a name="scenes"></a>Scény

|Name (Název)|Popis|
|---|---|
|id|ID místa činu.|
|Instance|Seznam časových rozsahů této scény (scéna může mít pouze 1 instanci).|

```json
"scenes":[  
    {  
      "id":0,
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },
    {  
      "id":1,
      "instances":[  
          {  
            "start":"0:00:06.34",
            "end":"0:00:47.047",
            "duration":"0:00:40.707"
          }
      ]
    },

]
```

#### <a name="shots"></a>Záběry

|Name (Název)|Popis|
|---|---|
|id|Id výstřelu.|
|Klíčových snímků|Seznam klíčových snímků v rámci snímku (každý má ID a seznam časových rozsahů instancí). Každá instance keyFrame má pole thumbnailId, které obsahuje ID miniatury klíčového prvku.|
|Instance|Seznam časových rozsahů tohoto snímku (snímek může mít pouze 1 instanci).|

```json
"shots":[  
    {  
      "id":0,
      "keyFrames":[  
          {  
            "id":0,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:00.209",
                  "end":"0:00:00.251",
                  "duration":"0:00:00.042"
                }
            ]
          },
          {  
            "id":1,
            "instances":[  
                {  
                  "thumbnailId":"00000000-0000-0000-0000-000000000000",
                  "start":"0:00:04.755",
                  "end":"0:00:04.797",
                  "duration":"0:00:00.042"
                }
            ]
          }
      ],
      "instances":[  
          {  
            "start":"0:00:00",
            "end":"0:00:06.34",
            "duration":"0:00:06.34"
          }
      ]
    },

]
```

#### <a name="brands"></a>Značky

Obchodní a produktové značky zjištěné v přepisu řeči na text a/nebo video ocr. To nezahrnuje vizuální rozpoznávání značek nebo detekce loga.

|Name (Název)|Popis|
|---|---|
|id|ID značky.|
|jméno|Jméno značky.|
|referenceId | Přípona značky wikipedia url. Například "Target_Corporation" je přípona [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | Značka je Wikipedia url, pokud existuje. Například [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Popis značek.|
|tags|Seznam předdefinovaných značek, které byly přidruženy k této značce.|
|spolehlivost|Hodnota spolehlivosti detektoru značky Video Indexer (0-1).|
|Instance|Seznam časových rozsahů této značky. Každá instance má brandType, který označuje, zda se tato značka objevila v přepisu nebo v rozpoznávání OCR.|

```json
"brands": [
{
    "id": 0,
    "name": "MicrosoftExcel",
    "referenceId": "Microsoft_Excel",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft_Excel",
    "referenceType": "Wiki",
    "description": "Microsoft Excel is a sprea..",
    "tags": [],
    "confidence": 0.975,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 00: 31.3000000",
        "end": "00: 00: 39.0600000"
    }
    ]
},
{
    "id": 1,
    "name": "Microsoft",
    "referenceId": "Microsoft",
    "referenceUrl": "http: //en.wikipedia.org/wiki/Microsoft",
    "description": "Microsoft Corporation is...",
    "tags": [
    "competitors",
    "technology"
    ],
    "confidence": 1.0,
    "instances": [
    {
        "brandType": "Transcript",
        "start": "00: 01: 44",
        "end": "00: 01: 45.3670000"
    },
    {
        "brandType": "Ocr",
        "start": "00: 01: 54",
        "end": "00: 02: 45.3670000"
    }
    ]
}
]
```

#### <a name="statistics"></a>statistiky

|Name (Název)|Popis|
|---|---|
|CorrespondenceCount|Počet korespondence ve videu.|
|SpeakerWordCount|Počet slov na řečníka.|
|SpeakerNumberOfFragments|Množství fragmentů, které má reproduktor ve videu.|
|ReproduktorLongestMonolog|Nejdelší monolog mluvčího. Pokud má reproduktor v monologu ticho, je součástí balení. Ticho na začátku a na konci monologu je odstraněno.| 
|MluvčíTalkToListenRatio|Výpočet je založen na čase stráveném na monologu mluvčího (bez ticha mezi nimi) děleno celkovým časem videa. Čas se zaokrouhlí na třetí desetinnou čárku.|

#### <a name="audioeffects"></a><a id="audioEffects"/>zvukové efekty

|Name (Název)|Popis|
|---|---|
|id|ID zvukového efektu.|
|type|Typ zvukového efektu (například Tleskání, Řeč, Ticho).|
|Instance|Seznam časových rozsahů, ve kterých se tento zvukový efekt objevil.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Clapping",
    "instances": [
    {
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
        "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>Pocity

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

#### <a name="visualcontentmoderation"></a>visualContentModeon

VisualContentModeon blok obsahuje časové rozsahy, které Video Indexer zjištěno, že potenciálně obsah pro dospělé. Pokud visualContentModeration je prázdný, neexistuje žádný obsah pro dospělé, který byl identifikován.

Videa, u kterých se zjistí, že obsahují obsah pro dospělé nebo pikantní obsah, mohou být k dispozici pouze pro soukromé zobrazení. Uživatelé mají možnost podat žádost o lidské přezkoumání obsahu, v takovém případě isadult atribut bude obsahovat výsledek lidské recenze.

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

#### <a name="textualcontentmoderation"></a>moderování textalContentModeon 

|Name (Název)|Popis|
|---|---|
|id|ID moderování textového obsahu.|
|zakázanéWordsCount |Počet zakázaných slov.|
|zakázanéWordsRatio |Poměr z celkového počtu slov.|

#### <a name="emotions"></a>Emoce

Video Indexer identifikuje emoce na základě řeči a zvukových podnětů. Identifikované emoce by mohly být: radost, smutek, hněv, nebo strach.

|Name (Název)|Popis|
|---|---|
|id|Id emocí.|
|type|Emoce moment, který byl identifikován na základě řeči a audio podněty. Emoce by mohla být: radost, smutek, hněv, nebo strach.|
|Instance|Seznam časových rozsahů, kde se tato emoce objevila.|

```json
"emotions": [{
    "id": 0,
    "type": "Fear",
    "instances": [{
      "adjustedStart": "0:00:39.47",
      "adjustedEnd": "0:00:45.56",
      "start": "0:00:39.47",
      "end": "0:00:45.56"
    },
    {
      "adjustedStart": "0:07:19.57",
      "adjustedEnd": "0:07:23.25",
      "start": "0:07:19.57",
      "end": "0:07:23.25"
    }]
  },
  {
    "id": 1,
    "type": "Anger",
    "instances": [{
      "adjustedStart": "0:03:55.99",
      "adjustedEnd": "0:04:05.06",
      "start": "0:03:55.99",
      "end": "0:04:05.06"
    },
    {
      "adjustedStart": "0:04:56.5",
      "adjustedEnd": "0:05:04.35",
      "start": "0:04:56.5",
      "end": "0:05:04.35"
    }]
  },
  {
    "id": 2,
    "type": "Joy",
    "instances": [{
      "adjustedStart": "0:12:23.68",
      "adjustedEnd": "0:12:34.76",
      "start": "0:12:23.68",
      "end": "0:12:34.76"
    },
    {
      "adjustedStart": "0:12:46.73",
      "adjustedEnd": "0:12:52.8",
      "start": "0:12:46.73",
      "end": "0:12:52.8"
    },
    {
      "adjustedStart": "0:30:11.29",
      "adjustedEnd": "0:30:16.43",
      "start": "0:30:11.29",
      "end": "0:30:16.43"
    },
    {
      "adjustedStart": "0:41:37.23",
      "adjustedEnd": "0:41:39.85",
      "start": "0:41:37.23",
      "end": "0:41:39.85"
    }]
  },
  {
    "id": 3,
    "type": "Sad",
    "instances": [{
      "adjustedStart": "0:13:38.67",
      "adjustedEnd": "0:13:41.3",
      "start": "0:13:38.67",
      "end": "0:13:41.3"
    },
    {
      "adjustedStart": "0:28:08.88",
      "adjustedEnd": "0:28:18.16",
      "start": "0:28:08.88",
      "end": "0:28:18.16"
    }]
  }
],
```

#### <a name="topics"></a>Témata

Video Indexer dělá odvození z hlavních témat z přepisů. Pokud je to možné, je zahrnuta taxonomie [IPTC](https://iptc.org/standards/media-topics/) druhé úrovně. 

|Name (Název)|Popis|
|---|---|
|id|ID tématu.|
|jméno|Název tématu, například: "Léčiva".|
|referenceId|Popis cesty odrážející hierarchii témat. Například: "Zdraví a pohoda / Lékařství a zdravotní péče / Léčiva".|
|spolehlivost|Skóre spolehlivosti v rozsahu [0,1]. Vyšší je jistější.|
|language|Jazyk použitý v tématu.|
|iptcName|Kódový název média IPTC, pokud je zjištěn.|
|Instance |V současné době Video Indexer neindexuje téma na časové intervaly, takže celé video se používá jako interval.|

```json
"topics": [{
    "id": 0,
    "name": "INTERNATIONAL RELATIONS",
    "referenceId": "POLITICS AND GOVERNMENT/FOREIGN POLICY/INTERNATIONAL RELATIONS",
    "referenceType": "VideoIndexer",
    "confidence": 1,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}, {
    "id": 1,
    "name": "Politics and Government",
    "referenceType": "VideoIndexer",
    "iptcName": "Politics",
    "confidence": 0.9041,
    "language": "en-US",
    "instances": [{
        "adjustedStart": "0:00:00",
        "adjustedEnd": "0:03:36.25",
        "start": "0:00:00",
        "end": "0:03:36.25"
    }]
}]
. . .
```

## <a name="next-steps"></a>Další kroky

[Portál pro vývojáře Video Indexer](https://api-portal.videoindexer.ai)

Informace o tom, jak vložit widgety do aplikace, naleznete v [tématu Vložení widgetů Video Indexer do aplikací](video-indexer-embed-widgets.md). 

