---
title: Vytvoření nové nabídky Dynamics 365 Business Central na komerčním trhu
description: Jak vytvořit novou nabídku Dynamics 365 Business Central pro zápis nebo prodej na Webu Azure Marketplace, AppSource nebo prostřednictvím programu Zprostředkovatel cloudových řešení (CSP) pomocí portálu Commercial Marketplace v Centru microsoftpartnerů.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 9a167ac1cc9b9ca8cc8951f14f43ba4cf5d10551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294417"
---
# <a name="create-a-new-dynamics-365-business-central-offer"></a>Vytvoření nové nabídky Dynamics 365 Business Central

Toto téma vysvětluje, jak vytvořit novou nabídku Dynamics 365 Business Central. [Microsoft Dynamics 365 Business Central](https://dynamics.microsoft.com/business-central) je systém plánování podnikových zdrojů (ERP), který zpracovává širokou škálu obchodních procesů, včetně financí, operací, dodavatelského řetězce, CRM a řízení projektů a elektronického obchodu. Prémiové balíčky také podporují klasický model nasazení a výrobu. Všechny nabídky pro Dynamics 365 Business Central musí projít naším certifikačním procesem.

Chcete-li začít vytvářet nabídky Dynamics 365 Business Central, nejprve [vytvořte účet Centra partnerů](./create-account.md) a otevřete [řídicí panel Komerční tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou stránkou **Přehled.**

![Řídicí panel Komerční tržiště v Partnerském centru](./media/new-offer-overview.png)

>[!Note]
> Po zveřejnění nabídky budou úpravy nabídky provedené v Partnerském centru aktualizovány pouze v systému a na frontách obchodů po opětovném publikování. Po provádění změn se ujistěte, že odešlete nabídku ke zveřejnění.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Vyberte tlačítko **+ Nová nabídka** a pak vyberte položku nabídky Dynamics **365 Business Central.** Zobrazí se dialogové okno **Nová nabídka.**

### <a name="offer-id-and-alias"></a>ID nabídky a alias

- **ID nabídky**: Jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID bude viditelné pro zákazníky v adrese URL pro nabídku marketplace a šablony Azure Resource Manager (pokud je k dispozici). ID nabídky musí být malé alfanumerické znaky (včetně spojovníků a podtržítk, ale bez mezer), musí být omezeny na 50 znaků a nelze je změnit po výběru **možnosti Vytvořit**.  Pokud zde například zadáte *test-offer-1,* bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`adresa URL nabídky .

- **Alias nabídky**: Název používaný k odkazování na nabídku v rámci Partnerského centra. Tento název nebude na trhu použit a liší se od názvu nabídky a dalších hodnot, které se zobrazí zákazníkům. Tuto hodnotu nelze změnit po výběru **možnosti Vytvořit**.

Po zadání **id nabídky** a **aliasu Nabídky**vyberte **Vytvořit**. Poté budete moci pracovat na všech různých částech vaší nabídky.

## <a name="offer-setup"></a>Nastavení nabídky

Stránka **Nastavení nabídky** vás požádá o následující informace. Nezapomeňte po dokončení těchto polí vybrat **uložit.**

### <a name="how-do-you-want-potential-customers-to-interact-with-this-listing-offer"></a>Jak chcete, aby potenciální zákazníci komunikovali s touto nabídkou nabídek?

Vyberte možnost, kterou chcete pro tuto nabídku použít.

#### <a name="get-it-now-free"></a>Získejte jej nyní (zdarma)

Uveďte svou nabídku zákazníkům zdarma tím, že poskytnete platnou adresu URL (začínající *http* nebo *https),* kde mohou přistupovat k vaší aplikaci.  Příklad: `https://contoso.com/my-app`

#### <a name="free-trial-listing"></a>Zkušební verze zdarma (výpis)

Uveďte svou nabídku zákazníkům s odkazem na bezplatnou zkušební verzi tím, že poskytnete platnou adresu URL (začínající *na http* nebo *https),* kde mohou získat zkušební verzi.  Například: `https://contoso.com/trial/my-app`. Nabídka výpis bezplatné zkušební verze jsou vytvořeny, spravovány a nakonfigurovány vaší službou a nemají předplatná spravuje společnost Microsoft.

> [!NOTE]
> Tokeny, které vaše aplikace obdrží prostřednictvím zkušebního odkazu, lze použít jenom k získání informací o uživateli prostřednictvím služby Azure Active Directory (Azure AD) k automatizaci vytváření účtu ve vaší aplikaci. Účty Microsoft nejsou podporovány pro ověřování pomocí tohoto tokenu.

#### <a name="contact-me"></a>Kontaktujte mě

Shromážděte kontaktní informace zákazníků propojením systému řízení vztahů se zákazníky (CRM). Zákazník bude požádán o povolení ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a zdrojem tržiště, kde našli vaši nabídku, budou odeslány do systému CRM, který jste nakonfigurovali. Další informace o konfiguraci aplikace CRM naleznete v tématu [Připojení správy zájemců](#connect-lead-management). 

### <a name="test-drive"></a>Zkušební jízda

Testovací jízda je skvělý způsob, jak předvést svou nabídku potenciálním zákazníkům tím, že jim dáte možnost "vyzkoušet si před nákupem", což vede ke zvýšené konverzi a generování vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jízdách.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Chcete-li povolit testovací jednotku, zaškrtněte **políčko Povolit testovací jednotku.** Poté budete muset nakonfigurovat demonstrační prostředí v [konfiguraci technické konfigurace testovací jednotky,](#test-drive-technical-configuration) aby zákazníci měli vaši nabídku vyzkoušet na pevně stanovenou dobu. 

#### <a name="type-of-test-drive"></a>Typ zkušební jízdy

Vyberte z následujících možností:

- **[Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)**: Šablona nasazení, která obsahuje všechny prostředky Azure, které tvoří vaše řešení. Produkty, které odpovídají tomuto scénáři, používají jenom prostředky Azure.
- **[Dynamics 365 for Business Central](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-business-central-offer)**: Microsoft hostuje a udržuje službu testovací jednotky (včetně zřizování a nasazování) pro systém plánování podnikových podnikových podnikových podnikových prostředků (finance, operace, dodavatelský řetězec, CRM atd.).  
- **[Dynamics 365 for Customer Engagement](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/dyn365ce/cpp-customer-engagement-offer)**: Společnost Microsoft hostuje a udržuje službu testovací jednotky (včetně zřizování a nasazování) pro systém Customer Engagement (prodej, služba, projektová služba, služba v terénu atd.).  
- **[Dynamics 365 for Operations](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cpp-dynamics-365-operations-offer)**: Společnost Microsoft hostuje a udržuje službu testovací jednotky (včetně zřizování a nasazování) pro systém plánování podnikových prostředků finance a operace (finance, operace, výroba, dodavatelský řetězec atd.). 
- **[Aplikace logiky](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/logic-app-test-drive)**: Šablona nasazení zahrnující všechny architektury komplexních řešení. Všechny vlastní produkty by měly používat tento typ testovací jednotky.
- **[Power BI](https://docs.microsoft.com/power-bi/service-template-apps-overview)**: Vložený odkaz na vlastní řídicí panel. Produkty, které chtějí předvést interaktivní vizuál Power BI, by měly používat tento typ testovací hodu. Tady stačí nahrát vložená adresa URL Power BI.

#### <a name="additional-test-drive-resources"></a>Další prostředky testovací jednotky

- [Technické osvědčené postupy pro testovací jízdu](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Osvědčené postupy pro marketing testovacího disku](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovací jednotky jeden pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Připojení správy potenciálních zákazníků

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace naleznete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Nezapomeňte **uložit** před přechodem na další sekci!

## <a name="properties"></a>Vlastnosti

Stránka **Vlastnosti** umožňuje definovat kategorie a odvětví používaná k seskupení nabídky na trhu, verzi aplikace a právní smlouvy podporující vaši nabídku. Po dokončení této stránky vyberte **Uložit.**

### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně tři kategorie, které budou použity k umístění vaší nabídky do příslušných oblastí vyhledávání na trhu. Nezapomeňte v popisu nabídky zjistit, jak vaše nabídka tyto kategorie podporuje. 

### <a name="industry"></a>Odvětví

[!INCLUDE [Industry Taxonomy](./includes/industry-taxonomy.md)]

### <a name="app-version"></a>verze aplikace

Zadejte číslo verze nabídky. Zákazníci uvidí tuto verzi uvedenou na stránce s podrobnostmi nabídky.

### <a name="terms-and-conditions"></a>Podmínky a ujednání

Uveďte své vlastní právní podmínky v oblasti **smluvních podmínek.** Můžete také zadat adresu URL, kde lze najít vaše smluvní podmínky. Zákazníci budou muset přijmout tyto podmínky, než budou moci vyzkoušet vaši nabídku.

## <a name="offer-listing"></a>Nabídka výpis

Na stránce Nabídky se zobrazují jazyky, ve kterých bude vaše nabídka uvedena. V současné době je angličtina **(Spojené státy)** jedinou dostupnou možností.

Pro každý jazyk/trh budete muset definovat podrobnosti tržiště (název nabídky, popis, obrázky atd.). Chcete-li tyto informace zadat, vyberte název jazyka/trhu.

> [!NOTE]
> Nabídka výpis obsah (například popis, dokumenty, screenshoty, podmínky použití, atd.) nemusí být v angličtině, pokud popis nabídky začíná frází, "Tato aplikace je k dispozici pouze v [non-anglický jazyk]." Je také přijatelné poskytnout *užitečnou adresu URL odkazu* pro nabízení obsahu v jiném jazyce, než který byl použit v obsahu nabídky.

### <a name="name"></a>Name (Název)

Jméno, které zde zadáte, se zákazníkům zobrazí jako název nabídky. Toto pole je předem vyplněno textem, který jste zadali pro **alias Nabídky** při vytváření nabídky, ale tuto hodnotu můžete změnit. Tento název může být chráněn ochrannou známkou (a můžete uvést ochranné známky nebo symboly autorských práv). Název nesmí mít více než 50 znaků a nesmí obsahovat žádné emodži.

### <a name="short-description"></a>Stručný popis

Uveďte krátký popis nabídky (až 100 znaků), který lze použít ve výsledcích vyhledávání na marketplace.

### <a name="description"></a>Popis

Poskytněte delší popis nabídky (až 3 000 znaků). Tento popis se zobrazí zákazníkům v přehledu výpisu marketplace. Uveďte návrh hodnoty vaší nabídky, klíčové výhody, přidružení kategorií a/nebo odvětví, příležitosti k nákupu v aplikaci a všechny požadované informace. 

Několik tipů pro psaní popisu:  

- V prvních několika větách popisu jasně popište hodnotu nabídky. Do návrhu hodnoty zahrňte následující položky:
  - Popis produktu
  - Typ uživatele, který využívá produkt
  - Potřeby nebo bolest zákazníka, kterou produkt řeší
- Mějte na paměti, že prvních několik vět může být zobrazeno ve výsledcích vyhledávače.  
- Při prodeji produktu se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou dodáváte.  
- Používejte co nejvíce slovní zásobu nebo formulace založené na výhodách.
- Zvažte použití značek HTML k formátování popisu a jeho většízapojení.

Chcete-li, aby byl popis nabídky poutavější, naformátujte popis pomocí editoru rtf.

![Použití editoru rtf](./media/text-editor2.png)

Pomocí editoru RTF použijte následující pokyny:

- Chcete-li změnit formát obsahu, zvýrazněte text, který chcete formátovat, a vyberte styl textu, jak je znázorněno níže:

     ![Změna formátu textu pomocí editoru RTF](./media/text-editor3.png)

- Chcete-li do textu přidat seznam s odrážkami nebo číslovaný seznam, použijte následující možnosti:

     ![Přidání seznamů pomocí editoru RTF](./media/text-editor4.png)

- Chcete-li k textu přidat nebo odebrat odsazení, použijte následující možnosti:

     ![Odsazení pomocí editoru rtf](./media/text-editor5.png)

### <a name="search-keywords"></a>Hledat klíčová slova

Volitelně můžete zadat až tři klíčová slova pro vyhledávání, která zákazníkům pomohou najít vaši nabídku na trhu. Chcete-li dosáhnout nejlepších výsledků, zkuste tato klíčová slova použít také v popisu.

### <a name="products-your-app-works-with"></a>Produkty, se kterými vaše aplikace pracuje

Pokud chcete zákazníkům dát vědět, že vaše aplikace funguje s konkrétními produkty, zadejte sem až tři názvy produktů.

### <a name="support-urls"></a>Adresy URL podpory

Tato část umožňuje poskytnout odkazy, které zákazníkům pomohou lépe porozumět vaší nabídce.

#### <a name="help-link"></a>Odkaz na nápovědu

Zadejte adresu URL, na které mohou zákazníci získat další informace o vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace. Jste zodpovědní za to, že vaše aplikace je v souladu se zákony a předpisy ochrany osobních údajů a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="contacts"></a>Kontakty

V této části je nutné zadat jméno, e-mail a telefonní číslo **kontaktu podpory** a **technického kontaktu**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici společnosti Microsoft a mohou být poskytnuty partnerům CSP.

V části **Kontakt podpory** musíte také zadat adresu **URL podpory,** kde mohou partneři CSP najít podporu pro vaši nabídku.

### <a name="supporting-documents"></a>Podklady

Zadejte zde alespoň jeden (a až tři) související marketingové dokumenty, jako jsou bílé knihy, brožury, kontrolní seznamy nebo prezentace. Tyto dokumenty musí být ve formátu PDF.

### <a name="marketplace-images"></a>Obrázky na marketplace

V této části můžete poskytnout loga a obrázky, které budou použity při zobrazování vaší nabídky zákazníkovi. Všechny obrázky musí být ve formátu PNG.

#### <a name="store-logos"></a>Loga obchodu

Zadejte logo nabídky ve dvou velikostech: **Malé (48 x 48)** a **Velké (216 x 216).**

#### <a name="hero"></a>Hrdina

Obrázek hrdiny je volitelný. Pokud zadáte jeden, musí měřit 815 x 290 pixelů.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Jsou vyžadovány alespoň tři snímky obrazovky a můžete přidat až pět. Všechny snímky obrazovky musí mít rozměry 1280 x 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která ukazují vaši nabídku. Tato videa by měla být umístěna na YouTube a / nebo Vimeo. Pro každý z nich zadejte název videa, jeho adresu URL a miniaturu videa (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

- [Doporučené postupy pro nabídky na marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="availability"></a>Dostupnost

Stránka **Dostupnost** nabízí možnosti, kde a jak zpřístupnit vaši nabídku.

### <a name="markets"></a>Trhy

V této části můžete určit trhy, na kterých by měla být vaše nabídka k dispozici. Chcete-li tak učinit, vyberte **možnost Upravit trhy**, ve kterém se zobrazí vyskakovací okno Výběr **trhu.**

Ve výchozím nastavení nejsou vybrány žádné trhy, ale musíte vybrat alespoň jeden trh, abyste mohli svou nabídku publikovat. Klikněte na **Vybrat vše,** chcete-li, aby vaše nabídka byla k dispozici na všech možných trzích, nebo vyberte konkrétní trhy, které chcete přidat. Po dokončení vyberte **Uložit**.

Vaše výběry se zde vztahují pouze na nové akvizice; Pokud už někdo vaši aplikaci má na určitém trhu a později tento trh odstraníte, lidé, kteří již mají nabídku na tomto trhu, ji mohou nadále používat, ale žádní noví zákazníci na tomto trhu nebudou moci vaši nabídku získat.

> [!IMPORTANT]
> Je vaší odpovědností splnit všechny místní právní požadavky, a to i v případě, že tyto požadavky nejsou uvedeny zde nebo v Partnerském centru.

Mějte na paměti, že i když vyberete všechny trhy, místní zákony a omezení nebo jiné faktory mohou zabránit tomu, aby byly určité nabídky uvedeny v některých zemích a oblastech.

### <a name="preview-audience"></a>Náhled publika

Než nabídku publikujete živě v širší nabídce marketplace, musíte ji nejprve zpřístupnit omezenému **okruhu uživatelů ve verzi Preview**. Zde zadejte **klávesu Skrýt** (libovolný řetězec používající pouze malá písmena a/nebo čísla). Členové okruhu uživatelů ve verzi Preview mohou pomocí tohoto skrytého klíče jako tokenu zobrazit náhled vaší nabídky na trhu.

Až budete připraveni zpřístupnit nabídku a odebrat omezení náhledu, budete muset **odebrat klíč Skrýt** a publikovat znovu.

## <a name="technical-configuration"></a>Technická konfigurace

Stránka **Technická konfigurace** definuje technické podrobnosti použité pro připojení k vaší nabídce. Toto připojení nám umožňuje zřídit vaši nabídku pro koncového zákazníka, pokud se ji rozhodne získat.

### <a name="package-type"></a>Typ balíčku

Vyberte možnost, která se vztahuje k vaší nabídce:

- **Přidat:** Doplňková aplikace rozšiřuje možnosti a stávající funkce Dynamics 365 Business Central. Další informace najdete v [tématu Doplňkové aplikace](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-add-on-apps).
- **Připojení**: Aplikaci Connect lze použít ve scénáři, kde je nutné navázat spojení mezi aplikací Dynamics 365 Business Central a řešením nebo službou jiného výrobce. Další informace najdete v tématu [Připojení](https://docs.microsoft.com/dynamics365/business-central/dev-itpro/developer/readiness/readiness-connect-apps).

### <a name="file-upload"></a>Nahrání souboru

Pokud jste vybrali **přidat na** výše, kde budete nahrát soubor balíčku nabídky, spolu se soubory balíčku pro všechny přípony, na kterých má závislosti.

#### <a name="extensions-package-file"></a>Soubor balíčku přípony

Nahrajte soubor balíčku rozšíření (.app) pro vaši nabídku.

#### <a name="library-package-file"></a>Soubor balíčku knihovny

Povinné, pokud vaše nabídka musí být nainstalována spolu s jiným rozšířením, které nebude publikováno na trhu. Pokud ano, nahrajte svůj soubor .app zde.

#### <a name="dependency-package-file"></a>Soubor balíčku závislostí

Povinné, pokud vaše nabídka musí být nainstalována spolu s jiným rozšířením, které již bylo publikováno na trhu. Pokud ano, `.app` nahrajte jeho nebo `.zip` soubor zde.

### <a name="url-to-app-installation"></a>Adresa URL instalace aplikace

Pokud jste vybrali **Připojit** výše, zadejte adresu URL pro instalaci aplikace zde.

## <a name="test-drive-technical-configuration"></a>Technická konfigurace zkušební jízdy

Pokud jste na stránce [Nastavení nabídky](#offer-setup) vybrali **Možnost Povolit testovací jízdu,** budete muset zde poskytnout podrobnosti, aby zákazníci mohli vyzkoušet zkušební jízdu vaší nabídky.

Stránka **Testovací jednotka** umožňuje nastavit ukázku (nebo "testovací jízdu"), která zákazníkům umožní vyzkoušet vaši nabídku před potvrzením nákupu. Další informace naleznete v článku [Co je testovací jízda?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive) Pokud již nechcete poskytovat zkušební jízdu pro vaši nabídku, vraťte se na stránku **[Nastavení nabídky](#offer-setup)** a odškrtněte **možnost Povolit testovací jednotku**.

K dispozici jsou následující typy testovacích jednotek, z nichž každý má své vlastní požadavky na technickou konfiguraci.

- [Azure Resource Manager](#technical-configuration-for-azure-resource-manager-test-drive)
- [Dynamics 365](#technical-configuration-for-dynamics-365-test-drive)
- [Aplikace logiky](#technical-configuration-for-logic-app-test-drive)
- [Power BI](#technical-configuration-not-required-for-power-bi-test-drives) (Technická konfigurace není nutná)

### <a name="technical-configuration-for-azure-resource-manager-test-drive"></a>Technická konfigurace pro testovací jednotku Azure Resource Manager

Šablona nasazení, která obsahuje všechny prostředky Azure, které tvoří vaše řešení. Produkty, které odpovídají tomuto scénáři, používají jenom prostředky Azure. Přečtěte si další informace o nastavení [testovací jednotky Azure Resource Manager](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

- **Oblasti** (povinné): V současné době existuje 26 oblastí podporovaných Azure, kde můžete zpřístupnit testovací jednotku. Obvykle budete chtít, aby vaše testovací jednotky k dispozici v oblastech, kde očekáváte největší počet zákazníků, aby mohli vybrat nejbližší oblast pro nejlepší výkon. Budete se muset ujistit, že vaše předplatné je povoleno nasadit všechny prostředky potřebné v každé z oblastí, které vybíráte.

- Instance : **Vyberte**typ (horký nebo studený) a počet dostupných instancí, které se vynásobí počtem oblastí, kde je vaše nabídka k dispozici.

**Hot**: Tento typ instance je nasazen a čeká na přístup pro vybranou oblast. Zákazníci mohou okamžitě přistupovat *k hot* instance testovací jednotky, spíše než muset čekat na nasazení. Kompromis emituje, že tyto instance jsou vždy spuštěné ve vašem předplatném Azure, takže jim vzniknou vyšší náklady na provozuna. Důrazně doporučujeme mít alespoň jednu *instanci Hot,* protože většina zákazníků nechce čekat na úplné nasazení, což má za následek pokles v použití zákazníka, pokud není k dispozici žádná instance *Hot.*

**Studená**: Tento typ instance představuje celkový počet instancí, které lze případně nasadit na oblast. Studené instance vyžadují celou šablonu Správce prostředků testovací jednotky k nasazení, když zákazník požaduje testovací jednotku, takže *instance Cold* jsou mnohem pomalejší než instance *Hot.* Kompromis je, že stačí zaplatit po dobu trvání testovací jednotky, *není* vždy spuštěna na předplatné Azure jako s *hot* instance.

- **Testovací jednotka Azure Resource Manager šablona:** Nahrajte .zip obsahující šablonu Azure Resource Manager.  Další informace o vytvoření šablony Azure Resource Manager v článku úvodního článku [Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).

- **Doba trvání zkušební jednotky** (povinné): Zadejte dobu, po kterou bude testovací jízda aktivní, v počtu hodin. Testovací cesta se automaticky ukončí po uplynutí tohoto časového období. Tato doba trvání může být nastavena pouze na celý počet hodin (například "2" hodiny, "1,5" není platný).

### <a name="technical-configuration-for-dynamics-365-test-drive"></a>Technická konfigurace pro testovací jízdu Dynamics 365

Společnost Microsoft může odebrat složitost nastavení testovací jednotky hostováním a údržbou zřizování a nasazování služeb pomocí tohoto typu testovací jednotky. Konfigurace pro tento typ hostované testovací jednotky je stejná bez ohledu na to, zda testovací jednotka cílí na cílovou skupinu Business Central, Customer Engagement nebo Operations.

- **Maximální počet souběžných testovacích jednotek** (povinné): Nastavte maximální počet zákazníků, kteří mohou testovací jednotku používat najednou. Každý souběžný uživatel bude využívat licenci Dynamics 365, když je testovací jednotka aktivní, takže budete muset zajistit, že máte k dispozici dostatek licencí pro podporu nastaveného maximálního limitu. Doporučená hodnota 3-5.

- **Doba trvání zkušební jízdy** (povinné): Zadejte dobu, po kterou bude testovací jednotka aktivní, definováním počtu hodin. Po uplynutí této doby relace skončí a již nebude spotřebovávat jednu z vašich licencí. Doporučujeme hodnotu 2-24 hodin v závislosti na složitosti vaší nabídky. Tato doba trvání může být nastavena pouze na celý počet hodin (například "2" hodiny, "1,5" není platný).  Uživatel může požádat o novou relaci, pokud jim dojde čas a chcete znovu získat přístup k testovací jednotce.

- **Adresa URL instance** (povinné): Adresa URL, na které zákazník zahájí testovací jízdu. Adresa URL instance Dynamics 365, ve které je aplikace spuštěna s nainstalovanými ukázkovými daty (například `https://testdrive.crm.dynamics.com`).

- **Adresa URL webového rozhraní API** instance (povinné): Načtěte adresu URL webového rozhraní API pro instanci Dynamics 365 tak, že se přihlásíte ke svému účtu Microsoft 365 a přejdete do **nastavení** \&gt; **Přizpůsobení** \&gt; **Zdroje pro vývojáře** \&gt; **Instance Web API (Adresa URL root služby)**, `https://testdrive.crm.dynamics.com/api/data/v9.0`zkopírujte adresu URL, která je zde nalezena (například ).

- **Název role** (povinné): Zadejte název role zabezpečení, který jste definovali ve vlastní testovací jednotce Dynamics 365, která bude uživateli přiřazena během testovací jednotky (například role testovací jednotky).

### <a name="technical-configuration-for-logic-app-test-drive"></a>Technická konfigurace testovací jednotky aplikace Logika

Všechny vlastní produkty by měly používat tento typ šablony nasazení testovací jednotky, která zahrnuje celou řadu složitých architektur řešení. Další informace o nastavení testovacích jednotek aplikace Logika najdete na webu [Operace](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) a [Zapojení zákazníků na](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) GitHubu.

- **Oblast** (povinný rozevírací seznam s jedním výběrem): Momentálně existuje 26 oblastí podporovaných Azure, kde můžete zpřístupnit testovací jednotku. Prostředky pro vaši aplikaci logiky se nasadí v oblasti, kterou vyberete. Pokud vaše aplikace logiky obsahuje vlastní prostředky uložené v určité oblasti, ujistěte se, že oblast je vybrána zde. Nejlepším způsobem je plně nasadit aplikaci logiky místně na vašem předplatném Azure na portálu a ověřit, že funguje správně před provedením tohoto výběru.

- **Maximální počet souběžných testovacích jednotek** (povinné): Nastavte maximální počet zákazníků, kteří mohou testovací jednotku používat najednou. Tyto testovací jednotky jsou již nasazeny, což zákazníkům umožňuje okamžitý přístup k nim bez čekání na nasazení.

- **Doba trvání zkušební jednotky** (povinné): Zadejte dobu, po kterou bude testovací jízda aktivní, v počtu hodin. Testovací jednotka se automaticky ukončí po uplynutí tohoto časového období.

- **Název skupiny prostředků Azure** (povinné): Zadejte název [skupiny prostředků Azure,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) kde se uloží testovací jednotka aplikace Logika.

- **Název aplikace logiky Azure** (povinné): Zadejte název aplikace logiky, která přiřadí testovací jednotku uživateli. Tato aplikace logiky musí být uložena ve skupině prostředků Azure výše.

- **Deprovision název aplikace logiky** (povinné): Zadejte název aplikace logiky, která deprovisions testovací jednotky po dokončení zákazníka. Tato aplikace logiky musí být uložena ve skupině prostředků Azure výše.

### <a name="technical-configuration-not-required-for-power-bi-test-drives"></a>Technická konfigurace není vyžadována pro testovací jízdy Power BI

Produkty, které chtějí předvést interaktivní vizuál Power BI, můžou pomocí vloženého odkazu sdílet vlastní řídicí panel jako testovací jízdu, bez nutnosti další technické konfigurace. Přečtěte si další informace o nastavení aplikací šablon[Power BI.](https://docs.microsoft.com/power-bi/service-template-apps-overview)

### <a name="deployment-subscription-details"></a>Podrobnosti o předplatném nasazení

Chcete-li nasadit testovací disk vaším jménem, vytvořte a poskytněte samostatné a jedinečné předplatné Azure. (Není vyžadováno pro testovací jednotky Power BI).

- **ID předplatného Azure** (vyžadováno pro aplikace Azure Resource Manager a Logic): Zadejte ID předplatného, které udělí přístup ke službám účtu Azure pro vytváření sestav a fakturaci využití prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure,](https://docs.microsoft.com/azure/billing/billing-create-subscription) které se použije pro testovací jednotky, pokud ho ještě nemáte. ID předplatného Azure najdete tak, že se přihlásíte na [portál Azure](https://portal.azure.com/) a přejdete na kartu **Předplatná** v nabídce na levé straně. Výběrem karty se zobrazí ID předplatného (například "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID klienta Azure AD** (povinné): Zadejte [ID klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)služby Azure Active Directory (AD). Chcete-li toto ID najít, přihlaste se na [portál Azure](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte **Vlastnosti a vyhledejte uvedené číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e). ID klienta vaší organizace můžete také vyhledat pomocí adresy [https://www.whatismytenantid.com](https://www.whatismytenantid.com)URL názvu domény na adrese: .

- **Název klienta Azure AD** (vyžadováno pro Dynamic 365): Zadejte název služby Azure Active Directory (AD). Chcete-li najít tento název, přihlaste se na [portál Azure](https://portal.azure.com/), v pravém horním rohu název klienta bude uveden pod názvem vašeho účtu.

- **ID aplikace Azure AD** (povinné): Zadejte [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)služby Azure Active Directory (AD). Chcete-li toto ID najít, přihlaste se na [portál Azure](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte **registrace aplikací**a vyhledejte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e).

- **Tajný klíč klienta aplikace Azure AD** (povinné): Zadejte [tajný klíč klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikace Azure AD . Chcete-li najít tuto hodnotu, přihlaste se na [portál Azure](https://portal.azure.com/). V levé nabídce vyberte kartu **Azure Active Directory,** vyberte **Registrace aplikací**a vyberte aplikaci testovací jednotky. Dále vyberte **Certifikáty a tajné kódy**, vyberte **Nový tajný klíč klienta**, zadejte popis, v **části** **Nikdy** nevyprší platnost , a pak zvolte **Přidat**. Ujistěte se, že zkopírujete hodnotu. (Neprocházejte se od stránky dříve, než to uděláte, jinak nebudete mít přístup k hodnotě.)

Nezapomeňte **uložit** před přechodem na další sekci!

### <a name="test-drive-marketplace-listings"></a>Test disk marketplace výpisy

Možnost **výpisu na Marketplace,** která se nachází na kartě **Testovací jednotka,** zobrazuje jazyky, ve kterých je testovací jednotka k dispozici. V současné době **angličtina (Spojené státy)** je jediné místo k dispozici. Vyberte název jazyka a zadejte informace, které popisují prostředí testovací jízdy.

- **Popis** (povinné): Popište zkušební jízdu, co bude prokázáno, cíle pro uživatele experimentovat s, funkce k prozkoumání a veškeré relevantní informace, které pomohou uživateli určit, zda získat vaši nabídku. Do tohoto pole lze zadat až 3 000 znaků textu. 

- **Přístup k informacím** (vyžadováno pro testovací jednotky Azure Resource Manager a Logic): Vysvětlete, co zákazník potřebuje vědět, aby mohl přistupovat k této testovací jednotce a používat ji. Projděte si scénář pro použití vaší nabídky a přesně to, co by měl zákazník vědět pro přístup k funkcím v průběhu testovací jízdy. Do tohoto pole lze zadat až 10 000 znaků textu.

- **Uživatelská příručka** (povinné): Podrobný návod k testovací jízdě. Uživatelská příručka by měla zahrnovat přesně to, co chcete, aby zákazník získal z testovací jízdy, a sloužit jako reference pro všechny otázky, které mohou mít. Soubor musí být ve formátu PDF a po nahrání musí být pojmenován (maximálně 255 znaků).

- **Videa: Přidejte videa** (volitelně): Videa lze nahrát na YouTube nebo Vimeo a odkazovat zde s odkazem a miniaturou obrázku (533 x 324 pixelů), aby si zákazník mohl zobrazit informace, které jim pomohou lépe porozumět testovací jízdě, včetně toho, jak úspěšně využívat funkce vaší nabídky a porozumět scénářům, které zvýrazňují jejich výhody.
  - **Jméno** (povinné)
  - **URL (pouze YouTube nebo Vimeo)** (povinné)
  - **Miniatura (533 x 324 px):** Soubor obrázku musí být ve formátu PNG.

## <a name="supplemental-content"></a>Doplňkový obsah

Tato stránka vám umožňuje poskytnout další informace o vaší nabídce, které nám pomohou ověřit vaši nabídku. Tyto informace se zákazníkům nezobrazují ani nepublikují na trhu.

### <a name="target-release"></a>Cílové vydání

Uveďte, které vydání aplikace Microsoft Dynamics Business Central je cílem řešení: **Aktuální**, **Další hlavní**nebo Další **dílčí**. Tyto informace nám umožňují odpovídajícím způsobem otestovat vaše řešení.

### <a name="supported-editions"></a>Podporované edice

Pokud vaše nabídka vyžaduje edici Premium aplikace Microsoft Dynamics 365 Business Central, vyberte pouze **premium.** V opačném případě vyberte **položky Essentials** i **Premium**.

### <a name="key-usage-scenario"></a>Scénář použití klíče

Je nutné `.pdf` nahrát soubor, který obsahuje seznam klíčových scénářů použití vaší nabídky uvedených v dokumentu (formát pdf). Všechny zde uvedené scénáře mohou být ověřeny naším ověřovacím týmem předtím, než schválíme vaši nabídku pro tržiště.

### <a name="app-tests-automation"></a>Automatizace testů aplikací

Volitelně můžete nahrát soubor **automatizace testů aplikací** zde (.app).

### <a name="test-accounts"></a>Testovací účty

Pokud je testovací účet potřebný k tomu, aby náš certifikační tým mohl vaši nabídku řádně zkontrolovat, nahrajte soubor .pdf, .doc nebo .docx s informacemi o **testovacích účtech.**

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do náhledu

Po dokončení všech požadovaných částí nabídky vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování.** 

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na stav dokončení pro každou část nabídky.
    - *Nezahájeno* - znamená, že sekce nebyla dotčena a musí být dokončena.
    - *Neúplné* - znamená, že sekce obsahuje chyby, které je třeba opravit nebo vyžadují poskytnutí dalších informací. Vraťte se do oddílu (oddílů) a aktualizujte ji.
    - *Dokončeno* - znamená, že sekce je kompletní, všechny požadované údaje byly poskytnuty a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky v úplném stavu.
- V části **Poznámky k certifikaci** poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že vaše aplikace bude správně testována, a navíc ke všem doplňkovým poznámkám užitečným pro pochopení vaší aplikace.
- Odešlete nabídku k publikování výběrem **možnosti Odeslat**. Pošleme vám e-mail, když je k dispozici předběžná verze nabídky, kterou můžete zkontrolovat a schválit. Vraťte se do Partnerského centra a vyberte **Go-live** pro nabídku, abyste zveřejnili svou nabídku veřejnosti (nebo pokud soukromou nabídku, soukromému publiku).

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
