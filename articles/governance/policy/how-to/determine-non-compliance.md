---
title: Určení příčiny nedodržování předpisů
description: Pokud prostředek není kompatibilní, existuje mnoho možných důvodů. Naučte se zjistit, co způsobilo nedodržování předpisů.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264632"
---
# <a name="determine-causes-of-non-compliance"></a>Určení příčiny nedodržování předpisů

Když je prostředek Azure určen jako nekompatibilní s pravidlem zásad, je užitečné pochopit, která část pravidla, se kterou částí pravidla prostředek nevyhovuje. Je také užitečné pochopit, jaká změna změnila dříve kompatibilní prostředek, aby byl nekompatibilní. Tyto informace lze najít dvěma způsoby:

> [!div class="checklist"]
> - [Podrobnosti o dodržování předpisů](#compliance-details)
> - [Historie změn (náhled)](#change-history)

## <a name="compliance-details"></a>Podrobnosti o dodržování předpisů

Pokud prostředek není kompatibilní, podrobnosti o dodržování předpisů pro tento prostředek jsou k dispozici na stránce **dodržování zásad dodržování předpisů.** Podokno podrobností o dodržování předpisů obsahuje následující informace:

- Podrobnosti o prostředku, jako je název, typ, umístění a ID prostředku
- Stav dodržování předpisů a časové razítko posledního vyhodnocení aktuálního přiřazení zásad
- Seznam _důvodů_ nesouladu se zdrojem

> [!IMPORTANT]
> Vzhledem k tomu, že podrobnosti o dodržování předpisů pro _nekompatibilní_ prostředek zobrazuje aktuální hodnotu vlastností tohoto prostředku, musí mít uživatel **operaci čtení** **typu** prostředku. Například pokud _nekompatibilní_ prostředek je **Microsoft.Compute/virtualMachines** pak uživatel musí mít **Microsoft.Compute/virtualMachines/read** operace. Pokud uživatel nemá potřebnou operaci, zobrazí se chyba přístupu.

Chcete-li zobrazit podrobnosti o dodržování předpisů, postupujte takto:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na stránce **Přehled** nebo **Dodržování předpisů** vyberte zásadu ve stavu **dodržování předpisů,** který není _kompatibilní_.

1. Na kartě **Dodržování předpisů prostředků** na stránce Dodržování **zásad** klepněte pravým tlačítkem myši nebo vyberte tři tečky prostředku ve stavu **dodržování předpisů,** který je _nekompatibilní_. Pak vyberte **Zobrazit podrobnosti o dodržování předpisů**.

   ![Zobrazit možnost podrobností o dodržování předpisů](../media/determine-non-compliance/view-compliance-details.png)

1. Podokno **Podrobnosti dodržování předpisů** zobrazuje informace z nejnovějšího vyhodnocení zdroje do aktuálního přiřazení zásad. V tomto příkladu je zjištěno, že pole **Microsoft.Sql/servers/version** je _12.0,_ zatímco definice zásady očekává _14.0_. Pokud prostředek není kompatibilní z více důvodů, každý je uveden v tomto podokně.

   ![Podokno podrobností o dodržování předpisů a důvody nesouladu](../media/determine-non-compliance/compliance-details-pane.png)

   Pro **auditIfNotExists** nebo **deployIfNotExists** definice zásady podrobnosti zahrnují **details.type** vlastnost a všechny volitelné vlastnosti. Seznam naleznete v tématu [auditIfNotExists vlastnosti](../concepts/effects.md#auditifnotexists-properties) a [deployIfNotExists vlastnosti](../concepts/effects.md#deployifnotexists-properties). **Naposledy vyhodnocený prostředek** je související prostředek z části **podrobností** definice.

   Příklad **částečnédeployIfNotExists** definice:

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

   ![Podokno podrobností o dodržování předpisů - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Chcete-li chránit data, pokud je hodnota vlastnosti _tajným tajemstvím,_ aktuální hodnota zobrazí hvězdičky.

Tyto podrobnosti vysvětlují, proč je prostředek aktuálně nekompatibilní, ale nezobrazovat, kdy byla provedena změna prostředku, který způsobil, že se stal nekompatibilním. Tyto informace naleznete [v tématu Historie změn (náhled)](#change-history) níže.

### <a name="compliance-reasons"></a>Důvody dodržování předpisů

Následující matice mapuje každý možný _důvod_ na odpovědný [stav](../concepts/definition-structure.md#conditions) v definici zásady:

|Důvod | Podmínka |
|-|-|
|Aktuální hodnota musí obsahovat cílovou hodnotu jako klíč. |containsKey nebo **notContainsKey** |
|Aktuální hodnota musí obsahovat cílovou hodnotu. |obsahuje nebo **neObsahuje** |
|Aktuální hodnota musí být rovna cílové hodnotě. |rovná se nebo **neEquals** |
|Aktuální hodnota musí být menší než cílová hodnota. |méně nebo **ne** greaterOrEquals |
|Aktuální hodnota musí být větší nebo rovna cílové hodnotě. |greaterOrEquals nebo **ne** méně |
|Aktuální hodnota musí být větší než cílová hodnota. |větší **not** nebo neméněOrEquals |
|Aktuální hodnota musí být menší nebo rovna cílové hodnotě. |lessOrEquals nebo **ne** větší |
|Aktuální hodnota musí existovat. |Existuje |
|Aktuální hodnota musí být v cílové hodnotě. |in nebo **notIn** |
|Aktuální hodnota musí být jako cílová hodnota. |To se mi líbí nebo **neTo** se mi líbí |
|Aktuální hodnota musí odpovídat rozlišování velkých a malých písmen cílové hodnoty. |shoda nebo **neMatch** |
|Aktuální hodnota musí nerozlišovat malá a velká písmena s cílovou hodnotou. |matchNesensitively **not** nebo notMatchInsensitively |
|Aktuální hodnota nesmí obsahovat cílovou hodnotu jako klíč. |notContainsKey nebo **neobsahuje** Klíč|
|Aktuální hodnota nesmí obsahovat cílovou hodnotu. |notContains nebo **neobsahuje** |
|Aktuální hodnota se nesmí rovnat cílové hodnotě. |notEquals nebo **nerovná** |
|Aktuální hodnota nesmí existovat. |**neexistuje**  |
|Aktuální hodnota nesmí být v cílové hodnotě. |notIn nebo **není** v |
|Aktuální hodnota nesmí být jako cílová hodnota. |netose nebo **nejako** |
|Aktuální hodnota nesmí odpovídat rozlišování velkých a malých písmen cílové hodnoty. |notMatch nebo **neodpovídá** |
|Aktuální hodnota nesmí odpovídat nerozlišujícím písmeny cílové hodnotě. |notMatchNecitlivě **not** nebo neodpovídáNecitlivě |
|Žádné související prostředky neodpovídají podrobnostem o efektu v definici zásady. |Zdroj typu definovaného v **souboru then.details.type** a související se zdrojem definovaným v **části if** pravidla zásady neexistuje. |

## <a name="compliance-details-for-guest-configuration"></a>Podrobnosti o dodržování předpisů pro konfiguraci hosta

Pro _auditIfNotExists zásady_ v kategorii _Konfigurace hosta,_ může být více nastavení vyhodnocena uvnitř virtuálního počítače a budete muset zobrazit podrobnosti nastavení. Pokud například auditujete seznam zásad hesel a pouze jedna z nich má stav _Nedodržují předpisy_, budete potřebovat vědět, které konkrétní zásady hesel nedodržují předpisy a proč.

Taky nemusíte mít přístup k přímému přihlášení k virtuálnímu počítači, ale musíte hlásit, proč je virtuální počítač _nekompatibilní_.

### <a name="azure-portal"></a>portál Azure

Začněte stejným postupem v části výše pro zobrazení podrobností o dodržování zásad.

V zobrazení podokna **Podrobnosti o dodržování předpisů** klepněte na odkaz **Poslední vyhodnocený prostředek**.

   ![Zobrazit podrobnosti definice auditIfNotExists](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

Na stránce **Přiřazení hosta** se zobrazí všechny dostupné podrobnosti o dodržování předpisů. Každý řádek v zobrazení představuje hodnocení, které bylo provedeno uvnitř počítače. Ve sloupci **Důvod** se zobrazí fráze popisující, proč je přiřazení hosta _nevyhovující._ Pokud například auditujete zásady hesel, zobrazí se ve sloupci **Důvod** text včetně aktuální hodnoty pro každé nastavení.

![Zobrazit podrobnosti o dodržování předpisů](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Můžete také zobrazit podrobnosti o dodržování předpisů z Azure PowerShellu. Nejprve se ujistěte, že máte nainstalovaný modul Konfigurace hosta.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Aktuální stav všech přiřazení hosta pro virtuální ho spolek můžete zobrazit pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Chcete-li zobrazit pouze _důvod_ fráze, která popisuje, proč je virtuální ho _dohledu nedodržující ,_ vrátí pouze důvod podřízené vlastnosti.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Můžete také výstup historie dodržování předpisů pro přiřazení hosta v oboru pro počítač. Výstup z tohoto příkazu obsahuje podrobnosti o každé sestavě pro virtuální ho.

> [!NOTE]
> Výstup může vrátit velký objem dat. Doporučujese uložit výstup v proměnné.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Chcete-li toto zobrazení zjednodušit, použijte parametr **ShowChanged.** Výstup z tohoto příkazu zahrnuje pouze sestavy, které následovaly po změně stavu dodržování předpisů.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="change-history-preview"></a><a name="change-history"/>Historie změn (náhled)

Jako součást nové **verze Public Preview**jsou k dispozici historie změn za posledních 14 dní pro všechny prostředky Azure, které podporují úplné odstranění [režimu](../../../azure-resource-manager/templates/complete-mode-deletion.md). Historie změn obsahuje podrobnosti o tom, kdy byla zjištěna změna a _vizuální rozdíl_ pro každou změnu. Zjišťování změn se aktivuje při přidání, odebrání nebo změně vlastností Správce prostředků.

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na stránce **Přehled** nebo **Dodržování předpisů** vyberte zásadu v libovolném stavu **dodržování předpisů**.

1. Na kartě **Dodržování předpisů prostředků** na stránce Dodržování **zásad** vyberte prostředek.

1. Na stránce Dodržování předpisů o **prostředcích** vyberte kartu **Historie změn (náhled).** Seznam zjištěných změn, pokud existují, jsou zobrazeny.

   ![Karta Historie změn zásad Azure na stránce Dodržování předpisů o prostředcích](../media/determine-non-compliance/change-history-tab.png)

1. Vyberte jednu z zjištěných změn. _Vizuální rozdíl_ pro prostředek je uveden na stránce **Historie změn.**

   ![Vizuální rozdíl historie změn zásad Azure na stránce Historie změn](../media/determine-non-compliance/change-history-visual-diff.png)

_Vizuální diff_ pomocpři identifikaci změn prostředku. Zjištěné změny nemusí souviset s aktuálním stavem dodržování předpisů prostředku.

Data historie změn poskytuje [Azure Resource Graph](../../resource-graph/overview.md). Pokud se chcete na tyto informace dotazovat mimo portál Azure, [přečtěte si](../../resource-graph/how-to/get-resource-changes.md)téma Získání změn prostředků .

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic Azure Policy](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).
