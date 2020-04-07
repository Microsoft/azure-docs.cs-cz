---
title: Vytvoření nabídky modulu Azure IoT Edge pomocí Partnerského centra – Azure Marketplace
description: Zjistěte, jak vytvořit nabídku modulu IoT Edge na Azure Marketplace pomocí Centra partnerů
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 6b56b748ef31bcfd33893e55d3ea5f8d9851a3ff
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674472"
---
# <a name="create-an-iot-edge-module-offer"></a>Vytvoření nabídky modulu IoT Edge

> [!IMPORTANT]
> Přesouváme správu vašich nabídek modulů IoT Edge z cloudového partnerského portálu do Partnerského centra. Dokud se vaše nabídky nemigrují, postupujte podle pokynů v [modulu IoT Edge, který nabízí přehled publikování](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-offer-process-parts) pro portál cloudových partnerů a spravujte své nabídky.

Tento článek popisuje, jak vytvořit a publikovat nabídku modulu Edge internetu věcí (IoT) pro Azure Marketplace.

Před vytvořením nabídky modulu IoT Edge musíte mít účet komerčního tržiště v Centru partnerů. Pokud jste si ho ještě nevytvořili, přečtěte si informace [o vytvoření komerčního účtu marketplace v Centru partnerů](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account).

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se do Partnerského centra.
2. V levé navigační nabídce vyberte **Přehled komerčního tržiště** > **.**

    ![Ilustruje nabídku navigace vlevo.](./media/cs-menu-overview.png)

3. Vyberte **+ Nová nabídka** > **Modul IoT Edge**. Zobrazí se dialogové okno **Nová nabídka.**

> [!IMPORTANT]
> Po publikování nabídky se úpravy provedené v Partnerském centru zobrazí pouze v obchodech po opětovném publikování nabídky. Ujistěte se, že po provedení změn vždy publikujete znovu.

### <a name="offer-id-and-alias"></a>ID nabídky a alias

Zadejte **ID nabídky**. Jedná se o jedinečný identifikátor pro každou nabídku ve vašem účtu.

- Toto ID je viditelné pro zákazníky ve webové adrese pro nabídku marketplace a šablony Azure Resource Manager, pokud je to možné.
- Použijte při tom jenom malá písmena a číslice. Může obsahovat pomlčky a podtržítka, ale žádné mezery a je omezena na 50 znaků. Pokud například zadáte **test-offer-1**, bude webová adresa nabídky `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`.
- ID nabídky nelze změnit po výběru možnosti Vytvořit.

Zadejte **alias Nabídka**. Toto je název používaný k odkazování na nabídku v Partnerském centru.

- Tento název se na trhu nepoužívá a liší se od názvu nabídky a dalších hodnot zobrazených zákazníkům.
- To nelze změnit po výběru **možnosti Vytvořit**.

Po zadání těchto dvou hodnot vyberte **Vytvořit** před pokračováním na další stránku, Nabídka přehled.

## <a name="offer-overview"></a>Přehled nabídky

Na stránce **Přehled nabídky** se zobrazí vizuální znázornění kroků potřebných k publikování této nabídky (dokončené i nadcházející) a jak dlouho by mělo trvat dokončení každého kroku.

Tato stránka obsahuje odkazy na provádění operací s touto nabídkou na základě výběru, který provedete. Například:

