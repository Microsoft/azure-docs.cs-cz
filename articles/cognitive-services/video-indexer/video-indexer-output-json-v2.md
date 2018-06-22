---
title: Vyhledejte ve výstupu Azure Video Indexer vytvořeného rozhraním v2 API | Microsoft Docs
description: Toto téma prověří Video Indexer výstup vytvořeného rozhraním v2 API.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cfowler
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 87301e4ce3c5b7db054b3dd86e8ee1ac5d90d3ca
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309056"
---
# <a name="examine-the-video-indexer-output-produced-by-v2-api"></a>Vyhledejte ve výstupu videa Indexer vytvořeného rozhraním v2 API

> [!Note]
> Rozhraní API V1 videu Indexer jsou už zastaralé a bude odebrána na 1. srpna 2018. Měli byste začít pomocí rozhraní API v2 Video Indexer, aby se zabránilo přerušení.
>
> Pro vývoj pomocí rozhraní API v2 Video Indexer, pokynů naleznete [zde](https://api-portal.videoindexer.ai/). 

Při volání **získat Index Video** rozhraní API a stav odpovědi je v pořádku, získat podrobný výstup JSON jako obsahu odpovědi. Obsah JSON obsahuje podrobnosti o zadaný video statistiky. K přehledům zahrnout dimenze jako: přepisy, ocrs, řezy, témata, bloky, atd. Dimenze mít instancí čas rozsahy, které zobrazit, když je každá dimenze zobrazovaly v videa.  

Souhrnná statistika na video můžete také vizuálně zkoumat stisknutím **přehrání** tlačítko na video na portálu Video Indexer. Další informace najdete v tématu [prohlížení a úpravy video insights](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Tento článek prozkoumá obsah JSON vrácený **získat Index Video** rozhraní API. 

> [!NOTE]
> Vypršení platnosti všech přístupových tokenů v Video indexeru je jedna hodina.


## <a name="root-elements"></a>Kořenových elementů

|Název|Popis|
|---|---|
|ID účtu|VI seznamu stop účet ID.|
|id|ID seznamu stop.|
|jméno|Název seznamu stop.|
|description|Popis seznamu stop.|
|Uživatelské jméno|Jméno uživatele, který vytvořil seznam stop.|
|vytvořené|Čas vytvoření seznamu stop.|
|privacyMode|Režim ochrany osobních údajů v seznamu stop (soukromého a veřejného).|
|state|Seznamu stop (nahrané, zpracování, zpracování, se nezdařila, v karanténě).|
|isOwned|Jestli seznam stop pochází z aktuálního uživatele.|
|iseditable –|Zda je aktuální uživatel oprávnění chcete-li upravit seznam stop.|
|isBase|Jestli seznam stop je základní seznam skladeb (video) nebo seznam stop, které se skládají z jiných videa (Odvozený).|
|durationInSeconds|Celková doba trvání seznamu stop.|
|summarizedInsights|Obsahuje jeden [summarizedInsights](#summarizedinsights).
|videa|Seznam [videa](#videos) vytváření seznamu stop.<br/>Pokud tento seznam stop z sestavený z jiných videa (Odvozený), časové rozsahy videa v tomto seznamu bude obsahovat pouze data z zahrnuté časových rozsahů.|

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

## <a name="summarizedinsights"></a>summarizedInsights

Tato část uvádí přehled k přehledům.

|Atribut | Popis|
|---|---|
|jméno|Název videa. Například monitorování Azure.|
|shortId|ID videa. Například 63c6d532ff.|
|privacyMode|Vaše rozpis může mít jeden z následujících režimů: **privátní**, **veřejné**. **Veřejné** -je viditelná pro všechny uživatele v účtu a na všechny uživatele, který obsahuje odkaz na video. **Privátní** -je viditelná pro všechny uživatele ve vašem účtu.|
|doba trvání|Obsahuje jeden dobu trvání, která popisuje čas výskytu přehledu. Doba trvání je v sekundách.|
|thumbnailUrl|Na videu miniaturu úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Všimněte si, že pokud je privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').|
|řezy|Může obsahovat jeden nebo více řezy. Další informace najdete v tématu [řezy](#faces).|
|Témata|Může obsahovat jedno nebo více témat. Další informace najdete v tématu [témata](#topics).|
|chráněny|Může obsahovat jeden nebo více chráněny. Další informace najdete v tématu [chráněny](#sentiments).|
|audioEffects| Může obsahovat jeden nebo více audioEffects. Další informace najdete v tématu [audioEffects](#audioeffects).|
|značky| Může obsahovat nula nebo více značek. Další informace najdete v tématu [značky](#brands).|
|Statistiky | Další informace najdete v tématu [statistiky](#statistics).|

### <a name="statistics"></a>Statistiky

|Název|Popis|
|---|---|
|CorrespondenceCount|Číslo srovnávací ve videu.|
|WordCount|Počet slov za mluvčího.|
|SpeakerNumberOfFragments|Množství fragmenty mluvčímu má v video.|
|SpeakerLongestMonolog|Nejdelší monolog mluvčího. Pokud má mluvčímu silences uvnitř monolog je součástí. Ticho na začátku a konci monolog se odebere.| 
|SpeakerTalkToListenRatio|Výpočet vychází čas strávený monolog mluvčího (bez nečinnosti v mezi) dělený celkový čas videa. Čas se zaokrouhlí na třetí desetinné čárky.|

## <a name="videos"></a>videa

|Název|Popis|
|---|---|
|ID účtu|VI na video účet ID.|
|id|ID videa.|
|jméno|Název na video.
|state|Stav na videu (nahrané, zpracování, zpracování, se nezdařila, v karanténě).|
|processingProgress|Průběh zpracování během zpracování (například 20 %).|
|failureCode|Kód chyby, pokud se nezdařilo zpracovat (například "UnsupportedFileType").|
|failureMessage|Zpráva selhání, pokud se nepodařilo zpracovat.|
|externalId|Na videu externí id (Pokud je zadána uživatelem).|
|adresy externalUrl|Na videu externí adresu url (Pokud je zadána uživatelem).|
|zprostředkovatele identity|Na videu externí metadata (Pokud je zadána uživatelem).|
|isAdult|Zda se na video byla ručně zkontrolovat a identifikovat jako dospělého video.|
|přehledy|Objekt statistiky.|
|thumbnailUrl|Na videu miniaturu úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Všimněte si, že pokud je privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').|
|publishedUrl|Adresa url Streamovat videa.|
|publishedUrlProxy|Adresa url Streamovat videa z (pro zařízení Apple).|
|viewToken|Token zkrátí zobrazení pro streamování videa.|
|sourceLanguage|Jazyk zdrojového videa.|
|Jazyk|Aktuální jazyk na videu (překlad).|
|indexingPreset|Přednastavení používá k přehrávání videa indexu.|
|streamingPreset|Přednastavení použít k publikování videa.|
|linguisticModelId|CRIS modelu použitému k transcribe videa.|

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
### <a name="insights"></a>přehledy

K přehledům jsou sady dimenze (například přepis řádky, řezy, značky atd.), kde každá dimenze je seznam jedinečných elementy (například face1, ze předu 2, ze předu 3), přičemž každý prvek má svou vlastní metadata a seznam její instance (která jsou časových rozsahů s Další volitelné metadata).

Řez může mít ID, název, na miniaturu, další metadata a seznam její dočasné instance (například: 00:00:05 – 00:00:10: 00:01:00 – 00:02:30 a 00:41:21 – 00:41:49.) Každá instance dočasné můžete mít další metadata. Například se plocha obdélníku koordinuje (20,230,60,60).

|Verze|Verze kódu|
|---|---|
|sourceLanguage|Jazyk zdrojového videa (za předpokladu, že jeden hlavní jazyk). Ve formě [BCP 47](https://tools.ietf.org/html/bcp47) řetězec.|
|Jazyk|Statistika jazyk (překladu ze zdrojového jazyka). Ve formě [BCP 47](https://tools.ietf.org/html/bcp47) řetězec.|
|přepis|[Přepis](#transcript) dimenze.|
|Rozpoznávání znaků|[Rozpoznávání znaků](#ocr) dimenze.|
|Klíčová slova|[Klíčová slova](#keywords) dimenze.|
|řezy|[Řezy](#faces) dimenze.|
|Popisky|[Popisky](#labels) dimenze.|
|snímky|[Snímky](#shots) dimenze.|
|značky|[Značky](#brands) dimenze.|
|audioEffects|[AudioEffects](#audioEffects) dimenze.|
|chráněny|[Chráněny](#sentiments) dimenze.|
|visualContentModeration|[VisualContentModeration](#visualcontentmoderation) dimenze.|
|textualConentModeration|[TextualConentModeration](#textualconentmoderation) dimenze.|

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
  "textualConentModeration": ...
}
```

#### <a name="transcript"></a>přepis

|Název|Popis|
|---|---|
|id|Identifikátor řádku.|
|text|Přepis, sám sebe.|
|Jazyk|Přepis jazyk. Určená pro podporu přepis, kde každý řádek může mít jiný jazyk.|
|instance|Seznam časových rozsahů, kde se objevil tohoto řádku. Pokud je instance přepis, bude mít pouze 1 instancí.|

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

#### <a name="ocr"></a>Rozpoznávání znaků

|Název|Popis|
|---|---|
|id|ID rozpoznávání znaků řádku.|
|text|Text rozpoznávání znaků.|
|Spolehlivosti|Rozpoznávání spolehlivosti.|
|Jazyk|Jazyk rozpoznávání znaků.|
|instance|Seznam časových rozsahů, kde se objevil tento rozpoznávání znaků (stejné rozpoznávání znaků může objevit vícekrát).|

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

#### <a name="keywords"></a>Klíčová slova

|Název|Popis|
|---|---|
|id|Id – klíčové slovo.|
|text|Text – klíčové slovo.|
|Spolehlivosti|– Klíčové slovo rozpoznávání spolehlivosti.|
|Jazyk|Jazyk – klíčové slovo (při překladu).|
|instance|Seznam časových rozsahů, kde se toto klíčové slovo objevil (klíčové slovo může objevit vícekrát).|

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

#### <a name="faces"></a>řezy

|Název|Popis|
|---|---|
|id|ID řez.|
|jméno|Název řez. Může být "Neznámý #0", zjištěné celebrit nebo osoby vyškolení zákazníka.|
|Spolehlivosti|Identifikace spolehlivosti řez.|
|description|Pokud je celebrit, může být jeho popis: "Satya Nadella se narodila v...". |
|thumbnalId|Id miniaturu této řez.|
|knownPersonId|Pokud se jedná o známé osoby, jeho interní ID.|
|ID reference|Pokud je celebrit Bing, jeho ID Bing.|
|referenceType.|Aktuálně právě Bing.|
|název|Pokud je celebrit, její název (například "ředitel společnosti Microsoft").|
|ImageUrl|Pokud je celebrit, jeho adresa url obrázku.|
|instance|Toto jsou instancí z kde tučné zobrazovaly v daném časovém rozsahu. Každá instance má také thumbnailsId. |

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

|Název|Popis|
|---|---|
|id|ID popisku.|
|jméno|Název popisku (například "Počítač", 'TV').|
|Jazyk|Popisek názvu jazyk (při překladu). BCP 47|
|instance|Seznam časových rozsahů, kde tento popisek zobrazovaly (štítek může objevit vícekrát). Každá instance obsahuje pole, spolehlivosti. |


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

#### <a name="shots"></a>snímky

|Název|Popis|
|---|---|
|id|Snímek ID.|
|klíčových snímků|Seznam klíčových snímků v rámci snímek (každá má ID a seznam instancí časových rozsahů). Instance klíčových snímků mít thumbnailId pole s miniaturu na klíčový snímek ID.|
|instance|Seznam časových rozsahů tento snímek (snímky máte pouze 1 instancí).|

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

#### <a name="brands"></a>značky

Firmy a produktu brand názvy v převod řeči na text přepis nebo Video rozpoznávání znaků. Nezahrnuje visual rozpoznávání značky nebo logo zjišťování.

|Název|Popis|
|---|---|
|id|ID značky.|
|jméno|Název značky.|
|ID reference | Přípona adresy URL wikipedia značky. Například "Target_Corporation" se o příponu [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
|referenceUrl | Značku je Wikipedia adresu url, pokud existuje. Příklad: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Popis značky.|
|tags|Seznam předdefinovaných značky, které byly přidruženy k této značky.|
|Spolehlivosti|Hodnota spolehlivosti detektor brand Video Indexer (0-1).|
|instance|Seznam časových rozsahů tuto značku. Každá instance má brandType, který označuje, zda tento brand zobrazovaly v zápis nebo rozpoznávání znaků.|

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

#### <a name="audioeffects"></a>audioEffects

|Název|Popis|
|---|---|
|id|ID zvuk vliv.|
|type|Zvuk vliv typ (například Clapping, rozpoznávání řeči, ticho).|
|instance|Seznam časových rozsahů, kde zobrazovaly tento zvuk vliv.|

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

#### <a name="sentiments"></a>chráněny

Jsou chráněny agregován podle jejich sentimentType pole (neutrální/kladné nebo záporné). Například 0,1 0, 0,1 0,2.

|Název|Popis|
|---|---|
|id|ID postojích.|
|Průměr |Průměr všech skóre všechny instance tohoto typu postojích - neutrální/kladné nebo záporné|
|instance|Seznam časových rozsahů, kde se toto postojích objevil.|

```json
"sentiments": [
{
    "id": 0,
    "averageScore": 0.87,
    "instances": [
    {
        "start": "00:00:23",
        "end": "00:00:41"
    }
    ]
}, {
    "id": 1,
    "averageScore": 0.11,
    "instances": [
    {
        "start": "00:00:13",
        "end": "00:00:21"
    }
    ]
}
]
```

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration obsahuje časových rozsahů, které Video Indexer zjištěn potenciálně obsah pro dospělé. Pokud visualContentModeration je prázdná, neexistuje žádný obsah pro dospělé, která byla identifikována.

Videa, které se nacházejí tak, aby obsahovala obsah pro dospělé nebo zájem může být k dispozici pouze privátní zobrazení. Uživatelé mají možnost odeslat žádost o kontrolu lidského obsahu, ve kterém bude obsahovat případ atribut IsAdult výsledek lidské kontrola.

|Název|Popis|
|---|---|
|id|ID visual přerušování obsahu.|
|adultScore|Skóre pro dospělé (z obsahu moderátora).|
|racyScore|Zájem skóre (z obsahu přerušování).|
|instance|Seznam časových rozsahů, kde se toto visual obsahu přerušování objevil.|

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

#### <a name="textualconentmoderation"></a>textualConentModeration 

|Název|Popis|
|---|---|
|id|Textový obsah přerušování ID.|
|bannedWordsCount |Počet zakázaných slov.|
|bannedWordsRatio |Poměr z celkového počtu slov.|


## <a name="next-steps"></a>Další postup

[Video Indexer rozhraní API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

Informace o tom, jak vložit pomůcky ve vaší aplikaci najdete v tématu [vložit Video Indexer pomůcky do svých aplikací](video-indexer-embed-widgets.md). 

