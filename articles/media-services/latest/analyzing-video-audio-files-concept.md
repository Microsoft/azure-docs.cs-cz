---
title: Analýza videosouborů a zvukových souborů
titleSuffix: Azure Media Services
description: Naučte se analyzovat zvuk a video obsah pomocí AudioAnalyzerPreset a VideoAnalyzerPreset v Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: f4784cc2e1b0276caf8326df8fad93b12f0d551d
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490070"
---
# <a name="analyze-video-and-audio-files-with-azure-media-services"></a>Analýza videosouborů a zvukových souborů pomocí Azure Media Services

[!INCLUDE [regulation](../video-indexer/includes/regulation.md)]

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services V3 umožňuje extrakci přehledů z vašich videosouborů a zvukových souborů pomocí Video Indexer. Tento článek popisuje předvolby analyzátoru Media Services V3 použité k extrakci těchto přehledů. Pokud chcete získat podrobnější přehledy, použijte přímo Video Indexer. Chcete-li pochopit, kdy použít předvolby Video Indexer vs. Media Services Analyzer, Projděte si [dokument porovnání](../video-indexer/compare-video-indexer-with-media-services-presets.md).

Existují dva režimy předvolby zvukového analyzátoru, Basic a Standard. Podívejte se na popis rozdílů v následující tabulce.

Chcete-li analyzovat obsah pomocí přednastavených Media Services V3, vytvořte **transformaci** a odešlete **úlohu** , která používá jedno z těchto přednastavení: [VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset) nebo **AudioAnalyzerPreset**. Kurz demonstrující použití **VideoAnalyzerPreset** najdete v tématu [analýza videí pomocí Azure Media Services](analyze-videos-tutorial-with-api.md).

> [!NOTE]
> Pokud používáte přednastavení videa nebo zvuku, pomocí Azure Portal nastavte účet tak, aby měl rezervované jednotky médií s 10 S3, i když to není potřeba. Pro přednastavení zvuku můžete použít buď S1, nebo S2. Další informace najdete v tématu [Škálování zpracování médií](media-reserved-units-cli-how-to.md).

## <a name="compliance-privacy-and-security"></a>Dodržování předpisů, ochrana osobních údajů a zabezpečení

