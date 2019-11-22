---
title: Vytvoření nové nabídky aplikací Azure na komerčním webu Marketplace
description: Postup vytvoření nové nabídky aplikací Azure pro výpis nebo prodej v Azure Marketplace, AppSource nebo prostřednictvím programu Cloud Solution Provider (CSP) pomocí portálu pro komerční tržiště v partnerském centru Microsoftu.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d7e05f12c04136c8394dbcb27b7a950fc5ce85d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281269"
---
# <a name="create-an-azure-application-offer"></a>Vytvoření nabídky aplikací Azure

Postup pro publikování nabídky aplikací Azure na komerčním webu Marketplace je popsaný tady.

## <a name="azure-application-offer-type"></a>Typ nabídky aplikace Azure

Toto téma popisuje základní informace o nabídkách aplikací Azure.  Než začnete s zahájením procesu publikování nové nabídky aplikací Azure na webu Marketplace, měli byste být obeznámeni s těmito pojmy.

### <a name="publishing-overview"></a>Přehled publikování

[Šablony řešení pro sestavování a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) jsou představení typu nabídky aplikace Azure:

* Jaké typy nabídek jsou k dispozici;
* Jaké technické prostředky jsou požadovány;
* Jak vytvořit šablonu Azure Resource Manager;
* Vývoj a testování uživatelského rozhraní aplikace;
* Jak publikovat nabídku aplikace;
* Proces revize aplikace

### <a name="types-of-azure-application-plans"></a>Typy plánů aplikací Azure

Existují dva druhy plánů aplikací Azure, spravované aplikace a šablony řešení.

* **Šablona řešení** je jedním z hlavních způsobů publikování řešení na webu Marketplace. Tento typ plánu se používá v případě, že vaše řešení vyžaduje další automatizaci nasazení a konfigurace než jeden virtuální počítač (VM).  Pomocí šablony řešení můžete automatizovat poskytování více prostředků, včetně virtuálních počítačů, sítí a úložných prostředků, a zajistit tak komplexní řešení IaaS.  Další informace o vytváření šablon řešení naleznete v dokumentaci k [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) .

