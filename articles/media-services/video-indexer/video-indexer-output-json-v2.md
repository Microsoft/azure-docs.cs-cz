---
title: Projděte si výstup Video Indexer vytvořený pomocí rozhraní v2 API – Azure
titleSuffix: Azure Media Services
description: Toto téma prověřuje Azure Media Services Video Indexer výstup vytvářený rozhraním API v2.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/16/2020
ms.author: juliako
ms.openlocfilehash: 2ac7c3c2149ce43c860c7726381733ef377de8d3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530735"
---
# <a name="examine-the-video-indexer-output"></a>Kontrola výstupu Video Indexer

Když je video indexované, Video Indexer vytvoří obsah JSON, který obsahuje podrobnosti o zadaných videích Insights. Přehledy zahrnují: přepisy, OCRs, obličeje, témata, bloky atd. Každý typ Insight zahrnuje instance časových rozsahů, které ukazují, kdy se ve videu zobrazí přehled. 

Přehledné přehledy videa můžete vizuálně prohlédnout kliknutím na tlačítko **Přehrát** na videu na webu [video indexer](https://www.videoindexer.ai/) . 

Rozhraní API můžete použít i tak, že zavoláte rozhraní **Get video index** API a stav odpovědi je OK, jako obsah odpovědi dostanete podrobný výstup JSON.

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Tento článek prověřuje výstup Video Indexer (obsah JSON). <br/>Informace o tom, jaké funkce a přehledy jsou k dispozici, najdete v tématu [video indexer Insights](video-indexer-overview.md#video-insights).

> [!NOTE]
> Vypršení platnosti všech přístupových tokenů v Video Indexer je jedna hodina.

## <a name="get-the-insights"></a>Získat přehledy

### <a name="insightsoutput-produced-in-the-websiteportal"></a>Přehledy/výstupy vytvořené na webu/portálu

1. Přejděte na web [Video Indexer](https://www.videoindexer.ai/) a přihlaste se.
1. Najděte video s výstupem, který chcete prošetřit.
1. Stiskněte **Play** (Přehrát).
1. Vyberte kartu **přehledy** (souhrnné přehledy) nebo kartu **Časová osa** (umožňuje filtrovat relevantní přehledy).
1. Stáhněte si artefakty a uveďte, co je v nich.

Další informace najdete v tématu [zobrazení a Úprava videí s přehledem](video-indexer-view-edit.md).

## <a name="insightsoutput-produced-by-api"></a>Insights/výstup vytvářený rozhraním API

1. Pro načtení souboru JSON volejte volání [Get video index API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Index?) .
1. Pokud vás zajímá také konkrétní artefakty, zavolejte na [získat rozhraní API pro stažení artefaktů videa](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Artifact-Download-Url?) .

    V volání rozhraní API zadejte požadovaný typ artefaktu (OCR, obličeje, klíčové snímky atd.).

## <a name="root-elements-of-the-insights"></a>Kořenové prvky přehledů

|Název|Description|
|---|---|
|accountId|ID účtu v seznamu testů|
|id|ID seznamu testů.|
|name|Název seznamu stop|
|description|Popis seznamu testů.|
|userName|Jméno uživatele, který vytvořil seznam testů.|
|vytvářejí|Čas vytvoření seznamu testů.|
|privacyMode|Režim ochrany osobních údajů seznamu testů (Private/Public).|
|state|Seznam testů (nahráno, zpracovává, zpracováno, selhalo, v karanténě).|
|Vlastněné|Označuje, zda byl seznam testů vytvořen aktuálním uživatelem.|
|Upravitelná|Určuje, jestli má aktuální uživatel autorizaci k úpravám seznamu testů.|
|Databáze|Označuje, zda se jedná o seznam je základní seznam (video) nebo seznam stop, který se skládá z jiných videí (odvozeno).|
|durationInSeconds|Celková doba trvání seznamu testů.|
|summarizedInsights|Obsahuje jeden [summarizedInsights](#summarizedinsights).
|videa|Seznam [videí](#videos) tvořících seznam stop.<br/>Pokud tento seznam vychází z časového rozsahu jiných videí (odvozený), budou videa v tomto seznamu obsahovat jenom data z zahrnutých časových rozsahů.|

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

V této části se zobrazuje souhrn přehledů.

|Atribut | Popis|
|---|---|
|name|Název videa Například Azure Monitor.|
|id|ID videa Například 63c6d532ff.|
|privacyMode|Váš rozpis může mít jeden z následujících režimů: **Private**, **Public**. **Veřejné** – video je viditelné všem uživatelům ve vašem účtu a všem, kdo má odkaz na video. **Soukromé** – video je viditelné všem uživatelům vašeho účtu.|
|doba trvání|Obsahuje jednu dobu trvání, která popisuje čas, kdy došlo k přehledu. Doba trvání je v sekundách.|
|thumbnailVideoId|ID videa, ze kterého byla Miniatura provedena
|thumbnailId|ID miniatury videa Chcete-li získat aktuální miniaturu, zavolejte funkci [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) a předejte ji ThumbnailVideoId a thumbnailId.|
|plošky/animatedCharacters|Může obsahovat nula nebo více plošek. Podrobnější informace najdete v tématu [plošky/animatedCharacters](#facesanimatedcharacters).|
|klíčová slova|Může obsahovat nula nebo více klíčových slov. Podrobnější informace najdete v tématu [klíčová slova](#keywords).|
|zabarvení|Může obsahovat nula nebo více zabarvení. Podrobnější informace najdete v tématu [zabarvení](#sentiments).|
|audioEffects| Může obsahovat nula nebo více audioEffects. Podrobnější informace najdete v tématu [audioEffects](#audioeffects-public-preview).|
|popisky| Může obsahovat nula nebo více popisků. Podrobnější informace najdete v tématu [Labels](#labels).|
|značky| Může obsahovat nula nebo více značek. Podrobnější informace najdete v tématu [značky](#brands).|
|týkají | Podrobnější informace najdete v tématu [Statistika](#statistics).|
|emoce| Může obsahovat nula nebo více emoce. Podrobnější informace najdete v tématu [emoce](#emotions).|
|popisující|Může obsahovat nula nebo více témat. [Témata](#topics) přehled.|

## <a name="videos"></a>videa

|Název|Description|
|---|---|
|accountId|ID účtu videa VI|
|id|ID videa|
|name|Název videa
|state|Stav videa (nahráno, zpracování, zpracování, selhalo, v karanténě).|
|processingProgress|Průběh zpracování během zpracování (například 20%).|
|failureCode|Kód chyby, pokud se nepovedlo zpracovat (například ' UnsupportedFileType ').|
|failureMessage|Zpráva o selhání, pokud se nepovedlo zpracovat.|
|externalId|Externí ID videa (Pokud určuje uživatel)|
|externalUrl|Externí adresa URL videa (Pokud určuje uživatel)|
|zprostředkovatele identity|Externí metadata videa (Pokud určuje uživatel)|
|z dospělého|Označuje, zda bylo video ručně zkontrolováno a identifikováno jako video z dospělého.|
|Insights|Objekt Insights Další informace najdete v tématu [přehledy](#insights).|
|thumbnailId|ID miniatury videa Chcete-li získat skutečný miniaturní hovor [Get-Thumbnail](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Thumbnail) a předat mu ID a thumbnailId videa.|
|publishedUrl|Adresa URL pro streamování videa|
|publishedUrlProxy|Adresa URL, ze které se má streamovat video (pro zařízení Apple)|
|viewToken|Krátkodobý token zobrazení pro streamování videa.|
|sourceLanguage|Zdrojový jazyk videa|
|language|Skutečný jazyk videa (překlad).|
|indexingPreset|Přednastavení použité k indexování videa|
|streamingPreset|Přednastavení použité k publikování videa.|
|linguisticModelId|Model položku CRI, který se používá k přepisovatí videa.|
|týkají | Další informace najdete v tématu [Statistika](#statistics).|

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
### <a name="insights"></a>Insights

Každý vhled (například záznamy přepisu, obličeje, značky atd.) obsahuje seznam jedinečných prvků (například face1, face2, face3) a každý prvek má vlastní metadata a seznam jeho instancí (které jsou časové rozsahy s dalšími volitelnými metadaty).

Ploška může mít ID, název, miniaturu, další metadata a seznam jeho dočasná instance (například: 00:00:05 – 00:00:10, 00:01:00-00:02:30 a 00:41:21 – 00:41:49). Každá dočasná instance může mít další metadata. Například souřadnice obdélníku plochy (20230, 60, 60).

|Verze|Verze kódu|
|---|---|
|sourceLanguage|Zdrojový jazyk videa (za předpokladu, že se jedná o jeden hlavní jazyk). Ve formě řetězce [BCP-47](https://tools.ietf.org/html/bcp47) .|
|language|Jazyk Insights (přeložený ze zdrojového jazyka). Ve formě řetězce [BCP-47](https://tools.ietf.org/html/bcp47) .|
|záznamy|Přehled [přepisu](#transcript)|
|OCR|Přehled [rozpoznávání OCR](#ocr) .|
|klíčová slova|[Klíčová slova](#keywords) Insight.|
|blocks|Může obsahovat jeden nebo více [bloků](#blocks)|
|plošky/animatedCharacters|Přehledy [obličeje a animatedCharacters](#facesanimatedcharacters)|
|popisky|Přehled [štítků](#labels)|
|řizování|Přehled [snímků](#shots) .|
|značky|Přehled [značek](#brands) .|
|audioEffects|Přehled [audioEffects](#audioeffects-public-preview)|
|zabarvení|Přehled [zabarvení](#sentiments)|
|visualContentModeration|Přehled [visualContentModeration](#visualcontentmoderation)|
|textualContentModeration|Přehled [textualContentModeration](#textualcontentmoderation)|
|emoce| Přehled [emoce](#emotions)|
|popisující|[Témata](#topics) přehled.|
|mluvčích|Přehled [mluvčích](#speakers) .|

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
id|ID bloku|
instance|Seznam časových rozsahů tohoto bloku|

#### <a name="transcript"></a>záznamy

|Název|Description|
|---|---|
|id|ID řádku|
|text|Samotný přepis.|
|spolehlivost|Spolehlivost přesnosti přepisu.|
|speakerId|ID mluvčího.|
|language|Jazyk přepisu. Má sloužit k podpoře přepisu, kde každý řádek může mít jiný jazyk.|
|instance|Seznam časových rozsahů, ve kterých se zobrazil tento řádek Pokud je instance přepisu, bude mít pouze jednu instanci.|

Příklad:

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "text":"ignite 2016. Your mission at Microsoft is to empower every",
  "confidence":0.8944,
  "speakerId":2,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
```

#### <a name="ocr"></a>OCR

|Název|Description|
|---|---|
|id|ID řádku OCR|
|text|Text OCR|
|spolehlivost|Spolehlivost rozpoznávání.|
|language|Jazyk OCR.|
|instance|Seznam časových rozsahů, ve kterých se zobrazilo toto optické rozpoznávání znaků (stejné optické rozpoznávání znaků se může několikrát zobrazit).|
|výška|Výška obdélníku OCR|
|top|Horní místo v px|
|left| Levé místo v px|
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

|Název|Description|
|---|---|
|id|ID klíčového slova.|
|text|Text klíčového slova|
|spolehlivost|Spolehlivost rozpoznávání klíčového slova|
|language|Jazyk klíčového slova (při překladu).|
|instance|Seznam časových rozsahů, ve kterých se toto klíčové slovo objevilo (klíčové slovo se může zobrazit víckrát).|

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

#### <a name="facesanimatedcharacters"></a>plošky/animatedCharacters

`animatedCharacters` element nahrazuje prvek `faces` pro případ, že bylo video indexováno pomocí animovaného modelu znaků. To se provádí pomocí vlastního modelu v Custom Vision, Video Indexer ho spouští na klíčových snímcích.

Pokud jsou k dispozici plošky (ne animované znaky), Video Indexer používá rozhraní API pro rozpoznávání tváře na všech snímcích videa k detekci plošek a celebrit.

|Název|Description|
|---|---|
|id|ID obličeje|
|name|Název obličeje. Může to být neznámý #0, identifikovaný celebrit nebo osoba školená zákazníkem.|
|spolehlivost|Spolehlivost identifikace obličeje.|
|description|Popis celebrit. |
|thumbnailId|ID miniatury této plochy.|
|knownPersonId|Pokud se jedná o známého uživatele, jeho interní ID.|
|referenceId|Pokud se jedná o celebrit Bingu, jeho ID Bingu.|
|Hodnota ReferenceType|Právě teď stačí Bing.|
|title|Pokud se jedná o celebrit, jeho titul (například "generální ředitel společnosti Microsoft").|
|imageUrl|Pokud se jedná o celebrit, jeho adresu URL obrázku.|
|instance|Jedná se o instance, kde se v zadaném časovém rozsahu objevila plocha. Každá instance má také thumbnailsId. |

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

#### <a name="labels"></a>popisky

|Název|Description|
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

#### <a name="scenes"></a>pozadí

|Název|Description|
|---|---|
|id|ID scény|
|instance|Seznam časových rozsahů této scény (scéna může mít jenom 1 instanci).|

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

#### <a name="shots"></a>řizování

|Název|Description|
|---|---|
|id|ID snímku|
|Klíčové snímky|Seznam klíčových snímků v rámci snímku (každý má ID a seznam časových rozsahů instancí). Každá instance klíčového snímku má pole thumbnailId, které obsahuje ID miniatury klíčového snímku.|
|instance|Seznam časových rozsahů tohoto snímku (u tohoto snímku může být jen 1 instance).|

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

#### <a name="brands"></a>značky

Názvy značek firmy a produktu zjištěné v řeči pro přepis textu a/nebo video optického rozpoznávání. Nezahrnuje vizuální rozpoznávání značek nebo rozpoznávání loga.

|Název|Description|
|---|---|
|id|ID značky|
|name|Název značek.|
|referenceId | Přípona adresy URL Wikipedii značky Například "Target_Corporation" je přípona [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation) .
|referenceUrl | Adresa URL Wikipedii značky, pokud existuje. Příklad: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
|description|Popis značek|
|tags|Seznam předdefinovaných značek, které byly přidruženy k této značce.|
|spolehlivost|Hodnota spolehlivosti Video Indexerho detektoru značky (0-1).|
|instance|Seznam časových rozsahů této značky. Každá instance má brandType, který označuje, zda se tato značka objevila v přepisu nebo v OCR.|

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

#### <a name="statistics"></a>týkají

|Název|Description|
|---|---|
|CorrespondenceCount|Počet korespondencí ve videu.|
|SpeakerWordCount|Počet slov na mluvčí.|
|SpeakerNumberOfFragments|Množství fragmentů, které má mluvčí ve videu.|
|SpeakerLongestMonolog|Nejdelší monolog mluvčího. Pokud mluvčí obsahuje tiché v monolog, je součástí. Odstraní se tiché na začátku a na konci monolog.| 
|SpeakerTalkToListenRatio|Výpočet vychází z doby strávené monologem mluvčího (bez ticha v mezi) dělený celkovým časem videa. Čas se zaokrouhluje na třetí desetinnou čárku.|

#### <a name="audioeffects-public-preview"></a>audioEffects (Public Preview)

|Název|Description
|---|---|
|id|ID zvukového efektu|
|typ|Typ zvukového efektu|
|instance|Seznam časových rozsahů, ve kterých se tento zvukový efekt objevil. Každá instance má pole s jistotou.|

```json
"audioEffects": [
{
    "id": 0,
    "type": "Siren",
    "instances": [
    {
       "confidence": 0.87,
        "start": "00:00:00",
        "end": "00:00:03"
    },
    {
       "confidence": 0.87,
       "start": "00:01:13",
        "end": "00:01:21"
    }
    ]
}
]
```

#### <a name="sentiments"></a>zabarvení

Zabarvení jsou agregované podle jejich sentimentType pole (kladné/neutrální/záporné). Například 0-0,1, 0,1-0,2.

|Název|Description|
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

#### <a name="visualcontentmoderation"></a>visualContentModeration

Blok visualContentModeration obsahuje časové rozsahy, které Video Indexer nalezené pro potenciálně obsah pro dospělé. Pokud je visualContentModeration prázdné, není k dispozici žádný obsah určený pro dospělé.

K dispozici jsou videa, která se nacházejí v obsahu pro dospělé nebo pikantní, a to pouze pro privátní zobrazení. Uživatelé mají možnost Odeslat žádost o revizi obsahu. v takovém případě bude atribut pro dospělé obsahovat výsledek lidské recenze.

|Název|Description|
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

#### <a name="textualcontentmoderation"></a>textualContentModeration 

|Název|Description|
|---|---|
|id|ID moderování textového obsahu|
|bannedWordsCount |Počet zakázaných slov.|
|bannedWordsRatio |Poměr z celkového počtu slov.|

#### <a name="emotions"></a>emoce

Video Indexer identifikuje emoce na základě hlasových a zvukových pomůcek. Identifikující emoce můžou být: radost, smutek, hněv nebo strach.

|Název|Description|
|---|---|
|id|ID emoce|
|typ|Moment, který byl identifikován v závislosti na řeči a zvukové pomůcky. Emoce můžou být: radost, smutek, hněv nebo strach.|
|instance|Seznam časových rozsahů, ve kterých se tyto emoce objevily|

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

#### <a name="topics"></a>popisující

Video Indexer vytváří odvození hlavních témat z přepisů. Pokud je to možné, je zahrnutá taxonomie [IPTC](https://iptc.org/standards/media-topics/) na druhé úrovni. 

|Název|Description|
|---|---|
|id|ID tématu|
|name|Název tématu, například: "farmaceutické".|
|referenceId|Popis cesty odrážející hierarchii témat Například: "zdravotnictví a komfort/lékařství a zdravotnictví/Farmaceutickés".|
|spolehlivost|Skóre spolehlivosti v rozsahu [0, 1]. Vyšší je spolehlivější.|
|language|Jazyk použitý v tématu.|
|IPTC|Název kódu média IPTC, pokud byl nalezen|
|instance |V současné době Video Indexer neindexuje téma do časových intervalů, takže se jako interval používá celé video.|

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

#### <a name="speakers"></a>mluvčích

|Název|Description|
|---|---|
|id|ID mluvčího.|
|name|Název mluvčího ve formě "mluvčího", *<number>* například: "mluvčí #1".|
|instance |Seznam časových rozsahů, ve kterých se tento mluvčí objevil.|

```json
"speakers":[
{
  "id":1,
  "name":"Speaker #1",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
{
  "id":2,
  "name":"Speaker #2",
  "instances":[
     {
    "adjustedStart":"0:00:12.81",
    "adjustedEnd":"0:00:17.03",
    "start":"0:00:12.81",
    "end":"0:00:17.03"
     }
  ]
},
` ` `
```
## <a name="next-steps"></a>Další kroky

[Portál pro vývojáře Video Indexer](https://api-portal.videoindexer.ai)

Informace o tom, jak do aplikace vkládat widgety, najdete v tématu věnovaném [vložení video indexer widgety do aplikací](video-indexer-embed-widgets.md). 

