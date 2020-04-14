---
title: Vytvoření nabídky kontejnerů Azure v Partnerském centru – Azure Marketplace
description: Tento článek popisuje, jak vytvořit a publikovat nabídku kontejneru pro Azure Marketplace.
author: mingshen
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: b96fe12ccc0292bb5f689fbecabd53d2af54846e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266315"
---
# <a name="create-an-azure-container-offer"></a>Vytvoření nabídky kontejneru Azure

> [!IMPORTANT]
> Správu nabídek Azure Container přesouváme z portálu partnerů cloudu do Centra partnerů. Dokud nebudou vaše nabídky migrovány, postupujte podle pokynů v [části Kontejnery](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-containers-offer) pro portál cloudových partnerů a spravujte své nabídky.

Tento článek popisuje, jak vytvořit a publikovat nabídku kontejneru pro Azure Marketplace. Před zahájením [vytvořte účet Commercial Marketplace v Centru partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se do [Centra partnerů](https://partner.microsoft.com/dashboard/home)a v horní nabídce vyberte **dashboard**.
2. V levé nabídce vyberte **Komerční tržiště**a pak **Přehled**.
3. Na stránce **Přehled** vyberte **+ Nová nabídka**a potom Azure **Container**. Zobrazí se dialogové okno **Nová nabídka.**

:::image type="content" source="media/azure-create-container-offer-images/azure-create-new-container.png" alt-text="Ilustrovala stránku Přehled v Centru partnerů. Zvýrazněné tlačítko Nová nabídka a nabídka služeb Consulting.":::

> [!TIP]
> Po publikování nabídky se úpravy provedené v Partnerském centru zobrazí pouze v obchodech po opětovném publikování nabídky. Ujistěte se, že po provedení změn vždy publikujete znovu.

### <a name="offer-id-and-alias"></a>ID nabídky a alias

Zadejte **ID nabídky**. Jedná se o jedinečný identifikátor pro každou nabídku ve vašem účtu.

- Toto ID můžou zákazníci vidět na webové adrese pro nabídku marketplace a případně šablony Azure Resource Manageru.
- Použijte při tom jenom malá písmena a číslice. Může obsahovat pomlčky a podtržítka, ale žádné mezery a je omezena na 50 znaků. Pokud například zadáte **test-offer-1**, bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- ID nabídky nelze změnit po výběru **možnosti Vytvořit**.

**Zadejte** **alias Nabídka**. Toto je název používaný k odkazování na nabídku v Partnerském centru.

- Tento název se na trhu nepoužívá a liší se od názvu nabídky a dalších hodnot zobrazených zákazníkům.
- To nelze změnit po výběru **možnosti Vytvořit**.

Před pokračováním vyberte **Vytvořit.**

## <a name="offer-overview"></a>Přehled nabídky

Na stránce **Přehled nabídky** se zobrazí vizuální znázornění kroků potřebných k publikování této nabídky (dokončené i nadcházející) a jak dlouho by mělo trvat dokončení každého kroku.

Tato stránka zobrazuje různé odkazy na základě aktuálního stavu nabídky. Příklad:

- Pokud je nabídka konceptem - [Odstranit koncept nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Pokud je nabídka živá - [Přestaňte nabídku prodávat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Pokud je nabídka ve verzi preview - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Pokud jste nedokončili odhlášení majitele stránek - [Zrušení publikování](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Nastavení nabídky

Podle těchto kroků nastavte nabídku.

### <a name="connect-lead-management--optional"></a>Připojit správu potenciálních zákazníků – volitelné

Při publikování nabídky na trhu pomocí Partnerského centra ji můžete připojit k systému správy vztahů se zákazníky (CRM). To vám umožní získat kontaktní informace zákazníků, jakmile někdo projeví zájem o váš produkt nebo jej používá.

1. **Vyberte cíl zájemce, kam chcete odeslat zájemce zákazníků**. Partnerské centrum podporuje následující crm systémy:

- [Dynamics 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics) pro zapojení zákazníků
- [Marketo](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-marketo)
- [Salesforce](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce)

> [!NOTE]
> Pokud váš CRM systém není uveden výše, použijte [Azure Table](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table) nebo [Https Endpoint](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-https) k ukládání dat zájemců zákazníků a pak data exportujte do systému CRM.

2. Při publikování v Partnerském centru propojte svou nabídku s cílem zájemce.
3. Zkontrolujte, zda je správně nakonfigurováno připojení k cíli zájemce. Po publikování v Centru partnerů ověříme připojení a pošleme vám testovacího zájemce. Zatímco zobrazíte náhled nabídky před její mizením, můžete také otestovat připojení zájemců tím, že se pokusíte nabídku zakoupit sami v prostředí preview.
4. Ujistěte se, že připojení k cíli zájemce zůstane aktualizované, abyste nepřišli o žádné zájemce.

Tady jsou některé další zdroje pro správu zájemců:

- [Přehled řízení potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads)
- [Nejčastější dotazy ke správě potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#frequently-asked-questions)
- [Běžné chyby konfigurace potenciálních zákazníků](https://docs.microsoft.com/azure/marketplace/lead-management-for-cloud-marketplace#common-lead-configuration-errors-during-publishing-on-cloud-partner-portal)
- [Přehled správy zájemců](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zkontrolujte, zda je blokování automaticky otevíraných míst vypnuté)

Před pokračováním k dalšímu oddílu Vlastnosti vyberte **Uložit pracovní verzi.**

### <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie použité k seskupení nabídky na trhu a právní smlouvy, které podporují vaši nabídku.

#### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně pět kategorií. Tyto kategorie se používají k umístění vaší nabídky do příslušných oblastí vyhledávání na trhu a zobrazují se na stránce s podrobnostmi o nabídce. V popisu nabídky vysvětlete, jak vaše nabídka podporuje tyto kategorie. Kontejnery se zobrazí v části **Kontejnery** a potom **kategorie Image kontejneru.**

#### <a name="legal"></a>Právní informace

Musíte poskytnout podmínky pro nabídku. Existují dvě možnosti:

- Použijte standardní smlouvu pro komerční tržiště společnosti Microsoft.
- Uveďte své vlastní podmínky.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardní smlouva pro komerční tržiště společnosti Microsoft

Nabízíme standardní šablonu smlouvy, která usnadňuje transakce na komerčním trhu. Můžete se rozhodnout nabídnout své řešení v rámci standardní smlouvy, kterou zákazníci potřebují pouze jednou zkontrolovat a přijmout. To je dobrá volba, pokud nechcete vytvářet vlastní podmínky.

Další informace o standardní smlouvě naleznete v [tématu Standardní smlouva pro komerční tržiště společnosti Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Můžete si také stáhnout [standardní smlouvy](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (ujistěte se, že vaše pop-up blokátor je vypnutý).

Chcete-li použít standardní smlouvu, zaškrtněte **políčko Použít standardní smlouvu pro komerční tržiště společnosti Microsoft** a klepněte na tlačítko **Přijmout**.

> [!NOTE]
> Po publikování nabídky pomocí smlouvy Standard pro komerční tržiště společnosti Microsoft nelze použít vlastní smluvní podmínky. Buď nabídněte své řešení podle standardní smlouvy, nebo podle svých vlastních podmínek.

:::image type="content" source="media/azure-create-container-offer-images/azure-create-1-standard-contract.png" alt-text="Ilustruje zaškrtávací políčko Použití standardní smlouvy pro komerční tržiště společnosti Microsoft.":::

##### <a name="your-own-terms-and-conditions"></a>Vaše vlastní podmínky

Chcete-li poskytnout vlastní smluvní podmínky, zadejte je do pole **Smluvní podmínky.** Do tohoto pole můžete zadat neomezené množství znaků textu. Zákazníci musí tyto podmínky přijmout, než budou moci vaši nabídku vyzkoušet.

Před pokračováním do další části Nabídka vyberte **Uložit pracovní verzi.**

## <a name="offer-listing"></a>Nabídka výpis

Tato stránka umožňuje definovat podrobnosti nabídky, které jsou zobrazeny na komerčním trhu. To zahrnuje název nabídky, popis a obrázky.

> [!NOTE]
> Podrobnosti nabídky nemusí být v angličtině, pokud popis nabídky začíná frází"Tato aplikace je k dispozici pouze v [neanglickém jazyce]." Je také v pořádku poskytnout užitečný odkaz pro nabízení obsahu v jazyce, který se liší od jazyka použitého v podrobnostech nabídky.

### <a name="name"></a>Name (Název)

Název, který zde zadáte, se zobrazí jako název nabídky. Toto pole je předvyplněno textem, který jste zadali do pole **Alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Může být chráněno ochrannou známkou (a můžete zahrnout symboly ochranných známek a autorských práv).
- Nesmí mít dlouhou více než 50 znaků.
- Nelze zahrnout emodži.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Stručný popis vaší nabídky. To může být až 100 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Podrobnější popis vaší nabídky. To může být až 256 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

### <a name="description"></a>Popis

Poskytněte delší popis nabídky až o 3 000 znacích. Zobrazí se zákazníkům v přehledu výpisu na marketplace.

Do popisu uveďte jednu nebo více z následujících věcí:

- Hodnota a klíčové výhody, které vaše nabídka poskytuje
- Kategorie nebo průmyslová sdružení nebo obojí
- Příležitosti k nákupu v aplikaci
- Veškerá požadovaná zveřejnění

Zde je několik tipů pro psaní popisu:

- Jasně popište hodnotu vaší nabídky v prvních několika větách vašeho popisu. Zahrňte následující položky:
  - Popis nabídky.
  - Typ uživatele, který využívá nabídky
  - Zákazník potřebuje nebo vydá adresy nabídek.
- Nezapomeňte, že prvních několik vět může být zobrazeno ve výsledcích hledání.
- Při prodeji produktu se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou vaše nabídka poskytuje.
- Snažte se používat oborově specifickou slovní zásobu nebo formulace založené na výhodách.

Chcete-li, aby **byl popis** nabídky poutavější, naformátujte popis pomocí editoru s formátovaným textem. s číslováním, odrážky, tučné písmo, kurzívou a odsazením, aby byl popis čitelnější.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustruje editor s formátovaným textem." border="false" :::

- Pomocí tohoto rozevíracího souboru aplikujte odstavcový styl na text.

    :::image type="content" source="media/text-editor3.png" alt-text="Ilustruje ovládací prvek styl textu v editoru rtf." border="false":::

- Pomocí těchto ikon můžete u textu použít číslování nebo odrážky.

     :::image type="content" source="media/text-editor4.png" alt-text="Ilustruje ovládací prvky s odrážkami a seznam čísel v editoru RTF." border="false":::

- Pomocí těchto ikon můžete přidat nebo odebrat odsazení do textu nebo z textu.

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustruje ovládací prvky odsazení v editoru rtf." border="false":::

#### <a name="privacy-policy-link"></a>Odkaz na zásady ochrany osobních údajů

Zadejte webovou adresu zásad ochrany osobních údajů vaší organizace. Jste zodpovědní za to, že vaše nabídka je v souladu se zákony a předpisy na ochranu osobních údajů. Jste také zodpovědní za zveřejnění platných zásad ochrany osobních údajů na svých webových stránkách.

#### <a name="useful-links"></a>Užitečné odkazy

Poskytněte doplňující online dokumenty o své nabídce. Můžete přidat až 25 odkazů. Chcete-li přidat odkaz, vyberte **+ Přidat odkaz** a vyplňte následující pole:

- **Název** – Zákazníci to uvidí na stránce s podrobnostmi o vaší nabídce.
- **Odkaz (URL)** – Zadejte odkaz pro zákazníky pro zobrazení vašeho online dokumentu. Propojení musí začínat http:// nebo https://.

### <a name="contact-information"></a>Kontaktní informace

Je nutné zadat jméno, e-mail a telefonní číslo **kontaktu podpory** a **technického kontaktu**. Tyto informace se zákazníkům nezobrazují, ale jsou k dispozici společnosti Microsoft. Může být také poskytnuta partnerům poskytovatele cloudových řešení (CSP).

- Kontakt podpory (povinné): Pro obecné dotazy podpory.
- Technický kontakt (povinné): Pro technické dotazy a certifikační problémy.
- Kontakt programu CSP (volitelné): Pro dotazy prodejce týkající se programu CSP.

V části **Kontakt podpory** najdete **web podpory,** kde partneři můžou najít podporu pro vaši nabídku na základě toho, jestli je nabídka dostupná v globálním Azure, Azure Government nebo v obou.

V **sekci kontakt programu CSP** uveďte odkaz **(CSP Program Marketing Materials),** kde mohou partneři CSP najít marketingové materiály pro vaši nabídku.

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

Další informace o vytváření nabídek najdete v [tématu Nabídka doporučených postupů pro nabízení nabídek.](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

### <a name="marketplace-images"></a>Obrázky na marketplace

Poskytněte loga a obrázky, které chcete použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazané obrázky budou odmítnuty.

#### <a name="store-logos"></a>Loga obchodu

 Zadejte soubory PNG s logem nabídky v každé z následujících čtyř velikostí pixelů:

- **Malé** (48 x 48)
- **Střední** (90 x 90)
- **Velké** (216 x 216)
- **Široká** (255 x 115)

Všechna čtyři loga jsou povinná a používají se na různých místech v seznamu tržiště.

#### <a name="screenshots-optional"></a>Snímky obrazovky (volitelné)

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každý musí mít velikost 1280 x 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelně)

Přidejte až pět videí, která ukazují vaši nabídku. Zadejte název videa, jeho webovou adresu a miniaturu obrázku PNG videa o rozměrech 1280 x 720 pixelů.

#### <a name="offer-examples"></a>Příklady pro nabídky

Následující příklady ukazují, jak se pole nabídky zobrazují na různých místech nabídky.

Zobrazuje stránku **nabídky na** Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-6-offer-listing-mkt-plc.png" alt-text="Ilustruje stránku nabídky na Azure Marketplace." :::

Zobrazuje výsledky hledání na Azure Marketplace:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-7-search-results-mkt-plc.png" alt-text="Ilustruje výsledky hledání na Azure Marketplace.":::

Zobrazuje stránku **nabídky na** webu Azure Portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-8-offer-listing-portal.png" alt-text="Ilustruje stránku nabídky výpis u Azure portal.":::

Zobrazuje výsledky hledání na webu Azure Portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-9-search-results-portal.png" alt-text="Ilustruje výsledky hledání na webu Azure Portal.":::

## <a name="preview"></a>Preview

Na kartě Náhled můžete zvolit omezenou **skupinu Pro-okruh uživatelů pro** ověření nabídky před jeho aktivním publikováním.

> [!IMPORTANT]
> Po zobrazení nabídky ve **verzi Preview**musíte vybrat **možnost Přejít živě,** abyste nabídku zveřejnili.

Zadejte okruh uživatelů náhledu pomocí identifikátorů GUID předplatných Azure spolu s volitelným popisem pro každou z nich. Ani jedno z těchto polí nemohou zákazníci vidět.

> [!NOTE]
> ID předplatného Azure najdete na stránce Předplatná na webu Azure Portal.

Přidejte alespoň jedno ID předplatného Azure, a to buď jednotlivě (až 10), nebo nahráním souboru CSV (až 100). Přidáním těchto ID předplatného určíte, kdo může zobrazit náhled vaší nabídky před publikováním v přímém přenosu. Pokud je vaše nabídka již aktivní, můžete si vybrat okruh uživatelů ve verzi Preview a otestovat změny nebo aktualizace nabídky.

> [!NOTE]
> Náhled okruhu uživatelů se liší od soukromého okruhu uživatelů. Předživých uživatelů **ve verzi Preview** můžete zobrazit a potvrdit všechny plány nabídek před tím, než budou na webu Marketplace aktivní, včetně těch, které budou publikovány pouze **soukromému** publiku (nastavené na kartě Dostupnost).

Před pokračováním vyberte **Uložit pracovní verzi.**

### <a name="plan-overview"></a>Přehled plánu

Tato karta umožňuje poskytnout různé možnosti plánu v rámci stejné nabídky. Tyto plány byly dříve označovány jako skladové jednotky nebo skladové jednotky. Plány se mohou lišit, pokud jde o to, jaké cloudy jsou k dispozici, jako jsou globální cloudy, vládní cloudy a obrázek, na který plán odkazuje. Chcete-li uvést nabídku na komerčním trhu, musíte nastavit alespoň jeden plán.

Po vytvoření plánů se na kartě **Přehled plánu** zobrazí:

- Názvy plánu
- Cenový model
- Dostupnost cloudu (globální nebo vládní)
- Aktuální stav publikování
- Všechny dostupné akce

Akce dostupné v přehledu plánu se liší v závislosti na aktuálním stavu plánu. Patří mezi ně:

- **Odstranit pracovní verzi** – Pokud je stav plánu koncept.
- **Zastavit plán prodeje** – Pokud je stav plánu publikován živě.

#### <a name="create-new-plan"></a>Vytvořit nový plán

Vyberte **Vytvořit nový plán**. Zobrazí se dialogové okno **Nový plán.**

V poli **ID plánu** vytvořte jedinečný identifikátor plánu pro každý plán v této nabídce. Toto ID bude viditelné pro zákazníky na webové adrese produktu. Používejte pouze malá písmena a čísla, pomlčky nebo podtržítka a maximálně 50 znaků.

> [!NOTE]
> ID plánu nelze změnit po výběru **možnosti Vytvořit**.

Do pole **Název plánu** zadejte název tohoto plánu. Zákazníci vidí tento název při rozhodování o tom, který plán vybrat v rámci vaší nabídky. Vytvořte jedinečný název pro každý plán v této nabídce. Můžete například použít název nabídky **systému Windows Server** s plány Windows Server **2016** a **Windows Server 2019**.

### <a name="plan-setup"></a>Plánování nastavení

Tato karta umožňuje zvolit, ve kterých cloudech je plán k dispozici. Vaše odpovědi na této kartě ovlivňují, která pole se zobrazí na jiných kartách.

#### <a name="cloud-availability"></a>Dostupnost cloudu

Váš plán musí být k dispozici alespoň v jednom cloudu.

Vyberte možnost **Azure Global,** aby váš plán mohli používat zákazníci ve všech globálních oblastech Azure, kteří používají komerční tržiště. Podrobnosti naleznete [v tématu Geografická dostupnost a podpora měny](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Tady vyberte možnost [**Azure Government Cloud,**](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome) aby se vaše řešení zobrazilo. Jedná se o vládní komunitní cloud s kontrolovaným přístupem pro zákazníky z amerických federálních, státních a místních nebo kmenových vládních agentur, stejně jako partnery, kteří jsou způsobilí jim sloužit. Jako vydavatel jste zodpovědní za veškeré kontroly dodržování předpisů, bezpečnostní opatření a osvědčené postupy pro tuto cloudovou komunitu. Azure Government používá fyzicky izolovaná datová centra a sítě (umístěné jenom v USA).

Před [publikováním](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners) na Azure Government, otestujte a potvrďte řešení v této oblasti, protože výsledky se mohou lišit. Pokud chcete vytvořit a otestovat své řešení, požádejte o zkušební účet ze [zkušební verze Microsoft Azure Government](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po publikování plánu a jeho dostupnosti v konkrétním cloudu ho nemůžete odebrat.

#### <a name="azure-government-cloud-certifications"></a>Certifikace Azure Government Cloud

Tuto možnost se dá zobrazit jenom v případě, že **azure government cloud** je vybraný v části Dostupnost **cloudu**.

Služby Azure Government zpracovávají data, která podléhají určitým vládním předpisům a požadavkům. Například FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 a CJIS.

Chcete-li zobrazit certifikace pro tyto programy, můžete poskytnout až 100 odkazů, které je popisují. Mohou to být odkazy na vaše záznamy v programu přímo nebo na vaše vlastní webové stránky. Tyto odkazy jsou viditelné jenom pro zákazníky Azure Government.

## <a name="plan-listing"></a>Výpis plánu

Tato karta zobrazuje konkrétní informace pro každý jiný plán v rámci aktuální nabídky.

### <a name="plan-name"></a>Název plánu

To to je pre-vyplněný s názvem, který dal svůj plán, když jste ho vytvořili. Tento název můžete podle potřeby změnit. Může mít dlouhou až 50 znaků. Tento název se zobrazí jako název tohoto plánu na Azure Marketplace a na webu Azure Portal. Používá se jako výchozí název modulu poté, co je plán připraven k použití.

### <a name="plan-summary"></a>Souhrn plánu

Krátké shrnutí softwarového plánu (nikoli nabídky). Tento souhrn se zobrazí ve výsledcích hledání Azure Marketplace a může obsahovat až 100 znaků.

### <a name="plan-description"></a>Popis plánu

Popište, čím je tento softwarový plán jedinečný, a také rozdíly mezi plány v rámci vaší nabídky. Nepopište nabídku, jen plán. Tento popis se zobrazí na Azure Marketplace a na webu Azure Portal na stránce **Nabídky.** Může se stát stejným obsahem, který jste zadali v souhrnu plánu, a může obsahovat až 2 000 znaků.

Po dokončení těchto polí vyberte **Uložit.**

#### <a name="plan-examples"></a>Příklady plánování

Následující příklady ukazují, jak se pole výpisu plánu zobrazují v různých zobrazeních.

Toto jsou pole na Azure Marketplace při zobrazení podrobností o plánu:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-10-plan-details-mtplc.png" alt-text="Ilustruje pole, která se zobrazí při zobrazení podrobností o plánu na Azure Marketplace.":::

Jedná se o podrobnosti plánu na webu Azure Portal:

:::image type="content" source="media/azure-create-container-offer-images/azure-create-11-plan-details-portal.png" alt-text="Ilustruje podrobnosti plánu na webu Azure Portal.":::

## <a name="plan-availability"></a>Dostupnost plánu

Pokud chcete publikovanou nabídku skrýt, aby ji zákazníci nemohli vyhledávat, procházet ji nebo ji zakoupit na trhu, zaškrtněte na kartě **Dostupnost** políčko **Skrýt plán.**

Toto pole se používá v těchto oblastech:

- Nabídka je určena k použití nepřímo, když odkazuje i přes jinou aplikaci.
- Nabídka by neměla být zakoupena jednotlivě.
- Plán byl použit pro počáteční testování a již není relevantní.
- Plán byl použit pro dočasné nebo sezónní nabídky a již by neměl být nabízen.

## <a name="technical-configuration"></a>Technická konfigurace

Ibi kontejneru musí být hostované v privátním [registru kontejnerů Azure](https://azure.microsoft.com/services/container-registry/). Na kartě **Technická konfigurace** zadejte referenční informace pro úložiště bitových obrázků kontejneru v registru kontejnerů Azure.

Po publikování nabídky se vaše image kontejneru zkopíruje na Azure Marketplace v konkrétním registru veřejných kontejnerů. Všechny požadavky na použití image kontejneru se zostiňují z registru veřejných kontejnerů Azure Marketplace, ne z vašeho soukromého. Podrobnosti najdete [v tématu Příprava technických prostředků Azure Container](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

### <a name="image-repository-details"></a>Podrobnosti o úložišti obrázků

Na kartě **Podrobnosti úložiště obrázků** zadejte následující informace.

**ID předplatného Azure** – zadejte ID předplatného, kde se hlásí využití a služby se účtují pro registr kontejnerů Azure, který zahrnuje image kontejneru. Toto ID najdete na [stránce Předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.

**Název skupiny prostředků Azure** – zadejte název [skupiny prostředků,](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) který obsahuje registr kontejnerů Azure s image kontejneru. Skupina prostředků musí být přístupná v ID předplatného (výše). Název najdete na stránce [Skupiny prostředků](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) na webu Azure Portal.

**Název registru kontejneru Azure** – zadejte název [registru kontejnerů Azure,](https://docs.microsoft.com/azure/container-registry/container-registry-intro) který má image kontejneru. Registr kontejnerů musí být ve skupině prostředků Azure, kterou jste zadali dříve. Uveďte pouze název registru, nikoli úplný název přihlašovacího serveru. Ujistěte se, že vynechat **azurecr.io** z názvu. Název registru najdete na [stránce Registry kontejnerů](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) na webu Azure Portal.

**Uživatelské jméno správce pro registr kontejnerů Azure** – zadejte [uživatelské jméno správce](https://docs.microsoft.com/azure/container-registry/container-registry-authentication#admin-account) propojené s registrem kontejnerů Azure, který má vaši image kontejneru. Uživatelské jméno a heslo jsou vyžadovány k zajištění přístupu vaší společnosti k registru. Chcete-li získat uživatelské jméno správce a heslo, nastavte vlastnost **s povolenou správcem** na **hodnotu True** pomocí rozhraní Příkazového řádku Azure (CLI). Volitelně můžete nastavit **uživatele správce** na **Povolit** na webu Azure Portal.

 :::image type="content" source="media/azure-create-container-offer-images/azure-create-12-update-container-registry-edit.png" alt-text="Ilustruje dialogové okno Aktualizovat registr kontejneru.":::

**Heslo pro registr kontejnerů Azure** – zadejte heslo pro uživatelské jméno správce, které je přidružené k registru kontejnerů Azure a má image kontejneru. Uživatelské jméno a heslo jsou vyžadovány k zajištění přístupu vaší společnosti k registru. Heslo můžete získat z portálu Azure tak, že přejdete na > **klíče přístupu k** **registru kontejneru**nebo pomocí příkazu Azure CLI pomocí [příkazu show](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show).

:::image type="content" source="media/azure-create-container-offer-images/azure-create-13-access-keys.png" alt-text="Ilustruje nabídku Přístupový klíč.":::

**Název úložiště v registru kontejnerů Azure**. Zadejte název úložiště registru kontejnerů Azure, který má vaši bitovou kopii. Při nabízení bitové kopie do registru uveďte název úložiště. Název úložiště můžete najít na stránce Úložiště >  [registru kontejnerů.](https://azure.microsoft.com/services/container-registry/)**Repositories** Další informace najdete [v tématu Zobrazení úložišť registru kontejnerů na webu Azure Portal](https://docs.microsoft.com/azure/container-registry/container-registry-repositories).

> [!NOTE]
> Po nastavení názvu jej nelze změnit. Pro každou nabídku ve vašem účtu použijte jedinečný název.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Značky obrázků pro nové verze vaší nabídky

Zákazníci musí být schopni automaticky získat aktualizace z Azure Marketplace při publikování aktualizace. Pokud se nechtějí aktualizovat, musí být schopni zůstat na konkrétní verzi obrázku. Můžete to provést přidáním nových značek obrázků pokaždé, když provedete aktualizaci obrázku.

### <a name="image-tag"></a>Značka obrázku

Toto pole musí obsahovat **nejnovější** značku, která odkazuje na nejnovější verzi obrázku na všech podporovaných platformách. Musí také obsahovat značku verze (například počínaje xx.xx.xx, kde xx je číslo). Zákazníci by měli používat [značky manifestu](https://github.com/estesp/manifest-tool) k cílení na více platforem. Všechny značky odkazované značkou manifestu musí být také přidány, abychom je mohli nahrát.

Všechny značky manifestu (s výjimkou nejnovější značky) musí začínat buď X.Y **-** nebo X.Y.Z- kde X, Y a Z jsou celá čísla. Pokud například **nejnovější** značka odkazuje na 1.0.1-linux-x64, 1.0.1-linux-arm32 a 1.0.1-windows-arm32, je třeba do tohoto pole přidat těchto šest značek. Podrobnosti najdete [v tématu Příprava technických prostředků Azure Container](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-technical-assets).

> [!NOTE]
> Nezapomeňte do obrázku přidat testovací značku, abyste mohli obrázek během testování identifikovat.

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky ji můžete odeslat ke kontrole a publikování.

V pravém horním rohu portálu vyberte **Zkontrolovat a** **publikovat**.

Na stránce recenze můžete:

- Podívejte se na stav dokončení pro každou část nabídky. Publikování nelze publikovat, dokud nebudou všechny části nabídky označeny jako úplné.
  - **Nezačal** – nebylspuštěn a je třeba je dokončit.
  - **Neúplné** – Má chyby, které je třeba opravit nebo vyžaduje, abyste poskytli další informace. Nápovědu naleznete v částech, které jsou uvedeny výše v tomto dokumentu.
  - **Dokončeno** – Zahrnuje všechna požadovaná data bez chyb. Všechny části nabídky musí být před odesláním nabídky dokončeny.
- Poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že vaše nabídka bude správně testována. Uveďte také všechny doplňkové poznámky, které jsou užitečné pro pochopení vaší nabídky.

Chcete-li odeslat nabídku k publikování, vyberte **publikovat**.

Pošleme vám e-mail s upozorněním, kdy bude k dispozici předběžná verze nabídky ke kontrole a schválení.

Pokud chcete nabídku zveřejnit (nebo soukromou nabídku soukromému publiku), přejděte do Partnerského centra a vyberte **Možnost Go-live**.

## <a name="next-step"></a>Další krok

- [Aktualizace stávající nabídky na komerčním marketplace](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer)
