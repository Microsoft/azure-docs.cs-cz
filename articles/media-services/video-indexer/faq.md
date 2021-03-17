---
title: Nejčastější dotazy týkající se Video Indexer – Azure
titleSuffix: Azure Media Services
description: Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Azure Media Services Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: 0fc28a1f808eeb2977b1dcca5046ed29933b8aa8
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028790"
---
# <a name="video-indexer-frequently-asked-questions"></a>Video Indexer nejčastějších dotazech

Tento článek obsahuje odpovědi na nejčastější dotazy týkající se Video Indexer.

## <a name="general-questions"></a>Obecné otázky

### <a name="what-is-video-indexer"></a>Co je Video Indexer?

Video Indexer je umělá služba inteligentních informací, která je součástí Microsoft Azure Media Services. Video Indexer poskytuje orchestraci více modelů strojového učení, které vám umožní snadno extrahovat podrobný přehled z videa. Aby bylo možné poskytovat rozšířené a přesné přehledy, Video Indexer využívá více kanálů videa: zvuk, řeč a vizuál. Přehledy Video Indexer můžou být používány mnoha způsoby, jako je vylepšení zjistitelnosti obsahu a usnadnění přístupu, vytváření nových příležitostí finanční zhodnocení nebo vytváření nových prostředí, která využívají přehledy. Video Indexer poskytuje webové rozhraní pro testování, konfiguraci a přizpůsobení modelů ve vašem účtu. Vývojáři mohou použít rozhraní API založené na REST k integraci Video Indexer do produkčního systému. 

### <a name="what-can-i-do-with-video-indexer"></a>Co se dá dělat s Video Indexer?

Mezi operace, které Video Indexer můžou provádět na médiích soubory, patří:

* Identifikace a extrakce řeči a identifikace mluvčích.
* Identifikace a extrakce textu na obrazovce ve videu.
* Zjišťování objektů v souboru videa.
* Identifikujte značky (např. Microsoft) ze zvukových stop a textu na obrazovce ve videu.
* Zjišťování a rozpoznávání plošek z databáze celebrit a uživatelsky definované databáze ploch.
* Extrahování témat, která jsou popsána, ale nejsou nutně zmíněná ve zvukovém a obrazovém obsahu
* Vytváření titulků nebo titulků ze zvukové stopy.

Další informace a další Video Indexer funkce najdete v tématu [Přehled](video-indexer-overview.md).

### <a name="how-do-i-get-started-with-video-indexer"></a>Návody začít s Video Indexer?

