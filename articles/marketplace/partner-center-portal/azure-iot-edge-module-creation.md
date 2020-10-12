---
title: Vytvoření nabídky modulu Azure IoT Edge s partnerským centrem v Azure Marketplace
description: Naučte se, jak vytvořit, nakonfigurovat a publikovat nabídku IoT Edge modulu v Azure Marketplace pomocí partnerského centra.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: keferna
ms.author: keferna
ms.date: 08/07/2020
ms.openlocfilehash: 5b823b0429feffa87a05faadc78574287bc9b4a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708335"
---
# <a name="create-an-iot-edge-module-offer"></a>Vytvoření nabídky modulu IoT Edge

Tento článek popisuje, jak vytvořit a publikovat nabídku modulu Internet věcí (IoT) Edge pro Azure Marketplace. Než začnete, [Vytvořte si účet komerčního tržiště v partnerském centru](create-account.md) , pokud jste to ještě neudělali. Ujistěte se, že je váš účet zaregistrovaný v programu komerčního tržiště.

## <a name="create-a-new-offer"></a>Vytvoření nové nabídky

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com/dashboard/home).
2. V nabídce vlevo – navigace vyberte **obchodní Marketplace**  >  **Přehled**.
3. Na stránce Přehled vyberte **+ Nová nabídka**  >  **IoT Edge modul**.

    ![Znázorňuje levou navigační nabídku.](./media/new-offer-iot-edge.png)

> [!IMPORTANT]
> Po publikování nabídky se úpravy provedené v partnerském centru zobrazí pouze v online obchodech po opětovném publikování nabídky. Nezapomeňte po provedení změn vždy znovu publikovat.

### <a name="offer-id-and-alias"></a>ID nabídky a alias

Zadejte **ID nabídky**. Toto je jedinečný identifikátor každé nabídky ve vašem účtu.

- Toto ID je viditelné pro zákazníky na webové adrese pro nabídku webu Marketplace a šablony Azure Resource Manager, pokud jsou k dispozici.
- Použijte při tom jenom malá písmena a číslice. Může zahrnovat pomlčky a podtržítka, ale ne mezery a je omezen na 50 znaků. Pokud například zadáte **příkaz Test-nabídky-1**, Webová adresa nabídky bude `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1` .
- ID nabídky nelze změnit po výběru možnost **vytvořit**.

Zadejte **alias nabídky**. Toto je název, který se používá pro nabídku v partnerském centru.

- Tento název se na webu Marketplace nepoužívá a liší se od názvu nabídky a dalších hodnot, které se zákazníkům zobrazují.
- Po výběru **vytvořit**se tato možnost nedá změnit.

Vyberte **vytvořit** pro vygenerování nabídky a pokračování.

## <a name="offer-overview"></a>Přehled nabídky

Na stránce **Přehled nabídky** se zobrazuje vizuální reprezentace kroků požadovaných k publikování této nabídky (dokončené i nadcházející) a o tom, jak dlouho by měly být jednotlivé kroky dokončeny.

Tato stránka obsahuje odkazy na provádění operací s touto nabídkou na základě vámi provedeného výběru. Například:

