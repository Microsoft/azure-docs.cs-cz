---
title: Uspořádání vaší faktury podle svých potřeb – Azure
description: Naučte se organizovat náklady na fakturu.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: c170a6a1a731a648c16e0081e760947256df8a0e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75991058"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Přizpůsobení nákladů pomocí přizpůsobení fakturačního účtu

Fakturační účet pro zákaznickou smlouvu od Microsoftu poskytuje flexibilitu v závislosti na vašich potřebách, ať už se jedná o oddělení, projekt nebo vývojové prostředí. 

Tento článek popisuje, jak můžete pomocí Azure Portal organizovat své náklady. Vztahuje se na fakturační účet smlouvy Microsoft Customer Agreement. [Ověřte si, jestli máte přístup ke smlouvě se zákazníkem Microsoftu](#check-access-to-a-microsoft-customer-agreement).

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strukturování účtu pomocí fakturačních profilů a oddílů faktur

V rámci fakturačního účtu smlouvy o zákaznících Microsoftu použijete fakturační profily a oddíly faktury k uspořádání vašich nákladů.

![Snímek obrazovky zobrazující hierarchii fakturace Microsoft Customer Agreement](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Fakturační profil

Fakturační profil představuje fakturu a související fakturační informace, jako jsou způsoby platby a fakturační adresa. Na začátku kalendářního měsíce se pro každý fakturační profil na vašem účtu vygeneruje měsíční faktura. Faktura obsahuje poplatky za využití Azure a další nákupy za předchozí měsíc.

Fakturační profil se při registraci do Azure automaticky vytvoří společně s fakturačním účtem. Můžete vytvořit další profily fakturace pro uspořádání nákladů v rámci více měsíčních faktur. 

> [!IMPORTANT]
>
> Vytváření dalších profilů fakturace může mít vliv na vaše celkové náklady. Další informace najdete v tématu [co je potřeba vzít v úvahu při přidávání nových fakturačních profilů](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Oddíl faktury

Oddíl faktury představuje seskupení nákladů ve vaší faktuře. Oddíl faktury se automaticky vytvoří pro každý Fakturační profil ve vašem účtu. Můžete vytvořit další oddíly, které budou organizovat náklady podle vašich potřeb. Každý oddíl faktury se zobrazí na faktuře s poplatky za tento měsíc. 

Následující obrázek ukazuje fakturu se dvěma částmi faktury – strojírenství a marketing. Souhrnné a podrobné poplatky za jednotlivé oddíly se zobrazí na faktuře. Ceny zobrazené na obrázku jsou například jenom pro účely a nepředstavují skutečné ceny služeb Azure. 

![Obrázek znázorňující fakturu s oddíly](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Struktura fakturačního účtu pro běžné scénáře

Tato část popisuje běžné scénáře pro uspořádání nákladů a odpovídajících struktur fakturačních účtů:

|Scénář  |Struktura  |
|---------|---------|
|Zaregistruje se konektor pro Azure a potřebuje jednu měsíční fakturu. | Fakturační profil a oddíl faktury. Tato struktura je automaticky nastavená na zásuvku, když se zaregistruje do Azure a nevyžaduje žádné další kroky. |

![Informační grafika pro jednoduchý scénář fakturace](./media/mca-section-invoice/organize-billing-scenario1.png)

|Scénář  |Struktura  |
|---------|---------|
|Contoso je malá organizace, která potřebuje jednu měsíční fakturu, ale náklady na skupinu podle jejich oddělení – marketing a finance.  | Fakturační profil pro contoso a část faktury pro marketingové a finanční oddělení. |

![Informační grafika pro jednoduchý scénář fakturace](./media/mca-section-invoice/organize-billing-scenario2.png)

|Scénář  |Struktura  |
|---------|---------|
|Společnost Fabrikam je organizací střední velikosti, která potřebuje samostatné faktury pro své technické a marketingové oddělení. Pro technické oddělení chtějí seskupit náklady podle prostředí – výroby a vývoje.  | Fakturační profil každého pro marketingové a finanční oddělení. V případě marketingového oddělení každé faktury pro produkční a vývojové prostředí. |

![Informační grafika pro jednoduchý scénář fakturace](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Vytvořit novou část faktury

Pokud chcete vytvořit oddíl faktury, musíte být **vlastníkem fakturačního profilu** nebo **přispěvatelem fakturačního profilu**. Další informace najdete v tématu [Správa fakturačních oddílů pro fakturační profil](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-section-invoice/search-cmb.png)

3. V levém podokně vyberte **profily fakturace** . V seznamu vyberte fakturační profil. Nový oddíl se zobrazí na faktuře vybraného fakturačního profilu. 

   [![snímek obrazovky, který zobrazuje seznam profilů fakturace](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. V levém podokně vyberte **části faktury** a v horní části stránky vyberte **Přidat** .

   [![snímek obrazovky, který zobrazuje oddíly přidání faktury](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Zadejte název oddílu faktury. 

   [![snímek obrazovky, který ukazuje stránku pro vytvoření oddílu faktury](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Vyberte **Vytvořit**.

## <a name="create-a-new-billing-profile"></a>Vytvořit nový fakturační profil

Chcete-li vytvořit fakturační profil, musíte být **vlastníkem fakturačního účtu** nebo **přispěvatelem fakturačního účtu**. Další informace najdete v tématu [Správa fakturačních profilů pro fakturační účet](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Vytváření dalších profilů fakturace může mít vliv na vaše celkové náklady. Další informace najdete v tématu [co je potřeba vzít v úvahu při přidávání nových fakturačních profilů](#things-to-consider-when-adding-new-billing-profiles).

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky znázorňující hledání položky Cost Management a fakturace](./media/mca-section-invoice/search-cmb.png)

3. V levém podokně vyberte **profily fakturace** a v horní části stránky vyberte **Přidat** .

   [![snímek obrazovky, který zobrazuje seznam profilů fakturace](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Pokud na stránce profil fakturace nevidíte tlačítko Přidat, tato funkce není pro váš účet k dispozici. V současné době je k dispozici pouze pro účty, které byly nastaveny při práci se zástupcem společnosti Microsoft.

4. Vyplňte formulář a klikněte na **Vytvořit**.

   [![snímek obrazovky, který ukazuje stránku vytvoření profilu fakturace](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Pole  |Definice  |
    |---------|---------|
    |Name (Název)     | Zobrazovaný název, který vám pomůže snadno identifikovat Fakturační profil v Azure Portal.  |
    |Číslo nákupní objednávky    | Volitelné číslo nákupní objednávky. Číslo nákupní objednávky se zobrazí na fakturách vygenerovaných pro fakturační profil. |
    |Fakturační adresa   | Fakturační adresa se zobrazí na fakturách vygenerovaných pro fakturační profil. |
    |E-mailová faktura   | Zaškrtněte pole e-mailová faktura, abyste obdrželi faktury za tento Fakturační profil e-mailem. Pokud se nerozhodnete, můžete faktury zobrazit a stáhnout v Azure Portal.|

5. Vyberte **Vytvořit**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Propojení poplatků s oddíly faktury a profily fakturace

Po přizpůsobení fakturačního účtu podle svých potřeb můžete propojit předplatná a další produkty s požadovaným oddílem faktury a fakturačním profilem.

### <a name="link-a-new-subscription"></a>Propojit nové předplatné

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Předplatná**.

   [![snímek obrazovky, který zobrazuje hledání na portálu pro předplatné](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. V horní části stránky vyberte **Přidat**.

   ![Snímek obrazovky, který zobrazuje tlačítko Přidat v zobrazení Předplatná](./media/mca-section-invoice/subscription-add.png)

4. Pokud máte přístup k několika fakturačním účtům, vyberte fakturační účet Microsoft Customer Agreement.

   ![Snímek obrazovky, který zobrazuje tlačítko Přidat v zobrazení Předplatná](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Vyberte fakturační profil, který se bude účtovat za použití předplatného. Poplatky za využití Azure a další nákupy pro toto předplatné se budou fakturovat na fakturu vybraného fakturačního profilu.

6. Pokud chcete propojit poplatky za předplatné, vyberte část faktura. Poplatky se budou zobrazovat v této části na faktuře s fakturačním profilem.

7. Vyberte plán Azure a zadejte popisný název vašeho předplatného. 

9. Klikněte na **Vytvořit**.  

### <a name="link-existing-subscriptions-and-products"></a>Propojení stávajících předplatných a produktů

Pokud máte existující předplatné Azure nebo jiné produkty, například Azure Marketplace a zdrojové prostředky aplikace, můžete je přesunout z jejich části stávající faktury do jiné faktury a změnit uspořádání vašich nákladů. 

> [!IMPORTANT]
>
> Odběry a další produkty lze přesunout pouze mezi oddíly faktury patřící do stejného fakturačního profilu. Přesun předplatných a produktů napříč fakturačními oddíly v různých profilech fakturace se nepodporuje.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).

2. Vyhledejte **Cost Management a fakturace**.

   ![Snímek obrazovky, který zobrazuje hledání předplatného na portálu](./media/mca-section-invoice/search-cmb.png)

3. Pokud chcete propojit předplatné s novou fakturou, vyberte **předplatná Azure** na levé straně obrazovky. Pro jiné produkty, jako jsou Azure Marketplace a zdrojové prostředky aplikací, vyberte možnost **periodické poplatky**.

   [![snímek obrazovky, který ukazuje možnost změny oddílu fakturace](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section.png#lightbox)

4. Na stránce klikněte na tři tečky (tři tečky) pro předplatné nebo produkt, které chcete propojit s částí nové faktury. Vyberte **část změnit fakturu**.

5. V rozevíracím seznamu vyberte část nová faktura. V rozevíracím seznamu se zobrazí pouze části faktury, které jsou přidruženy ke stejnému fakturačnímu profilu jako v části stávající faktura.

    [![snímek obrazovky, který zobrazuje výběr oddílu nové faktury](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Vyberte **Uložit**.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Věci, které je potřeba vzít v úvahu při přidávání nových fakturačních profilů

### <a name="azure-usage-charges-may-be-impacted"></a>Můžou to ovlivnit poplatky za využití Azure.

Ve vašem fakturačním účtu smlouvy o zákaznících Microsoftu se využití Azure agreguje měsíčně za každý Fakturační profil. Ceny za prostředky Azure s vrstvenými cenami se určují na základě využití jednotlivých fakturačních profilů samostatně. Při výpočtu ceny není využití v profilech fakturace agregované. To může mít vliv na celkové náklady na využití Azure u účtů s více fakturačními profily.

Pojďme se podívat na příklad toho, jak se u dvou scénářů liší náklady. Ceny použité v těchto scénářích slouží například jenom pro účely a nepředstavují skutečné ceny služeb Azure.

#### <a name="you-only-have-one-billing-profile"></a>Máte jenom jeden Fakturační profil.

Předpokládejme, že používáte úložiště objektů blob bloku Azure, což vede k 00184 za GB za prvních 50 terabajtů (TB) a pak. 00177 za GB pro další 450 terabajty (TB). V předplatných, které se fakturují do vašeho fakturačního profilu, jste použili 100 TB, v takovém případě se vám bude účtovat.

|  Ceny vrstev (USD) |Množství | Množství (USD)|
|---------|---------|---------|
|1,84 za TB za prvních 50 TB/měsíc    | 50 TB        | 92,0   |
|1,77 za TB pro další 450 TB/měsíc    |  50 TB         | 88,5   |
|Celkem     |     100 TB  | 180,5

Celkové poplatky za používání 100 TB dat v tomto scénáři jsou **180,5**

#### <a name="you-have-multiple-billing-profiles"></a>Máte několik profilů fakturace.

Nyní předpokládejme, že jste vytvořili jiný Fakturační profil a používali 50 GB prostřednictvím předplatných, která se účtují do prvního fakturačního profilu a 50 GB prostřednictvím předplatných, která se účtují do druhého fakturačního profilu, a to podle toho, kolik vám se vám bude účtovat.

`Charges for the first billing profile`

|  Ceny vrstev (USD) |Množství | Množství (USD)|
|---------|---------|---------|
|1,84 za TB za prvních 50 TB/měsíc    | 50 TB        | 92,0  |
|1,77 za TB pro další 450 TB/měsíc    |  0 TB         | 0.0  |
|Celkem     |     50 TB  | 92,0 

`Charges for the second billing profile`

|  Ceny vrstev (USD) |Množství | Množství (USD)|
|---------|---------|---------|
|1,84 za TB za prvních 50 TB/měsíc    | 50 TB        | 92,0  |
|1,77 za TB pro další 450 TB/měsíc    |  0 TB         | 0.0  |
|Celkem     |     50 TB  | 92,0 

Celkové poplatky za používání 100 TB dat v tomto scénáři jsou **184,0** (92,0 * 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Výhody rezervací Azure se nemůžou vztahovat na všechna předplatná.

Rezervace Azure se sdíleným oborem se používají u předplatných v jednom fakturačním profilu a nesdílí se mezi fakturačními profily. 

![Informační grafika pro aplikaci rezervací pro odlišnou strukturu fakturačního účtu](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

Ve výše uvedeném obrázku má společnost Contoso dva odběry. Zvýhodněné rezervace Azure se uplatní různě v závislosti na tom, jak je účet pro fakturaci strukturovaný. Ve scénáři na levé straně se výhoda rezervace použije u předplatných, která se účtují do technického fakturačního profilu. Ve scénáři na pravé straně se zvýhodněná rezervace použije jenom na předplatné 1, protože se jedná o jediné předplatné, které se účtuje do technického fakturačního profilu. 

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrola přístupu k zákaznické smlouvě Microsoftu
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontakty na podporu

Pokud potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.

## <a name="next-steps"></a>Další kroky

- [Vytvoření dalšího předplatného Azure pro smlouvu se zákazníkem Microsoftu](create-subscription.md)
- [Správa fakturačních rolí na webu Azure Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Získání role vlastníka fakturace pro předplatná Azure od uživatelů v jiných fakturačních účtech](mca-request-billing-ownership.md)
