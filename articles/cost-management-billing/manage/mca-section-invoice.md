---
title: Uspořádání faktury podle vašich potřeb – Azure
description: Zjistěte, jak uspořádat náklady na faktuře. Fakturační účet si můžete přizpůsobit vytvořením fakturačních profilů a oddílů faktury.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 6424fc0ff49566fad949b3fba4718acb2bad4cd3
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362763"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Uspořádání nákladů přizpůsobením fakturačního účtu

Fakturační účet pro smlouvu se zákazníkem Microsoftu poskytuje flexibilitu uspořádání nákladů podle vašich potřeb, ať už podle oddělení, projektu nebo vývojového prostředí.

Tento článek popisuje, jak můžete pomocí webu Azure Portal uspořádat náklady. Týká se fakturačního účtu smlouvy se zákazníkem Microsoftu. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strukturujte účet pomocí fakturačních profilů a oddílů faktur

Na fakturačním účtu pro smlouvu se zákazníkem Microsoftu používáte fakturační profily a oddíly faktury k uspořádání nákladů.

![Snímek obrazovky zobrazující hierarchii fakturace smlouvy se zákazníkem Microsoftu](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Fakturační profil

Fakturační profil představuje fakturu a související fakturační informace, jako jsou způsoby platby a fakturační adresa. Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Ta obsahuje poplatky za využití Azure a další nákupy z předchozího měsíce.

Fakturační profil se automaticky vytvoří společně s fakturačním účtem při registraci do Azure. Můžete vytvořit další fakturační profily a uspořádat náklady v rámci několika měsíčních faktur.

> [!IMPORTANT]
>
> Vytváření dalších fakturačních profilů může mít vliv na celkové náklady. Další informace najdete v tématu [Aspekty, které je potřeba vzít v úvahu při přidávání nových fakturačních profilů](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Oddíl faktury

Oddíl faktury představuje seskupení nákladů na faktuře. Oddíl faktury se automaticky vytvoří pro každý fakturační profil ve vašem účtu. Můžete vytvořit další oddíly pro uspořádání nákladů podle vašich potřeb. Každý oddíl faktury se zobrazuje na faktuře s poplatky vzniklými v daném měsíci.

Následující obrázek ukazuje fakturu se dvěma oddíly faktury – pro technické oddělení a marketing. Souhrn a podrobné poplatky pro každý oddíl se zobrazují na faktuře. Ceny zobrazené na obrázku jsou pouze pro ukázku a nepředstavují skutečné ceny služeb Azure.

![Obrázek znázorňující fakturu s oddíly](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Struktura fakturačního účtu pro běžné scénáře

Tato část popisuje běžné scénáře uspořádání nákladů a odpovídající struktury fakturačních účtů:

|Scénář  |Struktura  |
|---------|---------|
|Honza si zaregistruje služby Azure a potřebuje jednu měsíční fakturu. | Fakturační profil a oddíl faktury. Tato struktura se pro Honzu automaticky nastaví, když se zaregistruje do Azure a nevyžaduje žádné další kroky. |

![Infografika pro scénář jedné fakturace](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scénář  |Struktura  |
|---------|---------|
|Contoso je malá organizace, která potřebuje jednu měsíční fakturu, ale seskupuje náklady podle oddělení – marketingového a technického oddělení.  | Fakturační profil pro Contoso a oddíly faktury pro marketingové a technické oddělení. |

![Infografika pro scénář více fakturací](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scénář  |Struktura  |
|---------|---------|
|Společnost Fabrikam je organizací střední velikosti, která potřebuje samostatné faktury pro technické a marketingové oddělení. Pro technické oddělení chtějí seskupit náklady podle prostředí – produkčního a vývojového.  | Fakturační profil pro marketingové a technické oddělení. Technické oddělení má oddíly faktury pro produkční a vývojové prostředí. |

![Infografika pro scénář více fakturací s oddělenou fakturací za produkční a vývojové prostředí](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Vytvoření nového oddílu faktury

Pokud chcete vytvořit oddíl faktury, musíte být **vlastníkem fakturačního profilu** nebo **přispěvatelem fakturačního profilu**. Další informace najdete v tématu [Správa oddílů faktur pro fakturační profil](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-section-invoice/search-cmb.png)

3. V levém podokně vyberte **Fakturační profily**. V seznamu vyberte fakturační profil. Nový oddíl se zobrazí na faktuře vybraného fakturačního profilu.

   [![Snímek obrazovky se seznamem fakturačních profilů](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. V levém podokně vyberte **Oddíly faktury** a pak v horní části stránky vyberte **Přidat**.

   [![Snímek obrazovky znázorňující přidání oddílů faktury](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Zadejte název oddílu faktury.

   [![Snímek obrazovky, který zobrazuje stránku pro vytvoření oddílu faktury](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Vyberte **Vytvořit**.

## <a name="create-a-new-billing-profile"></a>Vytvoření nového fakturačního profilu

Pokud chcete vytvořit fakturační profil, musíte být **vlastníkem fakturačního účtu** nebo **přispěvatelem fakturačního účtu**. Další informace najdete v tématu [Správa fakturačních profilů pro fakturační účet](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Vytváření dalších fakturačních profilů může mít vliv na celkové náklady. Další informace najdete v tématu [Aspekty, které je potřeba vzít v úvahu při přidávání nových fakturačních profilů](#things-to-consider-when-adding-new-billing-profiles).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-section-invoice/search-cmb.png)

3. V levém podokně vyberte **Fakturační profily** a pak v horní části stránky vyberte **Přidat**.

   [![Snímek obrazovky se seznamem fakturačních profilů a vybranou možností Přidat](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Pokud na stránce fakturačního profilu nevidíte tlačítko Přidat, není tato funkce pro váš účet k dispozici. V současné době je k dispozici pouze pro účty, které byly nastaveny při práci se zástupcem společnosti Microsoft.

4. Vyplňte formulář a klikněte na **Vytvořit**.

   [![Snímek obrazovky se stránkou pro vytvoření fakturačního profilu](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Pole  |Definice  |
    |---------|---------|
    |Název     | Zobrazovaný název umožňující snadnou identifikaci fakturačního profilu na webu Azure Portal  |
    |Číslo objednávky    | Volitelné číslo nákupní objednávky Číslo nákupní objednávky se zobrazí na fakturách vygenerovaných pro fakturační profil. |
    |Fakturační adresa   | Fakturační adresa se zobrazí na fakturách vygenerovaných pro fakturační profil. |
    |Poslat fakturu e-mailem   | Zaškrtněte pole pro poslání faktury e-mailem, abyste faktury pro tento fakturační profil dostávali e-mailem. Pokud s tím nesouhlasíte, můžete si faktury zobrazit a stáhnout na webu Azure Portal.|

5. Vyberte **Vytvořit**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Propojení poplatků s oddíly faktury a fakturačními profily

Jakmile přizpůsobíte fakturační účet svým potřebám, můžete propojit předplatná a další produkty s požadovaným oddílem faktury a fakturačním profilem.

### <a name="link-a-new-subscription"></a>Propojení nového předplatného

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Předplatná**.

   [![Snímek obrazovky znázorňující hledání předplatného na webu Azure Portal](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. V horní části stránky vyberte **Přidat**.

   ![Snímek obrazovky s tlačítkem Přidat v zobrazení Předplatná pro přidání nového předplatného](./media/mca-section-invoice/subscription-add.png)

4. Pokud máte přístup k více fakturačním účtům, vyberte fakturační účet pro vaši smlouvu se zákazníkem Microsoftu.

   ![Snímek obrazovky s podoknem Vytvořit předplatné](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Vyberte fakturační profil, kterému se bude účtovat za využití předplatného. Poplatky za využití Azure a další nákupy pro toto předplatné se budou účtovat na fakturu vybraného fakturačního profilu.

6. Pokud chcete propojit poplatky za předplatné, vyberte oddíl faktury. Poplatky se budou zobrazovat v tomto oddílu na faktuře s fakturačním profilem.

7. Vyberte plán Azure a zadejte popisný název vašeho předplatného.

9. Klikněte na možnost **Vytvořit**.  

### <a name="link-existing-subscriptions-and-products"></a>Propojení stávajících předplatných a produktů

Pokud máte existující předplatná Azure nebo jiné produkty, například Azure Marketplace a zdrojové prostředky aplikace, můžete je přesunout z jejich stávajícího oddílu faktury do jiného oddílu faktury a změnit tak uspořádání nákladů.

> [!IMPORTANT]
>
> Předplatná a další produkty je možné přesouvat pouze mezi oddíly faktury, které náleží do stejného fakturačního profilu. Přesouvání předplatných a produktů do oddílů faktury v různých fakturačních profilech se nepodporuje.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management + Billing na webu Azure Portal](./media/mca-section-invoice/search-cmb.png)

3. Pokud chcete propojit předplatné s novým oddílem faktury, vyberte v levé části obrazovky **Předplatná Azure**. Pro jiné produkty, jako jsou Azure Marketplace a zdrojové prostředky aplikací, vyberte **Pravidelné poplatky**.

   [![Snímek obrazovky znázorňující možnost změny oddílu fakturace](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Na stránce klikněte na tři tečky u předplatného nebo produktu, které chcete propojit s novým oddílem faktury. Vyberte **Změnit oddíl faktury**.

5. V rozevíracím seznamu vyberte nový oddíl faktury. V rozevíracím seznamu se zobrazí pouze oddíly faktury, které jsou přidružené stejnému fakturačnímu profilu jako stávající oddíl faktury.

    [![Snímek obrazovky znázorňující výběr nového oddílu faktury](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Vyberte **Uložit**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Aspekty, které je potřeba vzít v úvahu při přidávání nových fakturačních profilů

### <a name="azure-usage-charges-may-be-impacted"></a>Mohou být ovlivněny poplatky za využití Azure

Na fakturačním účtu za smlouvu se zákazníkem Microsoftu se každý měsíc pro každý fakturační profil agreguje využití Azure. Ceny za prostředky Azure s cenami po úrovních se určují na základě využití pro jednotlivé fakturační profily samostatně. Při výpočtu ceny se využití neagreguje napříč fakturačními profily. To může mít vliv na celkové náklady na využití Azure u účtů s více fakturačními profily.

Pojďme se podívat na příklad toho, jak se u dvou scénářů liší náklady. Ceny použité ve scénářích jsou pouze pro ukázku a nepředstavují skutečné ceny služeb Azure.

#### <a name="you-only-have-one-billing-profile"></a>Máte jenom jeden fakturační profil.

Předpokládejme, že používáte úložiště objektů blob bloku Azure, které stojí 0,00184 USD za GB pro prvních 50 terabajtů (TB) a potom 0,00177 za GB pro dalších 450 terabajtů (TB). V předplatných, která se fakturují do vašeho fakturačního profilu, jste použili 100 TB. Účtovaly by se vám následující poplatky.

|  Ceny po úrovních (USD) |Množství | Množství (USD)|
|---------|---------|---------|
|1,84 za TB pro prvních 50 TB/měsíc    | 50 TB        | 92,0   |
|1,77 za TB pro dalších 450 TB/měsíc    |  50 TB         | 88,5   |
|Celkem     |     100 TB  | 180,5

Celkové poplatky za použití 100 TB dat v tomto scénáři jsou **180,5**.

#### <a name="you-have-multiple-billing-profiles"></a>Máte několik fakturačních profilů.

Nyní předpokládejme, že jste vytvořili jiný fakturační profil a využili 50 GB prostřednictvím předplatných, která se účtují do prvního fakturačního profilu, a 50 GB prostřednictvím předplatných, která se účtují do druhého fakturačního profilu. Budou vám účtovány následující poplatky.

`Charges for the first billing profile`

|  Ceny po úrovních (USD) |Množství | Množství (USD)|
|---------|---------|---------|
|1,84 za TB pro prvních 50 TB/měsíc    | 50 TB        | 92,0  |
|1,77 za TB pro dalších 450 TB/měsíc    |  0 TB         | 0,0  |
|Celkem     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Ceny po úrovních (USD) |Množství | Množství (USD)|
|---------|---------|---------|
|1,84 za TB pro prvních 50 TB/měsíc    | 50 TB        | 92,0  |
|1,77 za TB pro dalších 450 TB/měsíc    |  0 TB         | 0,0  |
|Celkem     |     50 TB  | 92,0

Celkové poplatky za použití 100 TB dat v tomto scénáři jsou **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Výhody rezervací Azure se nemusí vztahovat na všechna předplatná

Rezervace Azure se sdíleným oborem se používají u předplatných v jednom fakturačním profilu a nesdílí se mezi fakturačními profily.

![Informační grafika pro aplikaci rezervací pro jinou strukturu fakturačního účtu](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Na výše uvedeném obrázku má společnost Contoso dvě předplatná. Výhoda rezervace Azure se používá různě v závislosti na tom, jak je fakturační účet strukturovaný. Ve scénáři na levé straně se výhoda rezervace použije u obou předplatných, která se účtují do technického fakturačního profilu. Ve scénáři na pravé straně se výhoda rezervace použije pouze u předplatného 1, protože se jedná o jediné předplatné, které se účtuje do technického fakturačního profilu.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vytvoření dalšího předplatného Azure pro smlouvu se zákazníkem Microsoftu](create-subscription.md)
- [Správa fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Získání role vlastníka fakturace pro předplatná Azure od uživatelů v jiných fakturačních účtech](mca-request-billing-ownership.md)
