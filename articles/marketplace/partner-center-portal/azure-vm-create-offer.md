---
title: Vytvoření nabídky virtuálního počítače Azure v Azure Marketplace
description: Naučte se, jak vytvořit nabídku virtuálního počítače v Azure Marketplace s požadovanou SKU.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 7ecd68023c5c48a82c07d98615807a895cdd0cae
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743109"
---
# <a name="create-an-azure-virtual-machine-offer-in-azure-marketplace"></a>Vytvoření nabídky virtuálního počítače Azure v Azure Marketplace

Tento článek popisuje, jak vytvořit a publikovat nabídku virtuálních počítačů Azure pro [Azure Marketplace](https://azuremarketplace.microsoft.com/). Řeší virtuální počítače se systémem Windows i Linux, které obsahují operační systém, virtuální pevný disk (VHD) a až 16 datových disků. Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="introduction"></a>Úvod

### <a name="publishing-benefits"></a>Výhody publikování

Publikování do Azure Marketplace má následující výhody:

- Propagace vaší společnosti pomocí značky Microsoftu
- Dosahování více než 100 000 000 Office 365 a Dynamics 365 uživatelů a více než 200 000 organizací prostřednictvím Azure Marketplace
- Získejte vysoce kvalitní zájemce z těchto tržišť
- Povýšit své služby na pole Microsoftu a prodejní týmy

### <a name="before-you-begin"></a>Před zahájením

Pokud jste to ještě neudělali, Projděte si [příručku pro publikování nabídek virtuálních počítačů](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) a materiál tohoto virtuálního počítače Azure:

- Průvodci rychlým startem
  - [Šablony Azure pro rychlé zprovoznění](https://azure.microsoft.com/resources/templates/)
  - [Šablony Azure pro rychlý Start pro GitHub](https://github.com/azure/azure-quickstart-templates)
- Kurzy
  - [Virtuální počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Ukázky
  - [Ukázky v Azure CLI pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Azure PowerShell pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Ukázky v Azure CLI pro virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell pro virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Základy technického vědomí

Návrh, sestavování a testování těchto assetů trvá čas a vyžaduje technické znalosti platformy Azure a technologií, které slouží k sestavování této nabídky.

Váš technický tým by měl pochopit tyto technologie Microsoftu:

- Základní porozumění [službám Azure](https://azure.microsoft.com/services/)
- [Návrh a architekt aplikací Azure](https://azure.microsoft.com/solutions/architecture/)
- Praktické znalosti o sítích [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a [Azure](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **virtuální počítač Azure**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-azure-vm.png)

> [!NOTE]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v prodejní místa po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

## <a name="new-offer"></a>Nová nabídka

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku Marketplace a v Azure PowerShell a Azure CLI, pokud jsou k dispozici.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1** , bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="test-drive"></a>Testovací jednotka

Testovací jednotka je skvělým způsobem, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si předplatné před nákupem. Výsledkem je zvýšená konverze a generace vysoce kvalifikovaných potenciálních zákazníků. [Přečtěte si další informace o testovacích jednotkách](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive).

Pokud chcete pro pevný časový úsek povolit testovací jednotku, zaškrtněte políčko **Povolit testovací jednotku** . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

Další zdroje testovacích jednotek:

- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketingové osvědčené postupy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

### <a name="lead-management"></a>Správa potenciálních zákazníků

Při publikování vaší nabídky na komerčním webu Marketplace s partnerským centrem ho připojte k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt. Pokud povolíte **testovací** verzi (viz předchozí oddíl), bude se vyžadovat připojení k CRM. v opačném případě je to volitelné.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující systémy CRM:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zákaznickou zapojení
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Pokud váš systém CRM není uvedený výše, použijte [koncový bod](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo HTTPS k uložení zákaznických údajů o potenciálních zákaznících. Pak exportujte data do systému CRM.

2. Připojte svoji nabídku k cíli realizace při publikování v partnerském centru.
3. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. I když náhled nabídky před tím, než začne fungovat, můžete také otestovat své zájemce tím, že se pokusíte o nasazení nabídky sami v prostředí verze Preview.
4. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="properties"></a>Vlastnosti

Tato stránka vám umožní definovat kategorie a odvětví používané k seskupení vaší nabídky na webu Marketplace, ve vaší verzi aplikace a v právních smlouvách, které podporují vaši nabídku.

### <a name="categories"></a>Kategorie

Vyberte minimálně jednu a maximálně pět kategorií. Tyto kategorie slouží k umístění vaší nabídky do příslušných oblastí hledání na webu Marketplace. V popisu nabídky vysvětlete, jak vaše nabídka tyto kategorie podporuje. Nabídky virtuálních počítačů se zobrazí pod kategorií **COMPUTE** v Azure Marketplace.

### <a name="legal"></a>Právní informace

Pro tuto nabídku musíte zadat podmínky a ujednání. Máte dvě možnosti:

- Použití vlastních podmínek a ujednání
- Použití standardní smlouvy pro komerční tržiště Microsoftu

#### <a name="use-your-own-terms-and-conditions"></a>Použití vlastních podmínek a ujednání

Pokud chcete zadat vlastní podmínky a ujednání, zadejte až 10 000 znaků textu v poli **podmínky a ujednání** . Pokud budete potřebovat delší popis, zadejte jednu webovou adresu, na které odkazuje, kde se vaše podmínky a ujednání můžou najít. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Použití standardní smlouvy pro komerční tržiště Microsoftu

Pro zjednodušení procesu nákupu pro zákazníky a omezení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní smlouvu pro komerční tržiště. Když nabízený software nabídnete pod standardním kontraktem, zákazníci ho musí přečíst a přijmout jenom jednou a nemusíte vytvářet vlastní podmínky a ujednání.

Použijte standardní smlouvu tak, že zaškrtnete políčko **použít standardní kontrakt pro komerční web Marketplace** a pak ho **přijměte** v místním okně (pokud ho chcete zobrazit, můžete ho posouvat nahoru).

![Znázorňuje stránku s přehledem v partnerském centru s vybraným tlačítkem Nová nabídka a konzultační služby.](media/use-standard-contract.png)

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční web Marketplace nemůžete použít vlastní podmínky a ujednání. Nabídněte své řešení pod standardní smlouvou **nebo** podle vašich vlastních podmínek.

Další informace o standardní smlouvě najdete v tématu Standardní smlouva o [komerčním tržišti](https://docs.microsoft.com/azure/marketplace/standard-contract)společnosti Microsoft. [Standardní kontrakt](https://go.microsoft.com/fwlink/?linkid=2041178) si můžete stáhnout jako PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken).

##### <a name="standard-contract-amendments"></a>Změny smlouvy Standard

Standardní změny smlouvy umožňují vybrat standardní smluvní smlouvy pro jednoduchost a vytvořit si pro svůj produkt nebo firmu i své výrazy. Zákazníci si musí jenom projít změny smlouvy, pokud už zkontrolovali a přijali smlouvu Microsoft Standard. K dispozici jsou dva druhy změn: univerzální a vlastní.

**Univerzální změny** – tyto změny se aplikují na standardní smlouvu pro všechny zákazníky. Zobrazují se u každého zákazníka nabídky v toku nákupu. Zákazníci musí přijmout podmínky smlouvy Standard a jejich změny, aby mohli používat vaši nabídku. Pro každou nabídku můžete zadat jednu univerzální změnu. Do tohoto pole můžete zadat neomezený počet znaků. Tyto výrazy se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo Azure Portal během procesu zjišťování a nákupu.

**Vlastní změny** – jedná se o speciální změny standardní smlouvy, které jsou zaměřené na konkrétní zákazníky prostřednictvím ID klientů Azure. Můžete zvolit tenanta, kterého chcete cílit. S vlastními podmínkami změny v nákupním toku nabídky se zobrazí pouze zákazníci z tenanta. Zákazníci musí přijmout podmínky smlouvy Standard a jejich změny, aby mohli používat vaši nabídku.

Začněte tím, že vyberete **Přidat vlastní výrazy změny (max. 10)**. Pro každou nabídku můžete zadat až deset vlastních podmínek pro změnu.

- **Vlastní výrazy změny** – do pole vlastní výrazy změny zadejte vlastní výrazy pro změnu. Můžete zadat neomezený počet znaků. Pouze zákazníci z ID tenanta, které zadáte pro tyto vlastní výrazy, se zobrazí v nákupním toku nabídky v Azure Portal.
- **ID klientů** (požadováno) – každé vlastní změně může být až 20 ID klientů. Pokud přidáte vlastní změnu, musíte zadat aspoň jedno ID tenanta, které identifikuje zákazníka v Azure. vaši zákazníci si ho můžou najít v Azure v části a pak vlastnosti. Hodnota ID adresáře je ID tenanta (například 50c464d3-4930-494c-963c-1e951d15360e). ID tenanta organizace zákazníka můžete také najít pomocí webové adresy svého názvu domény na adrese [moje Microsoft Azure a ID tenanta Office 365?](https://www.whatismytenantid.com/).
- **Popis** (volitelné) – zadejte popisný popis ID tenanta, který vám pomůže identifikovat zákazníka, kterému cílíte na změnu.

> [!NOTE]
> Tyto dva typy změn zásobníků jsou navzájem navzájem. Zákazníci, na které cílíte vlastní změny, získají při nákupu také univerzální změnu standardní smlouvy.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tato stránka umožňuje definovat nabídky, jako je název nabídky, popis, odkazy a kontakty.

> [!NOTE]
> Obsah seznamu nabídek (například popis, dokumenty, snímky obrazovky a podmínky použití) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Můžete také zadat _užitečnou webovou adresu odkazu_ k poskytnutí obsahu v jiném jazyce, než který je použit v obsahu seznamu nabídky.

### <a name="marketplace-details"></a>Podrobnosti Marketplace

#### <a name="name"></a>Name

Název, který sem zadáte, se zákazníkům zobrazí jako název vašeho seznamu nabídek. Toto pole je předem vyplněno textem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Můžou být ochranné známky (a můžete zahrnout symboly ochranné známky a autorských práv).
- Nemůže být delší než 50 znaků.
- Nejde zahrnout emoji.

#### <a name="search-results-summary"></a>Souhrn výsledků hledání

Krátký popis vaší nabídky Může to být až 100 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

#### <a name="long-summary"></a>Dlouhý souhrn

Zadejte delší popis vaší nabídky. Může to být až 256 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

#### <a name="description"></a>Popis

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-link"></a>Odkaz zásady ochrany osobních údajů

Zadejte webovou adresu (URL) do zásad ochrany osobních údajů vaší organizace. Ujistěte se, že vaše nabídka vyhovuje předpisům a zákonům o ochraně osobních údajů. Na svém webu musíte taky publikovat platné zásady ochrany osobních údajů.

### <a name="useful-links"></a>Užitečné odkazy

Poskytněte dodatečné online dokumenty o vaší nabídce. Chcete-li přidat odkaz, vyberte **+ Přidat odkaz** a potom vyplňte následující pole:

- **Název** – zákazníci uvidí jméno na stránce s podrobnostmi.
- **Odkaz (URL)** – zadejte odkaz pro zákazníky k zobrazení online dokumentu.

### <a name="customer-support-links"></a>Odkazy na zákaznickou podporu

Poskytněte web podpory, kde se zákazníci můžou připojit k vašemu týmu podpory.

- Web Azure Global support
- Web podpory Azure Government

### <a name="partner-support-contact"></a>Kontakt na podporu partnerů

Poskytněte kontaktní informace partnerům Microsoftu, kteří se použijí, když vaši zákazníci otevřou lístek podpory. Tato akce nebude uvedena na webu Marketplace.

- Name
- E-mailu
- Telefon

### <a name="engineering-contact"></a>Technický kontakt

Poskytněte kontaktní informace Microsoftu pro použití v případě problémů s vaší nabídkou, včetně problémů s certifikací. Tato akce nebude uvedena na webu Marketplace.

- Name
- E-mailu
- Telefon

### <a name="marketplace-media"></a>Média Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz způsobí odmítnutí odeslání.

>[!Note]
>Pokud máte problém s nahráváním souborů, ujistěte se, že vaše místní síť neblokuje https://upload.xboxlive.com službu používanou partnerským centrem.

#### <a name="marketplace-logos"></a>Loga Marketplace

Zadejte soubory PNG pro logo vaší nabídky v následujících čtyřech velikostech v pixelech:

- **Malé** (48 x 48)
- **Střední** (90 × 90)
- **Velký** (216 x 216)
- **Roztažitelné** (255 × 115)

Všechny čtyři loga jsou povinná a používají se na různých místech v seznamu Marketplace.

#### <a name="screenshots"></a>Screenshoty

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Velikost každého snímku obrazovky musí být 1280 × 720 pixelů a ve formátu PNG. Každý snímek obrazovky musí obsahovat titulek.

#### <a name="videos"></a>Videa

Přidejte až pět videí, která předvádí vaši nabídku. Ty by se měly hostovat na externí službě video. Zadejte název, webovou adresu a miniaturu videa v obrazovém formátu PNG na 1280 x 720 pixelů.

Další materiály k seznamům Marketplace najdete v tématu [osvědčené postupy pro výpisy nabídky na webu Marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="preview"></a>Preview

Na kartě náhled vyberte **skupinu omezené verze Preview** pro ověření vaší nabídky před tím, než ji publikujete na širší cílovou skupinu Marketplace.

> [!IMPORTANT]
> Po kontrole nabídky ve verzi Preview vyberte možnost **Přejít na Live** a publikujte vaši nabídku pro veřejnou cílovou skupinu na komerčním webu Marketplace.

Vaše cílová skupina Preview se identifikuje pomocí identifikátorů GUID předplatného Azure, spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět. ID vašeho předplatného Azure můžete najít na stránce **předplatná** v Azure Portal.

Přidejte aspoň jedno ID předplatného Azure, a to buď samostatně (až 10), nebo nahráním souboru CSV (až 100). Přidáním těchto ID předplatných určíte, kdo může zobrazit náhled vaší nabídky předtím, než se publikuje živě. Je-li již vaše nabídka živá, můžete i nadále definovat cílovou skupinu verze Preview pro testování nabídky změn nebo aktualizací vaší nabídky.

> [!NOTE]
> Cílová skupina verze Preview se liší od soukromé cílové skupiny. Cílová skupina verze Preview může mít přístup k vaší nabídce _před tím, než_ se publikuje živě na tržištích. Můžou zobrazit a ověřit všechny plány, včetně těch, které budou dostupné jenom pro soukromou cílovou skupinu po úplném publikování vaší nabídky na webu Marketplace. Privátní skupina (definovaná na kartě ceny plánu **a dostupnost** ) má výhradní přístup k určitému plánu.

Než budete pokračovat k další části, vyberte **Uložit koncept** .

## <a name="plan-overview"></a>Přehled plánu

V partnerském centru můžete v rámci stejné nabídky poskytnout různé možnosti plánu. Tyto plány se dřív odkazovaly na SKU. Nabídka vyžaduje aspoň jeden plán, který se může lišit v souvislosti s finanční zhodnocení cílovou skupinou, oblastmi Azure, funkcemi nebo imagemi virtuálních počítačů.

Po vytvoření plánů se zobrazí karta **Přehled plánu** :

- Názvy plánů
- Modely licencí
- Cílová skupina (veřejná nebo soukromá)
- Stav aktuálního publikování
- Dostupné akce

Akce, které jsou k dispozici v přehledu plánu, se liší v závislosti na aktuálním stavu plánu. Patří mezi ně:

- **Odstranit koncept** – Pokud je stav plánu koncept
- **Zastavit prodej plánu** nebo **synchronizovat soukromou cílovou skupinu** – Pokud je stav plánu publikovaný živě

### <a name="create-new-plan"></a>Vytvořit nový plán

V horní části vyberte **+ vytvořit nový plán** . Zobrazí se dialogové okno **Nový plán** .

V poli **ID plánu** Vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude pro zákazníky viditelné na webové adrese produktu. Používejte jenom malá písmena a číslice, pomlčky nebo podtržítka a maximálně 50 znaků.

> [!NOTE]
> Po zvolení možnost **vytvořit**se ID plánu nedá změnit.

Do pole **název plánu** zadejte název pro tento plán. Zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Vytvořte jedinečný název, který bude jasně ukazovat rozdíly v jednotlivých plánech. Můžete například použít **Windows Server** s plány s průběžnými **platbami**, **BYOL**, **Advanced**a **Enterprise**.

Vyberte **Vytvořit**.

### <a name="plan-setup"></a>Nastavení plánu

Nastavte na nejvyšší úrovni konfiguraci pro typ plánu bez ohledu na to, jestli znovu používá technickou konfiguraci z jiného plánu, a ve kterých oblastech Azure by měl být plán k dispozici. Vaše výběry určují, která pole se zobrazí na jiných kartách pro stejný plán.

#### <a name="re-use-technical-configuration"></a>Znovu použít technickou konfiguraci

Pokud máte více než jeden plán stejného typu a jsou mezi nimi identické balíčky, můžete vybrat tento plán, který znovu **použije technickou konfiguraci z jiného plánu**. Tato možnost umožňuje vybrat jeden z dalších plánů stejného typu pro tuto nabídku a znovu použít jeho technickou konfiguraci.

> [!NOTE]
> Když znovu použijete technickou konfiguraci z jiného plánu, z tohoto plánu zmizí karta s celou **technickou konfigurací** . Pro tento plán se použijí i podrobnosti technické konfigurace z druhého plánu, včetně všech aktualizací, které v budoucnu provedete. Toto nastavení se po publikování tohoto plánu nedá změnit.

#### <a name="azure-regions"></a>Oblast Azure

Váš plán se musí zpřístupnit aspoň v jedné oblasti Azure.

Vyberte **globální možnost Azure** a zpřístupněte svůj plán zákazníkům ve všech globálních oblastech Azure, které mají integraci komerčního tržiště. Podrobnosti najdete v tématu [geografická dostupnost a podpora měny](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Vyberte možnost **Azure Government** pro zpřístupnění vašeho plánu v oblasti [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) . Tato oblast nabízí řízený přístup pro zákazníky ze federální, státní, místní nebo samosprávnéové entity USA a také pro partnery, kteří jim mají nárok na jejich obsluhu. Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA).

Před publikováním [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), otestujte a ověřte svůj plán v prostředí, protože se některé koncové body můžou lišit. Pokud chcete nastavit a otestovat svůj plán, vyžádejte si zkušební účet z [Microsoft Azure Government zkušební verze](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po publikování a dostupnosti plánu v konkrétní oblasti Azure tuto oblast nemůžete odebrat.

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

### <a name="pricing-and-availability"></a>Ceny a dostupnost

Na této kartě nakonfigurujete následující:

- Trhy, na které bude tento plán dostupný
- Cena za hodinu
- Zda má být plán viditelný pro všechny uživatele nebo pouze pro konkrétní zákazníky (soukromá cílová skupina)

#### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň v jednom trhu. Zaškrtněte políčko pro každé místo na trhu, kde by měl být tento plán k dispozici pro nákup (uživatelé na těchto trzích si můžou nabídku nasadit do všech oblastí Azure vybraných v **[Nastavení plánu](#plan-setup)**). Tlačítko **daň k dani** zobrazuje země nebo oblasti, ve kterých společnost Microsoft prochází prodej a používání daně vaším jménem. Publikování v Číně je omezené na plány, které jsou buď **bezplatné** , nebo mají **vlastní licenci** (BYOL).

Pokud jste už pro svůj plán nastavili ceny v USA dolary (USD) a přidáte další místo na trhu, cena za nový trh se vypočítá podle aktuálních směnných kurzů. Před publikováním si vždycky Projděte cenu za každý trh. Zkontrolujte ceny pomocí odkazu **exportovat ceny (XLSX)** po uložení změn.

Když odeberete trh, zákazníci z tohoto trhu používající aktivní nasazení nebudou moci vytvářet nová nasazení nebo škálovat existující nasazení. Existující nasazení nebudou ovlivněna.

#### <a name="pricing"></a>Ceny

**Licenční model** – můžete vybrat **měsíční plán na základě využití** a nakonfigurovat ceny pro tento plán nebo využít **vlastní licenci** , aby zákazníci mohli používat tento plán s existující licencí.

V případě měsíčního plánu účtovaného podle využití použijte jednu z následujících možností cenové položky:

- **Za jádro** – zadejte cenu za jádro v USA dolarech (USD). Vypočítáme ceny za jadernou velikost a pomocí aktuálního směnného kurzu se převede na místní měny.
- **Podle základní velikosti** – poskytuje ceny podle velikosti jader v USD. Ceny převedeme na místní měny pomocí aktuálního směnného kurzu.
- **Podle trhu a základní velikosti** – poskytněte ceny za jednotlivé základní velikosti pro všechny trhy. Ceny můžete importovat z tabulky.

> [!NOTE]
> Uložte cenové změny, abyste mohli exportovat data o cenách. Po zveřejnění ceny za uvedení na trh ve vašem plánu ji nelze později změnit. Před publikováním si zajistěte, aby byly tyto ceny správné, a to tak, že vyexportujete tabulku s cenami a zkontrolujete cenu

#### <a name="free-trial"></a>Bezplatná zkušební verze

Zákazníkům můžete nabídnout měsíční nebo tříměsíční bezplatnou zkušební verzi.

#### <a name="visibility"></a>Viditelnost

Každý plán můžete navrhnout tak, aby byl viditelný všem uživatelům nebo pouze předem vybraným cílovou skupinou. Přiřaďte členství v této omezené cílové skupině pomocí ID předplatných Azure.

**Veřejné** – váš plán může zobrazit každý.

**Soukromá skupina** – umožňuje, aby byl váš plán viditelný jenom pro předvybranou cílovou skupinu. Po zveřejnění jako soukromého plánu můžete cílovou skupinu aktualizovat nebo ji změnit na veřejné. Po veřejném naplánování musí zůstat na veřejném. Nemůžete ho změnit zpátky na soukromou.

**Skupina s omezeným přístupem (ID předplatných Azure)** – přiřadí cílovou skupinu, která bude mít přístup k tomuto privátnímu plánu pomocí ID předplatných Azure Volitelně můžete zahrnout popis každého ID předplatného Azure, které jste přiřadili. Pokud importujete tabulku CSV, přidejte až 10 ID předplatných ručně nebo 20 000. ID předplatných Azure jsou reprezentovaná jako identifikátory GUID a písmena musí být malá.

> [!NOTE]
> Soukromá nebo omezená skupina je odlišná od cílové skupiny Preview, kterou jste definovali na kartě **Preview** . Cílová skupina Preview má přístup k vaší nabídce _před tím, než_ se zveřejní na webu Marketplace. I když se soukromá skupina zvolí jenom pro konkrétní plán, cílová skupina Preview může pro účely ověřování Zobrazit všechny plány (soukromě nebo ne).

#### <a name="hide-plan"></a>Skrýt plán

Pokud má být váš virtuální počítač používán pouze nepřímo v případě, že je odkazován prostřednictvím jiné šablony řešení nebo spravované aplikace, zaškrtněte toto políčko pro publikování virtuálního počítače, ale jeho skrytí od zákazníků, kteří hledají a procházejí přímo pro něj.

> [!NOTE]
> Skryté plány nepodporují odkazy ve verzi Preview.

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="technical-configuration"></a>Technická konfigurace

Poskytněte image a další technické vlastnosti přidružené k tomuto plánu. Podrobnosti najdete v tématu [Vytvoření technického prostředku virtuálního počítače Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Tato karta se nezobrazí, pokud jste nakonfigurovali tento plán pro opětovné použití balíčků z jiného plánu na kartě **Nastavení plánu** .

#### <a name="operating-system"></a>Operační systém

- **Řada operačních systémů** – vybrat z operačního systému **Windows** nebo **Linux**
- **Vydaná verze** nebo **Dodavatel** – výběr dodavatele pro Windows Release nebo Linux
- **Popisný název OS** – vyberte popisný název operačního systému. Tento název je viditelný pro zákazníky.

#### <a name="recommended-vm-sizes"></a>Doporučené velikosti virtuálních počítačů

Vyberte až šest doporučených velikostí virtuálních počítačů, které se mají zobrazit v Azure Marketplace.

#### <a name="open-ports"></a>Otevřené porty

Otevřete veřejné nebo soukromé porty na nasazeném virtuálním počítači.

#### <a name="storage-option-for-deployment"></a>Možnost úložiště pro nasazení

**Možnost nasazení disku** – vyberte, jaký typ nasazení disku můžou vaši uživatelé použít při použití virtuálního počítače. Microsoft doporučuje omezení nasazení pouze na spravované disky.

#### <a name="properties"></a>Vlastnosti

**Podpora akcelerovaných sítí** – vyberte, jestli váš virtuální počítač podporuje [akcelerované síťové služby](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Image virtuálních počítačů

Zadejte verzi disku a identifikátor URI SAS pro image virtuálních počítačů. Přidejte k každé imagi virtuálního počítače až 16 datových disků. Zadejte v daném odeslání jenom jednu verzi nového obrazu na plán. Po publikování je obrázek možné upravovat, ale můžete ho odstranit. Odstraněním verze zabráníte novým i stávajícím uživatelům v nasazení nové instance odstraněné verze.

- **Verze disku** je verze image, kterou poskytujete.
- **Identifikátor URI SAS** je umístění v Azure Storage, kam jste uložili virtuální pevný disk operačního systému.
- Image datových disků jsou taky identifikátory URI SAS VHD uložené v úložišti Azure.
- Přidá do plánu pouze jeden obrázek pro každé odeslání.

Bez ohledu na to, jaký operační systém používáte, přidejte jenom minimální počet datových disků, které řešení potřebuje. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidávat disky během nebo po nasazení.

Než budete pokračovat, vyberte **Uložit koncept** a vraťte se do **přehledu plánu**.

## <a name="resell-through-csps"></a>Prodávejte dál prostřednictvím CSP

Rozšiřte svou nabídku tím, že ji zpřístupníte partnerům v programu [Cloud Solution Providers](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Všechny plány BYOL (Přineste si vlastní licenci) se automaticky přihlásily. Můžete si vybrat, jestli se chcete vyjádřit k BYOL plánům.

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="test-drive"></a>Testovací jednotka

Nastavte ukázku (testovací jednotku), která zákazníkům umožní vyzkoušet si vaši nabídku před nákupem. Chcete-li vytvořit ukázkové prostředí, které zákazníkům umožňuje vyzkoušet si vaši nabídku po určitou dobu, přečtěte si téma [testování nabídky na komerčním webu Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Chcete-li povolit testovací jednotku, zaškrtněte políčko Povolit testovací jednotku na kartě [nastavení nabídky](#test-drive) . Chcete-li odebrat testovací verzi z nabídky, zrušte zaškrtnutí tohoto políčka.

Další zdroje testovacích jednotek:

- [Marketingové osvědčené postupy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

Než budete pokračovat, vyberte **Uložit koncept** .

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných oddílů nabídky ji můžete odeslat pro kontrolu a publikování.

V pravém horním rohu portálu vyberte **zkontrolovat a publikovat**.

Na této stránce můžete:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky.
  - **Nezahájeno** – oddíl se nespustil a je potřeba ho dokončit.
  - **Nedokončeno** – oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje zadání dalších informací. Pokyny k aktualizaci této části najdete v částech výše v tomto dokumentu.
  - **Complete** – oddíl je dokončený a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky dokončené.
- **Poznámky k certifikaci** – poskytněte pokyny pro testování certifikačního týmu, aby se zajistilo správné testování vaší aplikace. Poskytněte jakékoli doplňkové poznámky užitečné pro porozumění vaší aplikaci.

Pokud chcete odeslat nabídku pro publikování, vyberte **zkontrolovat a publikovat**.

Pošleme vám e-mail s oznámením, že je k dispozici verze Preview této nabídky ke kontrole a schválení. Pokud chcete svou nabídku publikovat ve veřejném (nebo pokud soukromá nabídka, soukromé cílové skupině), přejděte do partnerského centra, Najděte stránku s **přehledem** vaší nabídky a vyberte možnost **jít-Live**. Stav nabídky se zobrazí v horní části stránky, pokud probíhá publikování.

### <a name="errors-and-review-feedback"></a>Chyby a kontrola zpětné vazby

Krok **ručního ověření** v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a jejích přidružených technických prostředků. Pokud tento proces zjistí chyby s vaší nabídkou, obdržíte zprávu o certifikaci, která podrobně popisuje problémy. Stačí provést požadované opravy a znovu publikovat vaši nabídku.

## <a name="offer-overview"></a>Přehled nabídky

Na stránce **Přehled nabídky** se zobrazuje vizuální reprezentace kroků požadovaných k publikování této nabídky (dokončené i probíhající) a jak dlouho by měly být jednotlivé kroky dokončeny.

Tato stránka obsahuje odkazy na provádění operací s touto nabídkou na základě vámi provedeného výběru. Například:

- Pokud je nabídka nabídkou konceptu a [odstranění konceptu](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Pokud je nabídka živá, [přestanou prodávat nabídku](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) .
- Pokud je nabídka ve verzi Preview – [Přejít do provozu](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Pokud jste nedokončili registraci vydavatele, [zrušte publikování](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Příklady Marketplace

Tyto příklady ukazují, jak se nabídka zobrazuje v Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Podrobnosti nabídky Azure Marketplace

![Příklad obrazovky s podrobnostmi nabídky Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Azure Marketplace výsledků hledání

![Příklad obrazovky Azure Marketplace podrobností hledání](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Podrobnosti o plánu Azure Marketplace

![Příklad obrazovky s podrobnostmi plánu Azure Marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Podrobnosti nabídky Azure Portal

![Příklad obrazovky s podrobnostmi nabídky Azure Portal](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Azure Portal výsledků hledání

![Příklad obrazovky Azure Portal výsledků hledání](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Podrobnosti o plánu Azure Portal

![Příklad obrazovky s podrobnostmi plánu Azure Portal](media/avm-create6.png)

## <a name="next-step"></a>Další krok

- [Aktualizace stávající nabídky na komerčním webu Marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
