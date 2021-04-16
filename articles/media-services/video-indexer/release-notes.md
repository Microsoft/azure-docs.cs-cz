---
title: Poznámky k verzi Azure Media Services Video Indexer | Microsoft Docs
description: Pokud chcete mít přehled o nejnovějším vývoji, najdete v tomto článku nejnovější aktualizace Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532897"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Poznámky k verzi Azure Media Services Video Indexer

>Přečtěte si informace o tom, kdy se má tato stránka na aktualizace znovu navštívit kopírováním a vložením této adresy URL: `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` do čtečky kanálů RSS.

V tomto článku najdete informace o tom, jak se chcete zabývat aktuálním vývojem.

* Nejnovější verze
* Známé problémy
* Opravy chyb
* Zastaralé funkce

## <a name="march-2021"></a>Březen 2021

### <a name="audio-analysis"></a>Analýza zvuku 

Analýza zvuku je teď k dispozici v dalším novém balíčku zvukových funkcí v jiném cenovém bodě. Nová předvolba **základní analýzy zvuku** nabízí možnost nedostatku pouze k extrakci popisků a titulků výstupů řeči, překladu a formátování. V předplatném se **základní zvuk** vytvoří ve vyúčtování dva samostatné měřiče, včetně řádku pro přepis a samostatného řádku pro formátování titulků a titulků. Další informace o cenách najdete na stránce s [cenami Media Services](https://azure.microsoft.com/pricing/details/media-services/) .

Nově přidaný svazek je k dispozici při indexování nebo opětovném indexování souboru tak, že vyberete **možnost Rozšířená volba**  ->  **základní zvukové** Předvolby (v rozevíracím seznamu **video a zvuk** ).

### <a name="new-developer-portal"></a>Nový portál pro vývojáře 

Video Indexer má nový [portál pro vývojáře](https://api-portal.videoindexer.ai/), vyzkoušení nových rozhraní API video indexer a vyhledání všech relevantních prostředků na jednom místě: [úložiště GitHub](https://github.com/Azure-Samples/media-services-video-indexer), [přetečení zásobníku](https://stackoverflow.com/questions/tagged/video-indexer), [video indexer technická komunita](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) s relevantními blogových příspěvků, [video indexer nejčastějších dotazů](faq.md), [uživatelských hlasech](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) a poskytování vašich názorů a návrhových funkcí a  [odkaz "CodePen"](https://codepen.io/videoindexer) s ukázkami kódu widgetů. 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Rozšířené možnosti přizpůsobení pro pomůcku Insight 

Sada SDK je teď dostupná pro vložení widgetu Video Indexer Insights ve vaší vlastní službě a přizpůsobení jeho stylu a dat. Sada SDK podporuje standardní pomůcku Video Indexer Insights a plně přizpůsobitelnou pomůcku přehledů. Ukázka kódu je k dispozici v [video indexer úložišti GitHubu](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization). Pomocí těchto pokročilých možností přizpůsobení může vývojář řešení použít vlastní stylování a přizpůsobovat si vlastní styl dat AI a prezentovat ho v widgetu Insight (s Video Indexer přehledy). 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Indexer nasazena v USA (střed) – sever, USA – západ a Kanada – střed 

Nyní můžete vytvořit Video Indexer placeného účtu v centrálních oblastech USA (střed) – sever, USA – západ a Kanada.
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Podpora pro nové zdrojové jazyky pro převod řeči na text (STT), překlad a hledání 

Video Indexer teď podporuje STT, překlady a hledání v dánštině ("da-DK"), norština (' NB-NO '), švédština (' sv-SE '), finština (' Fi-FI '), kanadská francouzština (' fr-CA '), thajština (' th-TH '), Arabština (ar-BH ', ' ar-EG ', ' ar-SWEETIQ ', ' ar-JO ', ' ar-KW ', ' ar-9,1 ', ' ar-OM ', ' ar-QA ', ' ar-S ' a ' ar-SY ') a turečtina (' tr-TR '). Tyto jazyky jsou k dispozici na webové stránce rozhraní API i Video Indexer. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Hledat podle tématu Video Indexer webu 

Pomocí funkce hledání teď můžete v horní části stránky [video indexer webu](https://www.videoindexer.ai/account/login) vyhledat videa s konkrétními tématy. 

## <a name="february-2021"></a>Únor 2021

### <a name="multiple-account-owners"></a>Více vlastníků účtu 

Do Video Indexer se přidala role vlastníka účtu. Můžete přidávat, měnit a odebírat uživatele. změňte jejich roli. Podrobnosti o tom, jak sdílet účet, najdete v tématu [pozvání uživatelů](invite-users.md).

### <a name="audio-event-detection-public-preview"></a>Detekce zvukové události (Public Preview)

> [!NOTE]
> Tato funkce je k dispozici pouze v účtech zkušební verze. 

Video Indexer nyní detekuje následující zvukové efekty v segmentech nehlasu obsahu: gunshot, skleněná Shatter, alarm, Siren, výbuch, pes kůra, výkřiku, Laughter, reelné reakce (, clapping a booing) a ticho. 

Nově přidaný zvuk, který ovlivňuje funkci, je k dispozici při indexování souboru výběrem **Možnosti Upřesnit možnost** Upřesnit  ->  **zvuk** (v části video a indexování zvuků). Standardní indexování bude zahrnovat jenom **tiché** a přeplněné **reakce**. 

Typ události **clapping** , který byl zahrnut v předchozím modelu zvukových efektů, je teď extrahovaný jako součást typu události **přeplněných reakcí** .

Pokud se rozhodnete zobrazit **přehledy** o videu na webu [video indexer](https://www.videoindexer.ai/) , projeví se na stránce zvukové efekty.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Detekce zvukové události":::

### <a name="named-entities-enhancement"></a>Vylepšení pojmenovaných entit  

Extrahovaný seznam lidí a umístění se obecně rozšířil a aktualizoval. 

Model teď navíc zahrnuje lidi a umístění v kontextu, které nejsou ve videu slavných jako Sam nebo Home. 

## <a name="january-2021"></a>Leden 2021

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer je nasazený v cloudu pro státní správu USA. 

V cloudu pro státní správu USA teď můžete vytvořit Video Indexer placeného účtu v oblastech) – Virginia a Arizona. V uvedené oblasti není dostupná Video Indexer nabídka bezplatné zkušební verze. Další informace najdete v dokumentaci k Video Indexer. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer nasazené v oblasti Indie – střed 

Nyní můžete vytvořit placený účet Video Indexer v oblasti Indie – střed. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Nový tmavý režim pro prostředí Video Indexer webu

Prostředí Video Indexer webů je teď dostupné v tmavém režimu. Chcete-li povolit režim tmavosti, otevřete panel nastavení a přepněte na možnost **tmavý režim** . 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Nastavení tmavého režimu":::

## <a name="december-2020"></a>Prosinec 2020

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer nasazena v Švýcarsko – západ a Švýcarsko – sever

V oblastech Švýcarsko – západ a Švýcarsko – sever teď můžete vytvořit placený účet Video Indexer.

## <a name="october-2020"></a>Říjen 2020

### <a name="animated-character-identification-improvements"></a>Vylepšení animovaných identifikací znaků  

Video Indexer podporuje detekci, seskupení a rozpoznávání znaků v animovaném obsahu prostřednictvím integrace s Cognitive Services vlastní vize. Do detekce a rozpoznávání znaků jsme přidali hlavní vylepšení tohoto algoritmu AI. v důsledku toho se výrazně vylepšuje přesnost a zjištěné znaky.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Authenticatication změny na webu plánovaného Video Indexer

Od 1. března 2021 se už nebudete moct zaregistrovat a přihlásit se k [portálu](video-indexer-use-apis.md) pro vývojáře [video indexer webu](https://www.videoindexer.ai/) pomocí Facebooku nebo LinkedInu.

Budete se moct zaregistrovat a přihlásit pomocí některého z těchto poskytovatelů: Azure AD, Microsoft a Google.

> [!NOTE]
> Účty Video Indexer připojené k LinkedInu a Facebooku nebudou dostupné po 1. březnu 2021. 
> 
> Měli byste [pozvat](invite-users.md) e-maily Azure AD, Microsoft nebo Google na účet video indexer, abyste k němu měli přístup i nadále. Můžete přidat další vlastníka podporovaných zprostředkovatelů, jak je popsáno v [pozvánce](invite-users.md). <br/>
> Případně můžete vytvořit placený účet a migrovat data.

## <a name="august-2020"></a>Srpen 2020

### <a name="mobile-design-for-the-video-indexer-website"></a>Mobilní návrh pro Video Indexer Web

Prostředí Video Indexer webu teď podporuje mobilní zařízení. Uživatelské prostředí reaguje na přizpůsobení velikosti mobilní obrazovky (kromě uživatelská rozhraní přizpůsobení). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Vylepšení usnadnění a opravy chyb 

Jako součást WCAG (pokyny pro přístupnost webového obsahu) je Video Indexer webů v rámci standardů Microsoftu pro usnadnění práce zarovnána se stupněm C. Bylo vyřešeno několik chyb a vylepšení souvisejících s navigací klávesnice, programovým přístupem a čtečkou obrazovky. 

## <a name="july-2020"></a>Červenec 2020

### <a name="ga-for-multi-language-identification"></a>GA pro identifikaci více jazyků

Identifikace ve více jazycích je přesunutá z verze Preview na GA a připravená na využití v produktivním prostředí.

Neexistuje žádný dopad na ceny související s přechodem "Preview na GA".

### <a name="video-indexer-website-improvements"></a>Vylepšení Video Indexer webu

#### <a name="adjustments-in-the-video-gallery"></a>Úpravy v galerii videí

Byl přidán nový panel hledání pro hledání ve hloubkovém přehledu s dalšími možnostmi filtrování. Výsledky hledání se taky vylepšily.

Nové zobrazení seznamu s možností seřadit a spravovat archiv videa s více soubory.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Nový panel pro snadné výběr a konfiguraci

Byl přidán postranní panel pro snadné výběr a konfiguraci uživatele, což umožňuje jednoduché a rychlé vytváření a sdílení účtů a také nastavení konfigurace.

Postranní panel se používá také pro uživatelské preference a nápovědu.

## <a name="june-2020"></a>Červen 2020

### <a name="search-by-topics"></a>Hledat podle témat

Teď můžete pomocí rozhraní API pro hledání vyhledat videa s konkrétními tématy (jenom rozhraní API).

Témata jsou přidána jako součást `textScope` (volitelný parametr). Podrobnosti najdete v tématu [rozhraní API](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos) .  

### <a name="labels-enhancement"></a>Vylepšení popisků

Popisek autor značky byl upgradován a nyní obsahuje více vizuálních popisků, které lze identifikovat.

## <a name="may-2020"></a>Květen 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer nasazena v Východní USA

Nyní můžete vytvořit Video Indexer placeného účtu v Východní USA oblasti.
 
### <a name="video-indexer-url"></a>Adresa URL Video Indexer

Video Indexer místní koncové body byly sjednoceny jenom se službou www. Není požadována žádná položka akce.

Od této chvíle dosáhnete www.videoindexer.ai, jestli je pro vkládání widgetů nebo přihlašování do Video Indexer webových aplikací.

Wus.videoindexer.ai se také přesměruje na www. Další informace jsou k dispozici v [vkládání video indexer widgetech v aplikacích](video-indexer-embed-widgets.md).

## <a name="april-2020"></a>Duben 2020

### <a name="new-widget-parameters-capabilities"></a>Nové možnosti parametrů widgetu

Widget **Insights** obsahuje nové parametry: `language` a `control` .

Widget **přehrávače** má nový `locale` parametr. Oba `locale` `language` parametry a ovládají jazyk přehrávače.

Další informace najdete v části [typy widgetů](video-indexer-embed-widgets.md#widget-types) . 

### <a name="new-player-skin"></a>Nový vzhled přehrávače

Nový vzhled přehrávače spuštěný s aktualizovaným návrhem

### <a name="prepare-for-upcoming-changes"></a>Příprava na nadcházející změny

* V dnešní době následující rozhraní API vrací objekt účtu:

    * [Vytvořit účet s placeným účtem](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Získat účet](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Získání účtů – autorizace](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Získání účtů s tokenem](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Objekt Account obsahuje `Url` pole odkazující na umístění [webu video indexer](https://www.videoindexer.ai/).
U placených účtů `Url` pole aktuálně odkazuje na interní adresu URL místo na veřejném webu.
V nadcházejících týdnech ji změníme a vrátíme adresu URL [video indexer webu](https://www.videoindexer.ai/) pro všechny účty (zkušební a placená).

    Nepoužívejte interní adresy URL, měli byste používat [video indexer veřejná rozhraní API](https://api-portal.videoindexer.ai/).
* Pokud vkládáte Video Indexer adresy URL do svých aplikací a adresy URL neodkazují na [web video indexer](https://www.videoindexer.ai/) nebo na koncový bod rozhraní API video indexer ( `https://api.videoindexer.ai` ), ale místo na místní koncový bod (například `https://wus2.videoindexer.ai` ), znovu vygenerujte adresy URL.

   Můžete to udělat buď pomocí:

    * Nahrazení adresy URL adresou URL odkazující na rozhraní API widgetů Video Indexer (například [pomůcka Insights](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Pomocí webu Video Indexer Vygenerujte novou vloženou adresu URL:
         
         Stisknutím tlačítka **Přehrát** přejděte na stránku videa – > klikněte na tlačítko pro **&lt; / &gt; vložení** – > zkopírujte adresu URL do vaší aplikace:
   
    Regionální adresy URL nejsou podporované a v nadcházejících týdnech se zablokují.

## <a name="january-2020"></a>Leden 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Podpora vlastního jazyka pro další jazyky

Video Indexer teď podporuje vlastní jazykové modely pro `ar-SY` , `en-UK` a `en-AU` (jenom rozhraní API).
 
### <a name="delete-account-timeframe-action-update"></a>Odstranit aktualizaci akce časového rámce účtu

Akce Odstranit účet nyní odstraní účet do 90 dnů, nikoli do 48 hodin.
 
### <a name="new-video-indexer-github-repository"></a>Nové úložiště GitHub Video Indexer

K dispozici je teď nový Video Indexer GitHub s různými projekty, příručky Začínáme a ukázky kódu: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Aktualizace Swagger

Video Indexer sjednocené **ověřování** a **operace** do jedné [specifikace video indexer openapi (Swagger)](https://api-portal.videoindexer.ai/api-details#api=Operations&operation). Vývojáři můžou najít rozhraní API na [portálu pro vývojáře v video indexer](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>Prosinec 2019

### <a name="update-transcript-with-the-new-api"></a>Aktualizace přepisu pomocí nového rozhraní API

Aktualizujte konkrétní oddíl v přepisu pomocí rozhraní API [Update-video-index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) .

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Oprava konfigurace účtu z Video Indexerového portálu

Teď můžete aktualizovat konfiguraci připojení Media Services, abyste mohli sami pomáhat s problémy, jako je: 

* nesprávný prostředek Azure Media Services
* změny hesla
* Mezi předplatnými se přesunuly Media Services prostředky.  

Pokud chcete opravit konfiguraci účtu, přejděte na portálu Video Indexer na kartu nastavení > účet (jako vlastník).

### <a name="configure-the-custom-vision-account"></a>Konfigurace účtu Custom Vision

Nakonfigurujte si účet Custom Vision na placené účty pomocí portálu Video Indexer (dřív to podporovala jenom rozhraní API). Pokud to chcete provést, přihlaste se k portálu Video Indexer, vyberte přizpůsobení modelu > animovaných znaků > nakonfigurovat. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scény, snímky a klíčové snímky – nyní v jednom podokně přehledu

Scény, snímky a klíčové snímky se teď sloučí do jednoho přehledu, aby bylo snazší spotřebovat a navigovat. Když vyberete požadovanou scénu, uvidíte, ze kterých snímků a klíčových snímků se skládá. 

### <a name="notification-about-a-long-video-name"></a>Oznámení o dlouhém názvu videa

Pokud je název videa delší než 80 znaků, Video Indexer při nahrávání zobrazovat popisnou chybu.

### <a name="streaming-endpoint-is-disabled-notification"></a>Oznámení o zakázaném koncovém bodu streamování

Pokud je koncový bod streamování zakázaný, Video Indexer se na stránce Player zobrazí popisná chyba.

### <a name="error-handling-improvement"></a>Vylepšení zpracování chyb

Stavový kód 409 se teď vrátí z [přeindexování videa](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) a aktualizuje rozhraní API pro [video index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) pro případ, že se video aktivně indexuje, aby se zabránilo přepsání aktuálních změn v indexu omylem.

## <a name="november-2019"></a>Listopad 2019
 
* Podpora jazykových modelů v korejštině

    Video indexer teď podporuje vlastní jazykové modely v korejštině ( `ko-KR` ) jak v rozhraní API, tak i na portálu. 
* Nové jazyky podporované pro převod řeči na text (STT)

    Rozhraní API pro Video Indexer teď podporují STT v arabštině Levantine (ar-SY), dialekt anglické Británie (en-GB) a dialektu Austrálie (EN-AU).
    
    V případě nahrávání videa nahrazujeme zh-HANS na zh-CN, obě jsou podporované, ale doporučuje se použít zh-CN a přesnější.
    
## <a name="october-2019"></a>Říjen 2019
 
* Hledání animovaných znaků v galerii

    Při indexování animovaných znaků je teď můžete vyhledat v zobrazení videa účtu. Další informace najdete v tématu [rozpoznávání animovaných znaků](animated-characters-recognition.md).

## <a name="september-2019"></a>Září 2019
 
V daty IBC 2019 bylo oznámeno více předběžných.
 
* Animované rozpoznávání znaků (Public Preview)

    Možnost rozpoznávat znaky skupinových reklam v animovaném obsahu prostřednictvím integrace s vlastními vizemi. Další informace naleznete v tématu [animovaná detekce znaků](animated-characters-recognition.md).
* Identifikace ve více jazycích (Public Preview)

    Detekuje segmenty v různých jazycích zvukové stopy a na základě nich vytvoří vícejazyčný přepis. Počáteční podpora: angličtina, španělština, němčina a francouzština. Další informace najdete v tématu [Automatická identifikace a přepis vícejazyčného obsahu](multi-language-identification-transcription.md).
* Extrakce pojmenovaná entita pro lidi a umístění

    Extrahuje značky, umístění a lidi z mluvené řeči a vizuálního textu prostřednictvím zpracování přirozeného jazyka (NLP).
* Klasifikace typu redakčního snímku

    Označování snímků pomocí redakčních typů, jako je uzavření, střední záběr, dva snímky, interiér, venkovní atd. Další informace najdete v tématu [zjištění typu redakčního snímku](scenes-shots-keyframes.md#editorial-shot-type-detection).
* Inferencing vylepšení – teď pokrývání úrovně 2
    
    V tématu Inferencing model teď podporuje hlubší členitost taxonomie IPTC. Přečtěte si úplné podrobnosti [Azure Media Services nové inovace s podporou AI](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Srpen 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer nasazena v Velká Británie – jih

Nyní můžete vytvořit placený účet Video Indexer v oblasti Velká Británie – jih.

### <a name="new-editorial-shot-type-insights-available"></a>Nové informace typu redakčního snímku přehled dostupných

Nové značky přidané do snímků videa poskytují redakční "typy snímků" a identifikují je pomocí běžných redakčních frází, které se používají v pracovním postupu pro vytváření obsahu, jako jsou například extrémní Closeup, Closeup, roztažitelné, střední, dva snímky, venkovní, interiér, levý obličej a pravý obličej (k dispozici ve formátu JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>K dispozici je extrakce nových entit pro lidi a umístění

Video Indexer identifikuje pojmenovaná umístění a lidi prostřednictvím zpracování přirozeného jazyka (NLP) z optického rozpoznávání a přepisu videa. Video Indexer používá algoritmus strojového učení k rozpoznání, kdy konkrétní umístění (třeba věž Eiffel Tower) nebo lidi (například Jan Novák) jsou ve videu volána.

### <a name="keyframes-extraction-in-native-resolution"></a>Extrakce klíčových snímků v nativním rozlišení

Klíčové snímky extrahované Video Indexer jsou k dispozici v původním rozlišení videa.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA pro školení vlastních modelů tváře z imagí

Školení ploch z imagí přesunutých z režimu náhledu do GA (k dispozici prostřednictvím rozhraní API a na portálu).

> [!NOTE]
> Neexistuje žádný dopad na ceny související s přechodem "Preview na GA".

### <a name="hide-gallery-toggle-option"></a>Skrýt přepínač galerie

Uživatel může zvolit skrytí karty galerie z portálu (podobně jako skrytí karty ukázky).
 
### <a name="maximum-url-size-increased"></a>Zvýšila se maximální velikost adresy URL.

Podpora řetězce dotazu URL 4096 (místo 2048) při indexování videa
 
### <a name="support-for-multi-lingual-projects"></a>Podpora vícejazyčných projektů

Projekty se teď dají vytvářet na základě videí indexovaných v různých jazycích (jenom rozhraní API).

## <a name="july-2019"></a>Červenec 2019

### <a name="editor-as-a-widget"></a>Editor jako widget

Editor AI Video Indexer je teď k dispozici jako widget, který se má vložit do zákaznických aplikací.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Aktualizace vlastního jazykového modelu z zavřeného souboru titulků z portálu

Zákazníci můžou jako vstup pro jazykové modely na stránce vlastní nastavení portálu zadat formáty souborů VTT, SRT aplikace a TTML.

## <a name="june-2019"></a>Červen 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer nasazené do Japonska – východ

Nyní můžete vytvořit placený účet Video Indexer v oblasti Japonsko – východ.

### <a name="create-and-repair-account-api-preview"></a>Vytvoření a oprava rozhraní API pro účty (Preview)

Přidalo se nové rozhraní API, které umožňuje [aktualizovat koncový bod nebo klíč připojení Azure Media Service](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services).

### <a name="improve-error-handling-on-upload"></a>Zlepšení zpracování chyb při nahrávání 

V případě nestandardního nastavení Azure Media Services účtu se vrátí Popisná zpráva.

### <a name="player-timeline-keyframes-preview"></a>Klíčové snímky časové osy přehrávače v Preview 

V časové ose přehrávače teď můžete zobrazit náhled obrazu pro každou časovou osu.

### <a name="editor-semi-select"></a>Editor – poloviční výběr

Teď si můžete zobrazit náhled všech přehledů, které jsou vybrané jako výsledek výběru konkrétního období přehledu v editoru.

## <a name="may-2019"></a>Květen 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Aktualizace vlastního jazykového modelu z zavřeného souboru titulků

[Vytvoření vlastního jazykového modelu](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) a aktualizace rozhraní API pro [vlastní jazykové modely](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) teď podporují formáty souborů vtt, SRT aplikace a TTML jako vstup pro jazykové modely.

Při volání [rozhraní API pro přepis aktualizace videa](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)se přepis přidá automaticky. Školicí model přidružený k videu se také aktualizuje automaticky. Informace o tom, jak přizpůsobit a naučit jazykové modely, najdete v tématu [přizpůsobení jazykového modelu pomocí video indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nové formáty přepisů ke stažení – TXT a CSV

Kromě skrytého formátu titulků, který už je podporovaný (SRT aplikace, VTT a TTML), teď Video Indexer podporuje stahování přepisu ve formátech TXT a CSV.

## <a name="next-steps"></a>Další kroky

[Přehled](video-indexer-overview.md)
