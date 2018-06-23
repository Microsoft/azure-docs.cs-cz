---
title: Azure media intelligence | Microsoft Docs
description: Pokud používáte službu Azure Media Services, můžete analyzovat vaše audio a video contnet pomocí AudioAnalyzerPreset a VideoAnalyzerPreset.
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
ms.openlocfilehash: c488060b9db0ba482d12eee2394e5149b918950e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331516"
---
# <a name="media-intelligence"></a>Inteligentní funkce médií

Azure Media Services REST API v3 umožňuje analyzovat audio a video obsahu. K analýze obsahu, můžete vytvořit **transformace** a odesílání **úlohy** , používá jednu z těchto přednastavení: **AudioAnalyzerPreset** nebo **VideoAnalyzerPreset** . 

## <a name="audioanalyzerpreset"></a>AudioAnalyzerPreset

**AudioAnalyzerPreset** umožňuje extrahovat více zvuk statistiky ze souboru zvuku a videa. Výstup obsahuje soubor JSON (s všechny statistiky) a soubor VTT pro zvuk přepis. Tato předvolba přijímá vlastnost, která určuje jazyk vstupní soubor ve formátu [BCP47](https://tools.ietf.org/html/bcp47) řetězec. Zvuk Statistika patří:

* Zvuk přepis – přepis mluvené slovo časová razítka. Jsou podporovány více jazyků
* Mluvčího indexování – mapování mluvčí a odpovídající mluvené slovo
* Rozpoznávání řeči postojích analysis – výstup analýzy postojích provést na zvukové přepis
* Klíčová slova – klíčová slova, které se extrahují z zvuk přepis.

## <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

**VideoAnalyzerPreset** umožňuje extrahovat více Statistika audia a videa z videosouboru. Výstup obsahuje soubor JSON (s všechny statistiky), soubor VTT video přepis a kolekci miniatur. Tato předvolba také přijme [BCP47](https://tools.ietf.org/html/bcp47) řetězec (představující jazyk videa) jako vlastnost. Video Statistika zahrnují zvuk Statistika uvedených výše a následující další položky:

* Čelí sledování – čas, během které se nacházejí ve videu řezy. Každý řez má id vzhled a odpovídající kolekci miniatur
* Visual text – text, který je zjišťován pomocí optické rozpoznávání znaků. Text je na čase razítkem a také použitou k extrakci klíčová slova (kromě zvuk přepis)
* Klíčové snímky – kolekce klíčových snímků, které se extrahují z přehrávání videa
* Visual obsahu přerušování – část videa, která jsou opatření příznakem jako dospělého nebo zájem ve své podstatě
* Poznámky – důsledkem zadávání poznámek k videa podle předem definované objektový model

##  <a name="insightsjson-elements"></a>insights.JSON elementy

Výstup obsahuje soubor JSON (insights.json) s všechny přehledy, které nebyly nalezeny video nebo ve zvukovém souboru. Json může obsahovat následující prvky:

### <a name="transcript"></a>přepis

|Název|Popis|
|---|---|
|id|Identifikátor řádku.|
|text|Přepis, sám sebe.|
|jazyk|Přepis jazyk. Určená pro podporu přepis, kde každý řádek může mít jiný jazyk.|
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

### <a name="ocr"></a>rozpoznávání znaků

|Název|Popis|
|---|---|
|id|ID rozpoznávání znaků řádku.|
|text|Text rozpoznávání znaků.|
|Spolehlivosti|Rozpoznávání spolehlivosti.|
|jazyk|Jazyk rozpoznávání znaků.|
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

### <a name="keywords"></a>klíčová slova

|Název|Popis|
|---|---|
|id|ID – klíčové slovo|
|text|Text – klíčové slovo.|
|Spolehlivosti|– Klíčové slovo rozpoznávání spolehlivosti.|
|jazyk|Jazyk – klíčové slovo (při překladu).|
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

### <a name="faces"></a>Řezy

|Název|Popis|
|---|---|
|id|ID řez.|
|jméno|Název řez. Může být "Neznámý #0", zjištěné celebrit nebo osoby vyškolení zákazníka.|
|Spolehlivosti|Identifikace spolehlivosti řez.|
|description|V případě celebrit, její popis. |
|thumbnalId|Id miniaturu této řez.|
|knownPersonId|V případě známé osoby, jeho interní ID.|
|ID reference|V případě celebrit Bing, jeho ID Bing.|
|referenceType.|Aktuálně právě Bing.|
|název|V případě celebrit, její název (například "ředitel společnosti Microsoft").|
|imageUrl|V případě celebrit, jeho adresa url obrázku.|
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

### <a name="labels"></a>popisky

|Název|Popis|
|---|---|
|id|ID popisku.|
|jméno|Název popisku (například "Počítač", 'TV').|
|jazyk|Popisek názvu jazyk (při překladu). BCP 47|
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

### <a name="shots"></a>snímky

|Název|Popis|
|---|---|
|id|Snímek ID.|
|klíčových snímků|Seznam klíčových snímků v rámci snímek (každá má ID a seznam instancí časových rozsahů).|
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
              "start": "00: 00: 00.1670000",
              "end": "00: 00: 00.2000000"
            }
          ]
        }
      ],
      "instances": [
        {
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
              "start": "00: 00: 05.2670000",
              "end": "00: 00: 05.3000000"
            }
          ]
        }
      ],
      "instances": [
        {
          "start": "00: 00: 05.2670000",
          "end": "00: 00: 10.3000000"
        }
      ]
    }
  ]
```


### <a name="sentiments"></a>Chráněny

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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Kurz: Analýza videa pomocí služby Azure Media Services](analyze-videos-tutorial-with-api.md)
