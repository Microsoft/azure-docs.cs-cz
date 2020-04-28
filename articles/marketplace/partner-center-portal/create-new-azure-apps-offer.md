---
title: Vytvoření nové nabídky Aplikací Azure na komerčním trhu
description: Jak vytvořit novou nabídku Aplikací Azure pro zápis nebo prodej na Webu Azure Marketplace, AppSource nebo prostřednictvím programu Zprostředkovatel cloudových řešení (CSP) pomocí portálu Commercial Marketplace v Centru partnerů Microsoftu.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 516ebce176f53a0495ea493f5327658162e7ea9e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867268"
---
# <a name="create-an-azure-application-offer"></a>Vytvoření nabídky aplikací Azure

Postup publikování nabídky aplikací Azure na komerčním trhu jsou popsané zde.

## <a name="azure-application-offer-type"></a>Typ nabídky aplikace Azure

Toto téma popisuje základy nabídek aplikací Azure.  Tyto koncepty byste měli znát před zahájením procesu publikování nové nabídky aplikací Azure na marketplace.

### <a name="publishing-overview"></a>Přehled publikování

Šablony [řešení pro vytváření](https://channel9.msdn.com/Events/Build/2018/BRK3603) videí a spravované aplikace pro Azure Marketplace představují úvod do typu nabídky aplikace Azure:

* Jaké typy nabídek jsou k dispozici;
* Jaká technická aktiva jsou požadována;
* Jak vytvořit šablonu Azure Resource Manager;
* Vývoj a testování ui aplikace;
* Jak publikovat nabídku aplikace;
* Proces kontroly žádosti.

### <a name="types-of-azure-application-plans"></a>Typy aplikačních plánů Azure

Existují dva druhy plánů aplikací Azure, spravovaných aplikací a šablon řešení.

* **Šablona řešení** je jedním z hlavních způsobů publikování řešení na marketplace. Tento typ plánu se používá, když vaše řešení vyžaduje další nasazení a automatizaci konfigurace nad rámec jednoho virtuálního počítače (VM).  Pomocí šablony řešení můžete automatizovat poskytování více než jednoho prostředku, včetně virtuálních her, sítí a prostředků úložiště, a poskytovat tak komplexní řešení IaaS.  Další informace o vytváření šablon řešení najdete v dokumentaci [k Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

* **Spravovaná aplikace** je podobná šablonám řešení s jedním klíčovým rozdílem. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Pomocí spravovaných aplikací můžete snadno vytvářet a doručovat plně spravované aplikace na klíč svým zákazníkům.  Další informace o výhodách a typech spravovaných aplikací najdete v přehledu [spravovaných aplikací Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Všechny aplikace Azure obsahují alespoň dva soubory `.zip` v kořenové složce archivu:

* Soubor šablony Správce prostředků s názvem [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Toto je šablona, která definuje prostředky pro nasazení do předplatného Azure zákazníka.  Příklady šablon Správce prostředků najdete v [galerii šablon Azure Quickstart](https://azure.microsoft.com/resources/templates/) nebo v odpovídajícím [úložišti GitHub: Azure Resource Manager Quickstart Templates.](https://github.com/azure/azure-quickstart-templates)

* Definice uživatelského rozhraní pro prostředí vytváření aplikací Azure s názvem [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  V uživatelském rozhraní zadáváte elementy, které uživatelům umožňují zadávat hodnoty parametrů.

Všechny nové nabídky aplikací Azure musí obsahovat [identifikátor GUID přiřazení využití partnera Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="before-you-begin"></a>Před zahájením

Projděte si následující dokumentaci k aplikacím Azure, která poskytuje rychlé starty, kurzy a ukázky.

* [Principy šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Rychlá zprovoznění:

    * [Šablony Azure Quickstart](https://azure.microsoft.com/documentation/templates/)
    * [Šablony githubazure rychlých startů](https://github.com/azure/azure-quickstart-templates)
    * [Publikování definice aplikace](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Nasazení aplikace katalogu služeb](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Kurzy:

    * [Vytvoření definičních souborů](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publikování aplikace Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Vzorky:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Řešení spravovaných aplikací](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Základy technických znalostí

Navrhování, vytváření a testování těchto prostředků nějakou dobu trvá a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky.

Váš technický tým by měl mít znalosti o následujících technologiích společnosti Microsoft:

* Základní znalosti [služeb Azure](https://azure.microsoft.com/services/)Services .
* Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/).
* Pracovní znalosti [virtuálních počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a Azure [Networking](https://azure.microsoft.com/services/?filter=networking#networking).
* Pracovní znalosti [Správce prostředků Azure](https://azure.microsoft.com/features/resource-manager/).
* Pracovní znalosti [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Navrhované nástroje

Chcete-li spravovat aplikaci Azure, zvolte jedno nebo obě následující skriptovací prostředí:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Doporučujeme přidat do vývojového prostředí následující nástroje:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) s následujícími rozšířeními:
    * Rozšíření: [Nástroje Azure Správce prostředků](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Rozšíření: [Zkrášlení](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Rozšíření: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostupné nástroje si můžete prohlédnout na stránce [Nástroje pro vývojáře Azure.](https://azure.microsoft.com/tools/)  Také pokud používáte Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Vytvoření nabídky aplikací Azure

Před vytvořením nabídky aplikací Azure musíte nejprve [vytvořit účet Centra partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) a otevřít řídicí panel [komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou kartou **Přehled.**

>[!Note]
>Po zveřejnění nabídky budou úpravy nabídky provedené v Partnerském centru aktualizovány pouze v systému a výlohách po opětovném publikování.  Po provedených změnách se ujistěte, že odešlete nabídku ke zveřejnění.

### <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Vyberte tlačítko **+ Nová nabídka a** pak vyberte položku nabídky Aplikace **Azure.** Zobrazí se dialogové okno **Nová nabídka.**

### <a name="offer-id-and-alias"></a>ID nabídky a alias

* **ID nabídky:** Jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID bude viditelné pro zákazníky v adrese URL pro nabídku marketplace a šablony Azure Resource Manager (pokud je k dispozici). <br> <br> ID nabídky musí být malé alfanumerické znaky (včetně spojovníků a podtržítk, ale bez mezer). Je omezena na 50 znaků a po výběru možnosti Vytvořit jej nelze změnit. <br> <br> Pokud `test-offer-1` zde zadáte například adresu URL `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`nabídky, bude . 

* **Alias nabídky**: Název používaný k odkazování na nabídku v rámci Partnerského centra. Tento název nebude na trhu použit a liší se od názvu nabídky a dalších hodnot, které se zobrazí zákazníkům. Tuto hodnotu nelze změnit po výběru **možnosti Vytvořit**.

Po zadání **id nabídky** a **aliasu Nabídky**vyberte **Vytvořit**. Poté budete moci pracovat na všech ostatních částech vaší nabídky.

## <a name="offer-setup"></a>Nastavení nabídky

Stránka **Nastavení nabídky** vás požádá o následující informace. Nezapomeňte po dokončení těchto polí vybrat **uložit.**

### <a name="test-drive"></a>Zkušební jízda

Testovací jízda je skvělý způsob, jak předvést svou nabídku potenciálním zákazníkům tím, že jim dáte možnost "vyzkoušet si před nákupem", což vede ke zvýšené konverzi a generování vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jízdách.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Chcete-li povolit testovací jednotku, zaškrtněte **políčko Povolit testovací jednotku.** Poté budete muset nakonfigurovat demonstrační prostředí v [konfiguraci technické konfigurace testovací jednotky,](#types-of-azure-application-plans) aby zákazníci měli vaši nabídku vyzkoušet na pevně stanovenou dobu. 

>[!Note]
>Vzhledem k tomu, že všechny aplikace Azure se implementují pomocí šablony Azure Resource Manager, jediný typ testovací jednotky, kterou lze nakonfigurovat pro aplikaci Azure, je [testovací jednotka založená na Azure Resource Manageru.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive)

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

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardní smlouva pro komerční trh společnosti Microsoft

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Nabídka výpis

Na stránce Nabídky se zobrazují jazyky, ve kterých bude vaše nabídka uvedena. V současné době je angličtina **(Spojené státy)** jedinou dostupnou možností.

Pro každý jazyk/trh budete muset definovat podrobnosti tržiště (název nabídky, popis, obrázky atd.). Chcete-li tyto informace zadat, vyberte název jazyka/trhu.

> [!NOTE]
> Nabídka výpis obsah (například popis, dokumenty, screenshoty, podmínky použití, atd.) nemusí být v angličtině, pokud popis nabídky začíná frází, "Tato aplikace je k dispozici pouze v [non-anglický jazyk]." Je také přijatelné poskytnout *užitečnou adresu URL odkazu* pro nabízení obsahu v jiném jazyce, než který byl použit v obsahu nabídky.

### <a name="name"></a>Název

Jméno, které zde zadáte, se zákazníkům zobrazí jako název nabídky. Toto pole je předem vyplněno textem, který jste zadali pro **alias Nabídky** při vytváření nabídky, ale tuto hodnotu můžete změnit. Tento název může být chráněn ochrannou známkou (a můžete uvést ochranné známky nebo symboly autorských práv). Název nesmí mít více než 50 znaků a nesmí obsahovat žádné emodži.

### <a name="summary"></a>Souhrn

Uveďte krátký popis nabídky (až 100 znaků), který lze použít ve výsledcích vyhledávání na marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Poskytněte delší popis nabídky (až 256 znaků). Popis lze použít ve výsledcích vyhledávání na marketplace.

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

### <a name="search-keywords"></a>Hledat klíčová slova

Volitelně můžete zadat až tři klíčová slova pro vyhledávání, která zákazníkům pomohou najít vaši nabídku na trhu. Chcete-li dosáhnout nejlepších výsledků, zkuste tato klíčová slova použít také v popisu.

### <a name="support-urls"></a>Adresy URL podpory

Tato část umožňuje poskytnout odkazy, které zákazníkům pomohou lépe porozumět vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace. Jste zodpovědní za to, že vaše aplikace je v souladu se zákony a předpisy ochrany osobních údajů a za poskytnutí platných zásad ochrany osobních údajů.

#### <a name="useful-links"></a>Užitečné odkazy

Poskytněte volitelné doplňkové online dokumenty o vašem řešení.  Přidejte další užitečné odkazy kliknutím **na + Přidat odkaz**.

### <a name="contacts"></a>Kontakty

V této části je nutné zadat jméno, e-mail a telefonní číslo **kontaktu podpory** a **technického kontaktu**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici společnosti Microsoft a mohou být poskytnuty partnerům CSP.

V části **Kontakt podpory** musíte také zadat adresu **URL podpory,** kde mohou partneři CSP najít podporu pro vaši nabídku.

### <a name="marketplace-images"></a>Obrázky na marketplace

V této části můžete poskytnout loga a obrázky, které budou použity při zobrazování vaší nabídky zákazníkovi. Všechny obrázky musí být ve formátu PNG.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že místní síť neblokuje službu https://upload.xboxlive.com používanou partnerským centrem.

#### <a name="store-logos"></a>Loga obchodu

Zadejte logo nabídky ve třech velikostech: **Malé (48 x 48),** **Střední (90 x 90)** a **Velké (216 x 216).**

#### <a name="hero"></a>Hrdina

Obrázek hrdiny je volitelný. Pokud zadáte jeden, musí měřit 815 x 290 pixelů.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Můžete přidat až pět snímků obrazovky. Všechny snímky obrazovky musí mít rozměry 1280 x 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která ukazují vaši nabídku. Tato videa by měla být umístěna na YouTube a / nebo Vimeo. Pro každou z nich zadejte název videa, jeho adresu URL a miniaturu videa (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

- [Doporučené postupy pro nabídky na marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Karta **Náhled** umožňuje definovat omezený **okruh uživatelů ve verzi Preview** pro ověření nabídky před publikováním nabídky živě širšímu okruhu uživatelů na marketplace.

> [!IMPORTANT]
> Musíte vybrat **Přejít živě,** než bude vaše nabídka zveřejněna živě veřejnému publiku na trhu po kontrole nabídky ve verzi Preview.

Vaše předběžná skupina uživatelů je označena identifikátory GUID předplatného Azure, které jsou spárovány s volitelným popisem pro každou z nich.  Žádné z těchto polí není pro zákazníky viditelné.

Přidejte až 10 ID předplatného Azure ručně nebo až 100, pokud nahrajete soubor CSV.  Přidáním těchto předplatných definujete cílovou skupinu, které bude před jeho úplném publikováním povolen přístup k náhledu k vaší nabídce.  Pokud je vaše nabídka již aktivní, můžete stále definovat náhled publika pro testování jakýchkoli změn nebo aktualizací nabídky.

>[!Note]
>Náhled okruhu uživatelů se liší od soukromého okruhu uživatelů. Před *publikováním* na tržištích je přístup k vaší nabídce ve verzi Preview povolen přístup k vaší nabídce. Můžete také vytvořit plán a zpřístupnit jej pouze soukromému publiku (na kartě Dostupnost plánu).  Váš okruh uživatelů ve verzi Preview bude moci zobrazit a ověřit všechny plány, včetně plánů, které budou k dispozici pouze soukromému publiku, jakmile bude vaše nabídka plně zveřejněna na trhu.

## <a name="plan-overview"></a>Přehled plánu

Karta **Přehled plánu** umožňuje poskytovat různé možnosti plánu v rámci stejné nabídky. Tyto plány (označované jako skuna na portálu partnerů cloudu) se mohou lišit, pokud jde o typ plánu (šablona řešení vs. spravovaná aplikace), zpeněžení nebo cílová skupina.  Nakonfigurujte alespoň jeden plán, abyste mohli uvést svou nabídku na trhu.

Po vytvoření se na této kartě zobrazí názvy plánu, ID, typ plánu, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce.

**Akce** dostupné v **přehledu plánu** se liší v závislosti na aktuálním stavu plánu a mohou zahrnovat:

* Pokud je stav plánu **Koncept** – Odstranit pracovní verzi.
* Pokud je stav plánu **Live** – Zastavte plán prodeje nebo synchronizujte soukromé publikum.

### <a name="create-new-plan"></a>Vytvořit nový plán

***ID plánu*** – vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude viditelné zákazníkům v adrese URL produktu.  Používejte pouze malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu je povoleno maximálně 50 znaků. Toto ID nelze po výběru příkazu vytvořit změnit.

***Název plánu*** – zákazníci se tomuto názvu zobrazí při rozhodování o tom, který plán chcete v rámci vaší nabídky vybrat. Vytvořte jedinečný název nabídky pro každý plán v této nabídce. Název plánu se používá k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (např. Název nabídky: Windows Server; Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Plánování nastavení

Karta **Nastavení plánu** umožňuje nastavit konfiguraci vysoké úrovně pro typ plánu, zda opakovaně používá balíčky z jiného plánu a v jakých cloudech by měl být plán k dispozici.  Vaše odpovědi na této kartě ovlivní, která pole se zobrazí na jiných kartách pro stejný plán.

#### <a name="plan-type"></a>Typ plánu

Jak je uvedeno v [typech plánů aplikací Azure](#types-of-azure-application-plans), vyberte, jestli váš plán bude obsahovat šablonu řešení nebo spravovanou aplikaci.

#### <a name="this-plan-reuses-packages"></a>Tento plán opakovaně používá balíčky

Pokud máte více než jeden plán stejného typu a balíčky jsou identické mezi nimi, můžete vybrat **tento plán opakovaně používá balíčky z jiného plánu**.  Vyberete-li tuto možnost, budete moci vybrat jeden z dalších plánů stejného typu pro tuto nabídku znovu použít balíčky z. 

>[!Note]
>Při opakovaném použití balíčků z jiného plánu zmizí z tohoto plánu celá karta Technická konfigurace.  Podrobnosti technické konfigurace z jiného plánu, včetně všech aktualizací, které provedete v budoucnu, budou použity také pro tento plán. <br> <br> Toto nastavení také nelze změnit po publikování tohoto plánu.

#### <a name="cloud-availability"></a>Dostupnost cloudu

Tento plán musí být k dispozici alespoň v jednom cloudu. 

Vyberte možnost **Veřejné Azure,** aby vaše řešení bylo možné nasadit zákazníkům ve všech veřejných oblastech Azure, které mají integraci Marketplace.  Přečtěte si další informace o [geografické dostupnosti](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Vyberte možnost **Azure Government Cloud,** aby bylo možné vaše řešení nasadit v [Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), což je cloud vládní komunity s řízeným přístupem pro zákazníky z amerického federálního, státního, místního nebo kmenového a partnerů, kteří mohou těmto entitám poskytovat služby.  Vy jako vydavatel nesete odpovědnost za veškeré kontroly dodržování předpisů, bezpečnostní opatření a osvědčené postupy, které budou sloužit této cloudové komunitě.  Azure Government používá fyzicky izolovaná datová centra a sítě (umístěné jenom v USA).  Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft doporučuje otestovat a ověřit vaše řešení v prostředí, protože některé koncové body se mohou lišit. Chcete-li zinscenovat a otestovat řešení, požádejte o zkušební účet z tohoto [odkazu](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>Jakmile je plán publikován jako dostupný v konkrétním cloudu, tento cloud nelze odebrat.

**Certifikace Azure Government Cloud**

Tato možnost se zobrazí jenom v případě, že azure **government cloud** je vybraná v části **Dostupnost cloudu**.

Služby Azure Government zpracovávají data, která podléhají určitým vládním předpisům a požadavkům, jako jsou FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 a CJIS.  Chcete-li zvýšit povědomí o certifikacích těchto programů, můžete poskytnout až 100 odkazů popisujících vaše certifikace.  Tyto odkazy mohou být buď odkazy na váš záznam v programu přímo, nebo odkazy na popisy vašeho souladu s nimi na vašich vlastních webových stránkách.  Tyto odkazy budou viditelné jenom pro zákazníky Azure Government Cloud.

## <a name="plan-listing"></a>Výpis plánu

Na kartě **výpis plánu** se zobrazí informace o výpisu specifické pro plán, které se mohou lišit mezi různými plány pro stejnou nabídku.

### <a name="name"></a>Název

Předem vyplněný na základě vašeho jména, které jste přiřadili k plánu při jeho vytvoření.  Tento název se zobrazí jako název tohoto "Softwarového plánu" zobrazeného na trhu.  Může obsahovat až 100 znaků.

### <a name="summary"></a>Souhrn

Poskytněte krátký souhrn softwarového plánu.  Může obsahovat až 100 znaků.

### <a name="description"></a>Popis

Tento popis je příležitostí vysvětlit, co dělá tento softwarový plán jedinečným a veškeré rozdíly od jiných softwarových plánů v rámci vaší nabídky. Může obsahovat až 2 000 znaků.

Po dokončení těchto polí vyberte **Uložit.**

## <a name="availability"></a>Dostupnost

Karta **Dostupnost** je viditelná pouze pro plány šablon řešení.  Plán můžete zviditelnit všem, pouze konkrétním zákazníkům (soukromým uživatelům uživatelů) a zda má být plán skrytý pouze pro použití v jiné šabloně řešení nebo ve spravovaných aplikacích.

### <a name="plan-audience"></a>Plánování publika

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele nebo pouze pro konkrétní cílovou skupinu podle vašeho výběru. Členství v této omezené cílové skupině můžete přiřadit pomocí ID předplatného Azure.

**Ochrana osobních údajů / Jedná se o soukromý plán** (Volitelné zaškrtávací políčko) - Zaškrtnutím tohoto políčka, aby byl váš plán soukromý a viditelný pouze pro omezené publikum podle vašeho výběru. Po publikování jako soukromý plán můžete okruh uživatelů aktualizovat nebo zvolit zpřístupnění plánu všem uživatelům. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny. (Plán nelze znovu nakonfigurovat jako soukromý plán).

**Omezená okruh uživatelů (ID předplatného Azure)** – přiřaďte okruh uživatelů, kteří budou mít přístup k tomuto soukromému plánu. Přístup se přiřazuje pomocí ID předplatného Azure s možností zahrnout popis každého přiřazeného ID předplatného Azure. Při importu souboru tabulky CSV lze přidat maximálně 10 ID předplatného nebo 20 000 ID předplatného zákazníků.  ID předplatného Azure jsou reprezentovány jako identifikátory GUID a písmena musí být malá písmena.

>[!Note]
>Soukromý okruh uživatelů (nebo omezený okruh uživatelů) se liší od náhledu publika, který jste definovali na kartě [**Náhled.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Před zveřejněním nabídky živě *na* tržišti je přístup k vaší nabídce ve verzi Preview. Zatímco označení soukromého publika se vztahuje pouze na konkrétní plán, náhled publika může zobrazit všechny plány (soukromé nebo ne) pro účely ověření.

### <a name="hide-plan"></a>Skrýt plán

Pokud je šablona řešení určena k nasazení pouze nepřímo, když se na ni odkazuje v jiné šabloně řešení nebo spravované aplikaci, zaškrtněte toto políčko, chcete-li šablonu řešení publikovat, ale skrýt ji před zákazníky, kteří ji přímo vyhledávají a procházejí.

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Karta **Ceny a dostupnost** je viditelná pouze pro spravované plány aplikací.  Můžete nakonfigurovat trhy, ve kterých bude tento plán k dispozici, cenu za měsíc správy řešení a zda chcete plán zviditelnit všem nebo pouze konkrétním zákazníkům (soukromé publikum).

### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň na jednom trhu. Zaškrtněte políčko pro libovolné místo na trhu, kde chcete tento plán zpřístupnit. Vyhledávací pole a tlačítko pro výběr zemí "Promované daně", ve kterých microsoft uložených daní a použití daně vaším jménem, jsou zahrnuty jako pomoc.

Pokud jste již nastavili ceny pro svůj plán v amerických dolarech (USD) a přidali další umístění na trhu, cena nového trhu bude vypočtena podle aktuálních směnných kurzů. Před publikováním vždy zkontrolujte cenu pro každý trh. Ceny lze zkontrolovat pomocí odkazu "Vývozní ceny (xlsx)" po uložení změn.

### <a name="pricing"></a>Ceny

Zadejte měsíční cenu pro tento plán.  Tato cena je navíc k jakékoli infrastruktuře Azure nebo nákladům na software s průběžnými platbami, které vzniknou prostředkům nasazeným tímto řešením.

Ceny stanovené v USD (USD = Americký dolar) jsou převedeny na místní měnu všech vybraných trhů pomocí aktuálních směnných kurzů při uložení. Před publikováním ověřte tyto ceny exportem tabulky cen a kontrolou ceny na jednotlivých trzích. Pokud chcete nastavit vlastní ceny na individuálním trhu, upravte a importujte tabulku cen. 

>[!Note]
>Chcete-li povolit export dat o cenách, musíte nejprve uložit změny cen.

Před publikováním si pečlivě zkontrolujte ceny, protože existují určitá omezení, co se může po publikování plánu změnit.  

>[!Note]
>Jakmile je cena za trh ve vašem plánu zveřejněna, nemůže být později změněna.

### <a name="plan-audience"></a>Plánování publika

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele nebo pouze pro konkrétní cílovou skupinu podle vašeho výběru. Členství v této omezené cílové skupině můžete přiřadit pomocí ID předplatného Azure.

**Ochrana osobních údajů / Jedná se o soukromý plán** (Volitelné zaškrtávací políčko) - Zaškrtnutím tohoto políčka, aby byl váš plán soukromý a viditelný pouze pro omezené publikum podle vašeho výběru. Po publikování jako soukromý plán můžete okruh uživatelů aktualizovat nebo zvolit zpřístupnění plánu všem uživatelům. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny. (Plán nelze znovu nakonfigurovat jako soukromý plán).

**Omezená okruh uživatelů (ID předplatného Azure)** – přiřaďte okruh uživatelů, kteří budou mít přístup k tomuto soukromému plánu. Přístup se přiřazuje pomocí ID předplatného Azure s možností zahrnout popis každého přiřazeného ID předplatného Azure. Při importu souboru tabulky CSV lze přidat maximálně 10 ID předplatného nebo 20 000 ID předplatného zákazníků.  ID předplatného Azure jsou reprezentovány jako identifikátory GUID a písmena musí být malá písmena.

>[!Note]
>Soukromý okruh uživatelů (nebo omezený okruh uživatelů) se liší od náhledu publika, který jste definovali na kartě [**Náhled.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  Před zveřejněním nabídky živě *na* tržišti je přístup k vaší nabídce ve verzi Preview. Zatímco označení soukromého publika se vztahuje pouze na konkrétní plán, náhled publika může zobrazit všechny plány (soukromé nebo ne) pro účely ověření.

## <a name="technical-configuration"></a>Technická konfigurace 

Karta **technická konfigurace** umožňuje nahrát balíček nasazení, který zákazníkům umožní nasadit váš plán.

>[!Note]
>Tato karta nebude viditelná, pokud jste tento plán nakonfigurovali pro opakované použití balíčků z jiného plánu na kartě **Nastavení plánu.**

### <a name="package-details"></a>Podrobnosti o balíčku

Podkarta **Podrobnosti balíčku** umožňuje upravit koncept verze technické konfigurace.

***Verze*** - Přiřazení aktuální verze technické konfigurace.  Při každém publikování změny na této stránce se tato verze zpřímí. Verze musí být `{integer}.{integer}.{integer}`ve formátu .

***Soubor*** balíčku`.zip`( ) - Tento balíček obsahuje všechny soubory šablon potřebné pro tento `.zip` plán, stejně jako všechny další prostředky, zabalené jako soubor.

Všechny balíčky plánu aplikací Azure musí obsahovat `.zip` tyto dva soubory v kořenové složce archivu:

* Soubor šablony Správce prostředků s názvem [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Tato šablona automatizuje nasazení prostředků do předplatného Azure zákazníkům.  Příklady šablon Správce prostředků najdete v [galerii šablon Azure Quickstart](https://azure.microsoft.com/documentation/templates/) nebo v odpovídajícím [úložišti GitHub: Azure Resource Manager Quickstart Templates.](https://github.com/azure/azure-quickstart-templates)

* Definice uživatelského rozhraní pro prostředí vytváření aplikací Azure s názvem [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Maximální podporované velikosti souborů jsou:

* Až 1 Gb v `.zip` celkové komprimované velikosti archivu

* Až 1 GB pro každý jednotlivý nekomprimovaný soubor v archivu `.zip`  

Všechny nové nabídky aplikací Azure musí také obsahovat identifikátor [GUID přiřazení využití partnera Azure.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že místní síť neblokuje službu https://upload.xboxlive.com používanou partnerským centrem.

### <a name="previously-published-packages"></a>Dříve publikované balíčky 

Dříve **publikované balíčky** podzáložka umožňuje zobrazit všechny publikované verze technické konfigurace.

## <a name="technical-configuration-managed-application-plans-only"></a>Technická konfigurace (pouze plány spravovaných aplikací)

Spravované plány aplikací mají další složitost na kartě **Technická konfigurace** nad rámec výše popsaných polí **verze** a **souboru.** 

### <a name="enable-just-in-time-jit-access"></a>Povolení přístupu za čas (JIT)

Tuto možnost vyberte, chcete-li pro tento plán povolit přístup k funkci Za chvíli (JIT).  Přístup JIT umožňuje požádat o zvýšený přístup k prostředkům spravované aplikace pro řešení potíží nebo údržbu. Vždy máte přístup jen pro čtení k prostředkům, ale pro určité časové období můžete mít větší přístup.  Další informace najdete [v tématu Povolení a vyžádání přístupu just-in-time pro spravované aplikace Azure](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Chcete-li vyžadovat, aby spotřebitelé spravované aplikace udělovali vašemu účtu trvalý přístup, ponechejte tuto možnost nezaškrtnutou.

>[!Note]
>Nezapomeňte aktualizovat `createUiDefinition.json` soubor, aby podporovaltuto funkci.  

### <a name="deployment-mode"></a>Režim nasazení

Vyberte, zda chcete při nasazování tohoto plánu nakonfigurovat režim **úplného** nebo **přírůstkového nasazení:** 

* V **úplném režimu**bude mít opětovné nasazení aplikace zákazníkem za následek odebrání prostředků ve skupině `mainTemplate.json`spravovaných prostředků, pokud prostředky nejsou definovány v . 
* V **přírůstkovém režimu**ponechá opětovné nasazení aplikace existující prostředky beze změny.

Další informace o režimech nasazení najdete v [tématu Režimy nasazení Správce prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>Adresa URL koncového bodu oznámení

Zadejte koncový bod HTTPS Webhook, který bude přijímat oznámení o všech operacích CRUD na instancích spravovaných aplikací této verze plánu.

### <a name="customize-allowed-customer-actions"></a>Přizpůsobení povolených akcí zákazníka

Tuto možnost vyberte, chcete-li určit, které akce`*/read`mohou zákazníci provádět se spravovanými prostředky kromě akcí , které jsou ve výchozím nastavení k dispozici. 

Uveďte zde další akce, které chcete zákazníkovi povolit, oddělené středníky.  Další informace najdete [v tématu Principy odepřít přiřazení pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Dostupné akce najdete v tématu [Operace zprostředkovatele prostředků Správce prostředků Azure](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Chcete-li například povolit spotřebitelům `Microsoft.Compute/virtualMachines/restart/action` restartování virtuálních počítačů, přidejte k povoleným akcím.

### <a name="global-azure--azure-government-cloud"></a>Globální Azure / Azure Government Cloud

Označte, kdo by měl mít přístup ke správě této spravované aplikace v každém podporovaném cloudu.  Uživatelé, skupiny nebo aplikace, kterým má být uděleno oprávnění ke skupině spravovaných prostředků, jsou identifikováni pomocí identit služby Azure Active Directory (AAD).

***ID klienta Služby Azure Active Directory*** – ID klienta AAD (označované také jako ID adresáře) obsahující identity uživatelů, skupin nebo aplikací, kterým chcete udělit oprávnění.  ID klienta AAD najdete na webu Azure Portal v [části Vlastnosti pro Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorizace*** – přidejte ID objektu služby Azure Active Directory uživatele, skupiny nebo aplikace, které chcete udělit oprávnění ke skupině spravovaných prostředků. Identifikujte uživatele podle jejich ID jistiny, které najdete v [okně Uživatelé služby Azure Active Directory na webu Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

Pro každý objekt zabezpečení vyberte jednu z předdefinovaných rolí Azure AD ze seznamu (vlastník nebo přispěvatel). Role, kterou vyberete, bude popisovat oprávnění, která bude mít hlavní povinný k prostředkům v předplatném zákazníka. Další informace najdete v tématu [Předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Další informace o řízení přístupu na základě rolí (RBAC), najdete [v tématu Začínáme s RBAC na webu Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>I když můžete přidat až 100 autorizací na cloud, je obecně jednodušší vytvořit skupinu uživatelů služby Active Directory a zadat její ID v hlavním ID.  To vám umožní přidat další uživatele do skupiny pro správu po nasazení plánu a snížit potřebu aktualizovat plán pouze přidat další autorizace.

### <a name="policy-settings"></a>Nastavení zásad

Použijte [zásady Azure](https://docs.microsoft.com/azure/governance/policy/overview) pro spravovanou aplikaci a určete požadavky na dodržování předpisů pro nasazené řešení.  Definice zásad a formátování hodnot parametrů najdete v tématu [Ukázky Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).  Můžete nakonfigurovat maximálně pět zásad a pouze jednu instanci každé možnosti zásad.  Některé zásady vyžadují další parametry.  Standardní skladová položka je vyžadována pro zásady auditu.  Název zásady je omezen na 50 znaků.

## <a name="co-sell"></a>Spoluprodej

Poskytnutí informací na kartě Cosell je pro publikování vaší nabídky zcela volitelné. Je nutné dosáhnout Co-sell Ready a IP Co-sell Ready status. Informace, které poskytnete, budou prodejními týmy společnosti Microsoft použity k tomu, aby se dozvěděly další informace o vašem řešení při vyhodnocování jeho vybavení potřebám zákazníků. Není k dispozici přímo zákazníkům.

Další informace o dokončení této karty naleznete [v tématu Možnost Spoluprodej v Centru partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Zkušební jízda

Karta **Testovací jednotka** umožňuje nastavit ukázku (nebo "testovací jízdu"), která zákazníkům umožní vyzkoušet si vaši nabídku před potvrzením nákupu. Další informace naleznete v článku [Co je testovací jízda?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Pokud již nechcete poskytovat zkušební jízdu pro vaši nabídku, vraťte se na stránku **Nastavení nabídky** a odškrtněte **možnost Povolit testovací jednotku**.

### <a name="technical-configuration"></a>Technická konfigurace

Aplikace Azure ze své podstaty používají typ testovací jednotky Azure Resource Manager.  Další informace najdete [v tématu Technická konfigurace testovací jednotky Azure Resource Manageru.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)

### <a name="deployment-subscription-details"></a>Podrobnosti o předplatném nasazení

Chcete-li nasadit testovací disk vaším jménem, vytvořte a poskytněte samostatné jedinečné předplatné Azure. (Není vyžadováno pro testovací jednotky Power BI).

- **ID předplatného Azure** (vyžadováno pro aplikace Azure Resource Manager a Logic): Zadejte ID předplatného, které udělí přístup ke službám účtu Azure pro vytváření sestav a fakturaci využití prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure,](https://docs.microsoft.com/azure/billing/billing-create-subscription) které se použije pro testovací jednotky, pokud ho ještě nemáte. ID předplatného Azure najdete tak, že se přihlásíte na [portál Azure](https://portal.azure.com/) a přejdete na kartu **Předplatná** v nabídce na levé straně. Výběrem karty se zobrazí Vaše ID předplatného (např. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **ID klienta Azure AD** (povinné): Zadejte [ID klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)služby Azure Active Directory (AD). Chcete-li toto ID najít, přihlaste se na [portál Azure](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte **Vlastnosti a vyhledejte uvedené číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e). ID klienta vaší organizace můžete také vyhledat pomocí adresy [https://www.whatismytenantid.com](https://www.whatismytenantid.com)URL názvu domény na adrese: .

- **Název klienta Azure AD** (vyžadováno pro Dynamic 365): Zadejte název služby Azure Active Directory (AD). Chcete-li najít tento název, přihlaste se na [portál Azure](https://portal.azure.com/), v pravém horním rohu název klienta bude uveden pod názvem vašeho účtu.

- **ID aplikace Azure AD** (povinné): Zadejte [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)služby Azure Active Directory (AD). Chcete-li toto ID najít, přihlaste se na [portál Azure](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte **Registrace aplikací**a vyhledejte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e).

- **Tajný klíč klienta aplikace Azure AD** (povinné): Zadejte [tajný klíč klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikace Azure AD . Chcete-li najít tuto hodnotu, přihlaste se na [portál Azure](https://portal.azure.com/). V levé nabídce vyberte kartu **Azure Active Directory,** vyberte **Registrace aplikací**a vyberte aplikaci testovací jednotky. Dále vyberte **Certifikáty a tajné kódy**, vyberte **Nový tajný klíč klienta**, zadejte popis, v **části** **Nikdy** nevyprší platnost , a pak zvolte **Přidat**. Ujistěte se, že zkopírujete hodnotu. (Neprocházejte se od stránky dříve, než jste zkopírovali hodnotu, jinak nebudete mít přístup k hodnotě.)

Nezapomeňte **uložit** před přechodem na další sekci!

### <a name="test-drive-listings-optional"></a>Výpisy zkušebních jízd (volitelné)

Možnost **Test Drive výpisy** nakartě **Test drive** zobrazuje jazyky (a trhy), kde je vaše testovací jednotka k dispozici, v současné době angličtina (Spojené státy) je jediné umístění k dispozici. Kromě toho tato stránka zobrazuje stav zápisu specifického pro jazyk a datum a čas, kdy byl přidán. Pro každý jazyk/trh budete muset definovat podrobnosti o testovací jízdě (popis, uživatelská příručka, videa atd.).

- **Popis** (povinné): Popište zkušební jízdu, co bude prokázáno, cíle pro uživatele experimentovat s, funkce k prozkoumání a veškeré relevantní informace, které pomohou uživateli určit, zda získat vaši nabídku. Do tohoto pole lze zadat až 3 000 znaků textu. 

- **Přístup k informacím** (vyžadováno pro testovací jednotky Azure Resource Manager a Logic): Vysvětlete, co zákazník potřebuje vědět, aby mohl přistupovat k této testovací jednotce a používat ji. Projděte si scénář pro použití vaší nabídky a přesně to, co by měl zákazník vědět pro přístup k funkcím v průběhu testovací jízdy. Do tohoto pole lze zadat až 10 000 znaků textu.

- **Uživatelská příručka** (povinné): Podrobný návod k testovací jízdě. Uživatelská příručka by měla zahrnovat přesně to, co chcete, aby zákazník získal z testovací jízdy, a sloužit jako reference pro všechny otázky, které mohou mít. Soubor musí být ve formátu PDF a po nahrání musí být pojmenován (maximálně 255 znaků).

- **Videa: Přidejte videa** (volitelně): Videa lze nahrát na YouTube nebo Vimeo a odkazovat zde s odkazem a miniaturou obrázku (533 x 324 pixelů), aby si zákazník mohl zobrazit informace, které jim pomohou lépe porozumět testovací jízdě, včetně toho, jak úspěšně využívat funkce vaší nabídky a porozumět scénářům, které zvýrazňují jejich výhody.
  - **Jméno** (povinné)
  - **URL (pouze YouTube nebo Vimeo)** (povinné)
  - **Miniatura (533 x 324 px):** Soubor obrázku musí být ve formátu PNG.

Po dokončení těchto polí vyberte **Uložit.**

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do náhledu

Po dokončení všech požadovaných částí nabídky vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování.** 

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na stav dokončení pro každou část nabídky.
    - *Nezahájeno* - znamená, že sekce nebyla dotčena a musí být dokončena.
    - *Neúplné* - znamená, že sekce obsahuje chyby, které je třeba opravit nebo vyžadují poskytnutí dalších informací. Vraťte se do oddílu (oddílů) a aktualizujte ji.
    - *Dokončeno* - znamená, že sekce je kompletní, všechny požadované údaje byly poskytnuty a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky v úplném stavu.
- Poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že vaše aplikace bude správně testována, a navíc ke všem doplňkovým poznámkám užitečným pro pochopení vaší aplikace.
- Odešlete nabídku k publikování výběrem **možnosti Odeslat**. Pošleme vám e-mail s upozorněním, kdy je k dispozici předběžná verze nabídky, abyste ji mohli zkontrolovat a schválit. Vraťte se do Partnerského centra a vyberte **Go-live** pro nabídku, abyste zveřejnili svou nabídku veřejnosti (nebo pokud soukromou nabídku, soukromému publiku).

### <a name="errors-and-review-feedback"></a>Chyby a zpětná vazba

Ruční **ověření** krok v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a související technické prostředky (zejména šablona Azure Resource Manager), problémy jsou obvykle prezentovány jako žádosti o přijetí vyhovek (PR) odkazy. Vysvětlení, jak zobrazit a reagovat na tyto PR, naleznete [v tématu Zpracování zpětné vazby od recenze](./azure-apps-review-feedback.md).

Pokud jste narazili na chyby v jednom nebo více krocích publikování, je nutné je opravit a znovu publikovat nabídku.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
