---
title: Přidělování nákladů na Azure
description: Tento článek vysvětluje vytváření pravidla alokace nákladů pro distribuci nákladů na předplatná, skupiny prostředků nebo značky.
author: bandersmsft
ms.author: banders
ms.date: 03/23/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: benshy
ms.openlocfilehash: 414ce626d76c9b7a7d073d6cbfa5a5f4446c3073
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025522"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Vytváření a správa pravidel alokace nákladů na Azure (Preview)

Velké podniky často mají služby nebo prostředky Azure, které jsou centrálně spravované, ale využívají je různá interní oddělení nebo obchodní jednotky. Centrální tým správy obvykle chce náklady na sdílené služby přerozdělit zpátky mezi interní oddělení nebo organizační obchodní jednotky, které tyto služby aktivně využívají. Tento článek vám pomůže pochopit a využívat alokaci nákladů ve službě Azure Cost Management.

V rámci alokace nákladů můžete přerozdělit nebo distribuovat náklady na sdílené služby z předplatných, skupin prostředků nebo značek na jiná předplatná, skupiny prostředků nebo značky ve vaší organizaci. Alokace nákladů přesouvá náklady na sdílené služby na jiné předplatné, skupiny prostředků nebo značky vlastněné interními odděleními nebo obchodními jednotkami, které tyto služby využívají. Jinými slovy, alokace nákladů pomáhá spravovat a demonstrovat _zodpovědnost za náklady_ mezi jednotlivými místy.

Alokace nákladů nemá vliv na vaši fakturu. Odpovědnost za vyúčtování se nemění. Primárním účelem alokace nákladů je pomoc při zpětném vyúčtování nákladů. Veškeré procesy vratek probíhají ve vaší organizaci mimo Azure. Alokace nákladů pomáhá přeúčtovat náklady tím, že se zobrazí jejich přerozdělení nebo distribuce.

Přidělené náklady se zobrazují v analýzách nákladů. Zobrazují se jako další položky spojené s cílovými předplatnými, skupinami prostředků nebo značkami, které zadáte při vytváření pravidla alokace nákladů.

> [!NOTE]
> Funkce alokace nákladů služby Azure Cost Management je momentálně ve verzi Public Preview. Některé funkce ve službě Cost Management nemusí být podporované nebo mohou mít omezené možnosti.

## <a name="prerequisites"></a>Předpoklady

- Alokace nákladů aktuálně podporuje zákazníky, kteří mají uzavřenou [Smlouvu se zákazníkem Microsoftu](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) nebo [smlouvu Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/).
- Pokud chcete vytvořit nebo spravovat pravidlo alokace nákladů, musíte použít účet podnikového správce pro [smlouvu Enterprise](../manage/understand-ea-roles.md). Nebo musíte být vlastníkem [fakturačního účtu](../manage/understand-mca-roles.md) pro Smlouvu se zákazníkem Microsoftu.

## <a name="create-a-cost-allocation-rule"></a>Vytvoření pravidla alokace nákladů

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com/).
2. Přejděte do části **Správa nákladů a fakturace** > **Správa nákladů**.
3. V části **Nastavení** > **Konfigurace** vyberte **Alokace nákladů (Preview)** .
4. Ujistěte se, že je vybraná správná registrace EA nebo fakturační účet.
5. Vyberte **+Přidat**.
6. Zadejte popisný text pro název pravidla alokace nákladů.

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="Příklad ukazující vytvoření názvu pravidla" lightbox="./media/allocate-costs/rule-name.png" :::

Datum zahájení vyhodnocení pravidla se použije k vygenerování předem vyplněných procentuálních hodnot alokace nákladů.

1. Vyberte **Přidat zdroje** a potom vyberte předplatná, skupiny prostředků nebo značky a zvolte náklady k distribuci.
2. Vyberte **Přidat cíle** a potom vyberte předplatná, skupiny prostředků nebo značky, které dostanou přidělené náklady.
3. Pokud potřebujete vytvořit další pravidla alokace nákladů, zopakujte tento postup.

## <a name="configure-the-allocation-percentage"></a>Konfigurace procent alokace

Nakonfigurujte procenta alokace a definujte poměrné rozdělení nákladů mezi zadané cíle. Při vytváření pravidla alokace můžete ručně definovat procentuální podíly v celých číslech. Nebo můžete náklady rozdělit poměrně na základě aktuálního využití výpočetních prostředků, úložiště nebo sítě napříč zadanými cíli.

