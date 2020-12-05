---
title: Plánování nabídky aplikací Azure pro komerční tržiště
description: Naučte se, jak naplánovat novou nabídku aplikací Azure pro výpis nebo prodej v Azure Marketplace nebo prostřednictvím programu Cloud Solution Provider (CSP) pomocí komerčního portálu pro Marketplace v partnerském centru Microsoftu.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 45bc3aaf5217c626e2593b4eda861eaace2a8be2
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620405"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Plánování nabídky aplikací Azure pro komerční tržiště

Tento článek popisuje různé možnosti a požadavky pro publikování nabídky aplikací Azure na komerční tržišti společnosti Microsoft.

## <a name="before-you-begin"></a>Než začnete

Návrh, sestavování a testování nabídek aplikací Azure vyžaduje technické znalosti platformy Azure i technologií používaných k sestavování této nabídky. Váš technický tým by měl mít znalosti o následujících technologiích Microsoftu:

- Základní porozumění [službám Azure](https://azure.microsoft.com/services/).
- Jak [navrhovat a architektovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a [Azure](https://azure.microsoft.com/services/?filter=networking#networking).
- Provozní znalosti [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
- Praktické znalosti formátu [JSON](https://www.json.org/).

### <a name="technical-documentation-and-resources"></a>Technická dokumentace a zdroje informací

Prostudujte si následující zdroje při plánování nabídky aplikací Azure pro komerční tržiště.

- [Principy šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
- Rychlá zprovoznění:
    - [Šablony Azure pro rychlé zprovoznění](https://azure.microsoft.com/documentation/templates/)
    - [Příručka k osvědčeným postupům pro šablony Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Publikování definice aplikace](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Nasazení aplikace katalogu služeb](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Kurzy:
    - [Vytvoření definičních souborů](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Vzory
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Řešení spravovaných aplikací](../azure-resource-manager/managed-applications/sample-projects.md)

[Šablony řešení pro vytváření videí a spravované aplikace pro Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) poskytují ucelený Úvod k typu nabídky aplikace Azure:

- Jaké typy nabídek jsou k dispozici
- Jaké technické prostředky jsou požadovány
- Jak vytvořit šablonu Azure Resource Manager
- Vývoj a testování uživatelského rozhraní aplikace
- Jak publikovat nabídku aplikace
- Proces revize aplikace

### <a name="suggested-tools"></a>Navrhované nástroje

Vyberte jedno z následujících skriptovacích prostředí, které vám pomůžou při správě aplikace Azure:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

Do vývojového prostředí doporučujeme přidat následující nástroje:

- [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) s následujícími příponami:
    - Rozšíření: [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Přípona: [Beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Přípona: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Dostupné nástroje můžete zkontrolovat na stránce [Azure vývojářské nástroje](https://azure.microsoft.com/tools/) . Pokud používáte aplikaci Visual Studio, přečtěte si [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="listing-options"></a>Možnosti zápisu

Po publikování vaší nabídky se možnosti výpisu vaší nabídky zobrazí jako tlačítko v levém horním rohu stránky se seznamem vaší nabídky. Například následující snímek obrazovky ukazuje stránku se seznamem nabídek v Azure Marketplace pomocí tlačítka _získat nyní_ . Pokud jste se rozhodli nabízet testovací verzi, zobrazí se také tlačítko _testovací jednotka_ .

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Ukazuje stránku se seznamem na Azure Marketplace.":::

## <a name="test-drive"></a>Testovací jednotka

Můžete zvolit povolení testovací jednotky pro nabídku aplikace Azure, která zákazníkům umožní vyzkoušet si vaši nabídku před nákupem. Další informace o testovacích jednotkách najdete v tématu [co je testovací jednotka?](what-is-test-drive.md). Informace o konfiguraci různých druhů testovacích jednotek najdete v tématu [Technická konfigurace testovacích](test-drive-technical-configuration.md)jednotek.

Můžete si také přečíst informace o [osvědčených postupech testovacích](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) jednotek a stáhnout si [Přehled testovacích jednotek](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (Ujistěte se, že je blokování automaticky otevíraných oken vypnuté).

> [!NOTE]
> Informace, které by si měl uživatel všimnout, i když skimmingBecause všechny aplikace Azure, které jsou implementované pomocí šablony Azure Resource Manager, jediný typ testovacího disku, který je k dispozici pro [aplikaci Azure, je testovací jednotka Azure Resource Manager založená na](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Potenciální zákazníci

Abyste mohli shromažďovat informace o zákaznících, musíte vaši nabídku připojit k systému správy vztahů se zákazníky (CRM). Zákazník bude požádán o oprávnění ke sdílení svých informací. Tyto podrobnosti o zákaznících spolu s názvem nabídky, ID a online obchodem, kde si vaši nabídku našli, budou odeslány do systému CRM, který jste nakonfigurovali. Komerční tržiště podporuje nejrůznější systémy CRM, společně s možností použití tabulky Azure nebo konfigurace koncového bodu HTTPS pomocí Power automatizuje.

Připojení k CRM můžete kdykoli přidat nebo změnit během vytváření nabídky nebo po ní. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Kategorie a podkategorie

Můžete zvolit aspoň jednu a až dvě kategorie pro seskupení vaší nabídky do odpovídajících oblastí hledání v obchodě na webu Marketplace. Pro každou primární a sekundární kategorii můžete zvolit až dvě podkategorie. Úplný seznam kategorií a podkategorií najdete v tématu [nabídky Doporučené postupy](gtm-offer-listing-best-practices.md#categories).

## <a name="legal-contracts"></a>Právní smlouvy

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní kontrakt, který můžete použít pro vaše nabídky na komerčním webu Marketplace. Když nabízený software obdržíte na základě standardního kontraktu, zákazníci ho musí přečíst a přijmout jenom jednou a vy nemusíte vytvářet vlastní podmínky.

Pokud se rozhodnete použít standardní smlouvu, máte možnost přidat do standardní smlouvy univerzální změny a až 10 vlastních změn. Místo standardní smlouvy můžete použít také vlastní podmínky a ujednání. Tyto podrobnosti budete spravovat na stránce **vlastnosti** . Podrobné informace najdete v tématu [standardní smlouva pro komerční tržiště Microsoftu](standard-contract.md).

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční web Marketplace nemůžete použít vlastní podmínky a ujednání. Jedná se o scénář "nebo". Vaše řešení si buď nabídnete v rámci standardní smlouvy, nebo podle vašich vlastních podmínek. Pokud chcete upravit podmínek standardní smlouvy, můžete to udělat prostřednictvím standardních změn smlouvy.

## <a name="offer-listing-details"></a>Podrobnosti o seznamu nabídek

Když vytvoříte novou nabídku aplikace Azure v partnerském centru, zadáte text, obrázky, volitelná videa a další podrobnosti na stránce seznam nabídek. Jedná se o informace, které se zákazníkům zobrazí při zjištění seznamu nabídek v Azure Marketplace, jak je znázorněno v následujícím příkladu.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Logo
2. Kategorie
3. Adresa podpory (odkaz)
4. Podmínky použití
5. Adresa zásad ochrany osobních údajů (odkaz)
6. Název nabídky
7. Souhrn
8. Popis
9. Snímky obrazovky a videa

Následující snímek obrazovky ukazuje, jak se v Azure Portal zobrazí informace o nabídce:

[![Ukazuje, jak se tato nabídka zobrazuje v Azure Portal.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Popisy volání

1. Nadpis
2. Popis
3. Užitečné odkazy
4. Screenshoty

> [!NOTE]
> Pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině), není nutné, aby byla v anglickém obsahu.

Abyste mohli snadněji vytvořit nabídku, připravte si některé z těchto položek předem. Pokud není uvedeno jinak, jsou vyžadovány následující položky.

- **Název**: Tento název se zobrazí jako název vaší nabídky na komerčním webu Marketplace. Název může být ve vaší společnosti. Nemůže obsahovat Emoji (Pokud se nejedná o symboly ochranné známky a copyrightu) a musí být omezený na 50 znaků.
- **Shrnutí výsledků hledání**: popište účel nebo funkci nabídky jako jednu větu, v prostém textu bez konců řádků, maximálně 100 znaků. Tento souhrn se používá ve výsledcích hledání na komerčních tržištích.
- **Krátký popis**: zadejte až 256 znaků prostého textu. Tento souhrn se zobrazí na stránce podrobností vaší nabídky.
- **Popis**: Tento popis se zobrazí v seznamech Azure Marketplace. Vezměte v úvahu zahrnutí hodnot – klíčové výhody, zamýšlená uživatelská základna, jakákoli kategorie nebo Oborová přidružení, možnosti nákupu v aplikaci, potřeby zákazníka nebo bolesti toho, že adresa nabídky obsahuje adresy, veškeré požadované informace a odkaz na Další informace.

    V tomto textovém poli jsou ovládací prvky editoru formátovaného textu, které můžete použít k lepšímu poutavení popisu. K formátování popisu můžete použít také značky HTML. Do tohoto pole můžete zadat až 3 000 znaků textu, včetně značek HTML a mezer. Další tipy najdete v tématu [Vytvoření skvělého popisu aplikace](/windows/uwp/publish/write-a-great-app-description) a [značek HTML podporovaných v popisech nabídky komerčního tržiště](supported-html-tags.md).

- **Hledaná klíčová slova** (volitelné): Poskytněte až tři klíčová slova pro hledání, která můžou zákazníci použít k vyhledání vaší nabídky v online obchodu. Pro dosažení nejlepších výsledků také použijte Tato klíčová slova v popisu. Nemusíte zahrnovat **název** a **Popis** nabídky. Tento text je automaticky zahrnutý do hledání.
- **Odkaz zásady ochrany osobních údajů**: adresa URL zásad ochrany osobních údajů vaší společnosti. Je nutné zadat platné zásady ochrany osobních údajů a zodpovídá za to, že vaše aplikace bude v souladu se zákony a předpisy ochrany osobních údajů.
- **Užitečné odkazy** (volitelné): můžete poskytnout odkazy na různé prostředky pro uživatele vaší nabídky. Například fóra, nejčastější dotazy a poznámky k verzi.
- **Kontaktní informace**: musíte určit následující kontakty z vaší organizace:
  - **Kontakt na podporu**: Zadejte jméno, telefon a e-mail pro partnery Microsoftu, kteří se použijí, když vaši zákazníci otevřou lístky. Musíte taky zahrnout adresu URL vašeho webu podpory.
  - **Technický kontakt**: Zadejte jméno, telefon a e-mailovou adresu Microsoftu pro použití přímo v případě problémů s vaší nabídkou. Tyto kontaktní údaje nejsou uvedené na komerčním webu Marketplace.
  - **Kontakt programu CSP** (volitelné): Zadejte jméno, telefon a e-mailovou adresu, pokud se přihlásíte k programu Cloud Solution Provider (CSP), aby vás mohli kontaktovat s případnými dotazy. Do svých marketingových materiálů můžete také přidat adresu URL.
- **Médium – loga**: Zadejte soubor PNG pro logo **velké** velikosti. Partnerské centrum použije toto k vytvoření **malého** a **středního** loga. Případně je můžete později nahradit různými obrázky.
  - Velký (od 216 x 216 do 350 × 350 px, požadováno)
  - Střední (90 x 90 px, volitelné)
  - Malý (48 x 48 px, volitelné)

  Tato loga se používají na různých místech v online obchodech:
  - Malé logo se zobrazí ve výsledcích hledání Azure Marketplace.
  - Logo střední se zobrazí při vytváření nového prostředku v Microsoft Azure.
  - Velké logo se zobrazí na stránce se seznamem nabídek v Azure Marketplace.

  Postupujte podle těchto pokynů pro vaše loga:

  - Design Azure má jednoduchou paletu barev. Omezte počet primárních a sekundárních barev ve vašem logu.
  - Barvami motivů na portálu jsou bílá a černá. Nepoužívejte tyto barvy jako barvu pozadí pro vaše logo. Použijte barvu, která vaše logo na portálu zvýrazní. Doporučujeme použít jednoduché primární barvy.
  - Pokud použijete průhledné pozadí, ujistěte se, že text ani logo nejsou bílé, černé nebo modré.
  - Vzhled a chování loga by mělo být ploché a vyhnout se přechodům na jeho logu nebo na pozadí. Na logo neumisťujte žádný text, a to ani název vaší společnosti nebo název značky. Rozmazaný obraz způsobí odmítnutí odeslání.
  - Zajistěte, aby logo nebylo roztažené.

- **Média – snímky obrazovky** (volitelné): Doporučujeme přidat snímky obrazovky, které ukazují, jak vaše nabídka funguje. Můžete přidat až pět obrazovek s následujícími požadavky, které ukazují, jak vaše nabídka funguje:
  - 1280 x 720 pixelů
  - soubor. png
  - Musí obsahovat titulek
- **Média – videa** (volitelné): můžete přidat až pět videí s následujícími požadavky, které demonstrují vaši nabídku:
  - Název
  - Adresa URL: musí být hostována pouze na YouTube nebo Vimeo.
  - Miniatura: soubor 1280 x 720. png

> [!NOTE]
> Vaše nabídka musí splňovat obecné [zásady certifikace komerčního tržiště](/legal/marketplace/certification-policies#100-general.md) , které se mají publikovat na komerčním webu Marketplace.

## <a name="preview-audience"></a>Cílová skupina Preview

Cílová skupina Preview má přístup k vaší nabídce před publikováním živě v online obchodech, aby bylo možné otestovat kompletní funkce před publikováním v reálném čase.

> [!NOTE]
> Cílová skupina verze Preview se liší od privátního plánu. Soukromý plán je ten, který zpřístupníte jenom pro konkrétní cílovou skupinu, kterou zvolíte. To vám umožní vyjednat vlastní plán s konkrétními zákazníky.

Nadefinujete cílovou skupinu verze Preview pomocí ID předplatného Azure spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět.

## <a name="technical-configuration"></a>Technická konfigurace

U spravovaných aplikací, které generují události měření pomocí [rozhraní API služby měření na webu Marketplace](partner-center-portal/marketplace-metering-service-apis.md), musíte zadat identitu, kterou bude služba používat při generování událostí měření.

Tato konfigurace je nutná, pokud chcete použít [událost dávkového použití](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event). V případě, že chcete odeslat [událost použití](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event), můžete také použít [službu metadat instance](../active-directory/managed-identities-azure-resources/overview.md) a získat [token nosiče tokenu JSON webového tokenu (Jwt)](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token).

- **Azure Active Directory ID tenanta** (povinné): uvnitř Azure Portal musíte [vytvořit aplikaci Azure Active Directory (AD)](../active-directory/develop/howto-create-service-principal-portal.md) , abychom mohli ověřit připojení mezi našimi dvěma službami za ověřenou komunikací. Pokud chcete najít [ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) pro vaši aplikaci Azure Active Directory (Azure AD), klikněte na okno [Registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) ve vašem Azure Active Directory. Ve sloupci **Zobrazovaný název** vyberte aplikaci. Pak vyhledejte **vlastnosti** a pak pro **ID adresáře (tenant)** (například `50c464d3-4930-494c-963c-1e951d15360e` ).
- **ID aplikace Azure Active Directory** (povinné): budete také potřebovat [ID aplikace](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) a ověřovací klíč. Pokud chcete najít ID aplikace, v Azure Active Directory v okně Registrace aplikací klikněte na okno [App registrations](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) . Ve sloupci **Zobrazovaný název** vyberte aplikaci a pak vyhledejte **ID aplikace (třeba klienta)** (například `50c464d3-4930-494c-963c-1e951d15360e` ). Ověřovací klíč najdete tak, že přejdete na **Nastavení** a vyberete **klíče**. Budete muset zadat popis a dobu trvání a pak zadat číselnou hodnotu.

> [!NOTE]
> ID aplikace Azure bude přidruženo k vašemu ID vydavatele a bude možné ho znovu použít jenom v rámci tohoto účtu vydavatele.

## <a name="additional-sales-opportunities"></a>Další prodejní příležitosti

Můžete si vybrat, jestli se chcete zúčastnit marketingových a prodejních kanálů podporovaných společností Microsoft. Při vytváření vaší nabídky v partnerském centru se zobrazí dvě karty na konec procesu:

- **Prodávejte dál prostřednictvím CSP**: tuto možnost použijte, pokud chcete, aby partneři poskytovatelé řešení Microsoft Cloud mohli své řešení znovu prodávat jako součást zahrnuté nabídky. Další informace najdete v tématu [program Cloud Solution Provider](./cloud-solution-providers.md) .
- **Spoluprodejní s Microsoftem**: Tato možnost umožňuje prodejcům Microsoftu zvážit, jaké řešení se svým zákazníkům nabízí při vyhodnocování vašich potřeb zákazníků. Podrobné informace o tom, jak připravit vaši nabídku k vyhodnocení, najdete v tématu [možnost společného prodeje na komerčním webu Marketplace](commercial-marketplace-co-sell.md). Podrobnosti o požadavcích na společný prodej IP adres najdete v tématu [požadavky na stav společného prodeje](https://aka.ms/CertificationPolicies#3000-requirements-for-co-sell-status). Další informace o marketingu vaší nabídky prostřednictvím kanálů partnerů Microsoft CSP najdete v tématu [poskytovatelé Cloud Solution Provider](cloud-solution-providers.md).

Další informace najdete v tématu věnovaném [růstu cloudového podnikání pomocí Azure Marketplace](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Plány

Nabídky aplikací Azure vyžadují aspoň jeden plán. Plán definuje rozsah a omezení řešení a související ceny, pokud jsou k dispozici. Můžete vytvořit několik plánů pro vaši nabídku, abyste svým zákazníkům poskytli různé technické a cenové možnosti.

Obecné pokyny k plánům, včetně cenových modelů a soukromých plánů, najdete v tématu [plány a ceny pro komerční nabídky na webu Marketplace](plans-pricing.md). Následující části popisují další informace, které jsou specifické pro plány aplikací Azure.

### <a name="types-of-plans"></a>Typy plánů

Existují dva druhy plánů aplikací Azure: _Šablona řešení_ a _spravovaná aplikace_. Oba typy plánů podporují automatizaci nasazení a konfigurace řešení mimo jeden virtuální počítač (VM). Proces poskytování více prostředků, včetně virtuálních počítačů, sítí a prostředků úložiště, můžete automatizovat, abyste mohli poskytovat složitá řešení, například IaaS řešení. Oba typy plánů můžou využívat mnoho různých druhů prostředků Azure, mimo jiné jenom virtuální počítače.

- Plány **šablon řešení** jsou jedním z hlavních způsobů publikování řešení na komerčním webu Marketplace. Plány šablon řešení se nedají v komerčním tržišti použít, ale můžou se používat k nasazení placených nabídek virtuálních počítačů, které se účtují prostřednictvím komerčního tržiště. Typ plánu šablony řešení použijte, když zákazník bude spravovat řešení a transakce se účtují prostřednictvím jiného plánu. Další informace o vytváření šablon řešení najdete v tématu [co je Azure Resource Manager?](../azure-resource-manager/management/overview.md)
- Plány **spravovaných aplikací** umožňují snadno sestavovat a doručovat plně spravované aplikace klíč pro vaše zákazníky. Mají stejné možnosti jako plány šablon řešení s některými klíčovými rozdíly:
    - Prostředky se nasazují do skupiny prostředků a spravují se vydavatelem aplikace. Skupina prostředků se nachází v rámci předplatného zákazníka, ale má k ní přístup určitá identita v rámci tenanta vydavatele. 
    - Jako vydavatel zadáte náklady na průběžnou podporu řešení a transakce se podporují prostřednictvím komerčního tržiště.
 
    Typ plánu spravované aplikace použijte, když vy nebo váš zákazník požadujete, aby bylo řešení spravované partnerem, nebo když nasadíte řešení založené na předplatném. Další informace o výhodách a typech spravovaných aplikací najdete v tématu [Přehled spravovaných aplikací Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Další kroky

- Chcete-li naplánovat šablonu řešení, přečtěte si téma [plánování šablony řešení pro nabídku aplikace Azure](plan-azure-app-solution-template.md).
- Pokud chcete naplánovat spravovanou aplikaci Azure, přečtěte si téma [plánování spravované aplikace Azure pro nabídku aplikací Azure](plan-azure-app-managed-app.md).