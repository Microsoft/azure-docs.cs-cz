---
title: Vytvoření nabídky aplikací Azure – Microsoft Commercial Marketplace
description: Přečtěte si postup a požadavky pro vytvoření nové nabídky aplikací Azure na portálu pro komerční tržišti v partnerském centru. Nabídku aplikací Azure můžete zobrazit nebo prodávat v Azure Marketplace nebo prostřednictvím programu Cloud Solution Provider (CSP).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: AarathiN
ms.author: aarathin
ms.date: 07/14/2020
ms.openlocfilehash: fb3a3ab5339186d8fa4e347d9d13e66940457f8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710715"
---
# <a name="create-an-azure-application-offer"></a>Vytvoření nabídky aplikací Azure

Tento článek vysvětluje kroky a požadavky pro vytvoření nové nabídky aplikace Azure na webu pro komerční tržišti. Tyto koncepty byste měli znát před vytvořením nové nabídky aplikací Azure.

Než budete moct publikovat novou nabídku aplikací Azure, [Vytvořte si v partnerském centru účet komerčního tržiště](create-account.md) a ujistěte se, že je váš účet zaregistrovaný v programu pro komerční Marketplace.

## <a name="before-you-begin"></a>Než začnete

Návrh, sestavování a testování nabídek aplikací Azure vyžaduje technické znalosti platformy Azure i technologií používaných k sestavování této nabídky. Váš technický tým by měl mít znalosti o následujících technologiích Microsoftu:

