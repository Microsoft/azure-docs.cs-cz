---
title: Nejčastější dotazy ohledně Video Indexer – Azure
titlesuffix: Azure Media Services
description: Získejte odpovědi na nejčastější dotazy týkající se Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.topic: article
ms.date: 01/31/2019
ms.author: juliako
ms.openlocfilehash: 84f2b4d68bf95d18a63401f796e2ffca682c808f
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567268"
---
# <a name="frequently-asked-questions"></a>Nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Video Indexer.

## <a name="general-questions"></a>Obecné otázky

### <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer je služba umělé inteligence, který je součástí Microsoft Azure Media Services. Video Indexer, poskytuje orchestraci více modelů strojového učení, které vám umožní snadno extrahovat podrobné informace z videa. Poskytnout přehled pokročilé a přesné, Video Indexer umožňuje použít více kanálů videa: zvuk, řeč nebo vizuál. Insights video Indexer lze použít v mnoha způsoby, jako jsou vylepšení obsahu objevitelnosti a dostupnosti, vytváření nových příležitostí finanční zhodnocení, nebo vytváření nového prostředí, která používat přehledy. Video Indexer poskytuje webové rozhraní pro testování, konfigurace a přizpůsobení sad modely ve vašem účtu. Vývojáři pomocí rozhraní API založené na REST integrovat funkce Video Indexer do produkčního systému. 

### <a name="what-can-i-do-with-video-indexer"></a>Co můžu dělat s Video Indexer?

Operace, které můžete provádět funkce Video Indexer na mediálních souborů patří:

* Identifikace a extrahování řeči a identifikaci mluvčích.
* Identifikace a extrahování na obrazovce text ve videu.
* Zjišťování objektů v souboru videa.
* Identifikujte značky (například: Microsoft) z zvukové stopy a na obrazovce text ve videu.
* Detekce a rozpoznávání tváře z databáze celebrit a uživatelem definované databázové tváří.
* Extrahování témata popsána, ale ne nutně podle audio a video obsahu.
* Vytváření z zvukové stopy titulků.

