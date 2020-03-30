---
title: Nejčastější dotazy týkající se video indexeru – Azure
titleSuffix: Azure Media Services
description: Tento článek poskytuje odpovědi na nejčastější dotazy týkající se Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: eacbeca275192e1a68b6682c3036da2d5c09bd54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619883"
---
# <a name="video-indexer-frequently-asked-questions"></a>Nejčastější dotazy videoindexeru

Tento článek odpovídá na nejčastější dotazy týkající se videoindexeru.

## <a name="general-questions"></a>Obecné otázky

### <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer je služba umělé inteligence, která je součástí mediálních služeb Microsoft Azure. Video Indexer poskytuje orchestraci více modelů strojového učení, které vám umožní snadno extrahovat přímý přehled z videa. Aby poskytoval pokročilé a přesné přehledy, využívá Video Indexer více kanálů videa: zvuk, řeč a obraz. Přehledy videoindexeru lze použít mnoha způsoby, například zlepšení mnoství a přístupnosti obsahu, vytváření nových příležitostí ke zpeněžení nebo vytváření nových prostředí, která tyto přehledy využívají. Video Indexer poskytuje webové rozhraní pro testování, konfiguraci a přizpůsobení modelů ve vašem účtu. Vývojáři mohou pomocí rozhraní API založenéna REST integrovat Video Indexer do produkčního systému. 

### <a name="what-can-i-do-with-video-indexer"></a>Co mohu dělat s video indexerem?

Mezi operace, které může videoindexer provádět s mediálními soubory, patří:

* Identifikace a extrakce řeči a identifikace řečníků.
* Identifikace a extrahování textu na obrazovce ve videu.
* Detekce objektů v souboru videa.
* Identifikujte značky (například Microsoft) ze zvukových stop a textu na obrazovce ve videu.
* Detekce a rozpoznávání tváří z databáze celebrit a uživatelem definované databáze tváří.
* Extrahování diskutovaných témat, ale ne nutně uvedených v audio a video obsahu.
* Vytváření skrytých titulků nebo titulků ze zvukové stopy.