Při distribuci nákladů podle nákladů na výpočetní funkce, nákladů na úložiště nebo nákladů na síť se procentní podíly odvodí vyhodnocením nákladů vybraných cílů. Náklady jsou přidružené k typu prostředku pro aktuální fakturační měsíc.

Při distribuci nákladů úměrně celkovým nákladům se proporcionální počet procent určuje na základě součtu nebo celkových nákladů na vybrané cíle pro aktuální fakturační měsíc.

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="Příklad ukazující procento přidělení" lightbox="./media/allocate-costs/cost-distribution.png" :::

Po nastavení se předem určená procenta definují jako fixní. Používají se pro všechny průběžné alokace. Procenta se mění jenom při ruční aktualizaci pravidla.

1. V seznamu **Předem vyplnit procento na** vyberte jednu z následujících možností.
    - **Distribuovat rovnoměrně:** Každý z cílů bude mít stejný počet procent z celkových nákladů.
    - **Celkové náklady:** Vytvoří se poměr úměrný cílům na základě jejich celkových nákladů. Tento poměr se použije k distribuci nákladů z vybraných zdrojů.
    - **Náklady na výpočty:** Vytvoří se poměr úměrný cílům na základě jejich nákladů na výpočetní funkce Azure (typy prostředků v oboru názvů [Microsoft.Compute](/azure/templates/microsoft.compute/allversions)). Tento poměr se použije k distribuci nákladů z vybraných zdrojů.
    - **Náklady na úložiště:** Vytvoří se poměr úměrný cílům na základě jejich nákladů na úložiště Azure (typy prostředků v oboru názvů [Microsoft.Storage](/azure/templates/microsoft.storage/allversions)). Tento poměr se použije k distribuci nákladů z vybraných zdrojů.
    - **Náklady na síť:** Vytvoří se poměr úměrný cílům na základě jejich nákladů na síť Azure (typy prostředků v oboru názvů [Microsoft.Network](/azure/templates/microsoft.network/allversions)). Tento poměr se použije k distribuci nákladů z vybraných zdrojů.
    - **Vlastní:** Umožňuje ruční zadání celočíselných hodnot počtů procent. Jejich celkový součet se musí rovnat 100 %.
1. Po dokončení konfigurace pravidla vyberte **Vytvořit**.

Pravidlo alokace začne se zpracováváním. Když je pravidlo aktivní, veškeré náklady vybraných zdrojů se přidělí zadaným cílům.

> [!NOTE] 
> Zpracování nového pravidla může trvat až dvě hodiny, než se dokončí a pravidlo bude aktivní.

## <a name="verify-the-cost-allocation-rule"></a>Ověření pravidla alokace nákladů

Když je pravidlo alokace nákladů aktivní, náklady z vybraných zdrojů se distribuují mezi zadané cíle alokace. K ověření správné alokace nákladů jednotlivým cílům využijte následující informace.

### <a name="view-cost-allocation-for-a-subscription"></a>Zobrazení alokace nákladů pro předplatné

Dopad pravidla alokace si můžete prohlédnout v analýze nákladů. Na webu Azure Portal přejděte na [Předplatná](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). V seznamu vyberte předplatné, na které cílí aktivní pravidlo alokace nákladů. Potom v nabídce vyberte **Analýza nákladů**. V analýze nákladů vyberte **Seskupit podle** a potom vyberte **Alokace nákladů**. Výsledné zobrazení ukazuje rychlé rozdělení nákladů vygenerovaných tímto předplatným. Zobrazí se také náklady přidělené tomuto předplatnému, podobně jako na následujícím obrázku.

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="Příklad ukazující rozpis nákladů" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>Zobrazení alokace nákladů pro skupinu prostředků

Ke zjištění dopadu pravidla alokace nákladů pro skupinu prostředků použijte podobný postup. Na webu Azure Portal přejděte na stránku [Skupina prostředků](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). V seznamu vyberte skupinu prostředků, na kterou cílí aktivní pravidlo alokace nákladů. Potom v nabídce vyberte **Analýza nákladů**. V analýze nákladů vyberte **Seskupit podle** a potom vyberte **Alokace nákladů**. Výsledné zobrazení ukazuje rychlé rozdělení nákladů vygenerovaných touto skupinou prostředků. Zobrazí se také náklady přidělené této skupině prostředků.