Důležitou připomínkou je, že je nutné dodržovat všechny použitelné zákony v používání Video Indexer a nesmíte používat Video Indexer ani žádnou jinou službu Azure způsobem, který porušuje práva jiných nebo může být škodlivá jiným osobám. Před nahráním videí, včetně jakýchkoli biometrických dat, do služby Video Indexer pro zpracování a ukládání, musíte mít všechna patřičná práva, včetně příslušných souhlasů, od jednotlivých jednotlivců ve videu. Pokud se chcete dozvědět o dodržování předpisů, ochraně osobních údajů a zabezpečení v Video Indexer, [podmínky Cognitive Services](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)Microsoftu. Pro závazky ochrany osobních údajů Microsoftu a jejich zpracování si přečtěte [prohlášení o zásadách ochrany osobních údajů](https://privacy.microsoft.com/PrivacyStatement)od Microsoftu, [podmínky online služeb](https://www.microsoft.com/licensing/product-licensing/products) ("OST") a [doplněk pro zpracování dat](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (DPA). Další informace o ochraně osobních údajů, včetně uchovávání, odstranění nebo zničení dat, jsou k dispozici v OST a [zde](../video-indexer/faq.md). Když použijete Video Indexer, souhlasíte s tím, že jste vázáni na Cognitive Services podmínky, OST, DPA a prohlášení o zásadách ochrany osobních údajů.

## <a name="built-in-presets"></a>Vestavěná přednastavení

Media Services aktuálně podporuje následující předdefinované předvolby analyzátoru:  

|**Název předvolby**|**Scénář**|**Podrobnosti**|
|---|---|---|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analýza standardu zvuk|Přednastavení používá předdefinovanou sadu operací analýzy založených na AI, včetně přepisu řeči. V současné době přednastavení podporuje zpracování obsahu pomocí jedné zvukové stopy, která obsahuje řeč v jednom jazyce. Jazyk pro datovou část zvuku ve vstupu můžete určit pomocí formátu BCP-47 pro ' Language tag-region '. Podporované jazyky jsou angličtina ("en-US" a "en-GB"), španělština (ES-ES a ES-MX), francouzština (' fr-FR '), italština (' IT-IT '), japonština (' IT-Japonsko '), portugalština (' pt-BR '), čínština (' zh-CN '), němčina (' de-DE '), Arabština (' ar-EG ' a ' ar-SY '), ruština (' ru-RU '), hindština (' Hi-IN ') a korejština (' ko-KR ').<br/><br/> Pokud jazyk není zadán nebo je nastaven na hodnotu null, automatické rozpoznávání jazyka zvolí první nalezený jazyk a pokračuje s vybraným jazykem po dobu trvání souboru. Funkce automatického rozpoznávání jazyka aktuálně podporuje angličtinu, čínštinu, francouzštinu, němčinu, italštinu, japonštinu, španělštinu, ruštinu a portugalštinu. Po zjištění prvního jazyka nepodporuje dynamické přepínání mezi jazyky. Funkce automatického rozpoznávání jazyka funguje nejlépe se zvukovým záznamem, který má jasně discernible řeč. Pokud automatické zjišování jazyka nenajde jazyk, přepis se vrátí do angličtiny.|
|[AudioAnalyzerPreset](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)|Analýza základního zvuku|Tento režim provádí přepis a generování VTT souboru titulků a titulků v textu. Výstup tohoto režimu zahrnuje soubor JSON Insights, včetně informací o klíčových slovech, přepisu a časování. V tomto režimu nejsou zahrnuté automatické rozpoznávání jazyka a diarizationy mluvčího. Seznam podporovaných jazyků je k dispozici [zde](#built-in-presets) .|
|[VideoAnalyzerPreset](/rest/api/media/transforms/createorupdate#videoanalyzerpreset)|Analýza zvuku a videa|Extrahuje přehledy (bohatá metadata) z zvukového i videa a vytvoří výstup souboru formátu JSON. Můžete určit, jestli chcete při zpracování videosouboru jenom extrahovat zvukové poznatky. Další informace najdete v tématu [Analýza videa](analyze-videos-tutorial-with-api.md).|
|[FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset)|Zjištění plošek přítomných ve videu|Popisuje nastavení, která se mají použít při analýze videa pro detekci všech plošek přítomných.|

### <a name="audioanalyzerpreset-standard-mode"></a>Standardní režim AudioAnalyzerPreset

Přednastavení umožňuje extrahovat z zvukového nebo videosouboru více zvukových přehledů.

Výstup obsahuje soubor JSON (se všemi přehledy) a soubor VTT pro přepis zvuku. Tato předvolba přijímá vlastnost, která určuje jazyk vstupního souboru ve formě [BCP47](https://tools.ietf.org/html/bcp47) řetězce. Mezi zvukové poznatky patří:

* **Přepis zvuku**: přepis mluvených slov s časovými razítky. Podporuje se několik jazyků.
* **Indexování mluvčího**: mapování mluvčích a odpovídajících mluveného slova.
* **Analýza mínění řeči**: výstup analýzy mínění provedených na přepisu zvuku.
* **Klíčová slova**: klíčová slova, která jsou extrahována ze zvukového přepisu.

### <a name="audioanalyzerpreset-basic-mode"></a>Základní režim AudioAnalyzerPreset

Přednastavení umožňuje extrahovat z zvukového nebo videosouboru více zvukových přehledů.

Výstup obsahuje soubor JSON a soubor VTT pro přepis zvuku. Tato předvolba přijímá vlastnost, která určuje jazyk vstupního souboru ve formě [BCP47](https://tools.ietf.org/html/bcp47) řetězce. Výstup obsahuje:

* **Přepis zvuku**: přepis mluvených slov s časovými razítky. Podporuje se víc jazyků, ale nezahrnují se automatické rozpoznávání jazyka a mluvčí diarization.
* **Klíčová slova**: klíčová slova, která jsou extrahována ze zvukového přepisu.

### <a name="videoanalyzerpreset"></a>VideoAnalyzerPreset

Přednastavení umožňuje extrahovat z videosouboru více zvukových a vizuálních přehledů. Výstup obsahuje soubor JSON (se všemi poznatky), soubor VTT pro přepis videa a kolekci miniatur. Tato předvolba také přijímá řetězec [BCP47](https://tools.ietf.org/html/bcp47) (představující jazyk videa) jako vlastnost. Video přehledy obsahují všechny výše uvedené zvukové poznatky a následující další položky:

* **Sledování obličeje**: čas, během kterého se ve videu nacházejí obličeje. Každá ploška má ID obličeje a odpovídající kolekci miniatur.
* **Vizuální text**: text, který se detekuje pomocí optického rozpoznávání znaků. Text je v časovém razítku a používá se také k extrakci klíčových slov (kromě zvukového přepisu).
* **Klíčové snímky**: kolekce klíčových snímků extrahovaných z videa.
* **Moderování vizuálního obsahu**: část videí označená jako dospělý nebo pikantní.
* **Anotace**: výsledek přidávání poznámek k videím na základě předem definovaného objektového modelu

## <a name="insightsjson-elements"></a>insights.jsu elementů

Výstup obsahuje soubor JSON (insights.json) se všemi přehledy, které najdete ve videu nebo ve zvukovém souboru. JSON může obsahovat následující prvky:

### <a name="transcript"></a>záznamy

|Název|Popis|
|---|---|
|id|ID řádku|
|text|Samotný přepis.|
|language|Jazyk přepisu. Má sloužit k podpoře přepisu, kde každý řádek může mít jiný jazyk.|
|instance|Seznam časových rozsahů, ve kterých se zobrazil tento řádek Pokud je instance přepisu, bude mít pouze jednu instanci.|

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

### <a name="ocr"></a>OCR

|Název|Popis|
|---|---|
|id|ID řádku OCR|
|text|Text OCR|
|spolehlivost|Spolehlivost rozpoznávání.|
|language|Jazyk OCR.|
|instance|Seznam časových rozsahů, ve kterých se zobrazilo toto optické rozpoznávání znaků (stejné optické rozpoznávání znaků se může několikrát zobrazit).|

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

### <a name="faces"></a>emotikon

|Název|Popis|
|---|---|
|id|ID obličeje|
|name|Název obličeje Může to být neznámý #0, identifikovaný celebrit nebo osoba školená zákazníkem.|
|spolehlivost|Spolehlivost identifikace obličeje.|
|description|Popis celebrit. |
|thumbnailId|ID miniatury této plochy.|
|knownPersonId|Interní ID (Pokud se jedná o známého uživatele).|
|referenceId|ID Bingu (Pokud se jedná o celebrit Bingu).|
|Hodnota ReferenceType|Aktuálně pouze Bing.|
|title|Název (Pokud se jedná o celebrit, například "generální ředitel společnosti Microsoft").|
|imageUrl|Adresa URL obrázku, pokud se jedná o celebrit.|
|instance|Instance, u kterých se v zadaném časovém rozsahu objevila plocha Každá instance má také thumbnailsId. |

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

### <a name="shots"></a>řizování

|Název|Popis|
|---|---|
|id|ID snímku|
|Klíčové snímky|Seznam klíčových snímků v rámci tohoto snímku (každý má ID a seznam časových rozsahů instancí). Instance klíčových snímků mají pole thumbnailId s ID miniatury klíčového snímku.|
|instance|Seznam časových rozsahů tohoto snímku (snímky mají pouze 1 instanci).|

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

### <a name="statistics"></a>týkají

|Název|Popis|
|---|---|
|CorrespondenceCount|Počet korespondencí ve videu.|
|WordCount|Počet slov na mluvčí.|
|SpeakerNumberOfFragments|Množství fragmentů, které má mluvčí ve videu.|
|SpeakerLongestMonolog|Nejdelší monolog mluvčího. Pokud mluvčí obsahuje tiché v monolog, je součástí této části. Odstraní se tiché na začátku a na konci monolog.|
|SpeakerTalkToListenRatio|Výpočet vychází z doby strávené monologem mluvčího (bez ticha v mezi) dělený celkovým časem videa. Čas se zaokrouhluje na třetí desetinnou čárku.|


### <a name="sentiments"></a>zabarvení

Zabarvení jsou agregované podle jejich sentimentType pole (kladné/neutrální/záporné). Například 0-0,1, 0,1-0,2.

|Název|Popis|
|---|---|
|id|ID mínění|
|averageScore |Průměr všech skóre všech instancí tohoto typu mínění-kladný/neutrální/záporný|
|instance|Seznam časových rozsahů, ve kterých se tento mínění objevil.|
|sentimentType |Typ může být "pozitivní", "neutrální" nebo "negativní".|

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
|id|ID popisku|
|name|Název popisku (například "Computer", "TV").|
|language|Název jazyka popisku (při překladu). BCP – 47|
|instance|Seznam časových rozsahů, ve kterých se tento popisek objevil (popisek se může zobrazit víckrát). Každá instance má pole s jistotou. |

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
|id|ID klíčového slova.|
|text|Text klíčového slova|
|spolehlivost|Spolehlivost rozpoznávání klíčového slova|
|language|Jazyk klíčového slova (při překladu).|
|instance|Seznam časových rozsahů, ve kterých se toto klíčové slovo objevilo (klíčové slovo se může zobrazit víckrát).|

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

Blok visualContentModeration obsahuje časové rozsahy, které Video Indexer nalezené pro potenciálně obsah pro dospělé. Pokud je visualContentModeration prázdné, není k dispozici žádný obsah určený pro dospělé.

K dispozici jsou videa, která se nacházejí v obsahu pro dospělé nebo pikantní, a to pouze pro privátní zobrazení. Uživatelé můžou odeslat žádost o revizi obsahu. v takovém případě `IsAdult` atribut bude obsahovat výsledek lidské recenze.

|Název|Popis|
|---|---|
|id|ID moderování vizuálního obsahu.|
|adultScore|Skóre dospělého (od moderátora obsahu).|
|racyScore|Pikantní skóre (z Moderování obsahu).|
|instance|Seznam časových rozsahů, ve kterých se objevilo moderování tohoto vizuálu obsahu|

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