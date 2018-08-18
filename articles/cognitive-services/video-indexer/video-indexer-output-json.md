---
title: Prozkoumání výstupu funkce Video Indexer pro Azure | Dokumentace Microsoftu
description: Toto téma popisuje výstupu funkce Video Indexer.
services: cognitive services
documentationcenter: ''
author: juliako
manager: cflower
ms.service: cognitive-services
ms.topic: article
ms.date: 05/30/2018
ms.author: juliako
ms.openlocfilehash: 7f61833d70cea3a6d176f5a08eb0db1c30e70e86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343867"
---
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Prozkoumání výstupu funkce Video Indexer vytvořeného rozhraním v1 API

> [!Note]
> Rozhraní API pro Video Indexer V1 jsou už zastaralé a odeberou se 1. srpna 2018. Měli byste začít pomocí rozhraní Video Indexer API v2 vyhnete se tak přerušením.
>
> Vývoj pomocí rozhraní API pro Video Indexer v2, najdete pokyny, nalezeno [tady](https://api-portal.videoindexer.ai/). 

Při volání **získat rozpis** rozhraní API a stav odpovědi je v pořádku, získejte podrobný výstup JSON jako obsah odpovědi. Obsah JSON obsahuje podrobné informace o zadané insights videa včetně (přepisu, OCRs, lidé). Podrobnosti zahrnují (témata) klíčová slova, tváří, bloky. Každý blok obsahuje časových rozsahů, řádky přepisu, řádky OCR, zabarvení, tváří a blokovat miniatury.

Můžete použít **získat rozpis** rozhraní API pro získání úplné rozpis video jako JSON obsahu.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Souhrnný přehled videa můžete také vizuálně zkoumat stisknutím kombinace kláves **Přehrát** tlačítko na video na portálu pro Video Indexer. Další informace najdete v tématu [prohlížení a úpravy nové poznatky z videí](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Tento článek zkoumá vrácený obsah JSON **získat rozpis** rozhraní API. Může být užitečné při kontrole [koncepty](video-indexer-concepts.md) článku.

> [!NOTE]
> Vypršení platnosti všech přístupových tokenů ve Video indexeru je jedna hodina.

## <a name="root-elements"></a>Kořenové prvky

Atribut | Popis
---|---
id|Id tohoto videa. Například 63c6d532ff.
oddíl|Logický oddíl, který může uživatel zadat v odesílání, aby bylo možné ho později vyhledat.
jméno|Název videa. Například Azure Monitor.
description|Popis videa Například "Jan Kemnetz spojí Scottu Hanselmanovi a ukazují, jak sílu monitorování dat Azure pomocí Azure monitoru."
uživatelské jméno|Tvůrce videa. Například videí webu Channel 9.
CreateTime |Čas vytvoření. Například 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Video může mít jednu z těchto režimů: **privátní**, **veřejné**. **Veřejné** – video je viditelné všem uživatelům v účtu a každý uživatel, který obsahuje odkaz na video. **Privátní** – video je viditelné všem uživatelům ve vašem účtu.
isOwned|Hodnota TRUE, pokud má aktuální uživatel je vlastníkem videa. V opačném případě hodnota false.  
isBase|Hodnota TRUE, pokud rozdělení je založená na zdroj videa. False, pokud je seznam testů, který je odvozen z jiného rozpis rozdělení.
durationInSeconds|Doba trvání videa.
summarizedInsights|Obsahuje nejméně jednu [summarizedInsights](#summarizedInsights).
rozdělení|Může obsahovat jednu nebo více [rozpisů](#breakdowns)
Sociální|Obsahuje nejméně jednu **sociální** element, který popisuje počet zobrazení videa a lajků.

## <a name="summarizedinsights"></a>summarizedInsights

Tato část uvádí přehled informací.

Atribut | Popis
---|---
jméno|Název videa. Například Azure Monitor.
shortId|ID videa. Například 63c6d532ff.
privacyMode|Vaše rozpis může mít jednu z těchto režimů: **privátní**, **veřejné**. **Veřejné** – video je viditelné všem uživatelům v účtu a každý uživatel, který obsahuje odkaz na video. **Privátní** – video je viditelné všem uživatelům ve vašem účtu.
doba trvání|Obsahuje jeden dobu, po kterou popisuje čas, kdy došlo k chybě přehledů. Doba trvání je během několika sekund.
thumbnailUrl|Miniatura videa úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Všimněte si, že pokud video privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').
tváří|Může obsahovat jednu nebo více [tváří](#faces)
témata|Může obsahovat jednu nebo více [témata](#topics)
zabarvení|Může obsahovat jednu nebo více [mínění](#sentiments)
audioEffects| Může obsahovat jednu nebo více [audioEffects](#audioEffects)
značky| Může obsahovat nula nebo více [značky](#brands)
Statistika|Další informace najdete v tématu [statistiky](#Statistics)
.
### <a name="statistics"></a>Statistika

|Název|Popis|
|---|---|
|CorrespondenceCount|Počet srovnávací ve videu.|
|WordCount|Počet slov za mluvčího.|
|SpeakerNumberOfFragments|Množství fragmenty mluvčího se ve videu.|
|SpeakerLongestMonolog|Nejdelší monolog tohoto mluvčího. Pokud má mluvčího silences uvnitř protokolu monolog je zahrnuté. Na začátku a konci protokolu monolog nečinnosti se odstraní.| 
|SpeakerTalkToListenRatio|Výpočet vychází čas strávený na mluvčího protokolu monolog (bez nečinnosti mezi) rozdělené podle celkové doby trvání videa. Čas se zaokrouhlí na třetí desetinné čárky.|

## <a name="breakdowns"></a>rozdělení

Tato část ukazuje podrobnosti o přehledy.

Atribut | Popis
---|---
id|ID rozdělení. Například 63c6d532ff.
state|Stav zpracování id daného rozdělení. Můžou mít jednu z následujících akcí: nahraný, zpracování, zpracování se nezdařila.
processingProgress|Průběh. Například 10 %.
externalId| ExternalId můžete nastavit při nahrávání. Například "4f9c3500-eca7-4ab3-987e-a745017af698." Je možné později článek vyhledat pro vaše videa pomocí tohoto externího ID.
adresy externalUrl|Adresy externalUrl můžete nastavit při nahrávání. 
zprostředkovatele identity|Při nahrávání můžete nastavit metadata. 
přehledy|Může obsahovat jednu nebo více [insights](#insights)
thumbnailUrl|Miniatura videa úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Všimněte si, že pokud video privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').
publishedUrl|Publikované adresy URL. Například "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest."
viewToken|Nosný token
sourceLanguage|Zdrojový jazyk. Jsou podporovány následující: čínština, anglické, francouzština, německé, italština, japonské, portugalština, ruština, španělština.
Jazyk|Jazyk přepisu.

## <a name="insights"></a>přehledy

Atribut | Popis 
---|---
transcriptBlocks|Může obsahovat jednu nebo více [transcriptBlocks](#transcriptBlocks)
témata|Může obsahovat jednu nebo více [témata](#topics)
tváří|Může obsahovat jednu nebo více [tváří](#faces)
Účastníci|Může obsahovat jednu nebo více [účastníků](#participants)
contentModeration|Může obsahovat jeden [contentModeration](#contentModeration)
audioEffectsCategories|Může obsahovat jednu nebo více [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>tváří

### <a name="summarizedinsights"></a>summarizedInsights

**tváří** , který se zobrazí pod **summarizedInsights**, zobrazit souhrn pro každou plochu nalezen ve videu.

Atribut | Popis 
---|---
id|ID osoby. Například 11775.
shortId|Krátké ID. Vzhledem k tomu, že seznam skladeb může být odvozen od několika rozdělení, toto ID je potřeba zjistit, které rozdělení je počátek pro každou plochu.  
jméno|Pokud je typ písma, se přidá jméno osoby. Například "Scott Hanselman." Pokud Neznámý typ písma, přidá se "Neznámý #". 
description|Pokud je typ písma, popis se vyplní podle rozhraní API Bingu pro vyhledávání. V opačném případě se popis **null**.
název|Pokud je typ písma, popis se vyplní podle rozhraní API Bingu pro vyhledávání. V opačném případě je název **null**.
thumbnailFullUrl|Rozpoznávání tváře na miniaturu úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Všimněte si, že pokud video privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').
Vzhled|Může obsahovat jednu nebo více [vzhled](#appearances)
seenDuration|Jak dlouho obličej viděla (v sekundách).
seenDurationRatio|Přítomnost vzhledem k videa doba trvání (0-1).

### <a name="breakdown-insights"></a>Rozpis insights

**tváří** , který se zobrazí pod **rozpisy**, popisují podrobnosti o každou tvář nalezen ve videu.

Atribut | Popis 
---|---
id|ID osoby. Například 11775.
bingId|
jméno|Pokud je typ písma, se přidá jméno osoby. Například "Scott Hanselman". Pokud Neznámý typ písma, přidá se "Neznámý #". 
thumbnailId|Například 616468f0-1636-4efa-94e7-262f2e575059.
description|Pokud je typ písma, popis se vyplní podle rozhraní API Bingu pro vyhledávání. V opačném případě se popis **null**.
název|Pokud je typ písma, popis se vyplní podle rozhraní API Bingu pro vyhledávání. V opačném případě je název **null**.
imageUrl|Tato adresa URL odkazuje na obrázek, který se přebírá ze zdroje videa.  
spolehlivosti|Skóre spolehlivosti (vyšší je lepší).
knownPersonId|Id známých osoby (například celebrit). Pokud uživatel není znám, obsahuje id nulami. Například e3eaff5f ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>témata

### <a name="summarizedinsights"></a>summarizedInsights

**témata** , který se zobrazí pod **summarizedInsights**, zobrazit souhrn každé téma nalezeno ve videu.

Atribut | Popis 
---|---
jméno|Název tématu (například "Azure"). 
Vzhled|Může obsahovat jednu nebo více [vzhled](#appearances).
isTranscript|Hodnota TRUE, v případě součástí řádné záznamy o studiu. False, v případě součástí optické rozpoznávání znaků.

### <a name="breakdown-insights"></a>Rozpis insights

**témata** , který se zobrazí pod **rozpisy**, popisují podrobnosti o každé téma nalezeno ve videu.

|Atribut | Popis |
|---|---|
|id|Téma jedinečné ID.|
|jméno|Název tématu.|
|stem|Tento atribut se v současné době nepoužívá.|
|slova|Tento atribut se v současné době nepoužívá.|
|pořadí|Skóre relevance (vyšší je lepší).|

## <a name="sentiments"></a>zabarvení

Atribut | Popis
---|---
sentimentKey| V současné době jsou podporovány následující zabarvení: kladná, neutrální, záporná. 
Vzhled|Může obsahovat jednu nebo více [vzhled](#appearances)|.
seenDurationRatio|Přítomnost vzhledem k videa doba trvání (0-1).

## <a name="audioeffects"></a>audioEffects

Atribut | Popis 
---|---
audioEffectKey| Platné hodnoty jsou: řeči, nečinnosti, HandClaps.
Vzhled|Může obsahovat jednu nebo více [vzhled](#appearances)
seenDurationRatio|Přítomnost vzhledem k videa doba trvání (0-1).
seenDuration|Jak dlouho zvukové efekty bylo k dispozici (v sekundách).

## <a name="appearances"></a>Vzhled

Atribut | Popis 
---|---
startTime| Hodnota času.
endTime|Hodnota času.
startSeconds| Hodnota času.
endSeconds| Hodnota času.

## <a name="participants"></a>Účastníci

Atribut | Popis 
---|---
id|ID člena.
jméno|Název člena. Například mluvčího č. 1.
pictureUrl|**PictureUrl** atribut je vyhrazená pro budoucí použití.

## <a name="contentmoderation"></a>contentModeration

Atribut | Popis 
---|---
adultClassifierValue|Úroveň spolehlivosti, že video obsahuje obsah pro dospělé.
racyClassifierValue|Úroveň spolehlivosti, že video má pikantní obsah.
bannedWordsCount|Počet vulgární slova. 
bannedWordsRatio|Poměr vulgární slova z celkového počtu slov.
reviewRecommended|Logická hodnota označující, pokud byste měli video ručně zkontrolovat.
isAdult|Logické hodnoty označující, pokud video se považuje za dospělý videa po ruční kontrolu.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Atribut | Popis 
---|---
type|ID kategorie.
key|Jeden z následujících: řeči, nečinnosti, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Atribut | Popis
---|---
id|ID bloku.
řádky|Může obsahovat jednu nebo více [řádky](#lines)
sentimentIds|**SentimentIds** atribut je vyhrazená pro budoucí použití.
thumbnailIds|**ThumbnailIds** atribut je vyhrazená pro budoucí použití.
mínění|Mínění v bloku (0-1, záporná hodnota, při pozitivní).
tváří|Může obsahovat jednu nebo více [tváří](#faces).
ocrs|Může obsahovat jednu nebo více [ocrs](#ocrs).
audioEffectInstances|Může obsahovat jednu nebo více [audioEffectInstances](#audioEffectInstances).
scén|Může obsahovat jednu nebo více [scén](#scenes).
Poznámky|Může obsahovat nula nebo více [poznámky](#annotations).

## <a name="ocrs"></a>ocrs

Popisuje, na kterém bodě v videa, textového obsahu byl nalezen. 

Atribut | Popis 
---|---
timeRange|Časový rozsah v původního videa.
adjustedTimeRange|AdjustedTimeRange je časový rozsah vzhledem k aktuálního seznamu testů. Protože z různých odvětví různých videa můžete vytvořit seznam stop, může trvat jednu hodinu videa a použijte jenom 1 řádek z něj, třeba 10:00-10:15. V takovém případě budete mít seznam testů s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
řádky|Může obsahovat jednu nebo více [řádky](#lines).

## <a name="lines"></a>řádky

### <a name="transcriptblocks"></a>transcriptBlocks

**řádky** , který se zobrazí pod **transcriptBlocks**, popisují řádky záznamy o studiu nalezen ve videu.

Atribut | Popis 
---|---
id| ID řádku.
timeRange|Časový rozsah v původního videa.
adjustedTimeRange|AdjustedTimeRange je časový rozsah vzhledem k aktuálního seznamu testů. Protože z různých odvětví různých videa můžete vytvořit seznam stop, může trvat jednu hodinu videa a použijte jenom 1 řádek z něj, třeba 10:00-10:15. V takovém případě budete mít seznam testů s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.
participantID| Id mluvčího tento řádek.
text| Přepis.
isIncluded| V základní rozpisy vždy hodnotu true. V odvozených seznamy stop řádky, které byly součástí zdroj videa nastavené na isIncluded = true. Všechny ostatní řádky jsou false.

### <a name="ocrs"></a>ocrs

**řádky** , který se zobrazí pod **ocrs**, popisují řádky OCRs nalezen ve videu.

Atribut | Popis 
---|---
id|ID optické rozpoznávání znaků.
Šířka|Tento atribut se v současné době nepoužívá.
Výška|Tento atribut se v současné době nepoužívá.
Jazyk|OCR jazyk.
Textové podrobnosti|OCR textu.
spolehlivosti|Skóre spolehlivosti (vyšší je lepší).

## <a name="scenes"></a>scén

Atribut | Popis 
---|---
id|ID scény.
timeRange|Obsahuje nejméně jednu **timeRange**.
klíčový snímek|Čas klíčový snímek.
snímky|Může obsahovat jednu nebo více [snímky](#shots).

## <a name="shots"></a>snímky

Atribut | Popis 
---|---
id||Snímek ID.
timeRange|Obsahuje nejméně jednu **timeRange**.
klíčový snímek|Čas klíčový snímek.

## <a name="audioeffectinstances"></a>audioEffectInstances

Atribut | Popis 
---|---
type|Index zvuku události: smích = 1, HandClaps = 2, Hudba = 3, řeči = 4, nečinnosti = 5
rozsahy|Může obsahovat jednu nebo více [rozsahy](#ranges).

## <a name="ranges"></a>rozsahy

**rozsahy** , který se zobrazí pod **audioEffectInstances**, popisují zvukové efekty v těchto oblastech.

Atribut | Popis 
---|---
timeRange|Časový rozsah v původního videa.
adjustedTimeRange|AdjustedTimeRange je časový rozsah vzhledem k aktuálního seznamu testů. Protože z různých odvětví různých videa můžete vytvořit seznam stop, může trvat jednu hodinu videa a použijte pouze jeden řádek z něj, třeba 10:00-10:15. V takovém případě budete mít seznam testů s 1 řádek, kde časový rozsah je 10:00-10:15, ale adjustedTimeRange je 00:00-00:15.

## <a name="annotations"></a>Poznámky

Vrací značky podle rozpoznatelných objektů, živých bytostí, krajin, akce a vizuální vzory.

|Atribut|Popis|
|---|---|
|id|ID poznámky.|
|Název|Název poznámky (třeba osoba, sportovní hra, Black snímků).|
|Vzhled|Může obsahovat jeden nebo více vzhled.|

## <a name="brands"></a>značky

Firmy a produktů názvy v převodu řeči na text přepisu a/nebo Video optické rozpoznávání znaků. To nezahrnuje visual rozpoznávání značky nebo detekce logo.

Atribut | Popis
---|---
id | ID značky.
jméno | Název značky ze služby Bing nebo vlastní značku.  
wikiId | Přípona adresy url wikipedie značky. Například "Target_Corporation" je přípona [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | Značka uživatele adresu url Wikipedie, pokud existuje. Příklad: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
spolehlivosti | Hodnota spolehlivosti detektoru Video Indexer značky (0-1).
description | Popis značky extrahovány ze encyklopedii Wikipedia. 
Vzhled | Může obsahovat jeden nebo více vzhled.
seenDuration | Přítomnost vzhledem k videa doba trvání (0-1).

## <a name="next-steps"></a>Další postup

Informace o tom, jak vytvořit vlastní rozpis naleznete v tématu [zobrazení a úprava přehledů funkce Video Indexer](video-indexer-view-edit.md).

Informace o tom, jak vložení widgetů do aplikace najdete v tématu [Video Indexer vložení widgetů do svých aplikací](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Další informace najdete v tématech

[Video Indexer API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Video Indexer s přehledem](video-indexer-overview.md)

