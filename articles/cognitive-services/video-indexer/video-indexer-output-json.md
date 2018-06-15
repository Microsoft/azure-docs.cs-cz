---
title: Vyhledejte ve výstupu videa Indexer Azure | Microsoft Docs
description: Toto téma prověří výstup Video Indexer.
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
# <a name="examine-the-video-indexer-output-produced-by-v1-api"></a>Vyhledejte ve výstupu videa Indexer vytvořeného rozhraním v1 API

> [!Note]
> Rozhraní API V1 videu Indexer jsou už zastaralé a bude odebrána na 1. srpna 2018. Měli byste začít pomocí rozhraní API v2 Video Indexer, aby se zabránilo přerušení.
>
> Pro vývoj pomocí rozhraní API v2 Video Indexer, pokynů naleznete [zde](https://api-portal.videoindexer.ai/). 

Při volání **získat členění** rozhraní API a stav odpovědi je v pořádku, získat podrobný výstup JSON jako obsahu odpovědi. Obsah JSON obsahuje podrobnosti o zadaný Statistika video včetně (přepis, OCRs, osoby). Podrobnosti jsou klíčová slova (témata), řezy, bloky. Každý blok zahrnuje časových rozsahů, přepis řádky, řádky rozpoznávání znaků, chráněny, řezy a blokovat miniatur.

Můžete použít **získat členění** rozhraní API získat úplné rozpis video jako JSON obsah.  
 
    GET https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/63c6d532ff HTTP/1.1
    Host: videobreakdown.azure-api.net
    Ocp-Apim-Subscription-Key: ••••••••••••••••••••••••••••••••
    
Souhrnná statistika na video můžete také vizuálně zkoumat stisknutím **přehrání** tlačítko na video na portálu Video Indexer. Další informace najdete v tématu [prohlížení a úpravy video insights](video-indexer-view-edit.md).

![Insights](./media/video-indexer-output-json/video-indexer-summarized-insights.png)

Tento článek prozkoumá obsah JSON vrácený **získat členění** rozhraní API. Může být užitečná, chcete-li zkontrolovat [koncepty](video-indexer-concepts.md) článku.

> [!NOTE]
> Vypršení platnosti všech přístupových tokenů v Video indexeru je jedna hodina.

## <a name="root-elements"></a>Kořenových elementů

Atribut | Popis
---|---
id|Id toto video. Například 63c6d532ff.
oddíl|Logický oddíl, který může uživatel zadat v nahrávání, aby bylo možné ho později vyhledat.
jméno|Název videa. Například monitorování Azure.
description|Popis videa Například "Jan Kemnetz spojí Scott Hanselman zobrazíte postup uplatněte monitorování dat Azure s Azure monitorování."
Uživatelské jméno|Tvůrce videa. Například Channel9 videa.
CreateTime |Čas vytvoření. Například 2017-03-31T16:36:41.4504249 + 00:00.
privacyMode|Videa může mít jeden z následujících režimů: **privátní**, **veřejné**. **Veřejné** -je viditelná pro všechny uživatele v účtu a na všechny uživatele, který obsahuje odkaz na video. **Privátní** -je viditelná pro všechny uživatele ve vašem účtu.
isOwned|Hodnota TRUE, pokud má aktuální uživatel vlastní videa. Jinak hodnota false.  
isBase|Hodnota TRUE, pokud rozdělení je založená na zdroj videa. False, pokud je seznam skladeb, který je odvozený od jiného rozpis rozdělení.
durationInSeconds|Doba trvání videa.
summarizedInsights|Obsahuje jeden [summarizedInsights](#summarizedInsights).
členění|Může obsahovat jedno nebo více [členění](#breakdowns)
Sociální|Obsahuje jeden **sociálních** element, který popisuje počet líbí a zobrazení videa.

## <a name="summarizedinsights"></a>summarizedInsights

Tato část uvádí přehled k přehledům.

Atribut | Popis
---|---
jméno|Název videa. Například monitorování Azure.
shortId|ID videa. Například 63c6d532ff.
privacyMode|Vaše rozpis může mít jeden z následujících režimů: **privátní**, **veřejné**. **Veřejné** -je viditelná pro všechny uživatele v účtu a na všechny uživatele, který obsahuje odkaz na video. **Privátní** -je viditelná pro všechny uživatele ve vašem účtu.
doba trvání|Obsahuje jeden dobu trvání, která popisuje čas výskytu přehledu. Doba trvání je v sekundách.
thumbnailUrl|Na videu miniaturu úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Všimněte si, že pokud je privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').
řezy|Může obsahovat jedno nebo více [řezy](#faces)
Témata|Může obsahovat jedno nebo více [témata](#topics)
chráněny|Může obsahovat jedno nebo více [chráněny](#sentiments)
audioEffects| Může obsahovat jedno nebo více [audioEffects](#audioEffects)
značky| Může obsahovat nula nebo více [značky](#brands)
Statistika|Další informace najdete v tématu [statistiky](#Statistics)
.
### <a name="statistics"></a>Statistika

|Název|Popis|
|---|---|
|CorrespondenceCount|Číslo srovnávací ve videu.|
|WordCount|Počet slov za mluvčího.|
|SpeakerNumberOfFragments|Množství fragmenty mluvčímu má v video.|
|SpeakerLongestMonolog|Nejdelší monolog mluvčího. Pokud má mluvčímu silences uvnitř monolog je součástí. Ticho na začátku a konci monolog se odebere.| 
|SpeakerTalkToListenRatio|Výpočet vychází čas strávený monolog mluvčího (bez nečinnosti v mezi) dělený celkový čas videa. Čas se zaokrouhlí na třetí desetinné čárky.|

## <a name="breakdowns"></a>členění

Tato část uvádí podrobnosti k přehledům.

Atribut | Popis
---|---
id|ID rozdělení. Například 63c6d532ff.
state|Stav zpracování dané rozpis id. Může být jeden z následujících: nahrané, zpracování, zpracování se nezdařila.
processingProgress|Průběh. Například 10 %.
externalId| ExternalId můžete nastavit při nahrávání. Například "4f9c3500-eca7-4ab3-987e-a745017af698." Později můžete hledat videa podle číslem ID této externí.
adresy externalUrl|Můžete nastavit adresy externalUrl během nahrávání. 
zprostředkovatele identity|Během nahrávání můžete nastavit metadat. 
přehledy|Může obsahovat jedno nebo více [statistiky](#insights)
thumbnailUrl|Na videu miniaturu úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...". Všimněte si, že pokud je privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').
publishedUrl|Adresa URL publikovaná. Například "https://BreakdownMedia.azureedge.net:443/d5e5232d-48e2-4fbc-9893-0ea6335da563/Azure%20Monitor%20%20Azure%20Friday.ism/manifest."
viewToken|Token nosiče
sourceLanguage|Jazyk zdrojového. Jsou podporovány následující: čínština, angličtinu, francouzštinu, němčině, Italská, japonské, portugalština, ruština, španělské.
Jazyk|Jazyk zápis.

## <a name="insights"></a>přehledy

Atribut | Popis 
---|---
transcriptBlocks|Může obsahovat jedno nebo více [transcriptBlocks](#transcriptBlocks)
Témata|Může obsahovat jedno nebo více [témata](#topics)
řezy|Může obsahovat jedno nebo více [řezy](#faces)
Účastníci|Může obsahovat jedno nebo více [účastníky](#participants)
contentModeration|Může obsahovat jeden [contentModeration](#contentModeration)
audioEffectsCategories|Může obsahovat jedno nebo více [audioEffectsCategories](#audioEffectsCategories)

## <a name="faces"></a>řezy

### <a name="summarizedinsights"></a>summarizedInsights

**řezy** , se zobrazí pod **summarizedInsights**, zobrazit souhrn každý řez nalezena ve videu.

Atribut | Popis 
---|---
id|ID osoby. Například 11775.
shortId|Krátký ID. Vzhledem k tomu, že seznam stop může být odvozen od několika členění, toto ID je potřeba a zjistěte, které rozdělení je zdrojem každý řez.  
jméno|Pokud je rozpoznána písmo, se přidá jméno osoby. Například "Scott Hanselman." Pokud řez neznámý, se přidá "Neznámý #". 
description|Pokud je rozpoznána písmo, je popis vyplní podle rozhraní API Bing hledání. Jinak je popis **null**.
název|Pokud je rozpoznána písmo, je popis vyplní podle rozhraní API Bing hledání. Jinak je název **null**.
thumbnailFullUrl|Miniatura se plocha úplné adresy URL. Například "https://www.videoindexer.ai/api/Thumbnail/3a9e38d72e/d1f5fac5-e8ae-40d9-a04a-6b2928fb5d10?accessToken=eyJ0eXAiOiJKV1QiLCJhbGciO...." Všimněte si, že pokud je privátní, adresa URL obsahuje přístupový token jednu hodinu. Po jedné hodině, adresa URL se již nebude platný a je nutné buď získat rozpis znovu s novou adresu url v ní nebo volejte GetAccessToken k získání nového tokenu přístupu a vytvořit úplnou adresu url. ručně ("https://www.videoindexer.ai/api/Thumbnail/[shortId] nebo [ThumbnailId]? accessToken = [accessToken]').
Vzhled|Může obsahovat jedno nebo více [vzhled](#appearances)
seenDuration|Pro dobu tučné zobrazila (v sekundách).
seenDurationRatio|Přítomnosti relativně k video doba trvání (0-1).

### <a name="breakdown-insights"></a>Statistika rozdělení

**řezy** , se zobrazí pod **členění**, popisují podrobnosti o jednotlivých vzhled nalezena ve videu.

Atribut | Popis 
---|---
id|ID osoby. Například 11775.
bingId|
jméno|Pokud je rozpoznána písmo, se přidá jméno osoby. Například "Scott Hanselman". Pokud řez neznámý, se přidá "Neznámý #". 
thumbnailId|Například 616468f0-1636-4efa-94e7-262f2e575059.
description|Pokud je rozpoznána písmo, je popis vyplní podle rozhraní API Bing hledání. Jinak je popis **null**.
název|Pokud je rozpoznána písmo, je popis vyplní podle rozhraní API Bing hledání. Jinak je název **null**.
ImageUrl|Tato adresa URL odkazuje na obrázek, který je převzat ze zdroj videa.  
Spolehlivosti|Skóre spolehlivosti (je vyšší lepší).
knownPersonId|Id známých osoby (například celebrit). Pokud uživatel nezná, obsahuje id nuly. Například e3eaff5f-ee1b-4eac-80ce-ebac47aadf64.

## <a name="topics"></a>Témata

### <a name="summarizedinsights"></a>summarizedInsights

**témata** , se zobrazí pod **summarizedInsights**, zobrazit souhrn každého nalezeného na videu.

Atribut | Popis 
---|---
jméno|Název tématu (například "Azure"). 
Vzhled|Může obsahovat jedno nebo více [vzhled](#appearances).
isTranscript|Hodnota TRUE, pokud nalezen v přepisu. Hodnotu false, pokud nalezen v rozpoznávání znaků.

### <a name="breakdown-insights"></a>Statistika rozdělení

**témata** , se zobrazí pod **členění**, popisují podrobnosti o jednotlivých téma nalezeno ve videu.

|Atribut | Popis |
|---|---|
|id|Téma jedinečné ID.|
|jméno|Název tématu.|
|kmen|Tento atribut se v současné době nepoužívá.|
|slova|Tento atribut se v současné době nepoužívá.|
|Pořadí|Skóre relevance (je vyšší lepší).|

## <a name="sentiments"></a>chráněny

Atribut | Popis
---|---
sentimentKey| V současné době jsou podporovány následující chráněny: kladné, neutrální, záporné. 
Vzhled|Může obsahovat jedno nebo více [vzhled](#appearances)|.
seenDurationRatio|Přítomnosti relativně k video doba trvání (0-1).

## <a name="audioeffects"></a>audioEffects

Atribut | Popis 
---|---
audioEffectKey| Platné hodnoty jsou: rozpoznávání řeči, nečinnosti, HandClaps.
Vzhled|Může obsahovat jedno nebo více [vzhled](#appearances)
seenDurationRatio|Přítomnosti relativně k video doba trvání (0-1).
seenDuration|Jak dlouho zvuk efektu nacházel (v sekundách).

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
pictureUrl|**PictureUrl** atribut je vyhrazena pro budoucí použití.

## <a name="contentmoderation"></a>contentModeration

Atribut | Popis 
---|---
adultClassifierValue|Hladina spolehlivosti, že na video má obsah pro dospělé.
racyClassifierValue|Hladina spolehlivosti, že na video má zájem obsah.
bannedWordsCount|Počet vulgárnost slova. 
bannedWordsRatio|Poměr vulgárnost slova z celkového počtu slov.
reviewRecommended|Logickou hodnotu udávající, pokud by měl být video ručně zkontrolovat.
isAdult|Logické hodnoty, která udává, pokud na video je považován za dospělého video po ruční kontrola.

## <a name="audioeffectscategories"></a>audioEffectsCategories

Atribut | Popis 
---|---
type|ID kategorie.
key|Jeden z následujících: rozpoznávání řeči, nečinnosti, HandClaps. 

## <a name="transcriptblocks"></a>transcriptBlocks

Atribut | Popis
---|---
id|ID bloku.
řádky|Může obsahovat jedno nebo více [řádky](#lines)
sentimentIds|**SentimentIds** atribut je vyhrazena pro budoucí použití.
thumbnailIds|**ThumbnailIds** atribut je vyhrazena pro budoucí použití.
Postojích|Postojích v bloku (0-1, záporné k kladné).
řezy|Může obsahovat jedno nebo více [řezy](#faces).
ocrs|Může obsahovat jedno nebo více [ocrs](#ocrs).
audioEffectInstances|Může obsahovat jedno nebo více [audioEffectInstances](#audioEffectInstances).
scény|Může obsahovat jedno nebo více [scény](#scenes).
Poznámky|Může obsahovat nula nebo více [poznámky](#annotations).

## <a name="ocrs"></a>ocrs

Popisuje co bodu v video, textového obsahu byl nalezen. 

Atribut | Popis 
---|---
timeRange|Časové rozmezí v původní video.
adjustedTimeRange|AdjustedTimeRange je časový rozsah, relativně k aktuální seznam stop. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, můžete proveďte video jednu hodinu a používat pouze 1 řádek z něj, například 10:00 – 10:15. V takovém případě bude mít seznam stop 1 řádek, kde časový rozsah je 10:00 – 10:15, ale adjustedTimeRange je 00:00 – 00:15.
řádky|Může obsahovat jedno nebo více [řádky](#lines).

## <a name="lines"></a>řádky

### <a name="transcriptblocks"></a>transcriptBlocks

**řádky** , se zobrazí pod **transcriptBlocks**, popisují řádků přepisy nalezena ve videu.

Atribut | Popis 
---|---
id| Identifikátor řádku.
timeRange|Časové rozmezí v původní video.
adjustedTimeRange|AdjustedTimeRange je časový rozsah, relativně k aktuální seznam stop. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, můžete proveďte video jednu hodinu a používat pouze 1 řádek z něj, například 10:00 – 10:15. V takovém případě bude mít seznam stop 1 řádek, kde časový rozsah je 10:00 – 10:15, ale adjustedTimeRange je 00:00 – 00:15.
participantID| Id mluvčího tohoto řádku.
text| Přepis.
isIncluded| V základní členění vždy hodnotu true. V odvozených seznamy stop, jsou řádky, které byly zahrnuté ve zdroji video, nastavte na isIncluded = true. Všechny ostatní řádky jsou false.

### <a name="ocrs"></a>ocrs

**řádky** , se zobrazí pod **ocrs**, popisují řádků OCRs nalezena ve videu.

Atribut | Popis 
---|---
id|ID rozpoznávání znaků.
Šířka|Tento atribut se v současné době nepoužívá.
Výška|Tento atribut se v současné době nepoužívá.
Jazyk|Jazyk rozpoznávání znaků.
textData|Text rozpoznávání znaků.
Spolehlivosti|Skóre spolehlivosti (je vyšší lepší).

## <a name="scenes"></a>scény

Atribut | Popis 
---|---
id|ID scény.
timeRange|Obsahuje jeden **timeRange**.
klíčový snímek|Čas klíče rámečku.
snímky|Může obsahovat jedno nebo více [snímky](#shots).

## <a name="shots"></a>snímky

Atribut | Popis 
---|---
id||Snímek ID.
timeRange|Obsahuje jeden **timeRange**.
klíčový snímek|Čas klíče rámečku.

## <a name="audioeffectinstances"></a>audioEffectInstances

Atribut | Popis 
---|---
type|Index zvuk události: smích = 1, HandClaps = 2, Hudba = 3, řeči = 4, interval = 5
rozsahy|Může obsahovat jedno nebo více [rozsahy](#ranges).

## <a name="ranges"></a>rozsahy

**rozsahy** , se zobrazí pod **audioEffectInstances**, popisují zvuk důsledky v těchto oblastech.

Atribut | Popis 
---|---
timeRange|Časové rozmezí v původní video.
adjustedTimeRange|AdjustedTimeRange je časový rozsah, relativně k aktuální seznam stop. Vzhledem k tomu, že můžete vytvořit seznam stop z různých odvětví různých videa, může trvat jednu hodinu video a použijte pouze jeden řádek z něj, například 10:00 – 10:15. V takovém případě bude mít seznam stop 1 řádek, kde časový rozsah je 10:00 – 10:15, ale adjustedTimeRange je 00:00 – 00:15.

## <a name="annotations"></a>Poznámky

Vrátí značky na základě rozpoznatelném objekty, životností lidé, kulisy, akce a visual vzory.

|Atribut|Popis|
|---|---|
|id|ID anotace.|
|Název|Název poznámky (například osoba, sportovní herní rámce černé).|
|Vzhled|Může obsahovat jeden nebo více vzhled.|

## <a name="brands"></a>značky

Firmy a produktu brand názvy v převod řeči na text přepis nebo Video rozpoznávání znaků. Nezahrnuje visual rozpoznávání značky nebo logo zjišťování.

Atribut | Popis
---|---
id | ID značky.
jméno | Název značky ze služby Bing nebo vlastní značky.  
wikiId | Přípona adresy URL wikipedia značky. Například "Target_Corporation" se o příponu [ https://en.wikipedia.org/wiki/Target_Corporation ](https://en.wikipedia.org/wiki/Target_Corporation).
wikiUrl | Značku je Wikipedia adresu url, pokud existuje. Příklad: [https://en.wikipedia.org/wiki/Target_Corporation](https://en.wikipedia.org/wiki/Target_Corporation).
Spolehlivosti | Hodnota spolehlivosti detektor brand Video Indexer (0-1).
description | Popis brand extrahují z Wikipedia. 
Vzhled | Může obsahovat jeden nebo více vzhled.
seenDuration | Přítomnosti relativně k video doba trvání (0-1).

## <a name="next-steps"></a>Další postup

Informace o tom, jak vytvořit vlastní rozpis naleznete v tématu [zobrazit a upravit Video Indexer insights](video-indexer-view-edit.md).

Informace o tom, jak vložit pomůcky ve vaší aplikaci najdete v tématu [vložit Video Indexer pomůcky do svých aplikací](video-indexer-embed-widgets.md). 

## <a name="see-also"></a>Další informace najdete v tématech

[Video Indexer rozhraní API](https://videobreakdown.portal.azure-api.net/docs/services/582074fb0dc56116504aed75/operations/5857caeb0dc5610f9ce979e4)

[Video s přehledem indexeru](video-indexer-overview.md)