Video Indexer zahrnuje bezplatnou zkušební nabídku, která vám poskytne 600 minut v rámci webového rozhraní a 2 400 minut přes rozhraní API. Můžete se [přihlásit k video Indexer webovému rozhraní](https://www.videoindexer.ai/) a vyzkoušet si ho pro vlastní webovou identitu a bez nutnosti nastavovat předplatné Azure. Pomocí [tohoto prostředí pro snadné představení](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md) získáte lepší představu o použití video indexer.

K indexování videí a zvukových letí ve velkém měřítku můžete Video Indexer připojit k placenému Microsoft Azuremu předplatnému. Další informace o cenách najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

Další informace o tom, jak [začít, najdete](video-indexer-get-started.md)v článku Začínáme.

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Potřebuji pro použití Video Indexer znalosti kódování?

Pomocí Video Indexer webového rozhraní můžete vyhodnotit, konfigurovat a spravovat váš účet **bez nutnosti kódování**.  Až budete připraveni k vývoji složitějších aplikací, můžete použít [rozhraní video indexer API](https://api-portal.videoindexer.ai/) k integraci video indexer do vlastních aplikací, webů nebo [vlastních pracovních postupů pomocí technologií bez serveru, jako je Azure Logic Apps](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/) nebo Azure Functions.

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Potřebuji pro použití Video Indexer dovednosti strojového učení?

Ne, Video Indexer poskytuje integraci více modelů strojového učení do jednoho kanálu. Indexování videa nebo zvukového souboru prostřednictvím Video Indexer načte celou sadu přehledů extrahovaných na jednu sdílenou časovou osu bez dovedností strojového učení nebo znalostí o algoritmech potřebných v části zákazníka.

### <a name="what-media-formats-does-video-indexer-support"></a>Jaké formáty médií Video Indexer podporují?

Video Indexer podporuje většinu běžných formátů médií. Další podrobnosti najdete v seznamu [formátů Azure Media Encoder Standard](../latest/media-encoder-standard-formats.md) .

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>Návody nahrát mediální soubor do Video Indexer a jaká jsou omezení?

Ve webovém portálu Video Indexer můžete nahrát soubor médií pomocí dialogového okna pro nahrání souboru nebo tak, že přejdete na adresu URL, která přímo hostuje zdrojový soubor (viz [příklad](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)). Žádná adresa URL, která hostuje mediální obsah pomocí elementu iFrame nebo kódu pro vložení, nebude fungovat (viz [příklad](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)). 

Další informace najdete v tomto [Průvodci](./upload-index-videos.md).

#### <a name="limitations"></a>Omezení

* Název videa nesmí překročit 80 znaků.
* Pokud nahráváte video pomocí pole bajtů, velikost videa je omezená na 2 GB (a 30 GB při použití adresy URL). 

Úplný seznam najdete v tématu [požadavky na nahrávání a omezení](upload-index-videos.md#uploading-considerations-and-limitations).

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Jak dlouho trvá Video Indexer k extrakci přehledů z médií?

Doba potřebná k indexování videosouboru nebo zvukového souboru, a to pomocí rozhraní Video Indexer API i Video Indexer webového rozhraní, závisí na několika parametrech, jako je třeba délka souboru a kvalita, počet přehledů nalezených v souboru, počet dostupných [rezervovaných jednotek](../previous/media-services-scale-media-processing-overview.md) a zda je povolen [koncový bod streamování](../previous/media-services-streaming-endpoints-overview.md) . Doporučujeme, abyste spustili několik testovacích souborů s vlastním obsahem a získali průměrnou představu, abyste měli lepší představu.

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Můžu vytvořit vlastní pracovní postupy pro automatizaci procesů pomocí Video Indexer?

Ano, Video Indexer můžete integrovat do technologií bez serveru, jako jsou Logic Apps, flow a [Azure Functions](https://azure.microsoft.com/services/functions/). Další podrobnosti o [aplikaci logiky](https://azure.microsoft.com/services/logic-apps/) a konektorech [Flow](https://flow.microsoft.com/en-us/) pro video indexer najdete [zde](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/). Některé projekty automatizace, které provádějí partneři, můžete zobrazit v úložišti [ukázek video indexer](https://github.com/Azure-Samples/media-services-video-indexer) .

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Ve kterých oblastech Azure je video indexer k dispozici?

Můžete vidět, které oblasti Azure Video Indexer k dispozici na stránce [oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all) .

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>Můžu přizpůsobit Video Indexer modely pro můj konkrétní případ použití? 

Yes. V Video Indexer můžete přizpůsobit některé z dostupných modelů, aby lépe vyhovovaly vašim potřebám. 

Například náš model person podporuje dopředné 1 000 000i rozpoznávání celebrit, ale můžete ho také naučit, aby rozpoznal jiné plošky, které nejsou v této databázi. 

Podrobnosti najdete v článcích o přizpůsobení [osob](customize-person-model-overview.md), [značek](customize-brands-model-overview.md)a [jazykových](customize-language-model-overview.md) modelů. 

###  <a name="can-i-edit-the-videos-in-my-library"></a>Můžu upravit videa v knihovně?

Yes. Kliknutím na tlačítko **Upravit video** v zobrazení knihovny nebo kliknutím na tlačítko **otevřít v editoru** na displeji přehrávače se dostanete na kartu **projekty** . Můžete vytvořit nový projekt a přidat další videa z knihovny, abyste je mohli společně upravovat. až budete hotovi, můžete si video vykreslit a stáhnout. 

Pokud chcete získat přehled o novém videu, Indexujte ho pomocí Video Indexer a zobrazí se ve vaší knihovně se svými přehledy.

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>Je možné indexovat více zvukových streamů nebo kanálů?

Pokud je k dispozici více zvukových streamů, Video Indexer převezme první výskyt dat a zpracuje pouze tento datový proud. V jakémkoli audio streamu Video Indexer procesy přebírají různé kanály (pokud jsou k dispozici) a zpracovávají je jako mono. V případě manipulace s datovými proudy/kanály můžete v souboru použít příkazy FFmpeg ještě před jejich indexováním.

### <a name="what-is-the-sla-for-video-indexer"></a>Jaká je smlouva SLA pro Video Indexer?

Smlouva SLA pro Azure Media Service pokrývá Video Indexer a najdete je na stránce věnované [smlouvě SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) . Smlouva SLA platí jenom pro Video Indexer placené účty a nevztahuje se na bezplatnou zkušební verzi.

## <a name="privacy-questions"></a>Otázky týkající se ochrany osobních údajů

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Jsou videosoubory a zvukové soubory indexované Video Indexer uloženy?

Ano, pokud soubor neodstraníte z Video Indexer, a to buď pomocí Video Indexer webu nebo rozhraní API, jsou vaše videosoubory a zvukové soubory uložené. Pro bezplatnou zkušební verzi se videosoubory a zvukové soubory, které index najdete, ukládají v oblasti Azure Východní USA. V opačném případě se vaše video a zvukové soubory ukládají do účtu úložiště vašeho předplatného Azure.

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Můžu odstranit moje soubory uložené na portálu Video Indexer?

Ano, video a zvukové soubory můžete vždycky odstranit, stejně jako všechna metadata a přehledy z nich extrahovat Video Indexer. Po odstranění souboru z Video Indexer se soubor a jeho metadata a přehledy trvale odeberou z Video Indexer. Pokud jste ale ve službě Azure Storage implementovali vlastní řešení zálohování, zůstane soubor ve službě Azure Storage.

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Můžu řídit přístup uživatelů k mému Video Indexer účtu?

Ano, jenom správci účtu můžou pozvat a Zrušit pozvání uživatelů ke svým účtům a také přiřadit oprávnění k úpravám a kdo má přístup jen pro čtení.

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Kdo má přístup k mým videosouborům a zvukům, které byly indexovány nebo uloženy pomocí Video Indexer a metadata a přehledy, které byly extrahovány?

Obsah vašeho videa nebo zvuku, který je veřejný jako jeho nastavení ochrany osobních údajů, může přistupovat kdokoli, kdo má odkaz na vaše video nebo zvukový obsah a jeho přehledy. Váš obsah videa nebo zvuku, který má soukromé jako své nastavení ochrany osobních údajů, může být k dispozici pouze uživatelům, kteří byli pozváni na účet videa nebo zvukového obsahu. Nastavení ochrany osobních údajů obsahu se vztahuje také na metadata a přehledy, které Video Indexer extrahují. Nastavení ochrany osobních údajů přiřadíte při nahrávání videa nebo zvukového souboru. Můžete také změnit nastavení ochrany osobních údajů po indexování.

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Jaký přístup má společnost Microsoft na své video nebo zvukové soubory, které byly indexovány nebo uloženy pomocí Video Indexer a metadata a přehledy, které byly extrahovány?

Na základě [podmínek Azure Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) jste celý obsah sami a Microsoft bude mít přístup jenom k vašemu obsahu a metadatům a přehledům, které video indexer extrahuje z vašeho obsahu v závislosti na OST a prohlášení Microsoftu o zásadách ochrany osobních údajů.

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>Jsou vlastní modely, které jsou vytvořeny v mém účtu Video Indexer k dispozici pro jiné účty?

 Ne, vlastní modely, které vytvoříte ve vašem účtu, nejsou k dispozici pro žádný jiný účet. Video Indexer v současné době umožňuje vytváření vlastních [značek](customize-brands-model-overview.md), [jazyků](customize-language-model-overview.md)a modelů [uživatelů](customize-person-model-overview.md) v účtu. Tyto modely jsou k dispozici pouze v účtu, ve kterém jste modely vytvořili.
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Je obsah indexovaný Video Indexer uložený v oblasti Azure, kde používám Video Indexer?

Ano, obsah a jeho přehledy se uchovávají v rámci oblasti Azure, pokud nemáte v předplatném Azure ruční konfiguraci, která používá více oblastí Azure. 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Jaké jsou zásady ochrany osobních údajů pro Video Indexer?

Na Video Indexer se vztahuje [prohlášení o zásadách ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement). Prohlášení o zásadách ochrany osobních údajů vysvětluje osobní údaje procesů Microsoftu, jak je Microsoft zpracovává a pro jaké účely ji Microsoft zpracuje. Další informace o ochraně osobních údajů najdete na webu [Microsoft Trust Center](https://www.microsoft.com/trustcenter).

### <a name="what-certifications-does-video-indexer-have"></a>Jaké certifikace Video Indexer mají?

Video Indexer aktuálně má certifikaci SOC. Pokud si chcete projít certifikaci Video Indexer, přečtěte si prosím [Centrum zabezpečení Microsoftu](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure).

### <a name="what-is-the-difference-between-private-and-public-videos"></a>Jaký je rozdíl mezi soukromými a veřejnými videy? 

Po nahrání videa do Video Indexer můžete zvolit ze dvou nastavení ochrany osobních údajů: soukromé a veřejné. Veřejná videa jsou přístupná pro kohokoli, včetně anonymních a neidentifikovaných uživatelů. Soukromé objekty jsou omezeny výhradně na členy účtu. 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>Při pokusu o nahrání videa jako veřejně došlo k označení nevhodného nebo urážlivého obsahu, co to znamená? 

Při nahrávání videa do Video Indexer se automatická analýza obsahu provádí pomocí algoritmů a modelů, aby se zajistilo, že se nebude veřejně prezentovat žádný nevhodný obsah. Pokud se zjistí, že video bude podezřelé, že obsahuje explicitní obsah, nebude možné ho nastavit jako veřejný. Členové účtu ale můžou k němu přistupovat jako o soukromém videu (Zobrazit ho, Stáhnout přehledy a extrahovat artefakty a provádět další operace, které členové účtu mají k dispozici).   

Aby bylo možné nastavit video pro veřejný přístup, můžete použít tyto kroky: 

* Sestavte si vlastní vrstvu rozhraní (například aplikaci nebo Web) a použijte ji k interakci se službou Video Indexer. Tímto způsobem zůstane video na našem portálu soukromé a vaši uživatelé s ním můžou s vaším rozhraním pracovat. Můžete například dál získat přehledy nebo zobrazit video ve vlastním rozhraní. 
* Vyžádá si lidská recenze obsahu, což by vedlo k odebrání omezení za předpokladu, že obsah není explicitní. 

    Tuto možnost lze prozkoumat, pokud je Web Video Indexer používán přímo uživateli jako vrstva rozhraní a pro veřejné (neověřené) zobrazení. 

## <a name="api-questions"></a>Dotazy k rozhraní API

### <a name="what-apis-does-video-indexer-offer"></a>Jaká rozhraní API Video Indexer nabídku nabízí?

Rozhraní API Video Indexer umožňují indexování, extrakci metadat, správu prostředků, překlad, vkládání, přizpůsobení modelů a další. Podrobnější informace o používání rozhraní Video Indexer API najdete v [video indexer portálu pro vývojáře](https://api-portal.videoindexer.ai/).

### <a name="what-client-sdks-does-video-indexer-offer"></a>Jaké klientské sady SDK nabízí Video Indexer nabídky?

Aktuálně nejsou k dispozici žádné klientské sady SDK. Tým Video Indexer pracuje na sadách SDK a plánuje je brzy doručovat.

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Návody Začínáme s rozhraním API Video Indexer?

Sledujte [kurz: Začínáme s rozhraním video indexer API](video-indexer-use-apis.md).

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Jaký je rozdíl mezi rozhraním API Video Indexer a rozhraním API služby Azure Media Service V3?

V současné době dochází k překrytí funkcí, které nabízí rozhraní Video Indexer API a rozhraní API pro Azure Media Service v3. Další informace o tom, jak porovnat obě služby, najdete [tady](compare-video-indexer-with-media-services-presets.md).

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>Co je přístupový token rozhraní API a proč ho potřebuji?

Rozhraní Video Indexer API obsahuje rozhraní API pro autorizaci a rozhraní Operations API. Autorizační rozhraní API obsahuje volání, která vám umožní přístup k tokenu. Každé volání do API Operations by mělo být přidružené k přístupovému tokenu s odpovídajícím oborem autorizace volání.

Přístupové tokeny jsou potřebné k používání rozhraní Video Indexer API pro účely zabezpečení. Tím se zajistí, že přijdete o všechna volání, nebo na uživatele, kteří mají oprávnění k přístupu k vašemu účtu. 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>Jaký je rozdíl mezi přístupovými tokeny účtu, tokenem uživatele a přístupovým tokenem?

* Úroveň účtu – přístupové tokeny na úrovni účtu vám umožní provádět operace na úrovni účtu nebo na úrovni videa. Můžete například nahrát video, vypsat všechna videa a získat přehled o videích.
* Přístupové tokeny na úrovni uživatele umožňují provádět operace na úrovni uživatele. Třeba získat přidružené účty.
* Úroveň videa – přístupové tokeny na úrovni videa vám umožní provádět operace s konkrétním videem. Třeba získat nové přehledy z videa, stáhnout titulky, získat widgety apod.

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>Jak často je potřeba získat nový přístupový token? Kdy vyprší platnost přístupových tokenů?

Přístupové tokeny vyprší každou hodinu, takže musíte každou hodinu vygenerovat nový přístupový token. 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Jaké jsou možnosti přihlášení Video Indexer portálu pro vývojáře?

Podívejte se na poznámku k verzi týkající se [přihlašovacích informací](release-notes.md#october-2020).

Po registraci e-mailového účtu pomocí poskytovatele identity nemůžete tento e-mailový účet použít s jiným poskytovatelem identity.

## <a name="billing-questions"></a>Dotazy k fakturaci

### <a name="how-much-does-video-indexer-cost"></a>Kolik se Video Indexer náklady?

Video Indexer používá jednoduchý cenový model s průběžnými platbami podle aktuálního využití, který je založený na době trvání indexu. Další poplatky můžou platit pro kódování, streamování, ukládání, využití sítě a rezervované jednotky médií. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/) .

### <a name="when-am-i-billed-for-using-video-indexer"></a>Kdy se mi účtuje použití Video Indexer?

Při odesílání videa, které se má indexovat, bude uživatel definovat indexování, která bude analyzována jako video, analýza zvuku nebo obojí. Tím se určí, které skladové jednotky budou účtovány. Pokud během zpracování dojde k chybě kritické úrovně, jako odpověď se vrátí kód chyby. V takovém případě se neobjeví žádná fakturace.  Kritická chyba může být způsobena chybou v našem kódu nebo kritickou chybou v interní závislosti, kterou služba má. Chyby jako chybné identifikace nebo extrakce Insight nejsou považovány za kritické a vrátí odpověď. V každém případě, kdy se vrátí platná odpověď (kód bez chybového kódu), dojde k fakturaci.
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Nabízí Video Indexer bezplatnou zkušební verzi?

Ano, Video Indexer nabízí bezplatnou zkušební verzi, která poskytuje úplnou funkčnost služby a rozhraní API. Pro uživatele rozhraní API je k dispozici kvóta s 600 minutami pro webové uživatele a 2 400 minut. 

## <a name="next-steps"></a>Další kroky

* [Přehled](video-indexer-overview.md)
* [Stack Overflow](https://stackoverflow.com/search?q=video-indexer)
