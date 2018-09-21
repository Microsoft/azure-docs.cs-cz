---
title: Inteligentní funkce médií Azure | Dokumentace Microsoftu
description: Při použití služby Azure Media Services, můžete analyzovat vaše audio a video contnet pomocí AudioAnalyzerPreset a VideoAnalyzerPreset.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/24/2018
ms.author: juliako
ms.openlocfilehash: a428f76f1239e7e67b99d05b96d26abd601e89c6
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498687"
---
# <a name="media-intelligence"></a>Inteligentní funkce médií

Azure Media Services REST API v3 umožňuje analyzovat audio a video obsahu. K analýze obsahu, můžete vytvořit **transformace** a odeslat **úlohy** , který používá jednu z těchto předvolby: **AudioAnalyzerPreset** nebo **VideoAnalyzerPreset** . 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** umožňuje extrahovat více insights zvuk ze souboru zvuku nebo videa. Výstup obsahuje soubor JSON (všechny přehledy) a soubor VTT přepisu zvuku. Tato předvolba přijímá vlastnost, která určuje jazyk, který vstupního souboru ve formě [BCP47](https://tools.ietf.org/html/bcp47) řetězec. Zahrnout zvukové insights:

* Přepisování zvukového záznamu – přepis mluveného slova s časovými razítky. Podporuje více jazyků
* Mluvčího indexování – mapování mluvčích a odpovídající mluveného slova
* Provést analýzu subjektivního hodnocení řeči – výstup analýzy mínění na přepisování zvukového záznamu
* Klíčová slova – klíčová slova, která se extrahují z přepisování zvukového záznamu.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** umožňuje extrahovat více audio a video přehledy z videosouboru. Výstup obsahuje soubor JSON (s všechny přehledy), soubor VTT pro přepis videa a kolekce miniatur. Tato předvolba přijímá také [BCP47](https://tools.ietf.org/html/bcp47) string (představující jazyk videa) jako vlastnost. Poznatky z videí zahrnout všechny zvukové přehledy uvedených výše a navíc následující položky:

* Sledování pro rozpoznávání tváře – čas, během které tváře nacházejí ve videu. Každý obličej má id pro rozpoznávání tváře a odpovídající kolekci miniatur
* Visual text – text, který je zjištěn prostřednictvím optické rozpoznávání znaků. Text je čas razítkem a také použít k extrahování klíčových slov (navíc k přepisu zvuku)
* Klíčové snímky – kolekce klíčových snímků, které se extrahují z videa
* Moderování obsahu Visual – část videa, která jsou opatření příznakem jako pro dospělé nebo pikantního ze své podstaty
* Poznámka – výsledek přidávání poznámek videa podle předem daného objektový model

##  <a name="insightsjson-elements"></a>insights.JSON elementy

Výstup obsahuje všechny informace, které byly nalezeny v video nebo zvuk soubor JSON (insights.json). Ve formátu json může obsahovat následující prvky:

### <a name="transcript"></a>přepis

|Název|Popis|
|---|---|
|id|ID řádku.|
|text|Přepis samotný.|
|jazyk|Jazyk přepisu. Určené pro podporu přepisu, kde každý řádek může mít jiný jazyk.|
|instance|Seznam časových rozsahů, ve kterém se tento řádek. Pokud je instance přepisu, bude mít jenom 1 instance.|

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

### <a name="ocr"></a>optické rozpoznávání znaků

|Název|Popis|
|---|---|
|id|ID OCR řádku.|
|text|OCR textu.|
|spolehlivosti|Rozpoznávání spolehlivosti.|
|jazyk|OCR jazyk.|
|instance|Seznam časových rozsahů, ve kterém se objevil tento OCR (stejné OCR může objevit více než jednou).|

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

### <a name="faces"></a>tváří

|Název|Popis|
|---|---|
|id|ID tváře.|
|jméno|Název vzhledu. Může být "Neznámý #0", identifikovaný celebrit nebo trénovaného osoba zákazníka.|
|spolehlivosti|Identifikace spolehlivosti pro rozpoznávání tváře.|
|description|Popis celebrity. |
|thumbnalId|ID miniatury této pro rozpoznávání tváře.|
|knownPersonId|Pokud se jedná o známé osoba, jeho interní ID.|
|referenceId|Pokud je celebrit Bing, jeho ID Bingu.|
|Hodnota referenceType|Aktuálně jenom Bingu.|
|název|Pokud se jedná celebrit, jeho název (například "CEO společnosti Microsoft").|
|imageUrl|Pokud se jedná celebrit jeho adresa url obrázku.|
|instance|Toto jsou instance z kde zobrazovaly plochu v daném časovém rozsahu. Každá instance má také thumbnailsId. |

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

### <a name="shots"></a>snímky

|Název|Popis|
|---|---|
|id|Snímek ID.|
|klíčové snímky|Seznam klíčových snímků v rámci snímku (každý má ID a seznam instancí časových rozsahů). Instance klíčové snímky mají thumbnailId pole s Miniatura na klíčový snímek ID.|
|instance|Seznam časových rozsahů tento snímek (snímky mají jenom 1 instance).|

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

|Název|Popis|
|---|---|
|CorrespondenceCount|Počet srovnávací ve videu.|
|WordCount|Počet slov za mluvčího.|
|SpeakerNumberOfFragments|Množství fragmenty mluvčího se ve videu.|
|SpeakerLongestMonolog|Nejdelší monolog tohoto mluvčího. Pokud má mluvčího silences uvnitř protokolu monolog je zahrnuté. Na začátku a konci protokolu monolog nečinnosti se odstraní.| 
|SpeakerTalkToListenRatio|Výpočet vychází čas strávený na mluvčího protokolu monolog (bez nečinnosti mezi) rozdělené podle celkové doby trvání videa. Čas se zaokrouhlí na třetí desetinné čárky.|


### <a name="sentiments"></a>zabarvení

Zabarvení se agregují podle jejich sentimentType pole (neutrální/kladné nebo záporné). Například 0 0.1, 0.2 0,1.

|Název|Popis|
|---|---|
|id|ID mínění.|
|Průměr |Průměr všech skóre všech instancí tohoto typu mínění – pozitivní nebo neutrální nebo negativní|
|instance|Seznam časových rozsahů, ve kterém se objevil tento mínění.|
|sentimentType |Typ může být "Pozitivní", 'Neutrální' nebo "Záporné".|

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

### <a name="labels"></a>popisky

|Název|Popis|
|---|---|
|id|ID popisku.|
|jméno|Název popisku (například "Computer", "TV").|
|jazyk|Popisek názvu jazyka (při překladu). BCP-47|
|instance|Seznam časových rozsahů, ve kterém se tento popisek (popisek se může objevit více než jednou). Každá instance má pole jistotou. |


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

|Název|Popis|
|---|---|
|id|ID – klíčové slovo.|
|text|Text – klíčové slovo.|
|spolehlivosti|Klíčové slovo rozpoznávání spolehlivosti.|
|jazyk|Jazyk – klíčové slovo (při překladu).|
|instance|Seznam časových rozsahů, ve kterém se nacházela toto klíčové slovo (klíčové slovo může objevit více než jednou).|

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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration obsahuje časových rozsahů, které můžou mít obsah pro dospělé Video Indexer. Pokud visualContentModeration je prázdný, není žádný obsah pro dospělé, který byl identifikován.

Videa, které se nacházejí na obsah pro dospělé nebo pikantního mohou být k dispozici pouze privátní zobrazení. Uživatelé mají možnost odeslat žádost pro lidskou kontrolu obsahu, ve kterém bude obsahovat případ atribut IsAdult výsledek kontrolu člověkem.

|Název|Popis|
|---|---|
|id|ID visual moderování obsahu.|
|adultScore|Hodnocení obsahu pro dospělé (z content moderatoru).|
|racyScore|Hodnocení obsahu pro dospělé (z moderování obsahu).|
|instance|Seznam časových rozsahů, ve kterém se objevil tento visual moderování obsahu.|

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
## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Analýza videa pomocí služby Azure Media Services](analyze-videos-tutorial-with-api.md)