- Pokud je nabídka konceptem - [Odstranit koncept nabídky](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#delete-a-draft-offer)
- Pokud je nabídka živá - [Přestaňte nabídku prodávat](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan)
- Pokud je nabídka ve verzi preview - [Go-live](https://docs.microsoft.com/azure/marketplace/partner-center-portal/publishing-status#publisher-approval)
- Pokud jste nedokončili odhlášení majitele stránek - [Zrušit publikování.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#cancel-publishing)

## <a name="offer-setup"></a>Nastavení nabídky

Podle těchto kroků nastavte nabídku.

### <a name="connect-lead-management"></a>Připojení správy potenciálních zákazníků

Při publikování nabídky na trhu pomocí Partnerského centra ji můžete volitelně připojit k systému správy vztahů se zákazníky (CRM). To vám umožní získat kontaktní informace zákazníků, jakmile někdo projeví zájem o váš produkt nebo jej používá.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující crm systémy:

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
- [Přehled správy zájemců](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Zkontrolujte, zda je blokování automaticky otevíraných míst vypnuté).

Před pokračováním k dalšímu oddílu Vlastnosti vyberte **Uložit pracovní verzi.**

### <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie použité k seskupení nabídky na trhu a právní smlouvy, které podporují vaši nabídku.

#### <a name="category"></a>Kategorie

Vyberte minimálně jednu a maximálně pět kategorií. Tyto kategorie se používají k umístění vaší nabídky do příslušných oblastí vyhledávání na trhu a zobrazují se na stránce s podrobnostmi o nabídce. V popisu nabídky vysvětlete, jak vaše nabídka podporuje tyto kategorie. Na stránkách procházení jsou všechny moduly IoT Edge zobrazeny v kategorii  **modulu Internet věcí > IoT Edge.**

#### <a name="legal"></a>Právní informace

Musíte poskytnout podmínky pro nabídku. Máte dvě možnosti:

- Použijte standardní smlouvu pro obchodní tržiště společnosti Microsoft.
- Uveďte své vlastní podmínky.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardní smlouva pro Microsoft Commercial Marketplace

Nabízíme standardní šablonu smlouvy, která usnadňuje transakce na komerčním trhu. Můžete se rozhodnout nabídnout své řešení v rámci standardní smlouvy, kterou zákazníci potřebují pouze jednou zkontrolovat a přijmout. To je dobrá volba, pokud nechcete vytvářet vlastní podmínky.

Další informace o standardní smlouvě naleznete v [tématu Standardní smlouva pro komerční tržiště společnosti Microsoft](https://docs.microsoft.com/azure/marketplace/standard-contract). Můžete si také stáhnout [standardní smlouvy](https://go.microsoft.com/fwlink/?linkid=2041178) PDF (ujistěte se, že vaše pop-up blokátor je vypnutý).

Chcete-li použít standardní smlouvu, zaškrtněte **políčko Použít standardní smlouvu pro komerční tržiště společnosti Microsoft** a klepněte na tlačítko **Přijmout**.

> [!NOTE]
> Po publikování nabídky pomocí smlouvy Standard pro komerční tržiště společnosti Microsoft nelze použít vlastní smluvní podmínky. Buď nabídněte své řešení podle standardní smlouvy, nebo podle svých vlastních podmínek.

![Ilustruje použití standardní smlouvy pro komerční tržiště společnosti Microsoft zaškrtávací políčko.](./media/iot-edge-module-creation/iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Vaše vlastní podmínky

Chcete-li poskytnout vlastní smluvní podmínky, zadejte je do pole **Smluvní podmínky.** Do tohoto pole můžete zadat neomezené množství znaků textu. Zákazníci musí tyto podmínky přijmout, než budou moci vaši nabídku vyzkoušet.

Před pokračováním do další části Nabídka vyberte **Uložit pracovní verzi.**

## <a name="offer-listing"></a>Nabídka výpis

Zde definujete podrobnosti nabídky, které se zobrazují na trhu. To zahrnuje název nabídky, popis, obrázky a tak dále. Při konfiguraci této nabídky se řiďte zásadami uvedenými na stránce zásad společnosti Microsoft.

> [!NOTE]
> Podrobnosti nabídky nemusí být v angličtině, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [neanglickém jazyce]." Je také v pořádku poskytnout užitečný odkaz pro nabízení obsahu v jazyce, který se liší od jazyka použitého v podrobnostech nabídky.

### <a name="name"></a>Name (Název)

Název, který zde zadáte, se zobrazí jako název nabídky. Toto pole je předvyplněno textem, který jste zadali do pole **Alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Může být chráněnochranou od ochranné známky (a můžete zahrnout ochranné známky nebo symboly autorských práv).
- Nesmí mít dlouhou více než 50 znaků.
- Nelze zahrnout emodži.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Uveďte krátký popis nabídky. To může být až 100 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Poskytněte podrobnější popis nabídky. To může být až 256 znaků dlouhé a používá se ve výsledcích vyhledávání na marketplace.

### <a name="description"></a>Popis

Poskytněte delší popis nabídky až o 3 000 znacích. Zobrazí se zákazníkům v přehledu výpisu na marketplace.

Do popisu uveďte jednu nebo více z následujících věcí:

- Hodnota a klíčové výhody, které vaše nabídka poskytuje
- Kategorie nebo průmyslová sdružení nebo obojí
- Příležitosti k nákupu v aplikaci
- Veškerá požadovaná zveřejnění

Nabídky modulů IoT Edge musí obsahovat odstavec s minimálními hardwarovými požadavky v dolní části popisu. Například:

*Minimální požadavky na hardware: Linux x64 a arm32 OS, 1 GB paměti RAM, 500 Mb úložiště*

Zde je několik tipů pro psaní popisu:

- Jasně popište hodnotu vaší nabídky v prvních několika větách vašeho popisu. Zahrňte následující položky:
    - Popis nabídky.
    - Typ uživatele, který těží z nabídky.
    - Zákazník potřebuje nebo vydá adresy nabídek.
- Nezapomeňte, že prvních několik vět může být zobrazeno ve výsledcích hledání.
- Při prodeji produktu se nespoléhejte na funkce a funkce. Místo toho se zaměřte na hodnotu, kterou vaše nabídka poskytuje.
- Snažte se používat oborově specifickou slovní zásobu nebo formulace založené na výhodách.

Chcete-li, aby vaše nabídka **Popis** byl poutavější, naformátujte popis pomocí editoru rtf. Editor s formátovaným textem umožňuje přidávat čísla, odrážky, tučné písmo, kurzívu a odsazení, aby byl popis čitelnější.

:::image type="content" source="media/text-editor2.png" alt-text="Ilustruje editor s formátovaným textem." border="false":::

- Chcete-li změnit formát obsahu, zvýrazněte text, který chcete formátovat, a vyberte styl textu, jak je znázorněno na tomto snímku obrazovky:

     :::image type="content" source="media/text-editor3.png" alt-text="Ilustruje ovládací prvek styl textu v editoru rtf." border="false":::

- Chcete-li k textu přidat seznam s odrážkami nebo číslovaný seznam, použijte možnosti zobrazené na tomto snímku obrazovky:
  
    :::image type="content" source="media/text-editor4.png" alt-text="Ilustruje ovládací prvky s odrážkami a seznam čísel v editoru RTF." border="false":::

- Chcete-li k textu přidat nebo odebrat odsazení, použijte možnosti zobrazené na tomto snímku obrazovky:

    :::image type="content" source="media/text-editor5.png" alt-text="Ilustruje ovládací prvky odsazení v editoru rtf." border="false":::

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte webovou adresu zásad ochrany osobních údajů vaší organizace. Jste zodpovědní za to, že vaše nabídka je v souladu se zákony a předpisy na ochranu osobních údajů. Jste také zodpovědní za zveřejnění platných zásad ochrany osobních údajů na svých webových stránkách.

#### <a name="useful-links"></a>Užitečné odkazy

Poskytněte doplňující online dokumenty o své nabídce. Můžete přidat až 25 odkazů. Chcete-li přidat odkaz, vyberte **+ Přidat odkaz** a vyplňte následující pole:

- **Název** – Zákazníci uvidí název na stránce s podrobnostmi o vaší nabídce.
- **Odkaz (URL)** - Zadejte odkaz pro zákazníky pro zobrazení vašeho online dokumentu. Propojení musí začínat http:// nebo https://.

Nezapomeňte do dokumentace přidat alespoň jeden odkaz a jeden odkaz na kompatibilní zařízení IoT Edge z [katalogu zařízení Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Kontaktní informace

Je nutné zadat jméno, e-mail a telefonní číslo **kontaktu podpory** a **technického kontaktu.** Tyto informace se zákazníkům nezobrazují. Je k dispozici společnosti Microsoft a může být poskytnuta partnerům poskytovatele cloudových řešení (CSP).

- Kontakt podpory (povinné): Pro obecné dotazy podpory.
- Technický kontakt (povinné): Pro technické dotazy a certifikační problémy.
- Kontakt programu CSP (volitelné): Pro dotazy prodejce týkající se programu CSP.

V části **Kontakt podpory** zadejte webovou adresu **webu podpory,** kde partneři můžou najít podporu pro vaši nabídku na základě toho, jestli je nabídka dostupná v globálním Azure, Azure Government nebo obojím.

V **sekci kontakt programu CSP** uveďte odkaz **(CSP Program Marketing Materials),** kde mohou partneři CSP najít marketingové materiály pro vaši nabídku.

#### <a name="additional-marketplace-listing-resources"></a>Další zdroje informací o výpisu tržiště

Další informace o vytváření nabídek najdete v [tématu Nabídka doporučených postupů pro nabízení nabídek](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices).

### <a name="marketplace-images"></a>Obrázky na marketplace

Poskytněte loga a obrázky, které chcete použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazané obrázky budou odmítnuty.

#### <a name="store-logos"></a>Loga obchodu

Zadejte soubory PNG loga nabídky v každé z následujících čtyř velikostí pixelů:

- **Malé (48 x 48)**
- **Střední (90 x 90)**
- **Velké (216 x 216)**
- **Široká (255 x 115)**

Všechna čtyři loga jsou povinná a používají se na různých místech v seznamu tržiště.

#### <a name="screenshots-optional"></a>Snímky obrazovky (volitelné)

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každý musí mít velikost 1280 x 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelně)

Přidejte až pět videí, která ukazují vaši nabídku. Zadejte název videa, jeho webovou adresu a miniaturu .png obrázku videa o rozměrech 1280 x 720 pixelů.

#### <a name="offer-examples"></a>Příklady pro nabídky

Následující příklady ukazují, jak se pole nabídky zobrazují na různých místech nabídky.

Tento snímek obrazovky zobrazuje stránku **Nabídky na** Azure Marketplace.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-offer-listing-page.png" alt-text="Ilustruje stránku nabídky na Azure Marketplace.":::

Tento snímek obrazovky ukazuje výsledky hledání na Azure Marketplace:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-search-results.png" alt-text="Ilustruje výsledky hledání na Azure Marketplace.":::

Tento snímek obrazovky ukazuje stránku **nabídky na** webu Azure Portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-listing-page-azure-portal.png" alt-text="Ilustruje stránku nabídky výpis u Azure portal.":::

Tento snímek obrazovky zobrazuje výsledky hledání na webu Azure Portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-search-results.png" alt-text="Ilustruje stránku nabídky výpis u Azure portal.":::

Před pokračováním do dalšího oddílu Náhled vyberte **Uložit pracovní verzi.**

## <a name="preview"></a>Preview

Na **kartě Náhled**můžete zvolit omezenou **skupinu Pro nejširší publikum pro** ověření nabídky před publikováním živého publiku širšímu okruhu uživatelů na marketplace.

> [!IMPORTANT]
> Po zobrazení nabídky ve verzi Preview musíte vybrat **možnost Přejít živě,** abyste nabídku zveřejnili.

Zadejte okruh uživatelů náhledu pomocí identifikátorů GUID předplatných Azure spolu s volitelným popisem pro každou z nich. Ani jedno z těchto polí nemohou zákazníci vidět.

> [!NOTE]
> ID předplatného Azure najdete na stránce Předplatná na webu Azure Portal.

Přidejte alespoň jedno ID předplatného Azure, a to buď jednotlivě (až 10), nebo nahráním souboru CSV (až 100). Přidáním těchto ID předplatného definujete, kdo může zobrazit náhled vaší nabídky před publikováním v přímém přenosu. Pokud je vaše nabídka již aktivní, můžete definovat náhled publika a otestovat změny nebo aktualizace nabídky.

> [!NOTE]
> Náhled okruhu uživatelů se liší od soukromého okruhu uživatelů. Předživých uživatelů **ve verzi Preview** můžete zobrazit a potvrdit všechny plány nabídek před tím, než budou na webu Marketplace aktivní, včetně těch, které budou publikovány pouze **soukromému** publiku (nastavené na kartě Dostupnost).

Před pokračováním k další části možnost Uložit pracovní verzi vyberte Možnost Uložit **pracovní verzi,** přehled plánu.

### <a name="plan-overview"></a>Přehled plánu

Tato karta umožňuje poskytovat různé možnosti plánu v rámci stejné nabídky v Centru partnerů. Tyto plány byly dříve označovány jako Skladové jednotky nebo skladové jednotky. Plány se mohou lišit, pokud jde o to, jaké cloudy jsou k dispozici, jako jsou globální cloudy, vládní cloudy a obrázek, na který plán odkazuje. Chcete-li uvést nabídku na trhu, musíte nastavit alespoň jeden plán.

Po vytvoření plánů se na kartě **Přehled plánu** zobrazí:

- Názvy plánu
- Cenový model
- Dostupnost cloudu (globální nebo vládní)
- Aktuální stav publikování
- Všechny dostupné akce

Akce dostupné v přehledu plánu se liší v závislosti na aktuálním stavu plánu. Patří mezi ně:

- **Odstranit pracovní verzi**: Pokud je stav plánu koncept.
- **Zastavit plán prodeje**: Pokud je stav plánu publikován živě.

#### <a name="create-new-plan"></a>Vytvořit nový plán

Vyberte **Vytvořit nový plán**. Zobrazí se dialogové okno **Nový plán.**

V poli **ID plánu** vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude viditelné pro zákazníky na webové adrese produktu. Používejte pouze malá písmena a čísla, pomlčky nebo podtržítka a maximálně 50 znaků.

Do pole **Název plánu** zadejte název tohoto plánu. Zákazníci vidí tento název při rozhodování o tom, který plán vybrat v rámci vaší nabídky. Vytvořte jedinečný název pro každý plán v této nabídce. Můžete například použít název nabídky **systému Windows Server** s plány Windows Server **2016** a **Windows Server 2019**.

> [!NOTE]
> ID plánu nelze změnit po výběru **možnosti Vytvořit**.

Vyberte **Vytvořit**.

### <a name="plan-setup"></a>Plánování nastavení

Tato karta umožňuje nakonfigurovat, ve kterých cloudech je plán k dispozici. Vaše odpovědi na této kartě ovlivňují, která pole se zobrazí na jiných kartách.

#### <a name="cloud-availability"></a>Dostupnost cloudu

Váš plán musí být dostupný alespoň v jednom cloudu pomocí Azure IoT Hub.

Vyberte možnost **Azure Global,** aby váš plán mohli používat zákazníci ve všech globálních oblastech Azure, kteří používají tržiště. Podrobnosti naleznete [v tématu Geografická dostupnost a podpora měny](https://aka.ms/AzureGovCurrencies).

Tady vyberte možnost [Azure Government Cloud,](https://aka.ms/WhatIsAzureGovernment) aby se vaše řešení zobrazilo. Jedná se o vládní komunitní cloud s kontrolovaným přístupem pro zákazníky z amerických federálních, státních a místních nebo kmenových vládních agentur, stejně jako partnery, kteří jsou způsobilí jim sloužit. Jako vydavatel jste zodpovědní za veškeré kontroly dodržování předpisů, bezpečnostní opatření a osvědčené postupy pro tuto cloudovou komunitu. Azure Government používá fyzicky izolovaná datová centra a sítě (umístěné jenom v USA). Před [publikováním](https://aka.ms/azuregovpublish) na Azure Government, otestujte a potvrďte řešení v této oblasti, protože výsledky se mohou lišit. Chcete-li zinscenovat a otestovat řešení, požádejte o zkušební účet z [testovací verze Microsoft Azure Government](https://aka.ms/AzureGovernmentTrial).

> [!NOTE]
> Po publikování plánu a jeho dostupnosti v konkrétním cloudu ho nemůžete odebrat.

#### <a name="azure-government-cloud-certifications"></a>Certifikace Azure Government Cloud

Tato možnost se zobrazí jenom v případě, že azure **government cloud** je vybraná v části **Dostupnost cloudu**.

Služby Azure Government zpracovávají data, která podléhají určitým vládním předpisům a požadavkům. Například FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 a CJIS. Chcete-li zvýšit povědomí o certifikacích těchto programů, můžete poskytnout až 100 odkazů popisujících vaše certifikace. Mohou to být odkazy na vaše záznamy v programu přímo nebo na vaše vlastní webové stránky. Tyto odkazy jsou viditelné jenom pro zákazníky Azure Government.

## <a name="plan-listing"></a>Výpis plánu

Tato karta zobrazuje konkrétní informace pro každý jiný plán v rámci stejné nabídky.

### <a name="plan-name"></a>Název plánu

To to je pre-vyplněný s názvem, který dal svůj plán, když jste ho vytvořili. Tento název můžete podle potřeby změnit. Může mít dlouhou až 50 znaků. Tento název se zobrazí jako název tohoto plánu na Azure Marketplace a na webu Azure Portal. Používá se jako výchozí název modulu poté, co je plán připraven k použití.

### <a name="plan-summary"></a>Souhrn plánu

Poskytněte krátký přehled svého plánu (nikoli nabídku). Tento souhrn se zobrazí ve výsledcích hledání Azure Marketplace a může obsahovat až 100 znaků.

### <a name="plan-description"></a>Popis plánu

Popište, čím je tento plán jedinečný, a také rozdíly mezi plány v rámci nabídky. Nepopište nabídku, jen plán. Tento popis se zobrazí na Azure Marketplace a na webu Azure Portal na stránce Nabídky. Může se stát stejným obsahem, který jste zadali v souhrnu plánu, a může obsahovat až 2 000 znaků.

Po dokončení těchto polí vyberte **Uložit pracovní verzi.**

#### <a name="plan-examples"></a>Příklady plánování

Následující příklady ukazují, jak se pole výpisu plánu zobrazují v různých zobrazeních.

Toto jsou pole na Azure Marketplace při zobrazení podrobností o plánu:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-marketplace-plan-details.png" alt-text="Ilustruje pole, která se zobrazí při zobrazení podrobností o plánu na Azure Marketplace.":::

Jedná se o podrobnosti plánu na webu Azure Portal:

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-azure-portal-plan-details.png" alt-text="Ilustruje podrobnosti plánu na webu Azure Portal.":::

## <a name="availability"></a>Dostupnost

Pokud chcete publikovanou nabídku skrýt, aby ji zákazníci nemohli vyhledávat, procházet ji nebo ji zakoupit na trhu, zaškrtněte na kartě Dostupnost políčko **Skrýt plán.**

Toto pole se běžně používá, když:

- Nabídka je určena k použití pouze nepřímo, pokud odkazuje i přes jinou aplikaci.
- Nabídka by neměla být zakoupena jednotlivě.
- Plán byl použit pro počáteční testování a již není relevantní.
- Plán byl použit pro dočasné nebo sezónní nabídky a již by neměl být nabízen.

## <a name="technical-configuration"></a>Technická konfigurace

Typ nabídky **modulu IoT Edge** je specifický typ kontejneru, který běží na zařízení IoT Edge. Na kartě **Technická konfigurace** poskytnete referenční informace pro úložiště bitových bitových údajů kontejneru v [registru kontejnerů Azure](https://aka.ms/ContainerRegistry)spolu s nastavením konfigurace, které zákazníkům umožní snadno používat modul.

Po publikování nabídky se vaše image kontejneru IoT Edge zkopíruje na Azure Marketplace v konkrétním registru veřejných kontejnerů. Všechny požadavky od uživatelů Azure o použití vašeho modulu se obsluhují z registru veřejných kontejnerů Azure Marketplace, ne z vašeho registru privátního kontejneru.

Můžete cílit na více platforem a poskytnout několik verzí image kontejneru modulu pomocí značek. Další informace o značkách a správě verzí najdete [v tématu Příprava technických prostředků modulu IoT Edge](https://aka.ms/AzureIoTTechAsset).

### <a name="image-repository-details"></a>Podrobnosti o úložišti obrázků

Na kartě **Podrobnosti úložiště obrázků** zadáte následující informace.

**Vyberte zdroj bitové kopie**: Vyberte možnost **Registru kontejnerů Azure.**

**ID předplatného Azure**: Zadejte ID předplatného, kde se hlásí využití prostředků a služby se účtují pro registr kontejnerů Azure, který zahrnuje image kontejneru. Toto ID najdete na [stránce Předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) na webu Azure Portal.

**Název skupiny prostředků Azure**: Zadejte název [skupiny prostředků,](https://aka.ms/ResourceManagerAzurePortal) který obsahuje registr kontejnerů Azure s image kontejneru. Skupina prostředků musí být přístupná v ID předplatného (výše). Název najdete na stránce [Skupiny prostředků](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) na webu Azure Portal.

**Název registru kontejneru Azure**: Zadejte název [registru kontejnerů Azure,](https://aka.ms/DockerContainerRegistriesAzure) který má image kontejneru. Registr kontejnerů musí být k dispozici ve skupině prostředků Azure, kterou jste zadali dříve. Zadejte pouze název registru, nikoli úplný název přihlašovacího serveru. Ujistěte se, že vynechat **azurecr.io** z názvu. Název registru najdete na [stránce Registry kontejnerů](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) na webu Azure Portal.

**Uživatelské jméno správce pro registr kontejnerů Azure**: Zadejte [uživatelské jméno správce](https://aka.ms/AdminAccountContainerRegistry) přidružené k registru kontejnerů Azure, který má vaši image kontejneru. Uživatelské jméno a heslo jsou vyžadovány k zajištění přístupu vaší společnosti k registru. Chcete-li získat uživatelské jméno správce a heslo, nastavte vlastnost **s povolenou správcem** na **hodnotu True** pomocí rozhraní Příkazového řádku Azure (CLI). **Volitelně** můžete nastavit uživatele správce na **Povolit** na webu Azure Portal.

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-admin-user.png" alt-text="Ilustruje dialogové okno Aktualizovat registr kontejneru.":::

**Heslo pro registr kontejnerů Azure**: Zadejte heslo pro uživatelské jméno správce, které je přidružené k registru kontejnerů Azure a má image kontejneru. Uživatelské jméno a heslo jsou vyžadovány k zajištění přístupu vaší společnosti k registru. Heslo můžete získat z portálu Azure tak, že přejdete na > **klíče přístupu k** **registru kontejneru**nebo pomocí příkazu Azure CLI pomocí [příkazu show.](https://aka.ms/azacrcredentialshow)

:::image type="content" source="media/iot-edge-module-creation/iot-edge-module-username-password.png" alt-text="Ilustruje obrazovku přístupových kláves na webu Azure Portal.":::

**Název úložiště v registru kontejnerů Azure**. Zadejte název úložiště registru kontejnerů Azure, který má vaši bitovou kopii. Název úložiště zadáte při nabízení bitové kopie do registru. Název úložiště najdete na stránce [Registry](https://aka.ms/ContainerRegistry) > **Registry.** Další informace najdete [v tématu Zobrazení úložišť registru kontejnerů na webu Azure Portal](https://aka.ms/ContainerRegistryRepositoriesAzure). Všimněte si, že po nastavení názvu jej nelze změnit. Pro každou nabídku ve vašem účtu použijte jedinečný název.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Značky obrázků pro nové verze vaší nabídky

Zákazníci musí být schopni automaticky získat aktualizace z Azure Marketplace při publikování aktualizace. Pokud se nechtějí aktualizovat, musí být schopni zůstat na konkrétní verzi obrázku. Můžete to provést přidáním nových značek obrázků pokaždé, když provedete aktualizaci obrázku.

**Značka obrázku**. Toto pole musí obsahovat **nejnovější** značku, která odkazuje na nejnovější verzi obrázku na všech podporovaných platformách. Musí také obsahovat značku verze (například počínaje xx.xx.xx, kde xx je číslo). Zákazníci by měli používat [značky manifestu](https://aka.ms/GitHubmanifest-tool) k cílení na více platforem. Všechny značky odkazované značkou manifestu musí být také přidány, abychom je mohli nahrát. Všechny značky manifestu (s výjimkou nejnovější značky) musí začínat buď X.Y- nebo X.Y.Z- kde X, Y a Z jsou celá čísla. Pokud například nejnovější značka odkazuje na 1.0.1-linux-x64, 1.0.1-linux-arm32 a 1.0.1-windows-arm32, je třeba do tohoto pole přidat těchto šest značek. Podrobnosti o značkách a správě verzí najdete [v tématu Příprava technických prostředků modulu IoT Edge.](https://aka.ms/PrepareIoTEdgeModTechAssets)

### <a name="default-deployment-settings-optional"></a>Výchozí nastavení nasazení (volitelné)

Definujte nejběžnější nastavení pro nasazení modulu IoT Edge. Optimalizujte nasazení zákazníků tím, že jim umožníte spustit modul IoT Edge out-of-the-box s těmito výchozími nastaveními.

**Výchozí trasy**. IoT Edge Hub spravuje komunikaci mezi moduly, službou IoT Hub a zařízeními. Můžete nastavit trasy pro vstup a výstup dat mezi moduly a službou IoT Hub, což vám poskytuje flexibilitu při odesílání zpráv tam, kam potřebují, bez nutnosti dalších služeb pro zpracování zpráv nebo psaní dalšího kódu. Trasy jsou konstruovány pomocí dvojic název/hodnota. Můžete definovat až pět výchozích názvů tras, každý o výšce až 512 znaků.

Ujistěte se, že v hodnotě trasy používáte správnou [syntaxi postupu](https://aka.ms/DeclareRoutesAzureIoT) (obvykle definovanou jako FROM/message/* INTO $upstream). To znamená, že všechny zprávy odeslané libovolnými moduly přejdou do vašeho centra IoT Hub. Chcete-li odkazovat na modul, použijte jeho výchozí název modulu, který bude název **nabídky**, bez mezer nebo speciálních znaků. Chcete-li odkazovat na další moduly, které ještě nejsou známy, použijte <FROM_MODULE_NAME> konvence, aby vaši zákazníci věděli, že je třeba tyto informace aktualizovat. Podrobnosti o trasách IoT Edge najdete [v tématu Deklarování tras](https://aka.ms/DeclareRoutesAzureIoT).

Pokud například modul ContosoModule naslouchá vstupům na ContosoInput a výstupních datech na ContosoOutput, má smysl definovat následující dvě výchozí trasy:

- Název #1: ToContosoModule
- Hodnota #1: FROM /messages/modules/<FROM_MODULE_NAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Název #2: FromContosoModuleToCloud
- Hodnota #2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Výchozí modul twin požadované vlastnosti**. Dvojče modulu je dokument JSON v centru IoT Hub, který ukládá informace o stavu instance modulu, včetně požadovaných vlastností. Požadované vlastnosti se používají spolu s vykazošovacími vlastnostmi k synchronizaci konfigurace modulu nebo podmínek. Back-end řešení může nastavit požadované vlastnosti a modul je může číst. Modul může také přijímat oznámení o změně v požadovaných vlastnostech. Požadované vlastnosti jsou vytvořeny pomocí až pěti párů název/hodnota a každá výchozí hodnota musí být menší než 512 znaků. Můžete definovat až pět požadovaných vlastností dvojčete name/value. Hodnoty twin požadované vlastnosti musí být platný JSON, non-uvozené, bez polí s maximální vnořené hierarchie čtyř úrovní. Ve scénáři, kde parametr požadovaný pro výchozí hodnotu nedává smysl (například IP adresa serveru zákazníka), můžete přidat parametr jako výchozí hodnotu. Další informace o vlastnostech požadovaných dvěma dvojčaty naleznete v [tématu Definování nebo aktualizace požadovaných vlastností](https://aka.ms/DefineUpdateProperties).

Pokud například modul podporuje dynamicky konfigurovatelnou obnovovací frekvenci pomocí požadovaných vlastností twin, má smysl definovat následující výchozí požadovanou vlastnost dvojčete:

- Název #1: RefreshRate
- Hodnota #1: 60

**Výchozí proměnné prostředí**. Proměnné prostředí poskytují doplňující informace k modulu, který pomáhá procesu konfigurace. Proměnné prostředí jsou vytvářeny pomocí dvojic název/hodnota. Každý výchozí název a hodnota proměnné prostředí musí být menší než 512 znaků a můžete definovat až pět. Pokud parametr požadovaný pro výchozí hodnotu nedává smysl (například IP adresa serveru zákazníka), můžete přidat parametr jako výchozí hodnotu.

Pokud například modul vyžaduje přijetí podmínek použití před spuštěním, můžete definovat následující proměnnou prostředí:

- Název #1: ACCEPT_EULA
- Hodnota #1: Y

**Výchozí možnosti vytvoření kontejneru**. Možnosti vytváření kontejneru nasměrovat vytvoření kontejneru modulu IoT Edge Docker. IoT Edge podporuje možnosti vytvoření kontejneru modulu Docker u motoru. Zobrazit všechny možnosti na [seznam kontejnerů.](https://aka.ms/ContainerList) Pole možnosti vytvoření musí být platné JSON, bez uvozených znaků a menší než 512 znaků.

Pokud například modul vyžaduje vazbu portu, definujte následující možnosti vytvoření:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných částí nabídky ji můžete odeslat ke kontrole a publikování.

V pravém horním rohu portálu vyberte **Zkontrolovat a publikovat**.

Na stránce recenze vidíte stav publikování:

- Podívejte se na stav dokončení pro každou část nabídky. Publikování nelze publikovat, dokud nebudou všechny části nabídky označeny jako úplné.
    - **Nezahájeno** – oddíl nebyl spuštěn a je třeba jej dokončit.
    - **Neúplné** – v oddíle jsou chyby, které je třeba opravit nebo které vyžadují poskytnutí dalších informací. Pokyny naleznete v částech, které jsou uvedeny výše v tomto dokumentu.
    - **Dokončeno** - V sekci jsou všechna požadovaná data a nejsou žádné chyby. Všechny části nabídky musí být před odesláním nabídky dokončeny.
- Poskytněte certifikačnímu týmu pokyny k testování, abyste zajistili, že vaše nabídka bude správně testována. Uveďte také všechny doplňkové poznámky, které jsou užitečné pro pochopení vaší nabídky.

Chcete-li odeslat nabídku k publikování, vyberte **publikovat**.

Pošleme vám e-mail s upozorněním, kdy bude k dispozici předběžná verze nabídky ke kontrole a schválení. Pokud chcete nabídku zveřejnit (nebo soukromou nabídku soukromému publiku), přejděte do Partnerského centra a vyberte **Možnost Go-live**.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním trhu](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)