### <a name="view-cost-allocation-for-tags"></a>Zobrazení alokace nákladů pro značky

Na webu Azure Portal přejděte na **Správa nákladů a fakturace** > **Správa náklad** > **Analýza nákladů**. V analýze nákladů vyberte **Přidat filtr**. Vyberte **Značka**, zvolte klíč značky a označte hodnoty, které mají přidělené náklady.

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="Příklad ukazující náklady pro označené položky" lightbox="./media/allocate-costs/tagged-costs.png" :::

Tady je video, které ukazuje, jak vytvořit pravidlo přidělení nákladů.

>[!VIDEO https://www.youtube.com/embed/nYzIIs2mx9Q]


## <a name="edit-an-existing-cost-allocation-rule"></a>Úprava stávajícího pravidla alokace nákladů

Pravidlo alokace nákladů můžete upravit, pokud chcete změnit zdroj nebo cíl nebo pokud chcete aktualizovat předem vyplněný počet procent pro výpočetní funkce, úložiště nebo síť. Pravidla se upravují stejným způsobem, jako když se vytvářejí. Zpracování úpravy stávajících pravidel může trvat až dvě hodiny.

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy

Následující části obsahují běžné otázky týkající se přidělování nákladů.

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>Jaká jsou aktuální omezení pro alokaci nákladů ve verzi Public Preview?
<a name="limitations"></a>

V současné době se alokace nákladů podporuje ve službě Cost Management v zobrazeních analýzy nákladů, rozpočtů a prognóz. Přidělené náklady se zobrazují také v seznamu předplatných a na stránce s přehledem předplatných.

Alokace nákladů ve verzi Public Preview momentálně nepodporuje následující položky:

- Naplánované [exporty](tutorial-export-acm-data.md)
- Data zpřístupněná rozhraním API pro [podrobností využití](/rest/api/consumption/usagedetails/list)
- Oblast fakturace předplatných
- [Aplikace Power BI Cost Management](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Konektor Power BI Desktop](/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>Promítají se náklady do rozpočtů a zobrazení prognóz?
<a name="budgets-forecast"></a>

Ano. Přidělené náklady se promítají so rozpočtů a prognóz, které je také podporují. Zobrazení prognóz a rozpočtů ukazují přidělené náklady v souladu s konfigurací prostřednictvím pravidel alokace nákladů.

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>Co se stane po odstranění pravidla alokace?
<a name="delete-rule"></a>

Po odstranění pravidla alokace se odeberou všechny otevřené a aktivní měsíční fakturační náklady alokované jednotlivým cílům. Pokud pravidlo alokace nákladů existovalo několik měsíců, zůstanou historické údaje o přidělování pro předchozí měsíce tak, jak byly tímto pravidlem alokace nákladů původně nastavené.

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>Kdy je potřeba, aby správce registrace nebo fakturačního účtu vytvořil pravidla alokace nákladů?
<a name="why-admin"></a>

Pravidla alokace nákladů se vytvářejí v rozsahu registrace (smlouva Enterprise) nebo fakturačního účtu (Smlouva se zákazníkem Microsoftu). Oprávnění k provádění změn pro tyto rozsahy vyžadují oprávnění správce fakturace.

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>Proč pro pravidla existují omezení 25 zdrojů a cílů?
<a name="source-target-rule-limit"></a>

Jde o omezení verze Preview, které mají zajistit dobrou škálovatelnost a výkon alokace nákladů. Při přechodu do fáze obecné dostupnosti se tato omezení alokace nákladů pravděpodobně zvýší nebo zruší úplně.

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>Co se stane, když se pravidla alokace nákladů (zdroje/cíle) překrývají?
<a name="rule-overlap"></a>

Pravidla s překrývajícími se zdroji nebo cíli se nedoporučují. Pravidla alokace nákladů se aplikují v pořadí podle data vytvoření, takže v případě překrývání má přednost pravidlo alokace s nejdřívějším datem vytvoření.

## <a name="next-steps"></a>Další kroky

- Vytvoření nebo aktualizace pravidel alokace s využitím [rozhraní REST API pro alokaci nákladů](/rest/api/cost-management/costallocationrules)
- Další informace o [postupu při optimalizaci investic do cloudu s využitím služby Azure Cost Management](cost-mgt-best-practices.md)