* **Spravovaná aplikace** je podobná šablonám řešení a má jeden klíčový rozdíl. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Pomocí spravovaných aplikací můžete svým zákazníkům snadno sestavovat a doručovat plně spravované klíč aplikace.  Další informace o výhodách a typech spravovaných aplikací najdete v tématu [Přehled spravovaných aplikací Azure](https://docs.microsoft.com/azure/managed-applications/overview).

Všechny aplikace Azure obsahují v kořenové složce `.zip` archivu aspoň dva soubory:

* Soubor šablony Správce prostředků s názvem [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Jedná se o šablonu definující prostředky, které se mají nasadit do předplatného Azure zákazníka.  Příklady šablon Správce prostředků najdete v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/) nebo v příslušném úložišti [šablon pro rychlý Start Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .

* Definice uživatelského rozhraní pro prostředí pro vytváření aplikací Azure s názvem [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  V uživatelském rozhraní zadáváte elementy, které uživatelům umožňují zadávat hodnoty parametrů.

Všechny nové nabídky aplikací Azure musí zahrnovat [identifikátor GUID poskytovatele využívání zákaznických zákazníků Azure](??).

### <a name="before-you-begin"></a>Než začnete

Přečtěte si následující dokumentaci k aplikaci Azure, která poskytuje rychlé starty, kurzy a ukázky.

* [Principy šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Šablony rychlý start:

    * [Šablony pro rychlý Start Azure](https://azure.microsoft.com/documentation/templates/)
    * [Šablony Azure pro rychlý Start pro GitHub](https://github.com/azure/azure-quickstart-templates)
    * [Publikovat definici aplikace](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Nasadit aplikaci katalogu služeb](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Kurzy:

    * [Vytváření definičních souborů](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publikování aplikace Marketplace](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Vzory

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Řešení spravovaných aplikací](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Základy technického vědomí

Návrh, sestavování a testování těchto prostředků trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky.

Váš technický tým by měl mít znalosti o následujících technologiích Microsoftu:

* Základní porozumění [službám Azure](https://azure.microsoft.com/services/).
* Jak [navrhovat a architektovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
* Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a [Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Provozní znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Praktické znalosti formátu [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Navrhované nástroje

Vyberte jedno z následujících skriptovacích prostředí, které vám pomůžou při správě aplikace Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Do vývojového prostředí doporučujeme přidat následující nástroje:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) s následujícími příponami:
    * Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostupné nástroje můžete zkontrolovat na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/tools/) .  Také Pokud používáte aplikaci Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Vytvoření nabídky aplikací Azure

Než budete moct vytvořit nabídku aplikací Azure, musíte nejdřív [vytvořit účet partnerského centra](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) a otevřít [řídicí panel komerčního tržiště](https://partner.microsoft.com/dashboard/commercial-marketplace/offers)s vybranou kartou **Přehled** .

>[!Note]
>Po publikování nabídky se úpravy nabídky provedené v partnerském centru aktualizují jenom v systému a prodejní místa po opětovném publikování.  Po provedení změn Prosím zajistěte odeslání nabídky k publikování.

### <a name="create-a-new-offer"></a>Vytvoření nové nabídky

Vyberte tlačítko **+ Nová nabídka** a pak vyberte položku nabídky **aplikace Azure** . Zobrazí se dialogové okno **Nová nabídka** .

### <a name="offer-id-and-alias"></a>ID nabídky a alias

* **ID nabídky**: jedinečný identifikátor pro každou nabídku ve vašem účtu. Toto ID uvidí zákazníci v adrese URL pro nabídku Marketplace a šablony Azure Resource Manager (pokud jsou k dispozici). <br> <br> ID vaší nabídky musí být malé alfanumerické znaky (včetně spojovníků a podtržítek, ale bez mezer). Dá se omezit na 50 znaků a po výběru vytvořit se nedá změnit. <br> <br> Pokud například zadáte `test-offer-1` zde, adresa URL nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Alias nabídky**: název, který se používá k odkazování na nabídku v partnerském centru. Tento název se na webu Marketplace nepoužije a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazí. Tuto hodnotu nelze změnit po výběru možnost **vytvořit**.

Po zadání **ID nabídky** a **aliasu nabídky**vyberte **vytvořit**. Pak budete moci pracovat na všech ostatních částech vaší nabídky.

## <a name="offer-setup"></a>Nastavení nabídky

Na stránce **nastavení nabídky** se zobrazí výzva k zadání následujících informací. Nezapomeňte po dokončení těchto polí vybrat **Uložit** .

### <a name="test-drive"></a>Testovací verze

Testovací jednotka představuje skvělý způsob, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jednotkách.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Pak budete muset nakonfigurovat ukázkové prostředí v testovacím prostředí konfigurace [technické konfigurace](#types-of-azure-application-plans) , aby zákazníci mohli vyzkoušet vaši nabídku po určitou dobu. 

>[!Note]
>Vzhledem k tomu, že všechny aplikace Azure jsou implementované pomocí šablony Azure Resource Manager, jediný typ testovací jednotky, která se dá nakonfigurovat pro aplikaci Azure, je [Azure Resource Manager testovacích jednotek](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Další zdroje testovacích jednotek

- [Technické osvědčené postupy testovacích jednotek](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Osvědčené postupy pro testování a uvádění na trh](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek – jeden pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Připojit řízení zájemců

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Před přechodem k další části si nezapomeňte **Uložit** .

## <a name="properties"></a>Vlastnosti

Stránka **vlastností** vám umožní definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku. Po dokončení této stránky vyberte **Uložit** .

### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně tři kategorie, které se použijí k umístění vaší nabídky do příslušných oblastí hledání na webu Marketplace. Nezapomeňte si vyžádat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. 

### <a name="standard-marketplace-terms-and-conditions"></a>Podmínky a ujednání na úrovni Marketplace

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní šablonu smlouvy, aby usnadnila transakci na webu Marketplace.

Místo toho, abyste mohli vlastní podmínky a ujednání, si můžete vybrat, abyste software nabídli v rámci standardní smlouvy, které zákazníci stačí Vet a přijmout jenom jednou.

Standardní kontrakt najdete tady: https://go.microsoft.com/fwlink/?linkid=2041178

Pokud chcete použít standardní smlouvu, zaškrtněte políčko **použít standardní kontrakt?** .

#### <a name="terms-of-use"></a>Podmínky použití

Pokud políčko **použít standardní smlouvu?** nezaškrtněte, budete muset v poli **podmínky použití** zadat vlastní právní podmínku použití. Zadejte až 10 000 znaků textu nebo, pokud vaše podmínkami použití vyžadují delší popis, zadejte adresu URL, na které se můžou najít vaše další licenční smlouvy. Zákazníci budou muset tyto podmínky přijmout, aby mohli svoji aplikaci vyzkoušet.

## <a name="offer-listing"></a>Seznam nabídek

Na stránce se seznamem nabídek se zobrazují jazyky, ve kterých bude vaše nabídka uvedená. V současné době je jako jediná dostupná možnost **Angličtina (USA)** .

Pro každý jazyk nebo trh budete muset zadat podrobnosti o Marketplace (název nabídky, popis, obrázky atd.). Chcete-li poskytnout tyto informace, vyberte název jazyka/trhu.

> [!NOTE]
> Obsah seznamu nabídek (například popis, dokumenty, snímky obrazovky, podmínky použití atd.) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

### <a name="name"></a>Název

Název, který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole se předem vyplní textem, který jste zadali jako **alias nabídky** při vytváření nabídky, ale tuto hodnotu můžete změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

### <a name="summary"></a>Souhrn

Zadejte krátký popis vaší nabídky (až 100 znaků), který se dá použít ve výsledcích hledání na webu Marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Zadejte delší popis vaší nabídky (až 256 znaků). Popis se dá použít ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Popis

Zadejte delší popis vaší nabídky (až 3 000 znaků). Tento popis se zobrazí zákazníkům v přehledu výpisu na webu Marketplace. Zahrňte do své nabídky svou polohu, klíčové výhody, kategorii nebo přidružení oboru, možnosti nákupu v aplikaci a veškeré požadované informace. 

Pár tipů pro psaní popisu:  

- Jasně popište, jak se má v prvních několika větách popisu v této nabídce vyhodnotit. Zahrňte do svého umístění hodnoty tyto položky:
  - Popis produktu
  - Typ uživatele, který přináší výhody produktu
  - Potřeby zákazníků nebo bolesti, že adresy produktů
- Mějte na paměti, že ve výsledcích vyhledávacího modulu se může zobrazit několik prvních vět.  
- Nespoléhá se na funkce a funkce k prodeji vašeho produktu. Místo toho se zaměřte na hodnotu, kterou doručujete.  
- Používejte co nejvíc slovníků specifických pro konkrétní obor nebo využití s výhodami. 
- Zvažte použití značek HTML pro formátování popisu a lepší poutavost.

### <a name="search-keywords"></a>Hledat klíčová slova

Volitelně můžete zadat až tři klíčová slova pro hledání, která zákazníkům pomůžou najít vaši nabídku na webu Marketplace. Pro dosažení nejlepších výsledků zkuste použít tato klíčová slova i v popisu.

### <a name="support-urls"></a>Adresy URL podpory

V této části můžete zadat odkazy, které zákazníkům pomohou pochopit Další informace o vaší nabídce.

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

#### <a name="useful-links"></a>Užitečné odkazy

Poskytněte volitelné dodatečné online dokumenty k vašemu řešení.  Přidejte další užitečné odkazy kliknutím na **+ Přidat odkaz**.

### <a name="contacts"></a>Kontakty

V této části musíte zadat jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP.

V části **kontakt podpory** je také nutné zadat **adresu URL podpory** , kde mohou partneři CSP najít podporu pro vaši nabídku.

### <a name="marketplace-images"></a>Image Marketplace

V této části můžete zadat loga a obrázky, které se použijí při zobrazení vaší nabídky zákazníkovi. Všechny obrázky musí být ve formátu. png.

#### <a name="store-logos"></a>Ukládat loga

Poskytněte logo vaší nabídky ve třech velikostech: **malý (48 x 48)** , **střední (90 × 90)** a **Velká (216 x 216)** .

#### <a name="hero"></a>Hero

Obrázek Hero je nepovinný. Pokud zadáte jednu, musí změřit 815 x 290 pixelů.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte snímky obrazovky, které ukazují, jak vaše nabídka funguje. Můžete přidat až pět snímků obrazovky. Všechny snímky obrazovky musí být 1280 × 720 pixelů.

#### <a name="videos"></a>Videa

Volitelně můžete přidat až pět videí, která předvádí vaši nabídku. Tato videa by se měla hostovat na YouTube nebo Vimeo. U každého z nich zadejte název videa, jeho adresu URL a miniaturu obrázku (1280 x 720 pixelů).

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

- [Osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Preview

Karta **Náhled** umožňuje definovat **skupinu omezené verze Preview** pro ověření vaší nabídky před tím, než bude vaše nabídka publikována na širší cílovou skupinu Marketplace.

> [!IMPORTANT]
> Před tím, než se vaše nabídka po kontrole ve verzi Preview vrátí do veřejné skupiny Marketplace, musíte vybrat možnost **Přejít na aktivní** .

Vaše cílová skupina Preview se identifikuje pomocí identifikátorů GUID předplatných Azure, které jsou spárovány s volitelným popisem pro každý z nich.  Žádná z těchto polí nejsou viditelná pro zákazníky.

Pokud nahráváte soubor CSV, přidejte až 10 ID předplatných Azure ručně, nebo až 100.  Přidáním těchto předplatných definujete cílovou skupinu, pro kterou bude přístup k vaší nabídce povolený ve verzi Preview, než bude plně publikován.  Pokud je vaše nabídka už živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování změn nebo aktualizací vaší nabídky.

>[!Note]
>Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílová skupina Preview má povolený přístup k vaší nabídce *před* publikováním živě na Marketplace. Můžete se také rozhodnout vytvořit plán a zpřístupnit ho pouze pro privátní cílovou skupinu (pomocí karty plán dostupnosti).  Vaše cílová skupina ve verzi Preview bude moct zobrazit a ověřit všechny plány, včetně plánů, které budou k dispozici pouze pro privátní cílovou skupinu po úplném publikování vaší nabídky na webu Marketplace.

## <a name="plan-overview"></a>Přehled plánu

Karta **Přehled plánu** vám umožňuje v rámci stejné nabídky poskytnout různé možnosti plánu. Tyto plány (označované jako SKU v portál partnerů cloudu) se můžou lišit podle typu plánu (šablona řešení vs. spravovaná aplikace), finanční zhodnocení nebo cílová skupina.  Pokud chcete zobrazit seznam nabídek na webu Marketplace, nakonfigurujte aspoň jeden plán.

Po vytvoření se zobrazí vaše názvy plánů, ID, typ plánu, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce na této kartě.

**Akce** , které jsou k dispozici v **přehledu plánu** , se liší v závislosti na aktuálním stavu plánu a mohou zahrnovat:

* Pokud je stav plánu **koncept** – odstranit koncept.
* Pokud je stav plánu **Live** – zastavit prodej plánu nebo synchronizovat soukromou cílovou skupinu.

### <a name="create-new-plan"></a>Vytvořit nový plán

***ID plánu*** – pro každý plán v této nabídce Vytvořte jedinečné ID plánu. Toto ID se bude zobrazovat zákazníkům v adrese URL produktu.  Používejte jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu je povoleno maximálně 50 znaků. Po výběru možnosti vytvořit nelze toto ID změnit.

***Název plánu*** – zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Pro každý plán v této nabídce Vytvořte jedinečný název nabídky. Název plánu slouží k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (např. Název nabídky: Windows Server; plány: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Nastavení plánu

Karta **Nastavení plánu** vám umožní nastavit konfiguraci na nejvyšší úrovni pro typ plánu, bez ohledu na to, jestli znovu používá balíčky z jiného plánu, a jaké cloudy by měl mít plán k dispozici v.  Vaše odpovědi na této kartě budou mít vliv na to, která pole se zobrazí na jiných kartách pro stejný plán.

#### <a name="plan-type"></a>Typ plánu

Jak je uvedeno v [typech plánů aplikací Azure](#types-of-azure-application-plans), vyberte, jestli má váš plán obsahovat šablonu řešení nebo spravovanou aplikaci.

#### <a name="this-plan-reuses-packages"></a>Tento plán znovu používá balíčky.

Pokud máte více než jeden plán stejného typu a jsou mezi nimi stejné balíčky, můžete vybrat **Tento plán znovu používat balíčky z jiného plánu**.  Když vyberete tuto možnost, budete moci vybrat jeden z dalších plánů stejného typu, aby tato nabídka mohla znovu použít balíčky z. 

>[!Note]
>Když znovu použijete balíčky z jiného plánu, z tohoto plánu zmizí karta s celou technickou konfigurací.  Pro tento plán se použijí i podrobnosti technické konfigurace z druhého plánu, včetně všech aktualizací, které v budoucnu provedete. <br> <br> Toto nastavení se po publikování tohoto plánu také nedá změnit.

#### <a name="cloud-availability"></a>Dostupnost cloudu

Tento plán je potřeba zpřístupnit aspoň v jednom cloudu. 

Vyberte možnost **veřejné Azure** a zpřístupněte vaše řešení pro zákazníky ve všech veřejných oblastech Azure, které mají integraci na webu Marketplace.  Přečtěte si další informace o [geografické dostupnosti](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Vyberte možnost **cloudu Azure Government** , aby bylo možné nasadit vaše řešení do [cloudu Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), cloudu pro státní správu a řízený přístup pro zákazníky z federální, státní, místní nebo samosprávnéky USA, kteří mají nárok na poskytování těchto entit.  Jako vydavatel zodpovídáte za řízení dodržování předpisů, bezpečnostní opatření a osvědčené postupy pro obsluhu této komunity cloudu.  Azure Government používá fyzicky izolovaná datová centra a sítě (nacházejí se jenom v USA).  Před publikováním do [Azure Government](https://aka.ms/azuregovpublish)doporučuje společnost Microsoft testovat a ověřovat vaše řešení v prostředí, protože se některé koncové body můžou lišit. Pokud chcete své řešení připravit a testovat, vyžádejte si z tohoto [odkazu](https://azure.microsoft.com/global-infrastructure/government/request/)zkušební účet.

>[!Note]
>Jakmile je plán publikovaný jako dostupný v konkrétním cloudu, nedá se tento Cloud odebrat.

**Azure Government certifikace cloudu**

Tato možnost se zobrazí jenom v případě, že je v **cloudové dostupnosti**vybraná možnost **Azure Government Cloud** .

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy, jako je například FedRAMP, NIST 800,171 (DIB), ITAR, finanční 1075 úřad, DoD. L4 a CJIS.  Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které popisují vaše certifikace.  Tyto odkazy mohou být buď odkazy na váš seznam přímo v programu, nebo odkazy na popisy dodržování předpisů s nimi na vašich vlastních webech.  Tyto odkazy budou viditelné jenom pro cloudové zákazníky Azure Government.

## <a name="plan-listing"></a>Výpis plánu

Karta **seznam plánů** zobrazuje informace o seznamu specifického plánu, které se mohou lišit mezi různými plány pro stejnou nabídku.

### <a name="name"></a>Název

Předem vyplněné podle vašeho jména, které jste přiřadili k plánu při jeho vytvoření.  Tento název se zobrazí jako název tohoto "plánu softwaru" zobrazeného na webu Marketplace.  Může obsahovat až 100 znaků.

### <a name="summary"></a>Souhrn

Zadejte stručný souhrn vašeho plánu softwaru.  Může obsahovat až 100 znaků.

### <a name="description"></a>Popis

Tento popis je příležitost vysvětlit, co dělá tento plán softwaru jedinečný a jakékoli rozdíly od jiných plánů softwaru v rámci vaší nabídky. Může obsahovat až 2 000 znaků.

Po dokončení těchto polí vyberte **Uložit** .

## <a name="availability"></a>Dostupnost

Karta **dostupnosti** je viditelná jenom pro plány šablon řešení.  Plán můžete nastavit tak, aby byl viditelný pro všechny, jenom pro konkrétní zákazníky (soukromou cílovou skupinu) a jestli má být plán skrytý pro použití v jiných šablonách řešení nebo spravovaných aplikacích.

### <a name="plan-audience"></a>Cílová skupina plánu

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele, nebo jenom pro konkrétní cílovou skupinu, kterou zvolíte. V této omezené cílové skupině můžete přiřadit členství pomocí ID předplatných Azure.

**Ochrana osobních údajů/toto je soukromý plán** (volitelné zaškrtávací políčko) – zaškrtněte toto políčko, aby byl váš plán soukromý a viditelný jenom pro omezený počet uživatelů, které zvolíte. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo si vybrat, že plán bude dostupný všem. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny. (Plán se nedá nakonfigurovat jako privátní plán.)

**Skupina s omezeným přístupem (ID předplatných Azure)** – přiřadí cílovou skupinu, která bude mít přístup k tomuto privátnímu plánu. Přístup se přiřazuje pomocí ID předplatných Azure s možností zahrnout popis každého přiřazeného ID předplatného Azure. Pokud importujete soubor tabulky. csv, můžete přidat maximálně 10 ID předplatných nebo 20 000. ID předplatných zákazníků.  ID předplatných Azure se reprezentují jako identifikátory GUID a písmena musí být nižší než použita.

>[!Note]
>Privátní skupina (nebo skupina s omezeným přístupem) se liší od cílové skupiny Preview, kterou jste definovali na kartě [**Náhled**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Cílová skupina Preview má povolený přístup k vaší nabídce před tím, *než* je nabídka publikovaná živě na webu Marketplace. I když se označení soukromé cílové skupiny vztahuje pouze na konkrétní plán, cílová skupina verze Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

### <a name="hide-plan"></a>Skrýt plán

Pokud má být šablona řešení určena k nasazení pouze nepřímo, pokud se odkazuje i na jinou šablonu řešení nebo spravovanou aplikaci, zaškrtněte toto políčko, pokud chcete publikovat šablonu řešení, ale nebudete ji skrývat od zákazníků, kteří ji hledají a prohlížejí přímo.

## <a name="pricing-and-availability"></a>Ceny a dostupnost

Karta **ceny a dostupnost** je viditelná jenom pro plány spravovaných aplikací.  Můžete nakonfigurovat trhy, na které bude tento plán k dispozici, cenu za měsíc správy řešení a zda má být plán viditelný pro všechny nebo jen pro konkrétní zákazníky (soukromou cílovou skupinu).

### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro jakékoli místo na trhu, kde byste chtěli tento plán zpřístupnit. K nápovědě jsou k dispozici vyhledávací pole a tlačítko pro výběr zemí "daňového přiznání", ve kterých společnost Microsoft provádí prodej a používání daně vaším jménem.

Pokud jste už pro svůj plán nastavili ceny v USA dolary (USD) a přidáte další místo na trhu, cena za nový trh se vypočítá podle aktuálních směnných kurzů. Před publikováním si vždycky Projděte cenu za každý trh. Po uložení změn můžete pomocí odkazu exportovat ceny (XLSX) zkontrolovat ceny.

### <a name="pricing"></a>Ceny

Zadejte pro tento plán cenu za měsíc.  Tato cena se doplní na všechny infrastruktury Azure nebo náklady na software s průběžnými platbami, které vznikají prostředky nasazené tímto řešením.

Ceny nastavené v místní měně (USD = USA dolar) se převedou na místní měnu všech vybraných trhů pomocí aktuálních kurzů pro Exchange, které jsou k dispozici během instalace. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a zkontrolujete cenu na každém trhu. Pokud chcete nastavit vlastní ceny v individuálním trhu, upravte a importujte tabulku s cenami. 

>[!Note]
>Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen.

Před publikováním si pečlivě Projděte své ceny, protože existují určitá omezení, co se může po publikování plánu změnit.  

>[!Note]
>Jakmile je cena za trh v plánu publikovaná, nedá se později změnit.

### <a name="plan-audience"></a>Cílová skupina plánu

Máte možnost nakonfigurovat každý plán tak, aby byl viditelný pro všechny uživatele, nebo jenom pro konkrétní cílovou skupinu, kterou zvolíte. V této omezené cílové skupině můžete přiřadit členství pomocí ID předplatných Azure.

**Ochrana osobních údajů/toto je soukromý plán** (volitelné zaškrtávací políčko) – zaškrtněte toto políčko, aby byl váš plán soukromý a viditelný jenom pro omezený počet uživatelů, které zvolíte. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo si vybrat, že plán bude dostupný všem. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny. (Plán se nedá nakonfigurovat jako privátní plán.)

**Skupina s omezeným přístupem (ID předplatných Azure)** – přiřadí cílovou skupinu, která bude mít přístup k tomuto privátnímu plánu. Přístup se přiřazuje pomocí ID předplatných Azure s možností zahrnout popis každého přiřazeného ID předplatného Azure. Pokud importujete soubor tabulky. csv, můžete přidat maximálně 10 ID předplatných nebo 20 000. ID předplatných zákazníků.  ID předplatných Azure se reprezentují jako identifikátory GUID a písmena musí být nižší než použita.

>[!Note]
>Privátní skupina (nebo skupina s omezeným přístupem) se liší od cílové skupiny Preview, kterou jste definovali na kartě [**Náhled**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  Cílová skupina Preview má povolený přístup k vaší nabídce před tím, *než* je nabídka publikovaná živě na webu Marketplace. I když se označení soukromé cílové skupiny vztahuje pouze na konkrétní plán, cílová skupina verze Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

## <a name="technical-configuration"></a>Technická konfigurace 

Karta **Technická konfigurace** vám umožní nahrát balíček pro nasazení, který umožní zákazníkům nasadit váš plán.

>[!Note]
>Tato karta nebude viditelná, pokud jste nakonfigurovali tento plán na opětovné použití balíčků z jiného plánu na kartě **Nastavení plánu** .

### <a name="package-details"></a>Podrobnosti balíčku

**Podrobnosti o balíčku** subtab vám umožní upravit koncept verze vaší technické konfigurace.

***Verze*** – přiřaďte aktuální verzi technické konfigurace.  Zvyšte tuto verzi pokaždé, když publikujete změnu na této stránce. Verze musí být ve formátu `{integer}.{integer}.{integer}`.

***Soubor balíčku*** (`.zip`) – Tento balíček obsahuje všechny soubory šablon potřebné pro tento plán a další prostředky zabalené jako `.zip` soubor.

Všechny balíčky plánu aplikací Azure musí zahrnovat tyto dva soubory v kořenové složce `.zip` archivu:

* Soubor šablony Správce prostředků s názvem [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Tato šablona automatizuje nasazení prostředků do předplatného Azure Customer.  Příklady šablon Správce prostředků najdete v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/) nebo v příslušném úložišti [šablon pro rychlý Start Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .

* Definice uživatelského rozhraní pro prostředí pro vytváření aplikací Azure s názvem [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Všechny nové nabídky aplikací Azure musí zahrnovat i identifikátor GUID [poskytovatele využívání zákaznických zákazníků Azure](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

### <a name="previously-published-packages"></a>Dřív publikované balíčky 

**Dříve publikované balíčky** subtab vám umožní zobrazit všechny publikované verze vaší technické konfigurace.

## <a name="technical-configuration-managed-application-plans-only"></a>Technická konfigurace (jenom plány spravované aplikace)

Plány spravovaných aplikací mají další složitost na kartě **Technická konfigurace** nad rámec pole **verze** a **soubor balíčku** popsané výše. 

### <a name="enable-just-in-time-jit-access"></a>Povolit přístup JIT (just-in-time)

Tuto možnost vyberte, pokud chcete pro tento plán povolit přístup JIT (just-in-time).  Přístup JIT vám umožní požádat o zvýšený přístup k prostředkům spravované aplikace pro účely řešení potíží nebo údržby. Vždy máte přístup k prostředkům jen pro čtení, ale v určitém časovém období můžete mít větší přístup.  Další informace najdete v tématu [povolení a vyžádat přístup za běhu pro Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Pokud chcete vyžadovat, aby příjemci spravované aplikace udělily trvalému přístupu ke svému účtu, nechte tuto možnost nezaškrtnutou.

>[!Note]
>Aby bylo možné tuto funkci podporovat, nezapomeňte aktualizovat soubor `createUiDefinition.json`.  

### <a name="deployment-mode"></a>Režim nasazení

Určete, jestli se při nasazování tohoto plánu má nakonfigurovat **úplný** nebo **přírůstkový režim nasazení** : 

* V **režimu úplného**nasazení aplikace pro zákazníka dojde k odebrání prostředků ve spravované skupině prostředků, pokud prostředky nejsou definované v `mainTemplate.json`. 
* V **přírůstkovém režimu**opětovné nasazení aplikace opustí stávající prostředky beze změny.

Další informace o režimech nasazení najdete v tématu [Azure Resource Manager režimy nasazení](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>Adresa URL koncového bodu oznámení

Zadejte koncový bod Webhooku HTTPS pro příjem oznámení o všech operacích CRUD na instancích spravovaných aplikací této verze plánu.

### <a name="customize-allowed-customer-actions"></a>Přizpůsobení povolených akcí zákazníků

Tuto možnost vyberte, pokud chcete kromě akcí "`*/read`", které jsou ve výchozím nastavení dostupné, zadat i akce, které můžou zákazníci provádět na spravovaných prostředcích. 

Seznamte se s dalšími akcemi, které chcete, aby mohl váš zákazník provádět, oddělený středníky.  Další informace najdete v tématu [Principy přiřazení zamítnutí pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Dostupné akce najdete v tématu [Azure Resource Manager operací poskytovatele prostředků](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Pokud například chcete, aby uživatelé mohli restartovat virtuální počítače, přidejte `Microsoft.Compute/virtualMachines/restart/action` k povoleným akcím.

### <a name="global-azure--azure-government-cloud"></a>Globální cloud Azure/Azure Government

Určete, kdo má mít k této spravované aplikaci přístup pro správu v každém podporovaném cloudu.  Uživatelům, skupinám nebo aplikacím, kterým chcete udělit oprávnění pro spravovanou skupinu prostředků, se identifikuje pomocí identit Azure Active Directory (AAD).

***Azure Active Directory ID tenanta*** – ID tenanta AAD (označované také jako ID adresáře) obsahující identity uživatelů, skupin nebo aplikací, kterým chcete udělit oprávnění.  ID tenanta AAD můžete najít na Azure Portal ve [vlastnostech Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Autorizace*** – přidejte Azure Active Directory ID objektu uživatele, skupiny nebo aplikace, kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Identifikujte uživatele podle ID objektu zabezpečení, které najdete v okně [Azure Active Directory uživatelé na Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

U každého objektu zabezpečení vyberte v seznamu (vlastník nebo přispěvatel) jednu z předdefinovaných rolí Azure AD. Vybraná role popisuje oprávnění, která bude mít objekt zabezpečení u prostředků v rámci předplatného zákazníka. Další informace najdete v tématu [Předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Další informace o řízení přístupu na základě role (RBAC) najdete v tématu [Začínáme s RBAC v Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>I když můžete přidat až 100 autorizací pro každý Cloud, je obecně snazší vytvořit skupinu uživatelů služby Active Directory a zadat její ID v ID objektu zabezpečení.  To vám umožní přidat další uživatele do skupiny pro správu po nasazení plánu a snížit nutnost aktualizace plánu jenom na přidání dalších autorizací.

### <a name="policy-settings"></a>Nastavení zásad

Použijte [zásady Azure](https://docs.microsoft.com/azure/governance/policy/overview) na spravovanou aplikaci, abyste určili požadavky na dodržování předpisů pro nasazené řešení.  Definice zásad a formátování hodnot parametrů najdete v tématu [Ukázky Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).  Můžete nakonfigurovat maximálně pět zásad a jenom jednu instanci jednotlivých zásad.  Některé zásady vyžadují další parametry.  Pro zásady auditu se vyžaduje standardní SKU.  Název zásad je omezený na 50 znaků.

## <a name="co-sell"></a>Společný prodej

Poskytování informací na kartě spoluprodeje je zcela volitelné pro publikování vaší nabídky. Je nutné, abyste dosáhli svého stavu připraveného pro spoluprodejní a IP adresy pro vlastní prodej. Informace, které poskytnete, budou využívat prodejní týmy Microsoftu k získání dalších informací o řešení při vyhodnocování svých potřeb pro zákazníky. Není k dispozici přímo pro zákazníky.

Další informace o dokončení této karty najdete v tématu [možnost společného prodeje v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Testovací verze

Karta **testovací jednotka** vám umožní nastavit ukázku (nebo "testovací jednotku"), která zákazníkům umožní vyzkoušet si vaši nabídku před tím, než se ji zakoupí. Další informace najdete v článku [co je Test Drive?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Pokud již nechcete poskytnout testovací verzi vaší nabídky, vraťte se na stránku **nastavení nabídky** a zrušte zaškrtněte **možnost Povolit testovací jednotku**.

### <a name="technical-configuration"></a>Technická konfigurace

Aplikace Azure v podstatě používají Azure Resource Manager typ testovacího disku.  Další informace najdete v tématu [Technická konfigurace Azure Resource Manager testovacích jednotek](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) .

### <a name="deployment-subscription-details"></a>Podrobnosti předplatného nasazení

Chcete-li nasadit testovací jednotku vaším jménem, vytvořte a poskytněte samostatné jedinečné předplatné Azure. (Nevyžaduje se pro Power BI testovací jednotky).

- **ID předplatného Azure** (vyžadované pro Azure Resource Manager a Logic Apps): Zadejte ID předplatného, které vám umožní udělit přístup ke službám účtů Azure za účelem vytváření sestav a fakturace využívání prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure](https://docs.microsoft.com/azure/billing/billing-create-subscription) , které se použije pro testovací jednotky, pokud ho ještě nemáte. ID vašeho předplatného Azure můžete najít tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/) a přejdete na kartu **předplatná** v nabídce na levé straně. Výběrem karty zobrazíte ID předplatného (například a83645ac-1234-5ab6-6789-1h234g764ghty).

- **ID tenanta Azure AD** (povinné): zadejte [ID TENANTA](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu služby Active Directory, vyberte možnost * * vlastnosti a vyhledejte číslo **ID adresáře** (např. 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta vaší organizace můžete také vyhledat pomocí adresy URL vašeho názvu domény na adrese: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Název tenanta Azure AD** (vyžadováno pro dynamickou 365): zadejte název Azure Active Directory (AD). Pokud chcete najít tento název, přihlaste se k [Azure Portal](https://portal.azure.com/)v pravém horním rohu bude název vašeho tenanta uvedený pod názvem vašeho účtu.

- **ID aplikace Azure AD** (povinné): zadejte [ID aplikace](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **Registrace aplikací**a vyhledejte uvedené číslo **ID aplikace** (např. 50c464d3-4930-494c-963c-1e951d15360e).

- **Tajný kód klienta aplikace Azure AD** (povinné): zadejte [tajný kód klienta](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets)aplikace Azure AD. Tuto hodnotu najdete tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/). V nabídce vlevo vyberte kartu **Azure Active Directory** , vyberte **Registrace aplikací**a pak vyberte svoji aplikaci Test Drive. V dalším kroku vyberte **certifikáty a tajné klíče**, vyberte **nový tajný klíč klienta**, zadejte popis, v části **platnost**vyberte **nikdy** a pak zvolte **Přidat**. Nezapomeňte hodnotu zkopírovat. (Nevybírejte na stránce před zkopírováním hodnoty, nebo jinak nemáte přístup k této hodnotě.)

Před přechodem k další části si nezapomeňte **Uložit** .

### <a name="test-drive-listings-optional"></a>Výpisy testovacích jednotek (volitelné)

Možnost **výpisy testovacích jednotek** , která se nachází na kartě **testovací jednotka** , zobrazuje jazyky (a trhy), kde je k dispozici vaše testovací jednotka. v současné době je v angličtině (USA) dostupná pouze poloha. Kromě toho tato stránka zobrazuje stav seznamu pro konkrétní jazyk a datum a čas, kdy byl přidán. Pro každý jazyk nebo trh budete muset definovat podrobnosti testovacích jednotek (popis, uživatelská příručka, videa atd.).

- **Popis** (povinné): Popište si testovou jednotku, co se zobrazí, cíle pro uživatele, kteří mají experimentovat, funkce k prozkoumávání a jakékoli relevantní informace, které uživatelům pomůžou určit, jestli se má vaše nabídka získat. Do tohoto pole lze zadat až 3 000 znaků textu. 

- **Informace o přístupu** (vyžadované pro Azure Resource Manager a logické testovací jednotky): Vysvětlete, co zákazník potřebuje pro přístup k této testovací jednotce a k používání této zkušební jednotky znát. Projděte si scénář pro použití vaší nabídky a přesně toho, co by měl zákazník znát pro přístup k funkcím v rámci testovacích jednotek. Do tohoto pole lze zadat až 10 000 znaků textu.

- **User Manual** (povinné): podrobný návod k vašemu prostředí Test Drive. Ruční příručka uživatele by měla pokrývat přesně to, co chcete zákazníkovi získat ze zkušební jednotky a sloužit jako odkaz na případné dotazy, které mohou mít. Soubor musí být ve formátu PDF a po nahrání má název (maximálně 255 znaků).

- **Videa: přidat videa** (volitelné): videa se dají nahrát na YouTube nebo Vimeo a tady se odkazuje pomocí odkazu a miniatury (533 x 324 pixelů), aby si zákazník mohli zobrazit návod k informacím, který jim pomůže lépe porozumět testovacím jednotkám, včetně Jak úspěšně používat funkce vaší nabídky a porozumět scénářům, které zvýrazní jejich výhody.
  - **Název** (povinné)
  - **Adresa URL (jenom YouTube nebo Vimeo)** (povinné)
  - **Miniatura (533 x 324 px)** : soubor obrázku musí být ve formátu PNG.

Po dokončení těchto polí vyberte **Uložit** .

## <a name="publish"></a>Publikování

### <a name="submit-offer-to-preview"></a>Odeslat nabídku do verze Preview

Až dokončíte všechny požadované části nabídky, vyberte **publikovat** v pravém horním rohu portálu. Budete přesměrováni na stránku **Revize a publikování** . 

Pokud tuto nabídku publikujete poprvé, můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
    - *Nespuštěno* – znamená, že se oddíl nedotkl a je potřeba ho dokončit.
    - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je potřeba opravit, nebo vyžaduje, aby se poskytly další informace. Vraťte se prosím k oddílům a aktualizujte je.
    - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.
- Poskytněte pokyny k testování certifikačního týmu, abyste měli jistotu, že vaše aplikace je testována správně, kromě doplňkových poznámek, které jsou užitečné pro porozumění vaší aplikaci.
- Kliknutím na **Odeslat**nabídku pro publikování odešlete. Pošleme vám e-mail s oznámením, že je k dispozici verze Preview vaší nabídky, kterou si můžete prohlédnout a schválit. Vraťte se do partnerského centra a výběrem možnosti **Přejít do živé** nabídky publikujte vaši nabídku na veřejném (nebo v případě soukromé nabídky pro soukromou cílovou skupinu).

### <a name="errors-and-review-feedback"></a>Chyby a kontrola zpětné vazby

Krok **ručního ověření** v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a jejích přidružených technických prostředků (zejména šablony Azure Resource Manager). problémy se obvykle zobrazují jako odkazy na žádosti o přijetí změn (PR). Vysvětlení, jak zobrazit tyto PR a reagovat na ně, najdete v tématu [zpracování zpětné vazby na revizi](./azure-apps-review-feedback.md).

Pokud jste narazili na chyby v jednom nebo několika krocích publikování, je nutné je opravit a znovu publikovat vaši nabídku.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
