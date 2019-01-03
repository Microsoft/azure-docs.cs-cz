---
title: Nejčastější dotazy ohledně Video Indexer – Azure
titlesuffix: Azure Media Services
description: Získejte odpovědi na nejčastější dotazy týkající se Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 12/19/2018
ms.author: juliako
ms.openlocfilehash: 972858b4bae995b6e9073cf7ee451a317e9f3909
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730986"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Video Indexer.

## <a name="general-questions"></a>Obecné dotazy

### <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer je služba extrakci metadat pro audio a video mediálních souborů z Azure Media Services. Využívá širokou škálu algoritmů strojového učení k tomu, aby klasifikovat, analyzovat a získat přehled o video a audiostreamů obsahu pomocí [předdefinované modely](video-indexer-overview.md). Můžete použít tyto přehledy ve spoustě ohledů jako vylepšování obsahu objevitelnosti a dostupnosti, vytváření nových příležitostí finanční zhodnocení nebo vytváření nového prostředí, které využívají přehledy.

Video Indexer poskytuje webové rozhraní pro testování, konfigurace a vlastního nastavení a o rozhraní API založené na protokolu REST vývojářům integrovat do produkčního systému.

### <a name="what-can-i-do-with-video-indexer"></a>Co můžu dělat s Video Indexer?

Video Indexer lze provádět následující typy operací v mediálních souborů:

* Identifikujte a extrahovat řeči a identifikaci mluvčích.
* Identifikovat a na obrazovce extrahovat text ve videu.
* Identifikovat a označit objekty v souboru videa.
* Identifikujte značek jako je Microsoft z zvukové stopy a na obrazovce text ve videu.
* Detekci a rozpoznávání tváří z databáze celebrit a uživatelem definované databázové tváří.
* Extrakce klíčových slov z audio a video obsahu na základě textu na mluvené slovo a vizuální.
* Extrahujte témata popsané, ale ne nutně explicitně podle audio a video obsahu na základě textu na mluvené slovo a vizuální.
* Vytvoření z zvukové stopy titulků.

