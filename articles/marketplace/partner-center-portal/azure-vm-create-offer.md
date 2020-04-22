---
title: Vytvoření nabídky virtuálních strojů Azure – Azure Marketplace
description: Naučte se, jak vytvořit nabídku virtuálních strojů na komerčním trhu.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d5626f00e9627338349d9b579bcf26256148b551
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731267"
---
# <a name="create-an-azure-virtual-machine-offer"></a>Vytvoření nabídky virtuálního počítače Azure

> [!IMPORTANT]
> Přesouváme správu nabídek virtuálních virtuálních zařízení Azure z portálu partnerů cloudu do Centra partnerů. Dokud nebudou vaše nabídky migrovány, pokračujte podle pokynů v [nabídce Vytvořit virtuální počítač](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-offer) na portálu cloudových partnerů a spravujte své nabídky.

Tento článek popisuje, jak vytvořit a publikovat nabídku virtuálního počítače Azure na [Azure Marketplace](https://azuremarketplace.microsoft.com/). Řeší virtuální počítače založené na systému Windows i Linux, které obsahují operační systém, virtuální pevný disk (VHD) a až 16 datových disků.

## <a name="introduction"></a>Úvod

### <a name="publishing-benefits"></a>Publikování výhod

Publikování na Azure Marketplace má následující výhody:

- Propagace vaší společnosti pomocí značky Microsoft
- Oslovte přes 100 milionů uživatelů Office 365 a Dynamics 365 a více než 200 000 organizací prostřednictvím Azure Marketplace
- Získejte vysoce kvalitní potenciální zákazníky z těchto tržišť
- Získejte své služby propagované týmy microsoftu a telesales

### <a name="before-you-begin"></a>Než začnete

Pokud jste tak ještě neučinili, přečtěte si [průvodce publikováním nabídek virtuálních strojů](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines) a tento materiál virtuálního počítače Azure:

- Úvodní příručky
  - [Šablony Azure Quickstart](https://azure.microsoft.com/resources/templates/)
  - [Šablony githubazure rychlých startů](https://github.com/azure/azure-quickstart-templates)
- Kurzy
  - [Virtuální počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
  - [Virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)
- Ukázky
  - [Ukázky azure cli pro virtuální počítače s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/cli-samples)
  - [Virtuální počítače Azure PowerShell pro Linux](https://docs.microsoft.com/azure/virtual-machines/linux/powershell-samples)
  - [Ukázky nastavení příkazového příkazu Azure pro virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/windows/cli-samples)
  - [Azure PowerShell pro virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

### <a name="fundamentals-in-technical-knowledge"></a>Základy technických znalostí

Navrhování, vytváření a testování těchto prostředků vyžaduje čas a vyžaduje technické znalosti platformy Azure i technologií používaných k sestavení nabídky.

Technický tým by měl pochopit následující technologie společnosti Microsoft:

- Základní znalosti [služeb Azure](https://azure.microsoft.com/services/)
- Jak [navrhovat a navrhovat aplikace Azure](https://azure.microsoft.com/solutions/architecture/)
- Pracovní znalosti virtuálních [počítačů Azure](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)a Azure [Networking](https://azure.microsoft.com/services/?filter=networking#networking)

## <a name="create-an-azure-virtual-machine-offer"></a>Vytvoření nabídky virtuálního počítače Azure

Než budete moci vytvořit nabídku virtuálních automatů Azure, musíte mít komerční účet marketplace v Centru partnerů. Pokud jste si ho ještě nevytvořili, přečtěte si informace [o vytvoření komerčního účtu marketplace v Centru partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

1. Přihlaste se do [Centra partnerů](https://partner.microsoft.com/dashboard/home)a v horní nabídce vyberte **dashboard**.
2. V levém panelu nav vyberte **Komerční tržiště**a pak **Přehled**.
3. Na stránce **Přehled** vyberte **+ Nová nabídka**a potom virtuální počítač **Azure**. Zobrazí se dialogové okno **Nová nabídka.**

    ![Ilustruje stránku Přehled v Centru partnerů s vybraným tlačítkem Nová nabídka a nabídkou virtuálního počítače Azure.](media/avm-create7.png)

## <a name="offer-id-and-alias"></a>ID nabídky a alias

Zadejte **ID nabídky**. Jedná se o jedinečný identifikátor pro každou nabídku ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku marketplace a v Azure PowerShell a Azure CLI, pokud je to možné.
- Použijte při tom jenom malá písmena a číslice. Může obsahovat pomlčky a podtržítka, ale žádné mezery a je omezena na 50 znaků. Pokud zde například zadáte **test-offer-1,** bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- ID nabídky nelze změnit po výběru **možnosti Vytvořit**.

Zadejte **alias Nabídka**. Toto je název používaný pro nabídku v Partnerském centru. Tento název se na trhu nepoužívá a liší se od názvu nabídky a dalších hodnot zobrazených zákazníkům.

Vyberte **Vytvořit,** chcete-li nabídku vygenerovat a pokračovat.

## <a name="offer-setup"></a>Nastavení nabídky

### <a name="test-drive"></a>Zkušební jízda

Nastavte ukázku (testovací jízdu), která zákazníkům umožní vyzkoušet si vaši nabídku před zakoupením. Chcete-li vytvořit demonstrační prostředí, které zákazníkům umožní vyzkoušet si vaši nabídku na pevně stanovenou dobu, [přečtěte si část Test Drive your offer in the commercial marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Chcete-li povolit testovací jednotku, zaškrtněte **políčko Povolit testovací jednotku.** Chcete-li zkušební jízdu z nabídky odebrat, zrušte zaškrtnutí tohoto políčka.

Další prostředky testovací jednotky:

- [Osvědčené technické postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Marketingové osvědčené postupy](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Přehled testovacích jednotek](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF (ujistěte se, že vaše blokování automaticky otevíraných míst je vypnuté).

### <a name="lead-management"></a>Správa potenciálních zákazníků

Při publikování nabídky na komerčním trhu pomocí Partnerského centra ji připojte k systému řízení vztahů se zákazníky (CRM). To vám umožní získat kontaktní informace zákazníků, jakmile někdo projeví zájem o váš produkt nebo jej používá. Připojení k CRM je nutné, pokud povolíte **test drive** (viz předchozí část), jinak je to volitelné.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující crm systémy:
    - [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zapojení zákazníků
    - [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
    - [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

    > [!NOTE]
    > Pokud váš CRM systém není uveden výše, použijte [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) k ukládání dat potenciálních zákazníků. Potom exportujte data do systému CRM.

2. Při publikování v Partnerském centru propojte svou nabídku s cílem zájemce.
3. Zkontrolujte, zda je správně nakonfigurováno připojení k cíli zájemce. Po publikování v Centru partnerů ověříme připojení a pošleme vám testovacího zájemce. Zatímco zobrazíte náhled nabídky před tím, než se začne prohlížet, můžete také otestovat připojení zájemců tím, že se pokusíte nasadit nabídku sami v prostředí náhledu.
4. Ujistěte se, že připojení k cíli zájemce zůstane aktualizováno, abyste nepřišli o žádné zájemce.

Před pokračováním vyberte **Uložit pracovní verzi.**

## <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie a odvětví používaná k seskupení nabídky na trhu, verzi aplikace a právní smlouvy, které vaši nabídku podporují.

### <a name="categories"></a>Kategorie

Vyberte minimálně jednu a maximálně pět kategorií. Tyto kategorie se používají k umístění vaší nabídky do příslušných oblastí vyhledávání na trhu. V popisu nabídky vysvětlete, jak vaše nabídka podporuje tyto kategorie. Nabídky virtuálních automatů se zobrazují v kategorii **Výpočetní výkon** na Azure Marketplace.

### <a name="legal"></a>Právní informace

Musíte poskytnout podmínky pro nabídku. Máte dvě možnosti:

- Používejte své vlastní smluvní podmínky
- Použití standardní smlouvy pro komerční tržiště společnosti Microsoft

#### <a name="use-your-own-terms-and-conditions"></a>Používejte své vlastní smluvní podmínky

Chcete-li zadat vlastní smluvní podmínky, zadejte do pole **Podmínky** až 10 000 znaků textu. Pokud požadujete delší popis, zadejte jednu webovou adresu, která ukazuje na místo, kde lze najít vaše smluvní podmínky. Zobrazí se zákazníkům jako aktivní odkaz.

Zákazníci musí tyto podmínky přijmout, než budou moci vaši nabídku vyzkoušet.

#### <a name="use-the-standard-contract-for-the-microsoft-commercial-marketplace"></a>Použití standardní smlouvy pro komerční tržiště společnosti Microsoft

Pro zjednodušení procesu zásobování zákazníků a snížení právní složitosti pro dodavatele softwaru nabízí společnost Microsoft standardní smlouvu pro komerční trh. Když nabízíte software podle standardní smlouvy, zákazníci si jej musí přečíst a přijmout pouze jednou a nemusíte vytvářet vlastní smluvní podmínky.

Standardní smlouva můžete použít zaškrtnutím políčka **Použít standardní smlouvu pro komerční tržiště společnosti Microsoft** a potom **přijmout** v rozbalovacím okně (možná budete muset posunout nahoru, abyste ho viděli).

![Ilustruje stránku Přehled v Centru partnerů s vybraným tlačítkem Nová nabídka a nabídkou konzultační služby.](media/use-standard-contract.png)

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční tržiště nemůžete použít vlastní smluvní podmínky. Buď nabídněte své řešení podle standardní **smlouvy, nebo** podle svých vlastních podmínek.

Další informace o standardní smlouvě naleznete v tématu Standardní smlouva pro [komerční tržiště](https://docs.microsoft.com/azure/marketplace/standard-contract)společnosti Microsoft . [Standardní smlouvu](https://go.microsoft.com/fwlink/?linkid=2041178) si můžete stáhnout ve formátu PDF (zkontrolujte, zda je blokování automaticky otevíraných míst vypnuté).

##### <a name="standard-contract-amendments"></a>Standardní změny smlouvy

Standardní změny smlouvy umožňují vybrat standardní smluvní podmínky pro jednoduchost a vytvořit podmínky pro váš produkt nebo firmu. Zákazníci potřebují zkontrolovat změny smlouvy pouze v případě, že již přezkoumali a přijali standardní smlouvu společnosti Microsoft. K dispozici jsou dva druhy pozměňovacích návrhů: univerzální a zvyk.

**Univerzální změny** – Ty jsou uplatňovány univerzálně na standardní smlouvu pro všechny zákazníky. Jsou zobrazeny každému zákazníkovi nabídky v nákupním toku. Zákazníci musí před použitím vaší nabídky přijmout podmínky standardní smlouvy a dodatků. Můžete poskytnout jeden univerzální dodatek pro každou nabídku. Do tohoto pole můžete zadat neomezený počet znaků. Tyto podmínky se zobrazí zákazníkům v AppSource, Azure Marketplace a/nebo na webu Azure Portal během toku zjišťování a nákupu.

**Vlastní změny** – Jedná se o speciální změny standardní smlouvy, které jsou cílené na konkrétní zákazníky prostřednictvím ID klienta Azure. Můžete si vybrat klienta, na kterého chcete cílit. Pouze zákazníci z nájemce budou prezentovány s vlastní změny podmínky v nabídce nákupní tok. Zákazníci musí před použitím vaší nabídky přijmout podmínky standardní smlouvy a dodatků.

Začněte výběrem možnosti **Přidat vlastní podmínky změny (Max 10)**. Můžete poskytnout až deset vlastních podmínek změny na nabídku.

- **Podmínky vlastního dodatku** – Do kolonky pro vlastní změnu zadejte vlastní podmínky změny. Můžete zadat neomezený počet znaků. V nákupním toku nabídky na webu Azure Portal se zobrazí jenom zákazníkům z ID klienta, které zadáte pro tyto vlastní podmínky.
- **ID klienta** (povinné) – každý vlastní změna může být zaměřena až na 20 ID klienta. Pokud přidáte vlastní změnu, musíte zadat alespoň jedno ID klienta, které identifikuje vašeho zákazníka v Azure. zákazník pro vás může najít v Azure pod a pak vlastnosti. Hodnota ID adresáře je ID klienta (například 50c464d3-4930-494c-963c-1e951d15360e). ID klienta vašeho zákazníka můžete najít také pomocí webové adresy s názvem domény na adrese [What is my Microsoft Azure and Office 365 tenant ID?](https://www.whatismytenantid.com/)
- **Popis** (nepovinné) – Zadejte popis ID klienta, který vám pomůže identifikovat zákazníka, na kterého cílíte s dodatkem.

> [!NOTE]
> Tyto dva typy změn zásobníku na sebe. Zákazníci, na které se zaměňují s vlastními změnami, dostanou také univerzální změnu standardní smlouvy během nákupu.

Před pokračováním vyberte **Uložit pracovní verzi.**

## <a name="offer-listing"></a>Nabídka výpis

Tato stránka umožňuje definovat podrobnosti nabídky, jako je název nabídky, popis, odkazy a kontakty.

> [!NOTE]
> Obsah nabídky (například popis, dokumenty, snímky obrazovky a podmínky použití) nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [neanglickém jazyce]." Můžete také zadat _webovou adresu Užitečného odkazu_ a nabízet obsah v jiném jazyce, než který se používá v obsahu nabídky.

### <a name="marketplace-details"></a>Podrobnosti o tržišti

#### <a name="name"></a>Název

Jméno, které zde zadáte, se zákazníkům zobrazí jako název nabídky. Toto pole je předvyplněno textem, který jste zadali do pole **Alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Může být ochrannou známkou (a můžete zahrnout ochranné známky a symboly autorských práv)
- Nesmí mít dlouhou více než 50 znaků.
- Nelze zahrnout emodži.

#### <a name="search-results-summary"></a>Souhrn výsledků hledání

Stručný popis vaší nabídky. To může být až 100 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

#### <a name="long-summary"></a>Dlouhý souhrn

Poskytněte delší popis nabídky. To může být až 256 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

#### <a name="description"></a>Popis

Poskytněte podrobný popis nabídky s až 3 000 znaky. Zobrazí se zákazníkům v přehledu výpisu na komerčním trhu.

Do popisu uveďte jednu nebo více z následujících věcí:

- Hodnota a klíčové výhody vaší nabídky
- Kategorie nebo průmyslová sdružení nebo obojí
- Příležitosti k nákupu v aplikaci
- Veškerá požadovaná zveřejnění

Zde je několik tipů pro psaní popisu:

- Jasně popište hodnotu vaší nabídky v prvních několika větách vašeho popisu. Zahrňte následující položky:
  - Popis vaší nabídky.
  - Typ uživatele, který využívá vaši nabídku.
  - Potřeby nebo problémy zákazníka, které nabídka řeší.
- Nezapomeňte, že prvních několik vět může být zobrazeno ve výsledcích vyhledávače.
- Při prodeji vaší nabídky se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou vaše nabídka poskytuje.
- Používejte slova specifická pro dané odvětví nebo na základě zaměstnaneckých výhod.

Chcete-li, aby byl popis nabídky poutavější, naformátujte popis pomocí editoru rtf. Editor s formátovaným textem umožňuje přidávat čísla, odrážky, tučné písmo, kurzívu a odsazení, aby byl popis čitelnější.

![Ilustruje stránku Přehled v Centru partnerů s vybraným tlačítkem Nová nabídka a nabídkou konzultační služby.](media/power-bi-rich-text-editor.png)

#### <a name="privacy-policy-link"></a>Odkaz na zásady ochrany osobních údajů

Zadejte webovou adresu (URL) do zásad ochrany osobních údajů vaší organizace. Ujistěte se, že vaše nabídka je v souladu se zákony a předpisy na ochranu osobních údajů. Musíte také zveřejnit platné zásady ochrany osobních údajů na svých webových stránkách.

### <a name="useful-links"></a>Užitečné odkazy

Poskytněte doplňující online dokumenty o své nabídce. Chcete-li přidat odkaz, vyberte **+ Přidat odkaz** a vyplňte následující pole:

- **Jméno** – Zákazníci uvidí jméno na stránce podrobností.
- **Odkaz (URL)** – Zadejte odkaz pro zákazníky pro zobrazení vašeho online dokumentu.

### <a name="customer-support-links"></a>Odkazy na zákaznickou podporu

Poskytněte web podpory, kde se zákazníci mohou dostat k vašemu týmu podpory.

- Web globální podpory Azure
- Web podpory Azure Government

### <a name="partner-support-contact"></a>Kontakt na podporu partnera

Poskytněte partnerům společnosti Microsoft kontaktní informace, které budou používat, když zákazníci otevřou lístek podpory. Tato zpráva nebude uvedena na trhu.

- Název
- E-mail
- Telefon

### <a name="engineering-contact"></a>Technický kontakt

Poskytněte společnosti Microsoft kontaktní informace, které bude používat v případě problémů s vaší nabídkou, včetně problémů s certifikací. Tato zpráva nebude uvedena na trhu.

- Název
- E-mail
- Telefon

### <a name="marketplace-media"></a>Média marketplace

Poskytněte loga a obrázky, které chcete použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazané obrázky způsobí, že vaše podání bude odmítnuto.

#### <a name="marketplace-logos"></a>Loga tržiště

Zadejte soubory PNG s logem nabídky v následujících čtyřech velikostech pixelů:

- **Malé** (48 x 48)
- **Střední** (90 x 90)
- **Velké** (216 x 216)
- **Široká** (255 x 115)

Všechna čtyři loga jsou povinná a používají se na různých místech v seznamu tržiště.

#### <a name="screenshots"></a>Snímky obrazovek

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každý snímek obrazovky musí mít velikost 1280 x 720 pixelů a ve formátu PNG. Musíte také přidat titulek popisující snímek obrazovky.

#### <a name="videos"></a>Videa

Přidejte až pět videí, která ukazují vaši nabídku. Ty by měly být hostovány na externí video služby. Zadejte název každého videa, webovou adresu a miniaturu obrázku PNG videa o rozměrech 1280 x 720 pixelů.

Další zdroje informací o výpisech z marketplace najdete v [tématu Doporučené postupy pro nabídky marketplace](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

Před pokračováním vyberte **Uložit pracovní verzi.**

## <a name="preview"></a>Preview

Na kartě Náhled zvolte omezený **okruh uživatelů náhledu** pro ověření nabídky před publikováním živě širšímu okruhu uživatelů na marketplace.

> [!IMPORTANT]
> Po kontrole nabídky ve verzi Preview vyberte **možnost Přejít živě** a publikujte svou nabídku veřejnému publiku komerčního trhu.

Vaše předběžná skupina uživatelů je označena identifikátory GUID předplatného Azure spolu s volitelným popisem pro každou z nich. Ani jedno z těchto polí nemohou zákazníci vidět. ID předplatného Azure najdete na stránce **Předplatná** na webu Azure Portal.

Přidejte alespoň jedno ID předplatného Azure, a to buď jednotlivě (až 10), nebo nahráním souboru CSV (až 100). Přidáním těchto ID předplatného definujete, kdo může zobrazit náhled vaší nabídky před publikováním v přímém přenosu. Pokud je vaše nabídka již aktivní, můžete stále definovat náhled publika pro testování změn nabídky nebo aktualizací nabídky.

> [!NOTE]
> Náhled okruhu uživatelů se liší od soukromého okruhu uživatelů. Před publikováním ve verzi Preview má přístup k vaší nabídce _před_ její publikováním živě na tržištích. Mohou zobrazit a ověřit všechny plány, včetně těch, které budou k dispozici pouze pro soukromé publikum poté, co vaše nabídka je plně zveřejněna na trhu. Soukromý okruh uživatelů (definovaný na kartě **Ceny a dostupnost** plánu) má výhradní přístup k určitému plánu.

Před pokračováním k další části možnost Uložit pracovní verzi vyberte Možnost Uložit **pracovní verzi,** přehled plánu.

## <a name="plan-overview"></a>Přehled plánu

V partnerském centru můžete v rámci stejné nabídky zadat různé možnosti plánu. Tyto plány byly dříve označovány jako SKU. Nabídka vyžaduje alespoň jeden plán, který se může lišit, pokud jde o okruh uživatelů zpeněžení, oblasti Azure, funkce nebo image virtuálních počítače.

Po vytvoření plánů se na kartě **Přehled plánu** zobrazí:

- Názvy plánu
- Licenční modely
- Publikum (veřejné nebo soukromé)
- Aktuální stav publikování
- Dostupné akce

Akce dostupné v přehledu plánu se liší v závislosti na aktuálním stavu plánu. Patří mezi ně:

- **Odstranit pracovní verzi** – Pokud je stav plánu koncept
- **Zastavit prodej plánu** nebo **Synchronizovat soukromé publikum** – Pokud je stav plánu publikován živě

### <a name="create-new-plan"></a>Vytvořit nový plán

Vyberte **+ Vytvořit nový plán** nahoře. Zobrazí se dialogové okno **Nový plán.**

V poli **ID plánu** vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude viditelné pro zákazníky na webové adrese produktu. Používejte pouze malá písmena a čísla, pomlčky nebo podtržítka a maximálně 50 znaků.

> [!NOTE]
> ID plánu nelze změnit po výběru **možnosti Vytvořit**.

Do pole **Název plánu** zadejte název tohoto plánu. Zákazníci vidí tento název při rozhodování o tom, který plán vybrat v rámci vaší nabídky. Vytvořte jedinečný název, který jasně poukazuje na rozdíly v jednotlivých plánech. Můžete například použít **systém Windows Server** s plány **Průběžně zdatný**, **BYOL**, **Upřesnit**a **Enterprise**.

Vyberte **Vytvořit**.

### <a name="plan-setup"></a>Plánování nastavení

Nastavte konfiguraci vysoké úrovně pro typ plánu, zda opakovaně používá technickou konfiguraci z jiného plánu a ve kterých oblastech Azure by měl být k dispozici. Výběry zde určují, která pole se zobrazí na jiných kartách pro stejný plán.

#### <a name="reuse-technical-configuration"></a>Opětovné použití technické konfigurace

Pokud máte více než jeden plán stejného typu a balíčky jsou identické mezi nimi, můžete vybrat **tento plán znovu použije technickou konfiguraci z jiného plánu**. Tato možnost umožňuje vybrat jeden z dalších plánů stejného typu pro tuto nabídku a znovu použít jeho technickou konfiguraci.

> [!NOTE]
> Při opakovaném použití technické konfigurace z jiného plánu zmizí z tohoto plánu celá karta **Technická konfigurace.** Pro tento plán budou použity také technické podrobnosti o konfiguraci z jiného plánu, včetně všech aktualizací, které provedete v budoucnu. Toto nastavení nelze po publikování tohoto plánu změnit.

#### <a name="azure-regions"></a>Oblast Azure

Váš plán musí být k dispozici alespoň v jedné oblasti Azure.

Vyberte možnost **Azure Global,** chcete-li svůj plán zpřístupnit zákazníkům ve všech veřejných oblastech Azure, které mají integraci komerčního tržiště. Podrobnosti naleznete [v tématu Geografická dostupnost a podpora měny](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Vyberte možnost **Azure Government,** chcete-li svůj plán zpřístupnit v oblasti [Azure Government.](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) Tato oblast poskytuje kontrolovaný přístup zákazníkům z amerických federálních, státních, místních nebo kmenových subjektů, stejně jako partnerům, kteří jsou způsobilí k jejich poskytování. Vy jako vydavatel nesete odpovědnost za veškeré kontroly dodržování předpisů, bezpečnostní opatření a osvědčené postupy. Azure Government používá fyzicky izolovaná datová centra a sítě (umístěné jenom v USA).

Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)otestujte a ověřte svůj plán v prostředí, protože některé koncové body se mohou lišit. Pokud chcete plán nastavit a otestovat, požádejte o zkušební účet ze [zkušební verze Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po publikování plánu a k dispozici v určité oblasti Azure, nelze odebrat tuto oblast.

#### <a name="azure-government-certifications"></a>Certifikace Azure Government

Tato možnost se zobrazí jenom v případě, že vyberete **Azure Government** v oblastech **Azure**.

Služby Azure Government zpracovávají data, na která se vztahují určité vládní předpisy a požadavky. Například FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 a CJIS. Chcete-li zvýšit povědomí o certifikacích těchto programů, můžete poskytnout až 100 odkazů, které je popisují. Mohou to být buď odkazy na váš zápis přímo v programu, nebo odkazy na popisy vašeho dodržování na vašich webových stránkách. Tyto odkazy viditelné jenom pro zákazníky Azure Government.

Před pokračováním vyberte **Uložit pracovní verzi.**

### <a name="plan-listing"></a>Výpis plánu

Toto je místo, kde nakonfigurujete podrobnosti výpisu plánu. Na této kartě jsou zobrazeny konkrétní informace, které se mohou lišit mezi plány ve stejné nabídce.

#### <a name="plan-name"></a>Název plánu

Toto je předvyplněno názvem, který jste dali svému plánu, když jste ho vytvořili. Tento název se zobrazí na trhu jako název tohoto plánu a je omezen na 100 znaků.

#### <a name="plan-summary"></a>Souhrn plánu

Poskytněte krátký přehled svého plánu (nikoli nabídku). Tento souhrn je omezen na 100 znaků.

#### <a name="plan-description"></a>Popis plánu

Popište, čím je tento softwarový plán jedinečný, a také rozdíly mezi plány v rámci vaší nabídky. Nepopište nabídku, jen plán. Popis plánu může obsahovat až 2 000 znaků.

Před pokračováním vyberte **Uložit pracovní verzi.**

### <a name="pricing-and-availability"></a>Ceny a dostupnost

Na této kartě nakonfigurujete následující:

- Trhy, které bude tento plán k dispozici
- Cena za hodinu
- Určuje, zda má být plán viditelný pro všechny nebo pouze pro konkrétní zákazníky (soukromé publikum)

#### <a name="markets"></a>Trhy

Každý plán musí být k dispozici alespoň na jednom trhu. Zaškrtněte políčko pro každé místo na trhu, kde by měl být tento plán k dispozici pro nákup (uživatelé na těchto trzích mohou nabídku stále nasadit do všech oblastí Azure vybraných v **[nastavení plánu).](#plan-setup)** Tlačítko **Vypovězení daně** zobrazuje země, ve kterých společnost Microsoft vaším jménem u. Publikování do Číny je omezeno na plány, které jsou buď **zdarma,** nebo **přineste si vlastní licenci** (BYOL).

Pokud jste již nastavili ceny pro svůj plán v amerických dolarech (USD) a přidali další umístění na trhu, cena nového trhu bude vypočtena podle aktuálních směnných kurzů. Před publikováním vždy zkontrolujte cenu pro každý trh. Po uložení změn zkontrolujte ceny pomocí odkazu **Vývozní ceny (xlsx).**

Když odeberete trh, zákazníci z tohoto trhu pomocí aktivních nasazení nebudou moci vytvářet nová nasazení nebo vertikálně navýšit své stávající nasazení. Stávající nasazení nebudou ovlivněna.

#### <a name="pricing"></a>Ceny

**Licenční model** – Vyberte **měsíční fakturovaný plán založený na využití, chcete-li** nakonfigurovat ceny pro tento plán, nebo **přineste vlastní licenci,** která zákazníkům umožní používat tento plán s jejich stávající licencí.

Pro měsíční účtovaný plán založený na využití použijte jednu z následujících tří možností zadávání cen:

- **Za jádro** - Zadejte cenu za jádro v amerických dolarech (USD). Vypočítáme ceny za velikost jádra a převedeme na místní měny pomocí aktuálního směnného kurzu.
- **Podle velikosti jádra** – Poskytněte ceny za velikost jádra v USD. Ceny převedeme na místní měny pomocí aktuálního směnného kurzu.
- **Na trh a velikost jádra** – Poskytněte ceny pro každou velikost jádra pro všechny trhy. Ceny můžete importovat z tabulky.

> [!NOTE]
> Uložením změn cen povolíte export dat o cenách. Po zveřejnění ceny za trh ve vašem plánu ji nelze později změnit. Ujistěte se, že tyto ceny jsou přímo před publikováním exportem cenové tabulky a kontrolou ceny na každém trhu.

#### <a name="free-trial"></a>Bezplatná zkušební verze

Zákazníkům můžete nabídnout měsíční nebo tříměsíční bezplatnou zkušební verzi.

#### <a name="visibility"></a>Viditelnost

Každý plán můžete navrhnout tak, aby byl viditelný pro všechny nebo pouze pro předem vybranou cílovou skupinu. Přiřaďte členství v této omezené cílové skupině pomocí ID předplatného Azure.

**Veřejnost** - Váš plán může být viděn všemi.

**Soukromý okruh uživatelů** – Zviditelněte svůj plán pouze pro předem vybraný okruh uživatelů. Po publikování jako soukromý plán můžete okruh uživatelů aktualizovat nebo jej změnit na veřejný. Po zveřejnění plánu musí zůstat veřejný; Nemůžete to změnit zpět na soukromé.

**Omezený okruh uživatelů (ID předplatného Azure)** – přiřaďte okruh uživatelů, kteří budou mít přístup k tomuto soukromému plánu pomocí ID předplatného Azure. Volitelně můžete zahrnout popis každého ID předplatného Azure, které jste přiřadili. Přidejte až 10 ID předplatného ručně nebo 20 000, pokud importujete tabulku CSV. ID předplatného Azure jsou reprezentovány jako identifikátory GUID a písmena musí být malá písmena.

> [!NOTE]
> Soukromý okruh uživatelů nebo uživatelů s omezeným přístupem se liší od publika ve verzi Preview, kterou jste definovali na kartě **Náhled.** Před _publikováním_ na webu Marketplace má před publishovaná nabídka přístup k cílové skupině preview. Zatímco volba soukromého publika se vztahuje pouze na konkrétní plán, náhled publika může zobrazit všechny plány (soukromé nebo ne) pro účely ověření.

#### <a name="hide-plan"></a>Skrýt plán

Pokud váš virtuální počítač je určen k použití pouze nepřímo, když odkazuje prostřednictvím jiné šablony řešení nebo spravované aplikace, zaškrtněte toto políčko publikovat virtuální počítač, ale skrýt před zákazníky vyhledávání a procházení přímo.

> [!NOTE]
> Skryté plány nepodporují odkazy ve verzi Preview.

Před pokračováním vyberte **Uložit pracovní verzi.**

### <a name="technical-configuration"></a>Technická konfigurace

Zadejte obrázky a další technické vlastnosti spojené s tímto plánem. Podrobnosti najdete [v tématu Vytvoření technického prostředku virtuálního počítače Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Tato karta se nezobrazí, pokud jste tento plán nakonfigurovali pro opakované použití balíčků z jiného plánu na kartě **Nastavení plánu.**

#### <a name="operating-system"></a>Operační systém

- **Rodina operačních systémů** – Výběr z operačního systému **Windows** nebo **Linux**
- **Vydání** nebo **dodavatel** – zvolte windows vydání nebo Linux dodavatele
- **Popisný název operačního** systému – Zvolte popisný název operačního systému. Tento název je viditelný pro zákazníky

#### <a name="recommended-vm-sizes"></a>Doporučené velikosti virtuálních počítače

Vyberte až šest doporučených velikostí virtuálních počítačů, které chcete zobrazit na Azure Marketplace.

#### <a name="open-ports"></a>Otevřené porty

Otevřete veřejné nebo soukromé porty na nasazeném virtuálním počítači.

#### <a name="storage-option-for-deployment"></a>Možnost úložiště pro nasazení

**Možnost nasazení disku** – vyberte, jaký druh nasazení disku mohou uživatelé použít při používání virtuálního počítače. Společnost Microsoft doporučuje omezit nasazení pouze na spravované nasazení disku.

#### <a name="properties"></a>Vlastnosti

**Podpora zrychlených sítí** – vyberte, jestli váš virtuální počítač podporuje [zrychlené sítě](https://go.microsoft.com/fwlink/?linkid=2124513).

#### <a name="vm-images"></a>Image virtuálních počítačů

Zadejte verzi disku a identifikátor URI SAS pro bitové kopie virtuálních počítačů. Přidejte až 16 datových disků pro každou bitovou kopii virtuálního počítače. Zadejte pouze jednu novou verzi bitové kopie na plán v daném příspěvku. Po publikování obrázku jej nelze upravit, ale můžete ho odstranit. Odstranění verze zabrání novým i stávajícím uživatelům v nasazení nové instance odstraněné verze.

- **Verze disku** je verze obrázku, kterou poskytujete.
- **Identifikátor URI sas** je umístění ve službě Azure Storage, kde jste uložili virtuální pevný disk operačního systému.
- Image datových disků jsou také identifikátory URI SAS v pevném disku uložené v jejich úložišti Azure.
- Přidejte v plánu pouze jeden obrázek pro každý příspěvek.

Bez ohledu na to, který operační systém používáte, přidejte pouze minimální počet datových disků potřebných pro řešení. Zákazníci nemohou odebrat disky, které jsou součástí bitové kopie v době nasazení, ale mohou vždy přidat disky během nebo po nasazení.

Před pokračováním vyberte **Uložit pracovní verzi** a vraťte se do **přehledu plánu**.

## <a name="resell-through-csps"></a>Další prodej prostřednictvím csp

Rozšiřte dosah své nabídky tím, že ji zpřístupníte partnerům v programu [Zprostředkovatelé cloudových řešení](https://azure.microsoft.com/offers/ms-azr-0145p/) (CSP). Všechny plány Bring Your Own License (BYOL) jsou automaticky přihlášeny; můžete se rozhodnout, že se rozhodnete pro své plány, které nejsou BYOL.

Před pokračováním vyberte **Uložit pracovní verzi.**

## <a name="test-drive"></a>Zkušební jízda

Nastavte ukázku (testovací jízdu), která zákazníkům umožní vyzkoušet si vaši nabídku před zakoupením. Chcete-li vytvořit demonstrační prostředí, které zákazníkům umožní vyzkoušet si vaši nabídku na pevně stanovenou dobu, [přečtěte si část Test Drive your offer in the commercial marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/test-drive).

Chcete-li povolit testovací jednotku, zaškrtněte políčko Povolit testovací jednotku na kartě [Nabídka nastavení.](#test-drive) Chcete-li zkušební jízdu z nabídky odebrat, zrušte zaškrtnutí tohoto políčka.

Další prostředky testovací jednotky:

- Osvědčené technické postupy
- Marketingové osvědčené postupy
- Přehled testovacích jednotek (PDF; ujistěte se, že je blokování automaticky otevíraných míst vypnuté).

Před pokračováním vyberte **Uložit pracovní verzi.**

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky ji můžete odeslat ke kontrole a publikování.

V pravém horním rohu portálu vyberte **Zkontrolovat a publikovat**.

Na této stránce můžete:

- Podívejte se na stav dokončení pro každou část nabídky.
  - **Nespuštěno** – oddíl nebyl spuštěn a je třeba jej dokončit.
  - **Neúplné** – oddíl obsahuje chyby, které musí být opraveny nebo vyžaduje, abyste poskytli další informace. Pokyny k aktualizaci této části naleznete v částech, které byly uvedeny výše v tomto dokumentu.
  - **Dokončení** – sekce je dokončena a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky dokončeny.
- **Poznámky k certifikaci** – Poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že je vaše aplikace správně testována. Poskytněte všechny doplňkové poznámky užitečné pro pochopení vaší aplikace.

Chcete-li odeslat nabídku k publikování, vyberte **možnost Zkontrolovat a publikovat**.

Pošleme vám e-mail s upozorněním, kdy bude k dispozici předběžná verze nabídky ke kontrole a schválení. Pokud chcete nabídku zveřejnit (nebo soukromou nabídku soukromému publiku), přejděte do Centra partnerů, vyhledejte stránku **Přehled** nabídky a vyberte **Přejít na živo**. Stav nabídky se zobrazí v horní části stránky při probíhá publikování.

### <a name="errors-and-review-feedback"></a>Chyby a zpětná vazba

Krok **ručního ověření** v procesu publikování představuje rozsáhlou kontrolu vaší nabídky a souvisejících technických prostředků. Pokud tento proces odhalí chyby s vaší nabídkou, obdržíte zprávu o certifikaci, která podrobně popisuje problémy. Jednoduše proveďte požadované opravy a znovu publikujte svou nabídku.

## <a name="offer-overview"></a>Přehled nabídky

Stránka **Přehled nabídky** zobrazuje vizuální znázornění kroků potřebných k publikování této nabídky (dokončené i probíhající) a jak dlouho by mělo trvat dokončení každého kroku.

Tato stránka obsahuje odkazy na provádění operací s touto nabídkou na základě výběru, který provedete. Příklad:

- Pokud je nabídka konceptem - [Odstranit koncept nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Pokud je nabídka živá - [Přestaňte nabídku prodávat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Pokud je nabídka ve verzi preview - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Pokud jste nedokončili odhlášení majitele stránek - [Zrušení publikování](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="marketplace-examples"></a>Příklady marketplace

Tyto příklady ukazují, jak se nabídka zobrazí na Azure Marketplace.

### <a name="azure-marketplace-offer-details"></a>Podrobnosti o nabídce Azure Marketplace

![Příklad obrazovky s podrobnostmi o nabídce Azure Marketplace](media/avm-create1.png)

### <a name="azure-marketplace-search-results"></a>Výsledky hledání na Azure Marketplace

![Příklad obrazovky s podrobnostmi o hledání na Azure Marketplace](media/avm-create2.png)

### <a name="azure-marketplace-plan-details"></a>Podrobnosti o plánu Azure Marketplace

![Příklad obrazovky s podrobnostmi o plánu Azure Marketplace](media/avm-create3.png)

### <a name="azure-portal-offer-details"></a>Podrobnosti o nabídce portálu Azure

![Příklad obrazovky s podrobnostmi o nabídce portálu Azure](media/avm-create4.png)

### <a name="azure-portal-search-results"></a>Výsledky hledání na webu Azure Portal

![Příklad obrazovky s výsledky hledání na webu Azure Portal](media/avm-create5.png)

### <a name="azure-portal-plan-details"></a>Podrobnosti o plánu portálu Azure

![Příklad obrazovky s podrobnostmi o plánu portálu Azure](media/avm-create6.png)

## <a name="next-step"></a>Další krok

- [Aktualizace stávající nabídky na komerčním trhu](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