Další informace a další funkce Video Indexer, naleznete v tématu [přehled](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak můžu začít pracovat s Video Indexer?

Video Indexer zahrnuje bezplatné zkušební verze služeb, který vám poskytne 600 minut – webové rozhraní a 2 400 několika minut prostřednictvím rozhraní API. Je možné [přihlášení k webové rozhraní Video Indexer](https://www.videoindexer.ai/) a vyzkoušejte si to sami pomocí libovolné identitě webové a bez nutnosti nastavení předplatného služby Azure. 

Rejstřík videí a zvukových čarou ve velkém měřítku Video Indexer se můžete připojit k placeným předplatným Microsoft Azure. Můžete najít další informace o cenách na [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) stránky.

Můžete najít další informace o zahájení práce [Začínáme](video-indexer-get-started.md).

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Je třeba použít Video Indexer dovednosti kódování?

Webové rozhraní Video Indexer můžete použít k vyhodnocení, konfigurovat a spravovat váš účet s **bez nutnosti kódování**.  Až budete připravení k vývoji složitějších aplikací, můžete použít [Video Indexer API](https://api-portal.videoindexer.ai/) Video Indexer integrovat do vlastních aplikací, webů, nebo [vlastních pracovních postupů pomocí technologie bez serveru, jako jsou Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) nebo Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Je třeba machine learning dovednosti: pište Video Indexer použít?

Ne, Video Indexer umožňuje integraci několika modelech strojového učení do jednoho kanálu. Indexování videa nebo zvukový soubor prostřednictvím Video Indexer načte kompletní sadu insights extrahovala na jedné sdílené osy bez jakékoli strojového učení dovednosti a znalosti algoritmy potřeby v části zákazníka.

### <a name="what-media-formats-does-video-indexer-support"></a>Video Indexer Podpora formátů médií podporuje?

Video Indexer podporuje nejběžnější formáty multimédií. Odkazovat [Azure Media Encoderu standard formáty](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats) seznamu další podrobnosti.

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Jak provést budu při nahrávání média do Video Indexer?

V Video Indexer webový portál, můžete nahrát soubor média pomocí dialogu pro nahrání souboru nebo najetím myší na adresu URL, která přímo hostuje zdrojový soubor (viz [příklad](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Libovolnou adresu URL, která nebude fungovat hostitele média obsahu pomocí elementu iFrame nebo kód pro vložení (viz [příklad](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). Video Indexer API je potřeba zadat vstupní soubor prostřednictvím adresy URL nebo s polem bajtů. Odesílání prostřednictvím adresy URL pomocí rozhraní API jsou omezené na 10 GB, ale nemají doba trvání časového limitu. Další informace, přečtěte si tento [Příručka](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Jak dlouho trvá Video Indexer k extrahování přehledů z média?

Množství čas potřebný k indexování videa nebo zvukový soubor, pomocí rozhraní Video Indexer API a webové rozhraní Video Indexer, závisí na několika parametrů, třeba soubor délky a kvality, počet přehledů, které se nacházejí v souboru, počet [jednotky rezervované pro](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) k dispozici a zda [koncový bod streamování](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) je nebo není povolená. U většiny typů obsahu za předpokladu, že jsou povolené 10 rezervovaných jednotek S3 typu, můžeme předpovídat, že indexování přebírá z 1/3 ½ doby trvání zvukový nebo video soubor (například zdroj během 10 minut videa přebírá 3.5 až 5 minut). Doporučujeme však, že při spuštění několika testovacích souborů s vlastním obsahem a průměr získat lepší představu.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Můžete vytvořit vlastní pracovní postupy pro automatizaci procesů pomocí funkce Video Indexer

Ano, můžete integrovat funkce Video Indexer do technologiích bez serverů jako Logic Apps, Flow a [Azure Functions](https://azure.microsoft.com/services/functions/). Další podrobnosti najdete v [aplikace logiky](https://azure.microsoft.com/services/logic-apps/) a [tok](https://flow.microsoft.com/en-us/) konektory pro Video Indexer [tady](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>V které oblasti Azure je Video indexer dostupný?

Můžete zjistit, které oblasti Azure Video Indexer je k dispozici na [oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) stránky.

### <a name="what-is-the-sla-for-video-indexer"></a>Co je smlouva SLA pro Video Indexer?

Mediálních službách Azure SLA vztahuje Video Indexer a můžete najít na [SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) stránky. Smlouvy SLA pouze platí do nástroje Video Indexer placené účtů a se nevztahují na bezplatná zkušební verze.

## <a name="privacy-questions"></a>Otázek ochrany osobních údajů

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Indexovaných videí a zvukových souborů nástrojem Video Indexer uložené?

Ano, není-li odstranit soubor z Video Indexer, buď pomocí webu pro Video Indexer nebo rozhraní API, videí a zvukových souborů jsou uložené. Pro bezplatnou zkušební verzi, videa a zvuku soubory, které jsou uloženy indexu v oblasti Azure USA – východ. V opačném případě videí a zvukových souborů jsou uložené v účtu úložiště vašeho předplatného Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Můžete odstranit soubory, které jsou uložené na Video Indexer portálu?

Ano, můžete vždy odstranit vaše video a zvukové soubory a metadata a přehledy z nich extrahovaná modulem Video Indexer. Po odstranění souboru z Video Indexer, souboru a jeho metadat a informací díky jsou trvale odstraněn z Video Indexer. Nicméně pokud jste implementovali vlastní řešení zálohování ve službě Azure storage, soubor zůstane ve službě Azure storage.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Můžete řídit přístup uživatelů k Můj účet služby Video Indexer

Ano, jenom správce účtu může pozvat a zrušit pozvání uživatelů ke svým účtům, jakož i přiřadit, kdo má oprávnění pro úpravy a kdo má přístup jen pro čtení.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Kdo má přístup k souborům videa a zvuku, které byly indexované nebo uložit Video Indexer a metadat a přehledy, které byly extrahovány?

Vaše videa nebo zvukový obsah, které mají veřejné jako jeho nastavení ochrany osobních údajů je přístupný každému, kdo má odkaz na vaše videa nebo zvukový obsah a jeho insights. Vaše videa nebo zvukový obsah, který se mají soukromé jako jeho nastavení ochrany osobních údajů je přístupný pouze uživatelé, kteří byli pozváni do účtu videa nebo zvukový obsah. Nastavení ochrany osobních údajů obsah platí také pro metadata a přehledy, které se extrahuje Video Indexer. Přiřadíte nastavení ochrany osobních údajů při nahrávání videa nebo zvukový soubor. Můžete také změnit nastavení ochrany osobních údajů po indexování.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Moje videa nebo zvukový souborů, které byly indexované nebo uložit Video Indexer a metadat a přehledy, které byly extrahovány jaký přístup má Microsoft?

Za [podmínky Online služeb Azure](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) zcela vlastní obsah (OST) a Microsoft se jenom přístup k obsahu a metadat a přehledy, které funkce Video Indexer extrahuje ze svého obsahu podle úložiště offline a Microsoft Prohlášení o ochraně osobních údajů.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Jsou vlastní modely, které vytvářím Můj účet služby Video Indexer jako dostupné pro jiné účty?

 Ne, vlastní modely, které vytvoříte ve svém účtu nejsou k dispozici na jiný účet. Video Indexer aktuálně umožňuje vytvářet vlastní [značky](customize-brands-model-overview.md), [jazyk](customize-language-model-overview.md), a [osoba](customize-person-model-overview.md) modely ve vašem účtu. Tyto modely jsou dostupné jenom v účtu, ve kterém jste vytvořili modely.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Indexované Video Indexer uchovávat v rámci oblasti Azure, kde používám Video Indexer obsah?

Ano, obsah a jeho insights uchovávají v rámci oblasti Azure bez ruční konfigurace ve vašem předplatném Azure, která používá víc oblastí Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Co jsou zásady ochrany osobních údajů pro Video Indexer?

Video Indexer se bude vztahovat [prohlášení o ochraně osobních údajů Microsoft](https://privacy.microsoft.com/privacystatement). Prohlášení o ochraně osobních údajů vysvětluje osobní údaje, které Microsoft zpracovává, jak Microsoft procesy a pro jaké účely Microsoft zpracovává je. Další informace o ochraně osobních údajů, najdete [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Jaké certifikace Video Indexer máte?

Video Indexer nyní má hlavní certifikace SOC. Zkontrolujte certifikační Video Indexer, najdete [Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

## <a name="api-questions"></a>Dotazy rozhraní API

### <a name="what-apis-does-video-indexer-offer"></a>Rozhraní API, jejichž Video Indexer nabízí?

Rozhraní API pro video Indexer umožňuje indexování, extrahování metadat, správu majetku, překlad, vkládání, přizpůsobení těchto modelů a informace. Pokud chcete najdete podrobnější informace o používání rozhraní Video Indexer API, přečtěte si [portál pro vývojáře Video Indexer](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Jaké klientské sady SDK nabízí funkce Video Indexer?

Aktuálně neexistují žádné klienta, kterou nabízí sady SDK. Video Indexer tým pracuje na sady SDK a plány, které ho doručujte brzy.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak můžu začít pracovat s rozhraním API pro Video Indexer?

Postupujte podle [kurz: Začínáme s rozhraním API pro Video Indexer](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Jaký je rozdíl mezi Video Indexer API a rozhraní API v3 mediálních služeb Azure?

Aktuálně nejsou některé překrytí v funkcí, které nabízí rozhraní Video Indexer API a rozhraní API v3 mediálních služeb Azure. Další informace o tom, jak porovnat obě služby [tady](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Co je přístupový token rozhraní API a proč to potřebujete?

Video Indexer API obsahuje rozhraní API autorizace a operace rozhraní API. Povolení rozhraní API obsahuje volání, které vám poskytnou přístupový token. Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

K použití rozhraní Video Indexer API z bezpečnostních důvodů jsou požadována přístupové tokeny. Tím se zajistí, že všechna volání pocházejí od vás nebo uživatelům, kteří mají oprávnění k přístupu k účtu. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaký je rozdíl mezi Video přístupový token, přístupový token účtu a přístupový token uživatele?

* Úroveň účtu – tokenů úroveň přístupu účtu vám umožní provádět operace na úrovni účtu nebo videa. Například nahrát video, seznam všech videí, získat nové poznatky z videí.
* Uživatelů level – tokeny uživatele úroveň přístupu vám umožní provádět operace na úrovni uživatele. Třeba získat přidružené účty.
* Úrovni videa – tokeny přístupu na úrovni videa umožňují provádět operace s konkrétní videa. Třeba získat nové přehledy z videa, stáhnout titulky, získat widgety apod.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak často je potřeba získat nový přístupový token? Po vypršení platnosti tokenů přístupu?

Přístupové tokeny vyprší každou hodinu, takže je potřeba vygenerovat nový přístupový token každou hodinu. 

## <a name="billing-questions"></a>Dotazy k fakturaci

### <a name="how-much-does-video-indexer-cost"></a>Kolik stojí Video Indexer

Video Indexer využívá jednoduchou s průběžnými platbami cenový model založený na době trvání obsahu vstupu, který je index. Další se můžou vztahovat poplatky pro kódování, streamování, úložiště, využití sítě a média rezervovaných jednotek. Další informace najdete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) stránky.

### <a name="does-video-indexer-offer-a-free-trial"></a>Nabízí funkce Video Indexer bezplatnou zkušební verzi?

Ano, Video Indexer nabízí bezplatnou zkušební verzi, která poskytuje kompletní a funkce rozhraní API. Používá se kvóta 600 minut za videa pro uživatele webové rozhraní a 2 400 minut, než se uživatelé rozhraní API. 

## <a name="next-steps"></a>Další postup

[Přehled](video-indexer-overview.md)