- Pokud je nabídka nabídkou konceptu a [odstranění konceptu](update-existing-offer.md#delete-a-draft-offer)
- Pokud je nabídka živá, [přestanou prodávat nabídku](update-existing-offer.md#stop-selling-an-offer-or-plan) .
- Pokud je nabídka ve verzi Preview – [Přejít do provozu](publishing-status.md#publisher-approval)
- Pokud jste nedokončili registraci vydavatele [, zrušte publikování.](update-existing-offer.md#cancel-publishing)

## <a name="offer-setup"></a>Nastavení nabídky

Pomocí těchto kroků můžete nastavit nabídku.

### <a name="customer-leads"></a>Potenciální zákazníci

Když publikujete nabídku na Marketplace s partnerským centrem, můžete ji volitelně připojit k systému pro správu vztahů se zákazníky (CRM). To vám umožní získat kontaktní údaje zákazníka, jakmile někdo vyjádří zájem nebo použije váš produkt.

1. Vyberte cíl zájemců, kam chcete, abychom posílali potenciální zákazníky. Partnerské centrum podporuje následující systémy CRM:

    - [Dynamics 365](commercial-marketplace-lead-management-instructions-dynamics.md) pro zákaznickou zapojení
    - [Marketo](commercial-marketplace-lead-management-instructions-marketo.md)
    - [Salesforce](commercial-marketplace-lead-management-instructions-salesforce.md)

    > [!NOTE]
    > Pokud váš systém CRM není uvedený výše, použijte [koncový bod](commercial-marketplace-lead-management-instructions-https.md) [Azure Table](commercial-marketplace-lead-management-instructions-azure-table.md) nebo HTTPS k uložení zákaznických dat a pak EXPORTUJTE data do systému CRM.

2. Připojte svoji nabídku k cíli realizace při publikování v partnerském centru.
3. Potvrďte, že je správně nakonfigurované připojení k cíli realizace. Po publikování v partnerském centru ověříme připojení a pošleme vám testovacího vedoucího. I když náhled nabídky před tím, než začne fungovat, můžete také otestovat své zájemce tím, že se pokusíte nabídku koupit v prostředí verze Preview.
4. Ujistěte se, že připojení k cíli realizace zůstane aktualizované, takže nepřijdete o žádné zájemce.

Tady jsou některé další zdroje informací o vedoucím řízení:

- [Potenciální zákazníci z komerční nabídky na webu Marketplace](commercial-marketplace-get-customer-leads.md)
- [Běžné otázky týkající se správy potenciálních zákazníků](../lead-management-faq.md#common-questions-about-lead-management)
- [Řešení chyb konfigurace potenciálních zákazníků](../lead-management-faq.md#publishing-config-errors)
- [Přehled řízení vedoucích](https://assetsprod.microsoft.com/mpn/cloud-marketplace-lead-management.pdf) PDF (Ujistěte se, že je vypnutý blokování automaticky otevíraných oken).

Než budete pokračovat, vyberte **Uložit koncept** .

### <a name="properties"></a>Vlastnosti

Tato stránka umožňuje definovat kategorie používané k seskupení vaší nabídky na webu Marketplace a právní smlouvy, které vaši nabídku podporují.

#### <a name="category"></a>Kategorie

Vyberte kategorie a podkategorie a vložte nabídku do příslušných oblastí hledání na webu Marketplace. Nezapomeňte popsat, jak vaše nabídka podporuje tyto kategorie v popisu nabídky. Vyberte:

- Aspoň jedna a až dvě kategorie, včetně primární a sekundární kategorie (volitelné).
- Až dvě podkategorie pro každou primární nebo sekundární kategorii. Pokud není pro vaši nabídku k dispozici žádná podkategorie, vyberte možnost **Nepoužito**.

Úplný seznam kategorií a podkategorií v nabídce, kde najdete [Doporučené postupy](../gtm-offer-listing-best-practices.md). Na webu Marketplace jsou IoT Edge moduly vždy zobrazeny v **Internet of Things**  >  kategorii**modulu IoT Edge**Internet věcí   .

#### <a name="legal"></a>Právní informace

Pro tuto nabídku musíte zadat podmínky a ujednání. Máte dvě možnosti:

- Využijte standardní smlouvu pro komerční tržiště Microsoftu.
- Zadejte vlastní podmínky a ujednání.

##### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Smlouva standard pro komerční tržiště Microsoftu

Nabízíme standardní šablonu smlouvy, která usnadňuje transakce na komerčním webu Marketplace. Můžete si vybrat řešení v rámci standardní smlouvy, které zákazníci potřebují jenom jednou ověřit a přijmout. Tato možnost je vhodná, pokud nechcete vytvořit vlastní podmínky a ujednání.

Další informace o standardní smlouvě najdete v tématu [standardní smlouva o komerčním tržišti společnosti Microsoft](../standard-contract.md). Můžete si také stáhnout [standardní](https://go.microsoft.com/fwlink/?linkid=2041178) pracovní soubor PDF (Ujistěte se, že blokování automaticky otevíraných oken je vypnuté).

Pokud chcete použít standardní smlouvu, zaškrtněte políčko **použít standardní kontrakt pro komerční web Marketplace** a pak klikněte na **přijmout**.

> [!NOTE]
> Po publikování nabídky pomocí standardní smlouvy pro komerční tržiště společnosti Microsoft nemůžete použít vlastní podmínky a ujednání. Nabídněte své řešení pod standardní smlouvou nebo podle vašich vlastních podmínek.

![V této části se dozvíte, jak používat standardní kontrakt pro obchodní tržiště Microsoftu.](media//iot-edge-module-standard-contract-checkbox.png)

##### <a name="your-own-terms-and-conditions"></a>Vaše vlastní podmínky a ujednání

Pokud chcete zadat vlastní podmínky a ujednání, zadejte je do pole **podmínky a ujednání** . V tomto poli můžete zadat neomezené množství znaků textu. Zákazníci musí tyto podmínky přijmout, aby mohli svoji nabídku vyzkoušet.

Než budete pokračovat k další části seznamu nabídek, vyberte **Uložit koncept** .

## <a name="offer-listing"></a>Seznam nabídek

Tady definujete podrobnosti nabídky, které se zobrazí na webu Marketplace. To zahrnuje název nabídky, popis, obrázky a tak dále. Při konfiguraci této nabídky nezapomeňte postupovat podle pokynů na stránce Zásady Microsoftu.

> [!NOTE]
> Podrobnosti nabídky nejsou v anglickém jazyce nutné, pokud popis nabídky začíná frází "Tato aplikace je k dispozici pouze v [jazykovém jazyce, který není v angličtině]." Také je v pořádku poskytnutí užitečného odkazu na nabídku obsahu v jazyce, který se liší od toho, který se používá v podrobnostech seznamu nabídky.

### <a name="name"></a>Name

Název, který zde zadáte, se zobrazí jako název vaší nabídky. Toto pole je předem vyplněno textem, který jste zadali do pole **alias nabídky** při vytváření nabídky. Tento název můžete později změnit.

Název:

- Dá se použít jako ochranná známka (a může obsahovat symboly ochranné známky nebo autorských práv).
- Nemůže být delší než 50 znaků.
- Nejde zahrnout emoji.

### <a name="search-results-summary"></a>Souhrn výsledků hledání

Zadejte krátký popis vaší nabídky. Může to být až 100 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

### <a name="long-summary"></a>Dlouhý souhrn

Poskytněte podrobnější popis vaší nabídky. Může to být až 256 znaků dlouhé a používá se ve výsledcích hledání na webu Marketplace.

### <a name="description"></a>Description

[!INCLUDE [Long description-1](./includes/long-description-1.md)]

Nabídky modulu IoT Edge musí zahrnovat minimální odstavec požadavků na hardware ve spodní části popisu, například:

- Minimální požadavky na hardware: Linux x64 a arm32 OS, 1 GB paměti RAM, 500 MB úložiště

[!INCLUDE [Long description-2](./includes/long-description-2.md)]

[!INCLUDE [Long description-3](./includes/long-description-3.md)]

#### <a name="privacy-policy-url"></a>Adresa URL zásad ochrany osobních údajů

Zadejte webovou adresu zásad ochrany osobních údajů vaší organizace. Zodpovídáte za zajištění, že vaše nabídka vyhovuje zákonům a předpisům o ochraně osobních údajů. Zodpovídáte také za publikování platných zásad ochrany osobních údajů na vašem webu.

#### <a name="useful-links"></a>Užitečné odkazy

Poskytněte dodatečné online dokumenty o vaší nabídce. Můžete přidat až 25 odkazů. Chcete-li přidat odkaz, vyberte **+ Přidat odkaz** a potom vyplňte následující pole:

- **Název** – zákazníci uvidí název na stránce s podrobnostmi o vaší nabídce.
- **Odkaz (URL)** – zadejte odkaz pro zákazníky k zobrazení online dokumentu. Odkaz musí začínat na `http://` nebo `https://` .

Nezapomeňte přidat aspoň jeden odkaz na svou dokumentaci a jeden odkaz na kompatibilní IoT Edge zařízení z [katalogu zařízení Azure IoT](https://catalog.azureiotsolutions.com/).

### <a name="contact-information"></a>Kontaktní informace

Musíte zadat jméno, e-mail a telefonní číslo pro **kontakt podpory** a **technický kontakt.** Tyto informace se zákazníkům nezobrazují. Je k dispozici pro společnost Microsoft a může být poskytnuta partnerům Cloud Solution Provider (CSP).

- Kontakt podpory (povinné): pro obecné dotazy týkající se podpory.
- Technický kontakt (povinné): pro technické dotazy a problémy certifikace.
- Kontakt programu CSP (volitelné): pro otázky prodejců související s programem CSP.

V části **kontakt podpory** zadejte webovou adresu **webu podpory** , kde můžou partneři najít podporu pro vaši nabídku na základě toho, jestli je nabídka dostupná v globálním Azure, Azure Government nebo obojí.

V části **kontakt programu CSP** zadejte odkaz (**marketingové materiály programu CSP**), kde mohou partneři CSP najít marketingové materiály pro vaši nabídku.

#### <a name="additional-marketplace-listing-resources"></a>Další prostředky pro výpis Marketplace

Další informace o vytváření seznamů nabídek najdete v tématu [nabídky Doporučené postupy](../gtm-offer-listing-best-practices.md).

### <a name="marketplace-images"></a>Image Marketplace

Poskytněte loga a obrázky, které se mají použít s vaší nabídkou. Všechny obrázky musí být ve formátu PNG. Rozmazaný obraz se odmítne.

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

#### <a name="screenshots-optional"></a>Snímky obrazovky (volitelné)

Přidejte až pět snímků obrazovky, které ukazují, jak vaše nabídka funguje. Každá velikost musí být 1280 × 720 pixelů a ve formátu PNG.

#### <a name="videos-optional"></a>Videa (volitelné)

Přidejte až pět videí, která předvádí vaši nabídku. Zadejte název videa, jeho webovou adresu a miniaturu obrázku PNG o velikosti videa na 1280 × 720 pixelů.

#### <a name="marketplace--examples"></a>Příklady Marketplace

Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Velké logo
2. Kategorie
3. Adresa podpory (odkaz)
4. podmínky a ujednání
5. Adresa zásad ochrany osobních údajů (odkaz)
6. Name
7. Shrnutí
8. Description
9. Užitečné odkazy
10. Snímky obrazovky a videa

<br>Tady je příklad toho, jak se zobrazují informace nabídky ve výsledcích hledání Azure Marketplace:

:::image type="content" source="media/example-iot-azure-marketplace-offer-search-results.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Malé logo
2. Název nabídky
3. Souhrn výsledků hledání

<br>Tady je příklad toho, jak se zobrazují informace o nabídce v Azure Portal:

:::image type="content" source="media/example-iot-azure-portal-offer.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Název
2. Popis
3. Užitečné odkazy
4. Screenshoty

<br>Tady je příklad toho, jak se ve výsledcích hledání Azure Portal zobrazí informace o nabídce:

:::image type="content" source="media/example-iot-azure-portal-offer-search-results.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Malé logo
2. Název nabídky
3. Souhrn výsledků hledání

<br>Než budete pokračovat k další části, vyberte **Uložit koncept** .

## <a name="preview"></a>Preview

Na **kartě náhled**můžete zvolit **cílovou skupinu omezené verze Preview** pro ověření vaší nabídky před jejich publikováním na širší cílovou skupinu Marketplace.

> [!IMPORTANT]
> Po zobrazení této nabídky ve verzi Preview musíte vybrat možnost **Přejít na Live** a publikovat svou nabídku na veřejném.

Určete cílovou skupinu Preview pomocí identifikátorů GUID předplatného Azure, spolu s volitelným popisem pro každý z nich. Žádná z těchto polí nemohou zákazníci vidět.

> [!NOTE]
> ID vašeho předplatného Azure najdete na stránce Předplatná v Azure Portal.

Přidejte aspoň jedno ID předplatného Azure, a to buď samostatně (až 10), nebo nahráním souboru CSV (až 100). Přidáním těchto ID předplatných určíte, kdo může zobrazit náhled vaší nabídky předtím, než se publikuje živě. Pokud je vaše nabídka už živá, můžete definovat cílovou skupinu verze Preview, která testuje změny nebo aktualizace vaší nabídky.

Než budete pokračovat k další části, vyberte **Uložit koncept** .

## <a name="plan-overview"></a>Přehled plánu

Tato karta vám umožní v partnerském centru poskytnout různé možnosti plánu v rámci stejné nabídky. Plány (dříve nazývané SKU) se mohou lišit v souvislosti s dostupnými cloudy, jako jsou globální cloudy, cloudy státní správy a bitová kopie, na kterou plán odkazuje. Pokud chcete zobrazit seznam nabídek na webu Marketplace, musíte nastavit aspoň jeden plán.

Pro každou nabídku můžete vytvořit až 100 plánů: až 45 z nich může být privátní. Další informace o privátních plánech najdete v [části soukromé nabídky na komerčním webu Microsoft Marketplace](../private-offers.md).

Po vytvoření plánů se zobrazí karta **Přehled plánu** :

- Názvy plánů
- Cenový model
- Oblasti Azure (globální nebo státní správa)
- Stav aktuálního publikování
- Jakékoli dostupné akce

Akce, které jsou k dispozici v přehledu plánu, se liší v závislosti na aktuálním stavu plánu. Mezi ně patří:

- **Odstranit koncept**: Pokud je stav plánu koncept.
- **Zastavit prodej plánu**: Pokud je stav plánu publikovaný živě

### <a name="create-new-plan"></a>Vytvořit nový plán

Vyberte **vytvořit nový plán**. Zobrazí se dialogové okno **Nový plán** .

V poli **ID plánu** Vytvořte jedinečné ID plánu pro každý plán v této nabídce. Toto ID bude pro zákazníky viditelné na webové adrese produktu. Používejte jenom malá písmena a číslice, pomlčky nebo podtržítka a maximálně 50 znaků.

Do pole **název plánu** zadejte název pro tento plán. Zákazníci uvidí tento název při rozhodování, který plán vybrat v rámci vaší nabídky. Pro každý plán v této nabídce Vytvořte jedinečný název. Můžete například použít název nabídky **Windows serveru** s plány **Windows Server 2016** a **Windows Server 2019**.

> [!NOTE]
> Po zvolení možnost **vytvořit**se ID plánu nedá změnit.

Vyberte **Vytvořit**.

### <a name="plan-setup"></a>Nastavení plánu

Tato karta vám umožní nakonfigurovat, které cloudy jsou v plánu dostupné. Vaše odpovědi na této kartě mají vliv na to, která pole se zobrazí na jiných kartách.

#### <a name="azure-regions"></a>Oblast Azure

Všechny plány pro nabídky modulu IoT Edge jsou automaticky dostupné v **globálním Azure**.  Váš plán můžou používat zákazníci ve všech globálních oblastech Azure, které využívají Marketplace. Podrobnosti najdete v tématu [geografická dostupnost a podpora měny](../marketplace-geo-availability-currencies.md).

Pokud chcete, aby se vaše řešení zobrazilo tady, vyberte možnost [Azure Government](../../azure-government/documentation-government-welcome.md) . Toto je Cloud komunity státní správy s řízeným přístupem pro zákazníky ze federálních, státních a místních nebo samosprávné vládních úřadů USA a partnerů, kteří jim mají nárok na jejich obsluhu. Jako vydavatel zodpovídáte za řízení dodržování předpisů, míry zabezpečení a osvědčené postupy pro tuto komunitu cloudu. Azure Government používá fyzicky izolovaná datová centra a sítě (jenom v USA). Před [publikováním](../../azure-government/documentation-government-manage-marketplace-partners.md) Azure Government, otestujte a potvrďte své řešení v rámci této oblasti, protože výsledky se můžou lišit. Pokud chcete své řešení připravit a testovat, vyžádejte si zkušební účet z [Microsoft Azure Government zkušební verze](https://azure.microsoft.com/global-infrastructure/government/request/).

> [!NOTE]
> Po publikování a dostupnosti plánu v konkrétní oblasti už tuto oblast nemůžete odebrat.

#### <a name="azure-government-certifications"></a>Azure Government certifikace

Tato možnost se zobrazí jenom v případě, že je v **oblasti Azure**vybraná **Azure Government** .

Služba Azure Government Services zpracovává data, která se vztahují na určité předpisy a požadavky státní správy. Příklad: FedRAMP, NIST 800,171 (DIB), ITAR, finanční úřad 1075, DoD – L4 a CJIS. Pro zajištění povědomí o certifikaci pro tyto programy můžete poskytnout až 100 odkazů, které popisují vaše certifikace. Můžou to být odkazy na vaše výpisy v programu přímo nebo na váš vlastní web. Tyto odkazy jsou viditelné pouze pro Azure Government zákazníky.

### <a name="plan-listing"></a>Výpis plánu

Tato karta zobrazuje konkrétní informace pro každý jiný plán v rámci stejné nabídky.

### <a name="plan-name"></a>Název plánu

Tento název se předem vyplní názvem, který jste zadali při vytváření svého plánu. Podle potřeby můžete tento název změnit. Může mít délku až 50 znaků. Tento název se zobrazí jako název tohoto plánu v Azure Marketplace a Azure Portal. Používá se jako výchozí název modulu po tom, co je plán připravený k použití.

### <a name="plan-summary"></a>Souhrn plánu

Zadejte stručný souhrn plánu (ne nabídky). Tento souhrn se zobrazí ve výsledcích hledání Azure Marketplace a může obsahovat až 100 znaků.

### <a name="plan-description"></a>Popis plánu

Popište, co dělá tento plán jedinečný, a rozdíly mezi plány v rámci vaší nabídky. Nepopisujte nabídku, jenom plán. Tento popis se zobrazí v Azure Marketplace a Azure Portal na stránce se seznamem nabídek. Může to být stejný obsah, který jste zadali v souhrnu plánu a který obsahuje až 2 000 znaků.

Po dokončení těchto polí vyberte **Uložit koncept** .

#### <a name="plan-examples"></a>Příklady plánů

Tady je příklad podrobností Azure Marketplace plánu (všechny uvedené ceny jsou například jenom pro účely a neodpovídají skutečným nákladům):

:::image type="content" source="media/example-iot-azure-marketplace-plan.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Název nabídky
2. Název plánu
3. Popis plánu

<br>Tady je příklad podrobností o Azure Portal plánu (všechny uvedené ceny jsou například jenom pro účely a nejsou určené k projevení skutečných nákladů):

:::image type="content" source="media/example-iot-azure-marketplace-plan-details.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Název nabídky
2. Název plánu
3. Popis plánu

### <a name="availability"></a>Dostupnost

Pokud chcete publikovat publikovanou nabídku, aby ji zákazníci nemohli vyhledat, procházet nebo koupit na webu Marketplace, zaškrtněte políčko **Skrýt plán** na kartě dostupnost.

Toto pole se běžně používá v těchto případech:

- Tato nabídka má být použita pouze nepřímo, pokud se odkazuje i na jinou aplikaci.
- Nabídka by se neměla koupit samostatně.
- Plán byl použit pro počáteční testování a již není relevantní.
- Plán byl použit pro dočasné nebo sezónní nabídky a již by neměl být nabídnut.

## <a name="technical-configuration"></a>Technická konfigurace

Typ nabídky **IoT Edge modul** je konkrétní typ kontejneru, který běží na IoT Edgem zařízení. Na kartě **Technická konfigurace** poskytnete referenční informace pro úložiště imagí kontejneru v rámci [Azure Container Registry](https://azure.microsoft.com/services/container-registry/), spolu s nastaveními konfigurace, která zákazníkům umožňují snadno používat modul.

Po publikování nabídky se IoT Edge image kontejneru zkopíruje do Azure Marketplace v konkrétním veřejném registru kontejneru. Všechny požadavky uživatelů Azure na použití vašeho modulu se obsluhují z Azure Marketplaceho veřejného kontejneru kontejnerů, nikoli z vašeho privátního registru kontejneru.

Můžete cílit na více platforem a zadat několik verzí imagí kontejneru modulu pomocí značek. Další informace o značkách a verzích najdete v tématu [Příprava technických prostředků modulu IoT Edge](create-iot-edge-module-asset.md).

### <a name="image-repository-details"></a>Podrobnosti o úložišti imagí

Na kartě **Podrobnosti o úložišti imagí** zadáte následující informace.

**Vyberte zdroj bitové kopie**: vyberte možnost **Azure Container Registry** .

**ID předplatného Azure**: Zadejte ID předplatného, kde se nahlásí využití prostředků, a účtují se služby Azure Container Registry, které obsahují vaši image kontejneru. Toto ID najdete na [stránce Předplatná](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) v Azure Portal.

**Název skupiny prostředků Azure**: zadejte název [skupiny prostředků](../../azure-resource-manager/management/manage-resource-groups-portal.md) , která obsahuje Azure Container Registry s vaší imagí kontejneru. Skupina prostředků musí být přístupná v ID předplatného (výše). Název můžete najít na stránce [skupiny prostředků](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) v Azure Portal.

**Název služby Azure Container Registry**: zadejte název [Azure Container Registry](../../container-registry/container-registry-intro.md) , který obsahuje vaši image kontejneru. V rámci skupiny prostředků Azure, kterou jste zadali dříve, se musí vyskytovat registr kontejnerů. Zadejte pouze název registru, nikoli úplný název přihlašovacího serveru. Nezapomeňte vynechat **azurecr.IO** z názvu. Název registru najdete na [stránce Registry kontejneru](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) v Azure Portal.

**Uživatelské jméno správce pro Azure Container Registry**: zadejte [uživatelské jméno správce](../../container-registry/container-registry-authentication.md#admin-account), které je přidružené k Azure Container Registry s vaší imagí kontejneru. Aby vaše společnost měla přístup k registru, je nutné zadat uživatelské jméno a heslo. Pokud chcete získat uživatelské jméno a heslo správce, nastavte vlastnost s **povoleným správcem** na **hodnotu true** pomocí rozhraní příkazového řádku (CLI) pro Azure Command-Line. Volitelně můžete nastavit **uživatele správce** na **Povolení** v Azure Portal.

:::image type="content" source="media/example-iot-update-container-registry.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-description"></a>Popis volání

1. Uživatel s rolí správce

<br>**Heslo pro Azure Container Registry**: zadejte heslo pro uživatelské jméno správce, které je přidružené k Azure Container registry a má vaši image kontejneru. Aby vaše společnost měla přístup k registru, je nutné zadat uživatelské jméno a heslo. Heslo z Azure Portal můžete získat tak, že v **Container Registry**  >  **přístupové klávesy** nebo pomocí Azure CLI použijete [příkaz show.](https://docs.microsoft.com/cli/azure/acr/credential?view=azure-cli-latest#az-acr-credential-show)

:::image type="content" source="media/example-iot-access-keys.png" alt-text="Ukazuje, jak se tato nabídka zobrazuje v Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Popisy volání

1. Přístupové klíče
2. Uživatelské jméno
3. Heslo

**Název úložiště v rámci Azure Container Registry**. Zadejte název úložiště Azure Container Registry, které obsahuje váš obrázek. Při odesílání obrázku do registru zadáte název úložiště. Název úložiště můžete najít tak, že kliknete na stránku [Container Registry](https://azure.microsoft.com/services/container-registry/)  >  **úložišť**. Další informace najdete v tématu [zobrazení úložišť registru kontejnerů v Azure Portal](../../container-registry/container-registry-repositories.md). Po nastavení je název nelze změnit. Pro každou nabídku ve vašem účtu použijte jedinečný název.

### <a name="image-tags-for-new-versions-of-your-offer"></a>Značky obrázků pro nové verze vaší nabídky

Zákazníci musí být schopni při publikování aktualizace automaticky získávat aktualizace z Azure Marketplace. Pokud se nechtějí aktualizovat, musí být schopní zůstat na konkrétní verzi image. To můžete provést přidáním nových značek obrázků pokaždé, když provedete aktualizaci image.

**Značka obrázku** V tomto poli musí být **nejnovější** značka, která odkazuje na nejnovější verzi image na všech podporovaných platformách. Musí také obsahovat značku verze (například počínaje xx. xx. xx, kde XX je číslo). Zákazníci by měli používat [značky manifestu](https://github.com/estesp/manifest-tool) k cílení na více platforem. Všechny značky, na které se odkazuje značka manifestu, musí být také přidané, aby je bylo možné nahrát. Všechny značky manifestu (s výjimkou nejnovější značky) musí začínat znakem X. Y nebo X. Y. Z-, kde X, Y a Z jsou celá čísla. Pokud například nejnovější značka odkazuje na 1.0.1-Linux-x64, 1.0.1-Linux-arm32 a 1.0.1-Windows-arm32, je nutné do tohoto pole přidat tyto šest značek. Podrobnosti o značkách a verzích najdete v tématu [Příprava technických prostředků modulu IoT Edge.](create-iot-edge-module-asset.md)

### <a name="default-deployment-settings-optional"></a>Výchozí nastavení nasazení (volitelné)

Zadejte nejběžnější nastavení pro nasazení modulu IoT Edge. Optimalizujte zákaznická nasazení tak, že jim umožníte spustit modul IoT Edge předem s těmito výchozími nastaveními.

**Výchozí trasy**. Centrum IoT Edge spravuje komunikaci mezi moduly, IoT Hub a zařízeními. Můžete nastavit trasy pro vstup a výstup dat mezi moduly a IoT Hub, což vám poskytne flexibilitu při posílání zpráv tam, kde je potřeba, aby se zprávy nemusely zpracovávat, a to bez nutnosti dalších služeb pro zpracování zpráv nebo psaní dalšího kódu. Trasy jsou vytvořené pomocí párů název/hodnota. Můžete definovat až pět názvů výchozích tras, každé až 512 znaků.

Nezapomeňte použít správnou [syntaxi směrování](../../iot-edge/module-composition.md#declare-routes)) ve vaší hodnotě trasy (obvykle definované jako from/Message/* do $upstream). To znamená, že všechny zprávy odesílané všemi moduly přecházejí do vašeho IoT Hub. Chcete-li se podívat na váš modul, použijte výchozí název modulu, který bude vaším **názvem nabídky**, bez mezer nebo speciálních znaků. Chcete-li se podívat na jiné moduly, které ještě nejsou známy, použijte <konvenci FROM_MODULE_NAME>, aby vaši zákazníci věděli, že potřebují aktualizovat tyto informace. Podrobnosti o IoT Edge trasách naleznete v tématu [Declare Routes](../../iot-edge/module-composition.md#declare-routes)).

Pokud například modul ContosoModule poslouchá vstupy na ContosoInput a výstupní data v ContosoOutput, má smysl definovat následující dvě výchozí trasy:

- Název #1: ToContosoModule
- Value #1: FROM/Messages/modules/<FROM_MODULE_NAME>/Outputs/* do BrokeredEndpoint ("/modules/ContosoModule/inputs/ContosoInput")
- Název #2: FromContosoModuleToCloud
- Value #2: z/messages/modules/ContonsoModule/outputs/ContosoOutput do $upstream

**Výchozí modul vyplňování požadovaných vlastností**. Nevlákenný modul je dokument JSON v IoT Hub, který ukládá informace o stavu pro instanci modulu, včetně požadovaných vlastností. Požadované vlastnosti se používají spolu s nahlášenými vlastnostmi pro synchronizaci konfigurace nebo podmínek modulu. Back-end řešení může nastavit požadované vlastnosti a modul je může číst. Modul může také přijímat oznámení o změnách v požadovaných vlastnostech. Požadované vlastnosti jsou vytvořené s použitím až pěti párů název/hodnota a každá výchozí hodnota musí být kratší než 512 znaků. Můžete definovat až pět požadovaných vlastností s dvojitou hodnotou názvu nebo hodnoty. Hodnoty vlákenných požadovaných vlastností musí být platné JSON, bez řídicích znaků, bez polí s maximální vnořenou hierarchií čtyř úrovní. V případě, že parametr vyžadovaný pro výchozí hodnotu nemá smysl (například IP adresa serveru zákazníka), můžete jako výchozí hodnotu přidat parametr. Další informace o vyplňování požadovaných vlastností najdete v tématu [definice nebo aktualizace požadovaných vlastností](../../iot-edge/module-composition.md#define-or-update-desired-properties)).

Například pokud modul podporuje dynamickou konfigurovatelnou obnovovací frekvenci pomocí požadovaných vlastností, má smysl definovat následující výchozí vlastnost s dvojitou hodnotou:

- Název #1: RefreshRate
- Hodnota #1:60

**Výchozí proměnné prostředí**. Proměnné prostředí poskytují doplňkové informace modulu, který pomáhá procesu konfigurace. Proměnné prostředí se vytvářejí pomocí párů název/hodnota. Každý výchozí název proměnné prostředí a hodnota musí být kratší než 512 znaků a můžete definovat až pět. Pokud parametr vyžadovaný pro výchozí hodnotu nemá smysl (například IP adresa serveru zákazníka), můžete přidat parametr jako výchozí hodnotu.

Například pokud modul vyžaduje, aby před zahájením přijímal podmínky použití, můžete definovat následující proměnnou prostředí:

- Název #1: ACCEPT_EULA
- Hodnota #1: Y

**Možnosti vytváření výchozích kontejnerů** Možnosti vytvoření kontejneru směrují vytvoření kontejneru Docker modulu IoT Edge. IoT Edge podporuje možnosti vytvoření kontejneru rozhraní API pro modul Docker. Podívejte se na všechny možnosti v [kontejnerech seznamu.](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList) V poli vytvořit možnosti musí být platný kód JSON, jiný než řídicí znak a méně než 512 znaků.

Například pokud modul vyžaduje vazbu portu, definujte následující možnosti vytváření:

"HostConfig": {"PortBindings": {"5012/TCP": [{"HostPort": "5012"}]}

## <a name="review-and-publish"></a>Kontrola a publikování

Po dokončení všech požadovaných oddílů nabídky ji můžete odeslat pro kontrolu a publikování.

V pravém horním rohu portálu vyberte **zkontrolovat a publikovat**.

Na stránce recenze uvidíte stav publikování:

- Podívejte se na téma stav dokončení pro jednotlivé části nabídky. Dokud nebudou všechny oddíly nabídky označeny jako úplné, nelze publikovat.
    - **Nespuštěno** – oddíl se nespustil a musí se dokončit.
    - **Nedokončeno** – oddíl obsahuje chyby, které je třeba opravit, nebo vyžaduje zadání dalších informací. Pokyny najdete v předchozích částech tohoto dokumentu.
    - **Complete** – oddíl obsahuje všechna požadovaná data a nejsou k dispozici žádné chyby. Aby bylo možné nabídku odeslat, musí být všechny oddíly nabídky dokončené.
- Poskytněte pokyny pro testování certifikačního týmu, abyste měli jistotu, že vaše nabídka je testována správně. Poskytněte také všechny doplňkové poznámky, které jsou užitečné pro porozumění vaší nabídce.

Pokud chcete odeslat nabídku pro publikování, vyberte **publikovat**.

Pošleme vám e-mail s oznámením, že je k dispozici verze Preview této nabídky ke kontrole a schválení. Pokud chcete svou nabídku publikovat na veřejném, klikněte na partnerské centrum a vyberte **jít-Live**.

## <a name="next-steps"></a>Další kroky

- [Aktualizace stávající nabídky na komerčním webu Marketplace](https://docs.microsoft.com//azure/marketplace/partner-center-portal/update-existing-offer)