Další informace najdete v tématu [Přehled](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak můžu začít pracovat s Video Indexer?

Video Indexer je zdarma vyzkoušet. Bezplatná zkušební verze vám poskytne 600 minut – webové rozhraní a 2 400 minut prostřednictvím rozhraní API.

Rejstřík videí a zvukových čarou ve velkém měřítku Video Indexer se můžete připojit k placeným předplatným Microsoft Azure. Můžete najít další informace o cenách na [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) stránky.

Můžete najít další informace o zahájení práce [Začínáme](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Je třeba použít Video Indexer dovednosti kódování?

Rozhraní Video Indexer API můžete integrovat do kanálu, nebo můžete použít portál Video Indexer a není potřeba psát jakýkoli kód vůbec. 

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Je třeba machine learning dovednosti: pište Video Indexer použít?

Ne, můžete extrahovat přehledy z obrazový a zvukový obsah zcela bez jakékoli strojového učení dovednosti a znalosti algoritmy. 

### <a name="what-media-formats-does-video-indexer-support"></a>Video Indexer Podpora formátů médií podporuje?

Video Indexer podporuje nejběžnější formáty multimédií. Najdete v seznamu Azure Media Encoderu standard formáty pro další podrobnosti.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Jak provést budu při nahrávání média do Video Indexer?

V Video Indexer webový portál můžete nahrát soubor média pomocí dialogu pro nahrání souboru nebo najetím myší na adresu URL, která přímo určuje soubor. Například, `http://contoso.cloudapp.net/videos/example.mp4`. Odkaz na stránku obsahující videopřehrávače jako `http://contoso.cloudapp.net/videos` nebude fungovat. Video Indexer API je potřeba zadat vstupní soubor prostřednictvím adresy URL nebo s polem bajtů. Všimněte si, že nahraje prostřednictvím adresy URL jsou omezené na 10 GB, ale nemají doba trvání časového limitu. Další informace, přečtěte si tento [Příručka](upload-index-videos.md).

### <a name="how-long-does-it-take-visual-indexer-to-extract-insights-from-media"></a>Jak dlouho trvá Visual indexeru k extrahování přehledů z média?

Množství čas potřebný k indexování videa nebo zvukový soubor, pomocí rozhraní Video Indexer API a webové rozhraní Video Indexer, závisí na více parametrů, jako je délka souboru a kvality, počet přehledy, na které se nacházejí v souboru, počet výpočetních sjednotí k dispozici, a určuje, zda je koncový bod streamování povolená nebo ne. U většiny typů obsahu za předpokladu, že jsou povolené 10 jednotek ru S3, můžeme předpovídat, že indexování bude trvat od 1/3 ½ délka videa.  Doporučujeme však, že při spuštění několika testovacích souborů s vlastním obsahem a průměr získat lepší představu. 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>V které oblasti Azure je Video indexer dostupný?

Můžete zjistit, které oblasti Azure Video Indexer je k dispozici na [oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) stránky.

### <a name="what-is-the-sla-for-video-indexer"></a>Co je smlouva SLA pro Video Indexer?

Bezplatná zkušební verze pro Video Indexer neuzavírá žádná smlouva SLA. Služba Azure Media smlouva SLA se vztahuje Video Indexer.

Informace můžete najít na [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_0/) stránky.

## <a name="billing-questions"></a>Dotazy k fakturaci

### <a name="how-much-does-video-indexer-cost"></a>Kolik stojí Video Indexer

Video Indexer používá jednoduché platit za využité cenový model podle doby trvání vstupního obsahu. Další se můžou vztahovat poplatky pro kódování, streamování, úložiště, využití sítě a média rezervovaných jednotek. Aktuální ceny najdete [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) stránky.

### <a name="does-video-indexer-offer-a-free-trial"></a>Nabízí funkce Video Indexer bezplatnou zkušební verzi?

Ano, Video Indexer nabízí bezplatnou zkušební verzi, která je hostována v oblasti Azure USA – východ a poskytuje kompletní a funkce rozhraní API. Používá se kvóta 10 hodin za videa pro uživatele, webu a 40 hodin pro rozhraní API uživatele. 

## <a name="security-questions"></a>Bezpečnostní otázky

### <a name="are-audio-and-video-files-indexed-by-video-indexer-stored"></a>Indexovaných zvukových souborů a videosouborů nástrojem Video Indexer uložené?

Ano, pokud se pokusíte odstranit soubor z Video Indexer buď pomocí webu pro Video Indexer nebo rozhraní API, videí a zvukových souborů se uloží. Pro bezplatnou zkušební verzi, videa a zvuku souborů, které si index se uloží v oblasti Azure USA – východ. V opačném případě videí a zvukových souborů se uloží v aktuálním účtu úložiště vašeho předplatného Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Můžete odstranit soubory, které jsou uložené na Video Indexer portálu?

Ano, můžete vždy odstranit videosoubory a zvukové soubory, jakož i jejich z Video Indexer. Po odstranění souborů je trvale odstraněný z Video Indexer a všude, kde Video Indexer uložili soubor (oblasti Azure USA – východ pro účty a účet úložiště z vašeho předplatného Azure jinak).

### <a name="who-has-access-to-my-audio-and-video-files-and-that-have-been-indexed-andor-stored-by-video-indexer"></a>Kdo má přístup k audio a video soubory a, který je indexované nebo uložit nástrojem Video Indexer?

Ano, můžete vždy odstranit videosoubory a zvukové soubory, jakož i jejich z Video Indexer. Po odstranění souborů je trvale odstraněný z Video Indexer a všude, kde Video Indexer uložili soubor (oblasti Azure USA – východ pro účty a účet úložiště z vašeho předplatného Azure jinak).

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Můžete řídit přístup uživatelů k Můj účet služby Video Indexer

Ano, můžete vybrat, kdo je pozvaný do účtu.

### <a name="who-has-access-to-the-insights-that-were-extracted-from-my-audio-and-video-files-by-video-indexer"></a>Kdo má přístup k přehledům, které byly z mé zvukových souborů a videosouborů extrahovaná modulem Video Indexer?

Videa, které mají veřejné jako jeho nastavení ochrany osobních údajů je přístupný každému, kdo má odkaz na video a jeho insights. Videa, které mají privátní jako jeho nastavení ochrany osobních údajů je přístupný pouze uživatelé, kteří byli pozváni do účtu videa.

### <a name="do-i-still-own-my-content-that-have-been-indexed-and-stored-by-video-indexer"></a>Můžu pořád Můj obsah, který se indexují a uložené modulem Video Indexer vlastní?

Ano, za [podmínky Online služeb Azure](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), vlastní obsah.

### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Indexované Video Indexer uchovávat v rámci oblasti Azure, kde používám Video Indexer obsah?

Ano, obsah a jeho insights budou zachovány v rámci oblasti Azure bez ruční konfigurace ve vašem předplatném Azure, která používá víc oblastí Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Co jsou zásady ochrany osobních údajů pro Video Indexer?

Video Indexer se bude vztahovat [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="api-questions"></a>Dotazy rozhraní API

### <a name="what-are-the-differences-in-capability-on-the-video-indexer-website-versus-the-video-indexer-api"></a>Jaké jsou rozdíly mezi funkce na webu Video Indexer a Video Indexer API?

S [Video Indexer](https://www.videoindexer.com) webu, můžete vyhledávání v knihovně videí, analyzovat přehledy, přizpůsobení modelů, nastavte v účtu a upravovat vaše videa s lehkostí a elegancí. S robustní [rozhraní REST API služby Video Indexer](https://api-portal.videoindexer.ai/), můžete integrovat s žádnou infrastrukturu nebo vložení widgetů přímo v webu nebo aplikaci.

### <a name="what-apis-does-video-indexer-offer"></a>Rozhraní API, jejichž Video Indexer nabízí?

Můžete najít informace o použití rozhraní Video Indexer API na [portál pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/)

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak můžu začít pracovat s rozhraním API pro Video Indexer?

Postupujte podle [kurz: Začínáme s rozhraním API pro Video Indexer](video-indexer-use-apis.md).

### <a name="what-is-an-access-token"></a>Co je přístupový token?

Video Indexer API obsahuje rozhraní API autorizace a operace rozhraní API. Povolení rozhraní API obsahuje volání, které vám poskytnou přístupový token. Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaký je rozdíl mezi Video přístupový token, přístupový token účtu a přístupový token uživatele?

Představují rozsah autorizace volání.

* Uživatelů level – tokeny uživatele úroveň přístupu vám umožní provádět operace na úrovni uživatele. Třeba získat přidružené účty.
* Úroveň účtu – tokenů úroveň přístupu účtu vám umožní provádět operace na úrovni účtu nebo videa. Například nahrát video, vypsat všechna videa, získat přehledy z videa apod.
* Úrovni videa – tokeny přístupu na úrovni videa umožňují provádět operace s konkrétní videa. Třeba získat nové přehledy z videa, stáhnout titulky, získat widgety apod.

Můžete řídit, jestli tyto tokeny jsou jen pro čtení nebo povolit úpravy zadáním allowEdit = true nebo false.

### <a name="why-do-i-need-access-tokens-when-using-the-video-indexer-apis"></a>Proč potřebuji přístupové tokeny, při použití rozhraní Video Indexer API?

K použití rozhraní Video Indexer API z bezpečnostních důvodů jsou požadována přístupové tokeny. Tím se zajistí, že všechna volání pocházejí od vás nebo uživatelům, kteří mají oprávnění k přístupu k účtu. 

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak často je potřeba získat nový přístupový token? Po vypršení platnosti tokenů přístupu?

Přístupové tokeny vyprší každou hodinu, takže je potřeba vygenerovat nový přístupový token každou hodinu. 

## <a name="next-steps"></a>Další postup

[Přehled](video-indexer-overview.md)