* Základní porozumění [službám Azure](https://azure.microsoft.com/services/).
* Jak [navrhovat a architektovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
* Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a [Azure](https://azure.microsoft.com/services/?filter=networking#networking).
* Provozní znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Praktické znalosti formátu [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Technická dokumentace a zdroje informací

Projděte si následující zdroje při přípravě nabídky aplikací Azure pro komerční tržiště.

* [Principy šablon Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

* Rychlá zprovoznění:

    * [Šablony Azure pro rychlé zprovoznění](https://azure.microsoft.com/documentation/templates/)
    * [Příručka k osvědčeným postupům pro šablony Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    * [Publikování definice aplikace](../../managed-applications/publish-service-catalog-app.md)
    * [Nasazení aplikace katalogu služeb](../../managed-applications/deploy-service-catalog-quickstart.md)

* Kurzy:

    * [Vytvoření definičních souborů](../../managed-applications/publish-service-catalog-app.md)
    * [Publikování aplikace Marketplace](../../managed-applications/publish-marketplace-app.md)

* Vzory

    * [Azure CLI](../../managed-applications/cli-samples.md)
    * [Azure PowerShell](../../managed-applications/powershell-samples.md)
    * [Řešení spravovaných aplikací](../../managed-applications/sample-projects.md)

[Šablony řešení pro vytváření videí a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) poskytují ucelený Úvod k typu nabídky aplikace Azure:

* Jaké typy nabídek jsou k dispozici
* Jaké technické prostředky jsou požadovány
* Jak vytvořit šablonu Azure Resource Manager
* Vývoj a testování uživatelského rozhraní aplikace
* Jak publikovat nabídku aplikace
* Proces revize aplikace

### <a name="suggested-tools"></a>Navrhované nástroje

Vyberte jedno z následujících skriptovacích prostředí, které vám pomůžou při správě aplikace Azure:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Do vývojového prostředí doporučujeme přidat následující nástroje:

* [Průzkumník služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/) s následujícími příponami:
    * Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostupné nástroje můžete zkontrolovat na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/tools/) . Také Pokud používáte aplikaci Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="types-of-azure-application-plans"></a>Typy plánů aplikací Azure

Existují dva druhy plánů aplikací Azure: šablony řešení a spravované aplikace.

* **Šablona řešení** je jedním z hlavních způsobů publikování řešení na webu Marketplace. Tento typ plánu použijte v případě, že vaše řešení vyžaduje další automatizaci nasazení a konfigurace než jeden virtuální počítač (VM). Pomocí šablony řešení můžete automatizovat poskytování více prostředků, včetně virtuálních počítačů, sítí a úložných prostředků, a zajistit tak komplexní řešení IaaS.  Další informace o vytváření šablon řešení naleznete v tématu [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

* **Spravovaná aplikace** je podobná šablonám řešení a má jeden klíčový rozdíl. Ve spravované aplikaci se prostředky nasazují do skupiny prostředků, kterou spravuje vydavatel aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. Jako vydavatel určujete náklady na průběžnou podporu řešení. Pomocí spravovaných aplikací můžete svým zákazníkům snadno sestavovat a doručovat plně spravované klíč aplikace.  Další informace o výhodách a typech spravovaných aplikací najdete v tématu [Přehled spravovaných aplikací Azure](../../managed-applications/overview.md).

## <a name="technical-requirements"></a>Technické požadavky

Všechny aplikace Azure obsahují v kořenové složce archivu aspoň dva soubory `.zip` :

* Soubor šablony Správce prostředků s názvem [mainTemplate.js](../../azure-resource-manager/resource-group-overview.md).  Tato šablona definuje prostředky, které se mají nasadit do předplatného Azure zákazníka. Příklady šablon Správce prostředků najdete v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/) nebo v příslušném úložišti [šablon pro rychlý Start Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .

* Definice uživatelského rozhraní pro prostředí pro vytváření aplikací Azure s názvem [createUiDefinition.js](../../managed-applications/create-uidefinition-overview.md).  V uživatelském rozhraní zadáváte elementy, které uživatelům umožňují zadávat hodnoty parametrů.

Všechny nové nabídky aplikací Azure musí zahrnovat [identifikátor GUID poskytovatele využívání zákaznických zákazníků Azure](../azure-partner-customer-usage-attribution.md). 

Další informace o požadavcích na publikování pro každý plán aplikace najdete v tématu [Šablona řešení nabídky požadavky na publikování](../marketplace-solution-templates.md) a [spravovaná aplikace nabídky požadavky na publikování](../marketplace-managed-apps.md).

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

>[!NOTE]
>Po publikování nabídky se v online obchodech s úpravami, které provedete v partnerském centru, nezobrazí, dokud tuto nabídku znovu nepublikujete. Nezapomeňte vždy znovu publikovat nabízenou nabídku po provedení změn.

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).

1. V nabídce vlevo vyberte **obchodní Marketplace**  >  **Přehled**.

1. Na stránce Přehled vyberte **+ Nová nabídka**  >  **aplikace Azure**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-azure-app.png)

1. Na stránce **Nová nabídka** zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

     * Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
     * Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
     * ID nabídky nelze změnit po výběru možnost **vytvořit**.

1. Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

     * Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
     * Po zvolení možnost **vytvořit**se alias nabídky nedá změnit.

1. Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

Stránka pro **nastavení nabídky** je místo, kde můžete nakonfigurovat testovací jednotku a správu vedoucích služeb pro vaši nabídku.

### <a name="test-drive"></a>Testovací jednotka

Testovací jednotka je skvělým způsobem, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si předplatné před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jednotkách](../what-is-test-drive.md).

Pokud chcete pro pevný časový úsek povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka. Nakonfigurujte prostředí testovacích jednotek v části [Technická konfigurace testovacích jednotek](#test-drive-technical-configuration) dále v tomto tématu.

Další informace najdete v tématu [Test Drive vaší nabídky na komerčním webu Marketplace](test-drive.md). Můžete si také přečíst informace o [osvědčených postupech testovacích](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) jednotek a stáhnout si [Přehled testovacích jednotek PDF](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (Ujistěte se, že je blokování automaticky otevíraných oken vypnuté).

>[!Note]
>Vzhledem k tomu, že všechny aplikace Azure jsou implementované pomocí šablony Azure Resource Manager, jediný typ testovacího disku, který je k dispozici pro aplikaci Azure, je [Azure Resource Manager testovacího disku](../azure-resource-manager-test-drive.md).

### <a name="customer-leads"></a>Potenciální zákazníci

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Další informace najdete v tématu [Přehled správy potenciálních zákazníků](./commercial-marketplace-get-customer-leads.md).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Stránka **vlastnosti** je místo, kde můžete definovat kategorie používané k seskupení vaší nabídky na webu Marketplace, verzi vaší aplikace a právní smlouvy podporující vaši nabídku.

### <a name="category"></a>Kategorie

Vyberte kategorie a podkategorie a vložte nabídku do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](../gtm-offer-listing-best-practices.md).

### <a name="legal"></a>Právní informace

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Seznam nabídek

Na této stránce můžete spravovat kopii a image pro vaši nabídku komerčního tržiště.

### <a name="marketplace-details"></a>Podrobnosti Marketplace

> [!NOTE]
> Obsah seznamu nabídek (například popis, dokumenty, snímky obrazovky a podmínky použití) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Je také přijatelné poskytnout *užitečnou adresu URL pro odkaz* k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu nabídky.

Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Marketplace (uvedené ceny jsou například jenom pro účely a nejsou určené k tomu, aby odrážely skutečné náklady):

:::image type="content" source="media/example-azure-marketplace-app.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Kategorie
3. Adresa podpory (odkaz)
4. Podmínky použití
5. Adresa zásad ochrany osobních údajů (odkaz)
6. Název nabídky
7. Shrnutí
8. Description
9. Snímky obrazovky a videa

<br>Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Portal:

:::image type="content" source="media/example-virtual-machine-container-iot-edge-saas.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Nadpis
2. Popis
3. Užitečné odkazy
4. Screenshoty

#### <a name="name"></a>Name

Název, který sem zadáte, se zákazníkům zobrazí jako název seznamu nabídek. Toto pole je předem vyplněno textem, který jste zadali pro možnost **alias nabídky** při vytváření nabídky, ale můžete tuto hodnotu změnit. Tento název může být ochranných známek (a může obsahovat symboly ochranných známek a autorských práv). Název nesmí být delší než 50 znaků a nesmí obsahovat žádné emoji.

#### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky, maximálně 100 znaků. Tento popis se dá použít ve výsledcích hledání.

#### <a name="long-summary"></a>Dlouhý souhrn

Zadejte delší popis nabídky, maximálně 256 znaků. Tento popis se dá použít ve výsledcích hledání.

#### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Rich text editor](./includes/rich-text-editor.md)]

#### <a name="search-keywords"></a>Hledaná klíčová slova

Volitelně můžete zadat až tři klíčová slova pro hledání, která zákazníkům pomůžou najít vaši nabídku na webu Marketplace. Pro dosažení nejlepších výsledků také použijte Tato klíčová slova v popisu.

#### <a name="privacy-policy-link"></a>Odkaz zásady ochrany osobních údajů

Zadejte adresu URL zásad ochrany osobních údajů vaší organizace. Zodpovídáte za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů, a za poskytnutí platných zásad ochrany osobních údajů.

### <a name="useful-links"></a>Užitečné odkazy

Kliknutím na **+ Přidat odkaz**přidejte odkazy na volitelné dodatečné online dokumenty o vašem řešení.

### <a name="contact-information"></a>Kontaktní informace

Zadejte jméno, e-mail a telefonní číslo pro **kontakt podpory**, **technický kontakt**a **program CSP**. Tyto informace se zákazníkům nezobrazují, ale budou k dispozici pro společnost Microsoft a mohou být poskytovány partnerům CSP. Některé kontakty mohou vyžadovat další informace.

### <a name="marketplace-media"></a>Média Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz způsobí odmítnutí odeslání.

[!INCLUDE [logo tips](../includes/graphics-suggestions.md)]

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="store-logos"></a>Ukládat loga

Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto k vytvoření **malého** a **středního** loga. Případně je můžete později nahradit různými obrázky.

- **Velký** (od 216 x 216 do 350 × 350 px, požadováno)
- **Střední** (90 x 90 px, volitelné)
- **Malý** (48 x 48 px, volitelné)

Tato loga se používají na různých místech v seznamu:

[!INCLUDE [logos-azure-marketplace-only](../includes/logos-azure-marketplace-only.md)]

[!INCLUDE [Logo tips](../includes/graphics-suggestions.md)]

#### <a name="screenshots"></a>Screenshoty

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Velikost každého snímku obrazovky musí být 1280 × 720 pixelů a ve formátu PNG. Každý snímek obrazovky musí obsahovat titulek.

#### <a name="videos"></a>Videa

Přidejte až pět videí, která předvádí vaši nabídku. Ty by se měly hostovat na externí službě video. Zadejte název, webovou adresu a miniaturu videa v obrazovém formátu PNG na 1280 x 720 pixelů.

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

- [Osvědčené postupy pro výpisy nabídky na webu Marketplace](../gtm-offer-listing-best-practices.md)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="preview-audience"></a>Cílová skupina Preview

Na kartě náhled vyberte **skupinu omezené verze Preview** pro ověření vaší nabídky před tím, než ji publikujete na širší cílovou skupinu Marketplace.

> [!IMPORTANT]
> Po kontrole nabídky ve verzi Preview vyberte možnost **Přejít na Live** a publikujte vaši nabídku pro veřejnou cílovou skupinu na komerčním webu Marketplace.

Vaše cílová skupina Preview se identifikuje pomocí identifikátorů GUID předplatného Azure, spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět. ID vašeho předplatného Azure můžete najít na stránce **předplatná** v Azure Portal.

Přidejte aspoň jedno ID předplatného Azure, a to buď samostatně (až 10), nebo nahráním souboru CSV (až 100). Přidáním těchto ID předplatných určíte, kdo může zobrazit náhled vaší nabídky předtím, než se publikuje živě. Je-li již vaše nabídka živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování nabídky změn nebo aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílová skupina verze Preview může mít přístup k vaší nabídce *před tím, než* se publikuje živě na tržištích. Můžou zobrazit a ověřit všechny plány, včetně těch, které budou dostupné jenom pro soukromou cílovou skupinu po úplném publikování vaší nabídky na webu Marketplace. Privátní skupina (definovaná na kartě ceny plánu **a dostupnost** ) má výhradní přístup k určitému plánu.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="technical-configuration"></a>Technická konfigurace

Tuto část dokončete jenom v případě, že vaše nabídka zahrnuje spravovanou aplikaci, která bude generovat události měření pomocí rozhraní API služby měření na webu Marketplace. Zadejte **Azure Active Directory ID tenanta** a **Azure Active Directory ID aplikace** , které bude služba používat při generování událostí měření.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="technical-configuration-offer-level"></a>Technická konfigurace (úroveň nabídky)

>[!Note]
>Technické podrobnosti na úrovni nabídky jsou volitelné.  Tyto podrobnosti musíte nakonfigurovat jenom v případě, že publikujete spravovanou aplikaci s měřením fakturace a máte službu, která se ověří pomocí tokenu zabezpečení Azure AD. Další informace najdete v tématu [strategie ověřování](./marketplace-metering-service-authentication.md) u různých možností ověřování.

Technická konfigurace definuje podrobnosti (ID tenanta a ID aplikace), které se používají k identifikaci vaší služby, což vygeneruje události měření pro spravovanou aplikaci pomocí [rozhraní API služby měření na webu Marketplace](./marketplace-metering-service-apis.md).  Zadejte identitu, kterou bude služba používat při generování událostí měření.

* **ID tenanta Azure AD** (povinné): uvnitř Azure Portal musíte [vytvořit aplikaci Azure Active Directory (AD)](../../active-directory/develop/howto-create-service-principal-portal.md) , abyste mohli ověřit, že připojení mezi našimi dvěma službami je za ověřenou komunikací. [ID tenanta](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)zjistíte tak, že přejdete na Azure Active Directory a vyberete **vlastnosti**a pak vyhledáte uvedené číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e).
* **ID aplikace Azure AD** (povinné): budete potřebovat také [ID aplikace](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)a ověřovací klíč. Tyto hodnoty získáte tak, že přejdete na Azure Active Directory a vyberete **Registrace aplikací**a pak vyhledáte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e). Ověřovací klíč najdete tak, že přejdete na **Nastavení** a vyberete **klíče**. Budete muset zadat popis a dobu trvání a pak zadat číselnou hodnotu.

>[!Note]
>ID aplikace Azure bude přidruženo k vašemu ID vydavatele a dá se znovu použít jenom v rámci tohoto účtu vydavatele.

>[!Note]
>Tato konfigurace je nutná, pokud chcete použít [událost dávkového použití](marketplace-metering-service-apis.md#metered-billing-batch-usage-event).  V případě, že chcete odeslat [událost využití](marketplace-metering-service-apis.md#metered-billing-single-usage-event)), můžete také použít [službu metadat instance](../../active-directory/managed-identities-azure-resources/overview.md) a získat [token nosiče tokenu JSON webového tokenu (Jwt)](pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

## <a name="plan-overview"></a>Přehled plánu

Tato karta vám umožní v rámci stejné nabídky poskytnout různé možnosti plánu. Tyto plány (dříve nazývané SKU) se můžou lišit v souvislosti s typem plánu (šablona řešení vs. spravovaná aplikace), finanční zhodnocení nebo cílová skupina. Pokud chcete zobrazit seznam nabídek na webu Marketplace, nakonfigurujte aspoň jeden plán.

Pro každou nabídku můžete vytvořit až 100 plánů: až 45 z nich může být privátní. Další informace o privátních plánech najdete v [části soukromé nabídky na komerčním webu Microsoft Marketplace](../private-offers.md).

Po vytvoření se zobrazí vaše názvy plánů, ID, typ plánu, dostupnost (veřejné nebo soukromé), aktuální stav publikování a všechny dostupné akce na této kartě.

**Akce** , které jsou k dispozici v **přehledu plánu** , se liší v závislosti na aktuálním stavu plánu a mohou zahrnovat:

* Pokud je stav plánu **koncept** – odstranit koncept.
* Pokud je stav plánu **Live** – zastavit prodej plánu nebo synchronizovat soukromou cílovou skupinu.

### <a name="create-new-plan"></a>Vytvořit nový plán

***ID plánu*** – pro každý plán v této nabídce Vytvořte jedinečné ID plánu. Toto ID se bude zobrazovat zákazníkům v adrese URL produktu.  Používejte jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. Pro toto ID plánu je povoleno maximálně 50 znaků. Po výběru možnosti vytvořit nelze toto ID změnit.

***Název plánu*** – zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Pro každý plán v této nabídce Vytvořte jedinečný název nabídky. Název plánu slouží k rozlišení softwarových plánů, které mohou být součástí stejné nabídky (například název nabídky: Windows Server; plány: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Nastavení plánu

Na této kartě můžete nastavit konfiguraci na nejvyšší úrovni pro typ plánu, bez ohledu na to, zda znovu používá balíčky z jiného plánu a jaké cloudy by měly být v nástroji k dispozici. Vaše odpovědi na této kartě budou mít vliv na to, která pole se zobrazí na jiných kartách pro stejný plán.

#### <a name="plan-type"></a>Typ plánu
Vyberte typ plánu pro vaši nabídku. Plán **šablony řešení** je spravován zcela zákazníkem. Plán **spravované aplikace** umožňuje vydavatelům spravovat aplikaci jménem zákazníka. Podrobnosti najdete v tématu [typy plánů aplikací Azure](#types-of-azure-application-plans).

#### <a name="re-use-technical-configuration"></a>Znovu použít technickou konfiguraci

Pokud máte více než jeden plán stejného typu a jsou mezi nimi stejné balíčky, můžete vybrat **Tento plán znovu používat balíčky z jiného plánu**.  Když vyberete tuto možnost, budete moci vybrat jeden z dalších plánů stejného typu, aby tato nabídka mohla znovu použít balíčky z.

>[!Note]
>Když znovu použijete balíčky z jiného plánu, z tohoto plánu zmizí karta s celou technickou konfigurací. Pro tento plán se použijí i podrobnosti technické konfigurace z druhého plánu, včetně všech aktualizací, které v budoucnu provedete.<br><br>Toto nastavení nelze po publikování tohoto plánu změnit.

#### <a name="azure-regions-cloud"></a>Oblasti Azure (Cloud)

Váš plán se musí zpřístupnit aspoň v jedné oblasti Azure.

Vyberte **globální možnost Azure** a zpřístupněte svůj plán zákazníkům ve všech globálních oblastech Azure, které mají integraci komerčního tržiště. Podrobnosti najdete v tématu [geografická dostupnost a podpora měny](../marketplace-geo-availability-currencies.md).

Vyberte možnost **Azure Government** pro zpřístupnění vašeho plánu v oblasti [Azure Government](../../azure-government/documentation-government-welcome.md) . Tato oblast nabízí řízený přístup pro zákazníky ze federální, státní, místní nebo samosprávnéové entity USA a také pro partnery, kteří jim mají nárok na jejich obsluhu. Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA).

Před publikováním [Azure Government](../../azure-government/documentation-government-manage-marketplace-partners.md), otestujte a ověřte svůj plán v prostředí, protože se některé koncové body můžou lišit. Pokud chcete nastavit a otestovat svůj plán, vyžádejte si zkušební účet z [Microsoft Azure Government zkušební verze](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!NOTE]
>Po publikování a dostupnosti plánu v konkrétní oblasti Azure tuto oblast nemůžete odebrat.

#### <a name="azure-government-certifications"></a>Azure Government certifikace

Tato možnost se zobrazí jenom v případě, že jste vybrali **Azure Government**.

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy. Příklad: FedRAMP, NIST 800,171 (DIB), ITAR, finanční úřad 1075, DoD – L4 a CJIS. Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které je popisují. Můžou to být buď odkazy na váš výpis v programu, nebo odkazy na popisy dodržování předpisů s nimi na vašich vlastních webech. Tyto odkazy jsou viditelné pouze pro Azure Government zákazníky.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="plan-listing"></a>Výpis plánu

Tady můžete nakonfigurovat podrobnosti o seznamu plánu. Tato karta zobrazuje konkrétní informace, které se můžou mezi plány v rámci stejné nabídky lišit.

#### <a name="plan-name"></a>Název plánu

Tato verze je předem vyplněná názvem, který jste zadali při vytváření vašeho plánu. Tento název se zobrazí na webu Marketplace jako název tohoto plánu a je omezený na 100 znaků.

#### <a name="plan-summary"></a>Souhrn plánu

Zadejte stručný souhrn plánu (ne nabídky). Tento souhrn je omezený na 100 znaků.

#### <a name="plan-description"></a>Popis plánu

Popište, co dělá tento plán softwaru jedinečný, i rozdíly mezi plány v rámci vaší nabídky. Nepopisujte nabídku, jenom plán. Popis plánu může obsahovat až 2 000 znaků.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="availability-solution-template-plans-only"></a>Dostupnost (pouze plány šablon řešení)

Plán můžete nastavit tak, aby byl viditelný pro všechny, jenom pro konkrétní zákazníky (soukromou cílovou skupinu) a jestli má být plán skrytý pro použití v jiných šablonách řešení nebo spravovaných aplikacích.

#### <a name="plan-visibility"></a>Zobrazení plánu

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny uživatele, nebo jenom pro konkrétní cílovou skupinu, kterou zvolíte. V této omezené cílové skupině můžete přiřadit členství pomocí ID předplatných Azure.

Vyberte **Toto je soukromý plán** , aby byl váš plán soukromý a viditelný jenom pro omezený počet uživatelů, kteří si zvolíte. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo si vybrat, že plán bude dostupný všem. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny uživatele. nedá se překonfigurovat jako soukromý plán.

Pokud nastavíte plán jako soukromý, zadejte **ID předplatného Azure** a jeho popis. Každé je cílová skupina, která bude mít přístup k tomuto privátnímu plánu. Přístup se přiřazuje pomocí ID předplatných Azure s možností zahrnout popis každého přiřazeného ID předplatného Azure. Přidejte až 10 ID předplatných zákazníků jednotlivě, nebo 20 000 importem souboru CSV. ID předplatných Azure jsou reprezentovaná jako identifikátory GUID a písmena musí být malá.

>[!Note]
>Soukromá nebo omezená skupina je odlišná od cílové skupiny Preview, kterou jste definovali na kartě **Preview** . Cílová skupina Preview má přístup k vaší nabídce _před tím, než_ se zveřejní na webu Marketplace. I když se soukromá skupina zvolí jenom pro konkrétní plán, cílová skupina Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

#### <a name="hide-plan"></a>Skrýt plán

Pokud má být šablona řešení určena k nasazení pouze nepřímo, pokud se odkazuje i na jinou šablonu řešení nebo spravovanou aplikaci, zaškrtněte toto políčko, pokud chcete publikovat šablonu řešení, ale nebudete ji skrývat od zákazníků, kteří ji hledají a prohlížejí přímo.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="pricing-and-availability-managed-application-plans-only"></a>Ceny a dostupnost (jenom plány spravované aplikace)

Použijte ho ke konfiguraci **trhů** , na které bude tento plán k dispozici, **ceny** za měsíc správy řešení a **plán** , aby se zobrazily informace o tom, jestli by je měli uvidí jenom konkrétní zákazníci (soukromá skupina).

Než budete pokračovat, vyberte **Uložit koncept** .

#### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro jakékoli místo na trhu, kde byste chtěli tento plán zpřístupnit. Seznam vyhledávacího pole a tlačítka pro výběr "vypoukázaného" země/oblastí, kde společnost Microsoft za vás vybírá prodej a používání daně, jsou součástí této služby.

Pokud jste už pro svůj plán nastavili ceny v USA dolary (USD) a přidáte další místo na trhu, cena za nový trh se vypočítá podle aktuálních směnných kurzů. Před publikováním si vždycky Projděte cenu za každý trh. Po uložení změn můžete pomocí odkazu exportovat ceny (XLSX) zkontrolovat ceny.

#### <a name="pricing"></a>Ceny

Zadejte pro tento plán cenu za měsíc.  Tato cena se doplní na všechny infrastruktury Azure nebo náklady na software s průběžnými platbami, které vznikají prostředky nasazené tímto řešením.

Kromě ceny za měsíc můžete také nastavit ceny za spotřebu nestandardních jednotek pomocí [měřených faktur](./azure-app-metered-billing.md).  V případě, že budete chtít, můžete nastavit cenu za měsíc na nulu a účtovat výhradně pomocí měřených faktur.

Ceny stanovené v USD (USD = USA dolar) se v místní měně všech vybraných trhů převedou na základě aktuálních směnných kurzů při uložení. Před publikováním tyto ceny ověřte, a to tak, že vyexportujete tabulku cen a zkontrolujete cenu na každém trhu. Pokud chcete nastavit vlastní ceny v individuálním trhu, upravte a importujte tabulku s cenami.

>[!Note]
>Aby bylo možné exportovat data o cenách, musíte nejdřív Uložit změny cen.

Před publikováním si pečlivě Projděte své ceny, protože existují určitá omezení, co se může po publikování plánu změnit.  

>[!Note]
>Jakmile je cena za trh v plánu publikovaná, nedá se později změnit.

#### <a name="plan-visibility"></a>Zobrazení plánu

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny uživatele, nebo jenom pro konkrétní cílovou skupinu, kterou zvolíte. V této omezené cílové skupině můžete přiřadit členství pomocí ID předplatných Azure.

Vyberte **Toto je soukromý plán** , aby byl váš plán soukromý a viditelný jenom pro omezený počet uživatelů, kteří si zvolíte. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo si vybrat, že plán bude dostupný všem. Jakmile je plán publikován jako viditelný pro všechny, musí zůstat viditelný pro všechny uživatele. nedá se překonfigurovat jako soukromý plán.

>[!Note]
>Soukromá nebo omezená skupina je odlišná od cílové skupiny Preview, kterou jste definovali na kartě **Preview** . Cílová skupina Preview má přístup k vaší nabídce _před tím, než_ se zveřejní na webu Marketplace. I když se soukromá skupina zvolí jenom pro konkrétní plán, cílová skupina Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

Pokud nastavíte plán jako soukromý, zadejte **ID předplatného Azure** a jeho popis. Každé je cílová skupina, která bude mít přístup k tomuto privátnímu plánu. Přístup se přiřazuje pomocí ID předplatných Azure s možností zahrnout popis každého přiřazeného ID předplatného Azure. Přidejte až 10 ID předplatných zákazníků jednotlivě, nebo 20 000 importem souboru CSV. ID předplatných Azure jsou reprezentovaná jako identifikátory GUID a písmena musí být malá.

>[!Note]
>Pro předplatná Azure vytvořená prostřednictvím prodejce programu Cloud Solution Provider (CSP) nejsou podporovány soukromé nabídky.

### <a name="technical-configuration"></a>Technická konfigurace

Tato karta vám umožní nahrát balíček pro nasazení, který zákazníkům umožní nasadit váš plán.

>[!Note]
>Tato karta nebude viditelná, pokud jste nakonfigurovali tento plán na opětovné použití balíčků z jiného plánu na kartě **Nastavení plánu** .

#### <a name="package-details"></a>Podrobnosti balíčku

Tato karta vám umožní upravit koncept verze vaší technické konfigurace.

**Verze** – přiřadí aktuální verzi technické konfigurace.  Zvyšte tuto verzi pokaždé, když publikujete změnu na této stránce. Verze musí být ve formátu `{integer}.{integer}.{integer}` .

**Soubor balíčku** (. zip) – Tento balíček obsahuje všechny soubory šablon potřebné pro tento plán a všechny další prostředky zabalené jako `.zip` soubor.

Všechny balíčky plánu aplikací Azure musí zahrnovat tyto dva soubory v kořenové složce `.zip` archivu:

* Soubor šablony Správce prostředků s názvem [mainTemplate.js](../../azure-resource-manager/resource-group-overview.md). Tato šablona automatizuje nasazení prostředků do předplatného Azure Customer.  Příklady šablon Správce prostředků najdete v [galerii šablon Azure pro rychlý Start](https://azure.microsoft.com/documentation/templates/) nebo v příslušném úložišti [šablon pro rychlý Start Azure Resource Manager](https://github.com/azure/azure-quickstart-templates) .
* Definice uživatelského rozhraní pro prostředí pro vytváření aplikací Azure s názvem [createUiDefinition.js](../../azure-resource-manager/managed-application-createuidefinition-overview.md).

Maximální podporované velikosti souborů jsou:

* Až 1 GB v celkové velikosti komprimovaného `.zip` archivu
* Až 1 GB pro libovolný jednotlivý nekomprimovaný soubor v `.zip` archivu  

Všechny nové nabídky aplikací Azure musí zahrnovat i identifikátor GUID [poskytovatele využívání zákaznických zákazníků Azure](../azure-partner-customer-usage-attribution.md) .

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

Plány spravované aplikace vyžadují další informace na této kartě.

#### <a name="previously-published-packages"></a>Dřív publikované balíčky

Dílčí karta **dříve publikované balíčky** umožňuje zobrazit všechny publikované verze technické konfigurace.

#### <a name="enable-just-in-time-jit-access"></a>Povolit přístup JIT (just-in-time)

Tuto možnost vyberte, pokud chcete pro tento plán povolit přístup JIT (just-in-time).  Přístup JIT vám umožní požádat o zvýšený přístup k prostředkům spravované aplikace pro účely řešení potíží nebo údržby. Vždy máte přístup k prostředkům jen pro čtení, ale v určitém časovém období můžete mít větší přístup.  Další informace najdete v tématu [povolení a vyžádat přístup za běhu pro Azure Managed Applications](../../managed-applications/request-just-in-time-access.md).  Pokud chcete vyžadovat, aby příjemci spravované aplikace udělily trvalému přístupu ke svému účtu, nechte tuto možnost nezaškrtnutou.

>[!Note]
>Nezapomeňte soubor aktualizovat tak, aby `createUiDefinition.json` podporoval tuto funkci.  

#### <a name="deployment-mode"></a>Režim nasazení

Určete, jestli se při nasazování tohoto plánu má nakonfigurovat **úplný** nebo **přírůstkový režim nasazení** : 

* V **režimu úplného**nasazení aplikace pro zákazníka dojde k odebrání prostředků ve spravované skupině prostředků, pokud prostředky nejsou definované v `mainTemplate.json` . 
* V **přírůstkovém režimu**opětovné nasazení aplikace opustí stávající prostředky beze změny.

Další informace o režimech nasazení najdete v tématu [Azure Resource Manager režimy nasazení](../../azure-resource-manager/deployment-modes.md).

#### <a name="notification-endpoint-url"></a>Adresa URL koncového bodu oznámení

Zadejte koncový bod Webhooku HTTPS pro příjem oznámení o všech operacích CRUD na instancích spravovaných aplikací této verze plánu.

#### <a name="customize-allowed-customer-actions"></a>Přizpůsobení povolených akcí zákazníků

Tuto možnost vyberte, pokud chcete určit, které akce můžou zákazníci provádět na spravovaných prostředcích, a to spolu s `*/read` akcemi, které jsou ve výchozím nastavení dostupné.

Seznamte se s dalšími akcemi, které chcete, aby mohl váš zákazník provádět, oddělený středníky.  Další informace najdete v tématu [Principy přiřazení zamítnutí pro prostředky Azure](../../role-based-access-control/deny-assignments.md). Dostupné akce najdete v tématu [Azure Resource Manager operací poskytovatele prostředků](../../role-based-access-control/resource-provider-operations.md). Například pokud chcete, aby uživatelé mohli restartovat virtuální počítače, přidejte `Microsoft.Compute/virtualMachines/restart/action` do povolených akcí.

#### <a name="global-azure--azure-government-cloud"></a>Globální cloud Azure/Azure Government

Určete, kdo má mít k této spravované aplikaci přístup pro správu v každém podporovaném cloudu. Uživatelům, skupinám nebo aplikacím, kterým chcete udělit oprávnění pro spravovanou skupinu prostředků, se identifikuje pomocí identit Azure Active Directory (AD).

**Azure Active Directory ID tenanta** – ID TENANTA Azure AD (označované taky jako ID adresáře) obsahující identity uživatelů, skupin nebo aplikací, kterým chcete udělit oprávnění. ID tenanta služby Azure AD najdete na Azure Portal v části [vlastnosti pro Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

**Autorizace** – přidejte Azure Active Directory ID objektu uživatele, skupiny nebo aplikace, kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Identifikujte uživatele podle ID objektu zabezpečení, které najdete v okně [Azure Active Directory uživatelé na Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

U každého objektu zabezpečení vyberte v seznamu (vlastník nebo přispěvatel) jednu z předdefinovaných rolí Azure AD. Vybraná role popisuje oprávnění, která bude mít objekt zabezpečení u prostředků v rámci předplatného zákazníka. Další informace najdete v tématu [Předdefinované role v Azure](../../role-based-access-control/built-in-roles.md). Další informace o řízení přístupu na základě role (RBAC) najdete v tématu [Začínáme s RBAC v Azure Portal](../../role-based-access-control/overview.md).

>[!Note]
>I když můžete přidat až 100 autorizací pro každý Cloud, je obecně snazší vytvořit skupinu uživatelů služby Active Directory a zadat její ID v ID objektu zabezpečení. To vám umožní přidat další uživatele do skupiny pro správu po nasazení plánu a snížit nutnost aktualizace plánu jenom na přidání dalších autorizací.

#### <a name="policy-settings"></a>Nastavení zásad

Použijte [zásady Azure](../../governance/policy/overview.md) na spravovanou aplikaci, abyste určili požadavky na dodržování předpisů pro nasazené řešení. Definice zásad a formátování hodnot parametrů najdete v tématu [Ukázky Azure Policy](../../governance/policy/samples/index.md). Můžete nakonfigurovat maximálně pět zásad a jenom jednu instanci jednotlivých zásad. Některé zásady vyžadují další parametry. Pro zásady auditu se vyžaduje standardní SKU. Název zásad je omezený na 50 znaků.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="co-sell-with-microsoft"></a>Společný prodej s Microsoftem

Poskytování informací na kartě spoluprodejní je zcela volitelné pro publikování vaší nabídky. Je nutné, abyste dosáhli svého stavu připraveného pro spoluprodejní a IP adresy pro vlastní prodej. Informace, které poskytnete, budou využívat prodejní týmy Microsoftu k získání dalších informací o řešení při vyhodnocování svých potřeb pro zákazníky. Není k dispozici přímo pro zákazníky.

Podrobnosti o této kartě najdete v tématu [možnost společného prodeje v partnerském centru](commercial-marketplace-co-sell.md).

## <a name="resell-through-csps"></a>Prodávejte dál prostřednictvím CSP

Rozšiřte svou nabídku tím, že ji zpřístupníte partnerům v programu [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). To umožňuje prodejcům prodávat vaši nabídku svým zákazníkům a vytvářet řešení v rámci sady.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive"></a>Testovací jednotka

Nastavte ukázku (testovací jednotku), která zákazníkům umožní vyzkoušet si vaši nabídku před nákupem. Chcete-li vytvořit ukázkové prostředí, které zákazníkům umožňuje vyzkoušet si vaši nabídku po určitou dobu, přečtěte si téma [testování nabídky na komerčním webu Marketplace](test-drive.md).

Chcete-li povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** na kartě [nastavení nabídky](#test-drive) . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

### <a name="test-drive-technical-configuration"></a>Technická konfigurace testovací verze

- **ID aplikace Azure AD** (povinné): zadejte [ID aplikace](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **Registrace aplikací**a vyhledejte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e).

#### <a name="deployment-subscription-details"></a>Podrobnosti předplatného nasazení

Pokud chcete, aby se testovací jednotka nasadila vaším jménem, vytvořte a poskytněte samostatné jedinečné předplatné Azure (nevyžadují se pro Power BI testovací jednotky).

* **ID předplatného Azure** (vyžadované pro Azure Resource Manager a Logic Apps) – zadejte ID předplatného, které vám umožní udělit přístup ke službám účtů Azure za účelem vytváření sestav a fakturace využívání prostředků. Doporučujeme zvážit [vytvoření samostatného předplatného Azure](../../billing/billing-create-subscription.md) , které se použije pro testovací jednotky, pokud ho ještě nemáte. ID vašeho předplatného Azure můžete najít tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/) a přejdete na kartu **předplatná** v nabídce na levé straně. Výběrem karty zobrazíte ID předplatného (například a83645ac-1234-5ab6-6789-1h234g764ghty).
* **ID tenanta Azure AD** (povinné) – zadejte [ID TENANTA](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v levé nabídce vyberte kartu Active Directory, vyberte možnost **vlastnosti**a vyhledejte číslo **ID adresáře** (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta vaší organizace můžete také vyhledat pomocí adresy URL vašeho názvu domény na adrese:  [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .
* **Název tenanta Azure AD** (vyžadováno pro dynamickou 365) – zadejte název Azure Active Directory (AD). Pokud chcete najít tento název, přihlaste se k [Azure Portal](https://portal.azure.com/)v pravém horním rohu bude název vašeho tenanta uvedený pod názvem vašeho účtu.
* **ID aplikace Azure AD** (požadováno) – zadejte [ID aplikace](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)Azure Active Directory (AD). Chcete-li zjistit toto ID, přihlaste se k [Azure Portal](https://portal.azure.com/), v nabídce vlevo na navigaci vyberte kartu Active Directory, vyberte možnost **Registrace aplikací**a vyhledejte uvedené číslo **ID aplikace** (například 50c464d3-4930-494c-963c-1e951d15360e).
* **Azure Active Directory tajný klíč klienta aplikace** (povinné) – zadejte svůj [tajný kód klienta](../../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)aplikace Azure AD. Tuto hodnotu najdete tak, že se přihlásíte k [Azure Portal](https://portal.azure.com/). V nabídce vlevo vyberte kartu **Azure Active Directory** , vyberte **Registrace aplikací**a pak vyberte svoji aplikaci Test Drive. V dalším kroku vyberte **certifikáty a tajné klíče**, vyberte **nový tajný klíč klienta**, zadejte popis, v části **platnost**vyberte **nikdy** a pak zvolte **Přidat**. Před ukončením této stránky nezapomeňte tuto hodnotu zkopírovat.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="marketplace-listing-optional"></a>Seznam Marketplace (volitelné)

Popište možnosti prostředí Test Drive.

<!-- The **Test Drive listings** option found under the **Test drive** tab displays the languages (and markets) where your test drive is available (currently English (United States) is the only location available). Additionally, this page displays the status of the language-specific listing and the date/time that it was added. You will need to define the test drive details (description, user manual, videos, etc.) for each language/market. -->

* **Popis** (povinné) – Popište si testovou jednotku, co se bude objevovat, co je potřeba pro uživatele experimentovat, funkce, které se mají prozkoumat, a všechny relevantní informace, které uživatelům pomůžou určit, jestli se má vaše nabídka získat. Do tohoto pole lze zadat až 3 000 znaků textu. 
* **Přístup k informacím** (vyžadovaným pro Azure Resource Manager a Logic test Disks) – Vysvětlete, co zákazník potřebuje pro přístup k této testovací jednotce a k používání této jednotky znát. Projděte si scénář pro použití vaší nabídky a přesně toho, co by měl zákazník znát pro přístup k funkcím v rámci testovacích jednotek. Do tohoto pole lze zadat až 10 000 znaků textu.
* **User Manual** (požadováno) – podrobný návod k vašemu prostředí Test Drive. Ruční příručka uživatele by měla pokrývat přesně to, co chcete zákazníkovi získat ze zkušební jednotky a sloužit jako odkaz na případné dotazy, které mohou mít. Soubor musí být ve formátu PDF a po nahrání má název (maximálně 255 znaků).
* **Videa: přidat videa** (volitelné) – videa se dají nahrát na YouTube nebo Vimeo a tady se odkazuje s odkazem a obrázkem miniatury (533 x 324 pixelů), takže zákazník může zobrazit návod k informacím, který jim pomůže lépe porozumět testovacím jednotkám, včetně toho, jak úspěšně používat funkce vaší nabídky a pochopit scénáře, které zvýrazní jejich výhody.
  * **Název** (povinné)
  * **Adresa** (jenom YouTube nebo Vimeo; povinné)
  * **Miniatura** (soubor obrázku musí být ve formátu PNG a 533 x 324 pixelů).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="publish"></a>Publikovat

Po dokončení všech požadovaných částí nabídky vyberte v pravém horním rohu portálu možnost **zkontrolovat a publikovat** .

Zkontrolujte stav dokončení pro jednotlivé části nabídky.
    - *Nezahájeno* – znamená, že se oddíl nedotkl a musí se dokončit.
    - *Nedokončeno* – znamená, že oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje, aby bylo k dispozici více informací. Vraťte se k oddílům a aktualizujte je.
    - *Complete* – znamená, že část je dokončena, byla zadána všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky v úplném stavu.

Pokud tuto nabídku publikujete poprvé, můžete k certifikačnímu týmu poskytnout pokyny pro testování, abyste měli jistotu, že se vaše aplikace správně testuje, a to spolu s případnými doplňkovými poznámkami, které jsou užitečné pro porozumění vaší aplikaci.

Vyberte **Odeslat** a odešlete svou nabídku k publikování. Pošleme vám e-mail s oznámením, že je k dispozici verze Preview vaší nabídky, kterou si můžete prohlédnout a schválit.

Vraťte se do partnerského centra a výběrem možnosti **Přejít do živé** nabídky publikujte vaši nabídku na veřejném (nebo v případě soukromé nabídky pro soukromou cílovou skupinu).

### <a name="errors-and-review-feedback"></a>Chyby a kontrola zpětné vazby

Krok **ručního ověření** v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a jejích přidružených technických prostředků (zejména šablony Azure Resource Manager). problémy se obvykle zobrazují jako odkazy na žádosti o přijetí změn (PR). Vysvětlení, jak zobrazit tyto PR a reagovat na ně, najdete v tématu [zpracování zpětné vazby na revizi](./azure-apps-review-feedback.md).

Pokud máte v jednom nebo několika krocích publikování chyby, opravte je ještě před opětovným publikováním vaší nabídky.

## <a name="next-steps"></a>Další kroky

* [Aktualizace stávající nabídky na komerčním marketplace](./update-existing-offer.md)
