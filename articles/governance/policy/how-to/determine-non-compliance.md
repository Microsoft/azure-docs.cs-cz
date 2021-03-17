---
title: Určení příčiny nedodržování předpisů
description: Pokud prostředek není kompatibilní, existuje mnoho možných důvodů. Přečtěte si, jak zjistit, co způsobilo nedodržení předpisů.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: a8168bf22aceaf5cbdec4b1346801aa62b7aa4ee
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439829"
---
# <a name="determine-causes-of-non-compliance"></a>Určení příčiny nedodržování předpisů

Pokud je u prostředku Azure zjištěno, že není kompatibilní s pravidlem zásad, je užitečné pochopit, na které straně pravidla prostředek není kompatibilní. Je také užitečné pochopit, jakou změnu změnil dříve kompatibilní prostředek, aby nesplňovala předpisy. Existují dva způsoby, jak najít tyto informace:

- [Podrobnosti o kompatibilitě](#compliance-details)
- [Historie změn (Preview)](#change-history)

## <a name="compliance-details"></a>Podrobnosti o kompatibilitě

Pokud prostředek nedodržuje předpisy, podrobnosti o kompatibilitě tohoto prostředku jsou k dispozici na stránce **dodržování zásad** . Podokno Podrobnosti o dodržování předpisů obsahuje následující informace:

- Podrobnosti o zdroji, jako je název, typ, umístění a ID prostředku
- Stav dodržování předpisů a časové razítko posledního vyhodnocení pro aktuální přiřazení zásad
- Seznam _důvodů_ neshody prostředků

> [!IMPORTANT]
> Vzhledem k podrobnostem dodržování předpisů pro prostředek, který _nedodržuje předpisy_ , se zobrazuje aktuální hodnota vlastností na daném prostředku, uživatel musí mít v **typu** prostředku operaci **čtení** . Například pokud je prostředek, _který nedodržuje předpisy_ , **Microsoft. COMPUTE/virtualMachines** , musí mít uživatel operaci **Microsoft. COMPUTE/virtualMachines/Read** . Pokud uživatel nemá potřebnou operaci, zobrazí se chyba přístupu.

Chcete-li zobrazit podrobnosti o kompatibilitě, postupujte takto:

1. Spusťte službu Azure Policy v Azure Portal tak, že vyberete **všechny služby** a pak vyhledáte a vyberete **zásadu**.

1. Na stránce **Přehled** nebo **dodržování předpisů** vyberte zásadu ve **stavu dodržování předpisů** , který _nedodržuje předpisy_.

1. Na kartě **Kompatibilita prostředků** na stránce **dodržování zásad** klikněte pravým tlačítkem myši nebo vyberte tři tečky prostředku ve **stavu dodržování předpisů** , který _nedodržuje předpisy_. Pak vyberte **Zobrazit podrobnosti o kompatibilitě**.

   :::image type="content" source="../media/determine-non-compliance/view-compliance-details.png" alt-text="Snímek obrazovky s odkazem zobrazit podrobnosti o dodržování předpisů na kartě Kompatibilita prostředků" border="false":::

1. V podokně **podrobností o dodržování předpisů** se zobrazují informace z posledního vyhodnocení prostředku na aktuální přiřazení zásad. V tomto příkladu se v poli **Microsoft. SQL/servery/verze** našla _12,0_ , zatímco definice zásady očekávala hodnotu _14,0_. Pokud prostředek z více důvodů není kompatibilní, každá z nich je uvedena v tomto podokně.

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane.png" alt-text="Snímek obrazovky s podoknem podrobností o dodržování předpisů a důvody pro nedodržování předpisů, které jsou aktuální hodnotou 12 a cílová hodnota je 14." border="false":::

   V případě definice zásady **auditIfNotExists** nebo **deployIfNotExists** obsahuje podrobnosti informace o vlastnosti **Details. Type** a všech volitelných vlastnostech. Seznam najdete v tématu [vlastnosti auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) a [vlastnosti deployIfNotExists](../concepts/effects.md#deployifnotexists-properties). **Poslední vyhodnocený prostředek** je související prostředek z oddílu **podrobností** definice.

   Příklad částečné definice **deployIfNotExists** :

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   :::image type="content" source="../media/determine-non-compliance/compliance-details-pane-existence.png" alt-text="Snímek obrazovky s podoknem podrobností o dodržování předpisů pro ifNotExists, včetně vyhodnoceného počtu prostředků" border="false":::

> [!NOTE]
> Pokud chcete chránit data, když je hodnota vlastnosti _tajná_ , aktuální hodnota zobrazí hvězdičky.

Tyto podrobnosti vysvětlují, proč je prostředek aktuálně nekompatibilní, ale nezobrazují, kdy došlo ke změně prostředku, který způsobil, že se neshoduje s předpisy. Informace najdete v části o [historii změn (Preview)](#change-history) níže.

### <a name="compliance-reasons"></a>Důvody pro dodržování předpisů

V následující matrici je možné podle příslušné [podmínky](../concepts/definition-structure.md#conditions) v definici zásady mapovat tyto _důvody_ :

|Důvod | Podmínka |
|-|-|
|Aktuální hodnota musí obsahovat cílovou hodnotu jako klíč. |ContainsKey – nebo **ne** notContainsKey |
|Aktuální hodnota musí obsahovat cílovou hodnotu. |obsahuje nebo **není** notContains |
|Aktuální hodnota musí být stejná jako cílová hodnota. |je rovno nebo **není** notEquals |
|Aktuální hodnota musí být menší než cílová hodnota. |menší nebo  negreaterOrEqualsé |
|Aktuální hodnota musí být větší než nebo rovna cílové hodnotě. |greaterOrEquals nebo **méně** |
|Aktuální hodnota musí být větší než cílová hodnota. |větší nebo  nelessOrEquals |
|Aktuální hodnota musí být menší nebo rovna cílové hodnotě. |lessOrEquals nebo **ne** větší |
|Aktuální hodnota musí existovat. |neexistuje |
|Aktuální hodnota musí být v cílové hodnotě. |v nebo **ne** notIn |
|Aktuální hodnota musí být stejná jako cílová hodnota. |Like nebo **Not** notLike |
|V aktuální hodnotě se musí rozlišovat velká a malá písmena, která se shodují s cílovou hodnotou. |shoda nebo  nenotMatch |
|Aktuální hodnota musí rozlišovat velikost písmen, která neodpovídá cílové hodnotě. |matchInsensitively nebo **ne** notMatchInsensitively |
|Aktuální hodnota nesmí obsahovat cílovou hodnotu jako klíč. |notContainsKey nebo **ne** ContainsKey –|
|Aktuální hodnota nesmí obsahovat cílovou hodnotu. |notContains nebo **Not** Contains |
|Aktuální hodnota nesmí být rovna cílové hodnotě. |notEquals nebo **nerovná se** |
|Aktuální hodnota nesmí existovat. |**neexistuje**  |
|Aktuální hodnota nesmí být v cílové hodnotě. |notIn nebo **ne** v |
|Aktuální hodnota nesmí být stejná jako cílová hodnota. |notLike nebo **Not** like |
|Aktuální hodnota nesmí rozlišovat velká a malá písmena pro cílovou hodnotu. |notMatch nebo **neodpovídá** |
|Aktuální hodnota nesmí rozlišovat velká a malá písmena. |notMatchInsensitively nebo **ne** matchInsensitively |
|Žádné související prostředky neodpovídají podrobnostem o vlivu v definici zásady. |Prostředek typu, který je definován v **then. details. Type** a souvisí s prostředkem definovaným v části **if** pravidla zásad, neexistuje. |

## <a name="component-details-for-resource-provider-modes"></a>Podrobnosti o komponentách pro režimy poskytovatele prostředků

Pro přiřazení s [režimem poskytovatele prostředků](../concepts/definition-structure.md#resource-manager-modes)vyberte prostředek, který _nedodržuje předpisy_ , aby bylo možné otevřít hlubší zobrazení. Na kartě **dodržování předpisů komponent** jsou další informace, které jsou specifické pro režim poskytovatele prostředků na přiřazené zásadě ukazující **komponentu** a **ID komponenty**, _které nedodržují předpisy_ .

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Snímek obrazovky karty dodržování předpisů pro součásti a podrobnosti o dodržování předpisů pro přiřazení režimu poskytovatele prostředků." border="false":::

## <a name="compliance-details-for-guest-configuration"></a>Podrobnosti o dodržování předpisů pro konfiguraci hosta

U zásad _auditIfNotExists_ v kategorii _Konfigurace hosta_ může být ve virtuálním počítači vyhodnoceno více nastavení a bude nutné zobrazit podrobnosti o jednotlivých nastaveních. Pokud například provádíte audit pro seznam zásad hesel a jenom jeden z nich má _nevyhovující_ stav, budete muset zjistit, které konkrétní zásady hesel nejsou kompatibilní a proč.

Je také možné, že nebudete mít přístup k virtuálnímu počítači přímo, ale potřebujete podávat zprávu o tom, proč virtuální počítač _nedodržuje předpisy_.

### <a name="azure-portal"></a>portál Azure

Začněte podle stejných kroků v části výše pro zobrazení podrobností o dodržování zásad.

V zobrazení podokna podrobností o dodržování předpisů vyberte odkaz **Poslední vyhodnocený prostředek**.

:::image type="content" source="../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png" alt-text="Snímek obrazovky se zobrazením podrobností o kompatibilitě definicí auditIfNotExists" border="false":::

Na stránce **přiřazení hosta** se zobrazí všechny dostupné podrobnosti o dodržování předpisů. Každý řádek v zobrazení představuje hodnocení, které bylo provedeno uvnitř počítače. Ve sloupci **důvod** se zobrazí fráze popisující, proč není přiřazení hostů _kompatibilní_. Pokud například provádíte audit zásad hesel, sloupec **důvod** by zobrazil text, včetně aktuální hodnoty pro každé nastavení.

:::image type="content" source="../media/determine-non-compliance/guestconfig-compliance-details.png" alt-text="Snímek obrazovky s podrobnostmi o dodržování předpisů pro přiřazení hostů" border="false":::

## <a name="change-history-preview"></a><a name="change-history"></a>Historie změn (Preview)

V rámci nové **verze Public Preview** jsou poslední 14 dní historie změn k dispozici pro všechny prostředky Azure, které podporují [odstranění režimu úplného režimu](../../../azure-resource-manager/templates/complete-mode-deletion.md). Historie změn poskytuje podrobnosti o tom, kdy byla zjištěna změna, a _vizuální rozdíl_ pro každou změnu. Detekce změn se aktivuje, když se přidají, odeberou nebo změní vlastnosti Azure Resource Manager.

1. Spusťte službu Azure Policy v Azure Portal tak, že vyberete **všechny služby** a pak vyhledáte a vyberete **zásadu**.

1. Na stránce **Přehled** nebo **dodržování předpisů** vyberte zásadu v jakémkoli **stavu dodržování předpisů**.

1. Na kartě **Kompatibilita prostředků** na stránce **dodržování zásad** vyberte prostředek.

1. Na stránce **Kompatibilita prostředků** vyberte kartu **historie změn (Preview)** . Zobrazí se seznam zjištěných změn, pokud existují.

   :::image type="content" source="../media/determine-non-compliance/change-history-tab.png" alt-text="Snímek obrazovky karty historie změn a zjištěné časy změny na stránce dodržování předpisů pro prostředky" border="false":::

1. Vyberte jednu z zjištěných změn. _Rozdíly ve vizuálním_ zdroji se zobrazí na stránce **historie změn** .

   :::image type="content" source="../media/determine-non-compliance/change-history-visual-diff.png" alt-text="Snímek obrazovky s vizuálním rozdílem v historii změn stavu před a po stavu na stránce Historie změn" border="false":::

_Vizuální rozdíl_ Aides při identifikaci změn prostředku. Zjištěné změny nemůžou souviset s aktuálním stavem dodržování předpisů daného prostředku.

Data historie změn poskytuje [Azure Resource Graph](../../resource-graph/overview.md). Chcete-li zadat dotaz na tyto informace mimo Azure Portal, přečtěte si téma [získání změn prostředků](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