Další informace a další funkce videoindexeru naleznete v [tématu Přehled](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Jak začít s video indexerem?

Video Indexer obsahuje bezplatnou zkušební verzi nabízí, která vám poskytuje 600 minut ve webovém rozhraní a 2 400 minut prostřednictvím rozhraní API. Můžete [se přihlásit do webového rozhraní Video Indexer](https://www.videoindexer.ai/) a vyzkoušet si to sami pomocí libovolné webové identity a bez nutnosti nastavení předplatného Azure. 

Chcete-li indexovat videa a zvuk letí ve velkém měřítku, můžete připojit Video Indexer k placenépředplatné Microsoft Azure. Další informace o cenách naleznete na stránce [s cenami.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

Další informace o tom, jak začít, najdete v [začínáme](video-indexer-get-started.md)v

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Potřebuji kódování dovednosti používat Video Indexer?

Webové rozhraní Video Indexer můžete použít k vyhodnocení, konfiguraci a správě účtu **bez nutnosti kódování**.  Až budete připraveni vyvíjet složitější aplikace, můžete pomocí [rozhraní API video indexeru](https://api-portal.videoindexer.ai/) integrovat video indexer do vlastních aplikací, webů nebo [vlastních pracovních postupů pomocí technologií bez serveru, jako jsou Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) nebo Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Potřebuji k používání videoindexeru dovednosti strojového učení?

Ne, Video Indexer poskytuje integraci více modelů strojového učení do jednoho kanálu. Indexování video nebo audio souboru prostřednictvím Video Indexer načte úplnou sadu poznatků extrahovaných na jedné sdílené časové ose bez jakýchkoli dovedností strojového učení nebo znalostí o algoritmech potřebných na straně zákazníka.

### <a name="what-media-formats-does-video-indexer-support"></a>Jaké formáty médií videoindexer podporuje?

Video Indexer podporuje většinu běžných formátů médií. Další podrobnosti najdete v seznamu [standardních formátů Kodéru médií Azure.](https://docs.microsoft.com/azure/media-services/latest/media-encoder-standard-formats)

### <a name="how-to-do-i-upload-a-media-into-video-indexer"></a>Jak nahrát médium do video indexeru?

Na webovém portálu Video Indexer můžete nahrát mediální soubor pomocí dialogového okna pro nahrávání souborů nebo odkazem na adresu URL, která je přímo hostitelem zdrojového souboru (viz [příklad).](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4) Žádná adresa URL, která je hostitelem mediálního obsahu pomocí kódu iFrame nebo embed, nebude fungovat (viz [příklad](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). Rozhraní API video indexeru vyžaduje zadání vstupního souboru pomocí adresy URL nebo bajtového pole. Nahrávání přes adresu URL pomocí rozhraní API je omezeno na 10 GB, ale nemá časový limit. Další informace naleznete v tomto [návodu .](https://docs.microsoft.com/azure/media-services/video-indexer/upload-index-videos)

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Jak dlouho trvá video indexeru extrahovat poznatky z médií?

Doba potřebný k indexování souboru videa nebo zvuku, a to jak pomocí rozhraní API video indexeru, tak webového rozhraní Video Indexer, závisí na více parametrech, jako je délka a kvalita souboru, počet přehledů nalezených v souboru, počet [rezervovaných jednotek,](https://docs.microsoft.com/azure/media-services/previous/media-services-scale-media-processing-overview) které jsou k dispozici, a na tom, zda je [koncový bod streamování](https://docs.microsoft.com/azure/media-services/previous/media-services-streaming-endpoints-overview) povolen nebo ne. Doporučujeme spustit několik testovacích souborů s vlastním obsahem a průměrně získat lepší představu.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Mohu vytvořit přizpůsobené pracovní postupy pro automatizaci procesů pomocí videoindexeru?

Ano, video indexer můžete integrovat do technologií bez serveru, jako jsou Logic Apps, Flow a [Azure Functions](https://azure.microsoft.com/services/functions/). Další podrobnosti o [aplikaci logiky](https://azure.microsoft.com/services/logic-apps/) a [toku](https://flow.microsoft.com/en-us/) konektory pro Video Indexer [zde](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). 

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Ve kterých oblastech Azure je k dispozici indexer videa?

Můžete zjistit, které oblasti Azure Video Indexer je k dispozici na stránce [oblasti.](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Lze přizpůsobit modely video indexeru pro konkrétní případ použití? 

Ano. V Video Indexer můžete přizpůsobit některé z dostupných modelů, aby lépe vyhovovalvašim potřebám. 

Například náš model Osoba podporuje out-of-the-box 1,000,000 tváře celebrity uznání, ale můžete také trénovat rozpoznat další tváře, které nejsou v této databázi. 

Podrobnosti naleznete v článcích o přizpůsobení [modelů Osoba](customize-person-model-overview.md), [Značky](customize-brands-model-overview.md)a [Jazyk.](customize-language-model-overview.md) 

### <a name="what-is-the-sla-for-video-indexer"></a>Co je sla pro video indexer?

SLA služby Azure Media Service pokrývá video indexer a najdete ji na stránce [SLA.](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) SLA se vztahuje pouze na placené účty Video Indexer a nevztahuje se na bezplatnou zkušební verzi.

## <a name="privacy-questions"></a>Otázky týkající se ochrany osobních údajů

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Jsou video a zvukové soubory indexovány video indexerem uloženy?

Ano, pokud soubor neodstraníte z video indexeru pomocí webu Video Indexer nebo rozhraní API, budou uloženy soubory videa a zvuku. Pro bezplatnou zkušební verzi jsou video a zvukové soubory, které indexujete, uloženy v oblasti Azure – východ USA. V opačném případě jsou vaše video a zvukové soubory uloženy v účtu úložiště vašeho předplatného Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Mohu odstranit soubory, které jsou uloženy na portálu Video Indexer?

Ano, vždy můžete odstranit video a audio soubory, stejně jako všechna metadata a přehledy získané z nich Video Indexer. Po odstranění souboru z video indexeru, soubor a jeho metadata a přehledy jsou trvale odebrány z Video Indexer. Pokud jste však implementovali vlastní řešení zálohování v úložišti Azure, soubor zůstane ve vašem úložišti Azure.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Mohu řídit přístup uživatelů ke svému účtu Video Indexer?

Ano, pouze správci účtu mohou pozvat a zrušit pozvání lidí ke svým účtům a přiřadit, kdo má oprávnění k úpravám a kdo má přístup jen pro čtení.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Kdo má přístup k mým video a zvukovým souborům, které byly indexovány a/ nebo uloženy video indexerem a metadata a přehledy, které byly extrahovány?

K vašemu videonebo zvukovému obsahu, který má jako nastavení ochrany osobních údajů veřejné, přistupuje kdokoli, kdo má odkaz na váš video nebo zvukový obsah a jeho přehledy. K vašemu videonebo zvukovému obsahu, který má soukromé nastavení ochrany osobních údajů, mají přístup pouze uživatelé, kteří byli pozváni k účtu video nebo zvukového obsahu. Nastavení ochrany osobních údajů vašeho obsahu se vztahuje také na metadata a přehledy, které video Indexer extrahuje. Při nahrávání video souboru nebo zvukového souboru přiřadíte nastavení ochrany osobních údajů. Po indexování můžete také změnit nastavení ochrany osobních údajů.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Jaký má společnost Microsoft přístup k mým video souborům nebo zvukovým souborům, které byly indexovány nebo uloženy programem Video Indexer, a k metadatům a přehledům, které byly extrahovány?

Podle [podmínek služeb Azure Online Services (OST)](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) zcela vlastníte svůj obsah a Microsoft bude přistupovat pouze k vašemu obsahu a metadatům a přehledům, které indexer videa získává z vašeho obsahu podle informací OST a prohlášení o zásadách ochrany osobních údajů společnosti Microsoft.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Jsou vlastní modely, které vytvářím ve svém účtu Video Indexer, dostupné pro jiné účty?

 Ne, vlastní modely, které vytvoříte ve svém účtu, nejsou k dispozici žádnému jinému účtu. Video Indexer v současné době umožňuje vytvářet vlastní [značky](customize-brands-model-overview.md), [jazyk](customize-language-model-overview.md)a [osoby](customize-person-model-overview.md) modely ve vašem účtu. Tyto modely jsou k dispozici pouze v účtu, ve kterém jste vytvořili modely.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Je obsah indexovaný video indexerem uchován v oblasti Azure, kde používám Video Indexer?

Ano, obsah a jeho přehledy se uchovávají v oblasti Azure, pokud nemáte ruční konfiguraci v předplatném Azure, která používá více oblastí Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Jaké jsou zásady ochrany osobních údajů pro Video Indexer?

Na indexer videa se vztahuje [Prohlášení společnosti Microsoft o zásadách ochrany osobních údajů](https://privacy.microsoft.com/privacystatement). Prohlášení o zásadách ochrany osobních údajů vysvětluje osobní údaje, které společnost Microsoft zpracovává, jak je zpracovává a pro jaké účely je zpracovává. Další informace o ochraně osobních údajů naleznete v [Centru zabezpečení společnosti Microsoft](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Jaké certifikace má Video Indexer?

Video Indexer má v současné době certifikaci SOC. Chcete-li zkontrolovat certifikaci video indexeru, naleznete v [centru zabezpečení společnosti Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Jaký je rozdíl mezi soukromými a veřejnými videi? 

Když se videa nahrají do Video Indexeru, můžete si vybrat ze dvou nastavení ochrany osobních údajů: soukromé a veřejné. Veřejná videa jsou přístupná všem, včetně anonymních a neidentifikovaných uživatelů. Soukromé jsou omezeny pouze na členy účtu. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Pokusil jsem se nahrát video jako veřejné a bylo označeno jako nevhodné nebo urážlivé, co to znamená? 

Při nahrávání videa do video indexeru se pomocí algoritmů a modelů provádí automatická analýza obsahu, aby se zajistilo, že nebude veřejně prezentován žádný nevhodný obsah. Pokud je zjištěno, že video je podezřelé jako obsahující explicitní obsah, nebude možné ho nastavit jako veřejné. Členové účtu však stále přístup jako soukromé video (zobrazit, stáhnout přehledy a extrahované artefakty a provádět další operace, které jsou k dispozici pro členy účtu).   

Chcete-li video nastavit pro veřejný přístup, můžete buď: 

* Vytvořte si vlastní vrstvu rozhraní (například aplikaci nebo web) a použijte ji k interakci se službou Video Indexer. Tímto způsobem video zůstane soukromé na našem portálu a vaši uživatelé mohou komunikovat s ním prostřednictvím vašeho rozhraní. Můžete například získat přehledy nebo povolit prohlížení videa ve vlastním rozhraní. 
* Požádejte o lidské přezkoumání obsahu, což by vedlo k odstranění omezení za předpokladu, že obsah není explicitní. 

    Tuto možnost lze prozkoumat, pokud web Video Indexer je používán přímo uživateli jako vrstva rozhraní a pro veřejné (neověřené) zobrazení. 

## <a name="api-questions"></a>Otázky api

### <a name="what-apis-does-video-indexer-offer"></a>Jaká api nabízí Video Indexer?

Rozhraní API video indexeru umožňuje indexování, extrahování metadat, správu datových zdrojů, překlad, vkládání, přizpůsobení modelů a další. Podrobnější informace o používání rozhraní API pro indexer videa naleznete na [portálu pro vývojáře videoindexeru](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Jaké sady SDK klienta nabízí video indexer?

V současné době nejsou nabízeny žádné klientské sady SDK. Tým Video Indexer pracuje na sadách SDK a plánuje je brzy dodat.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Jak začít s rozhraním API videoindexeru?

Postupujte [podle kurzu: začínáme s rozhraním API video indexeru](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Jaký je rozdíl mezi rozhraním API pro indexování videa a rozhraním AZURE Media Service v3 API?

V současné době existují některé překrývání funkcí nabízených rozhraní API video indexeru a Azure Media Service v3 ROZHRANÍ API. Více informací o porovnání obou služeb naleznete [zde](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Co je přístupový token rozhraní API a proč ho potřebuji?

Rozhraní API video indexeru obsahuje rozhraní API pro autorizaci a rozhraní OPERATIONS API. Rozhraní API autorizací obsahuje volání, která poskytují přístupový token. Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

Přístupové tokeny jsou potřebné k použití api video indexeru pro účely zabezpečení. Tím zajistíte, že všechny hovory budou přicházet od vás nebo od těch, kteří mají přístupová oprávnění k vašemu účtu. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaký je rozdíl mezi tokenem přístupu k účtu, tokenem přístupu uživatele a tokenem pro přístup videa?

* Úroveň účtu – přístupové tokeny na úrovni účtu vám umožní provádět operace na úrovni účtu nebo na úrovni videa. Například nahrajte video, uveďte seznam všech videí, získejte přehledy videa.
* Úroveň uživatele – přístupové tokeny na úrovni uživatele umožňují provádět operace na úrovni uživatele. Třeba získat přidružené účty.
* Úroveň videa – přístupové tokeny na úrovni videa vám umožní provádět operace s konkrétním videem. Třeba získat nové přehledy z videa, stáhnout titulky, získat widgety apod.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak často potřebuji získat nový přístupový token? Kdy vyprší platnost přístupových tokenů?

Platnost přístupových tokenů vyprší každou hodinu, takže každou hodinu je třeba vygenerovat nový přístupový token. 

## <a name="billing-questions"></a>Dotazy k fakturaci

### <a name="how-much-does-video-indexer-cost"></a>Kolik video indexer stojí?

Video Indexer používá jednoduchý model průběžných plateb na základě doby trvání vstupu obsahu, který indexujete. Za kódování, streamování, úložiště, využití sítě a rezervované jednotky médií mohou být účtovány další poplatky. Další informace naleznete na stránce [s cenami.](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)

### <a name="when-am-i-billed-for-using-video-indexer"></a>Kdy se mi účtuje používání videoindexeru?

Při odesílání videa, které má být indexováno, uživatel definuje indexování jako analýzu videa, analýzu zvuku nebo obojí. Tím určíte, které skubudou budou účtovány. Pokud dojde k chybě kritické úrovně během zpracování, kód chyby bude vrácen jako odpověď. V takovém případě nedojde k žádné fakturaci.  Kritická chyba může být způsobena chybou v našem kódu nebo kritickou chybou ve vnitřní závislosti, kterou má služba. Chyby, jako je nesprávná identifikace nebo extrakce vhledu, nejsou považovány za kritické a je vrácena odpověď. V každém případě, kdy je vrácena platná odpověď (kód bez chyby), dojde k fakturaci.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Nabízí video indexer bezplatnou zkušební verzi?

Ano, Video Indexer nabízí bezplatnou zkušební verzi, která poskytuje kompletní služby a funkce rozhraní API. K dispozici je kvóta 600 minut videa pro uživatele webového rozhraní a 2 400 minut pro uživatele rozhraní API. 

## <a name="next-steps"></a>Další kroky

[Přehled](video-indexer-overview.md)
