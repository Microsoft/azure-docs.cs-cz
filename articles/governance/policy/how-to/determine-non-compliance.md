---
title: Určení příčiny nedodržování předpisů
description: Pokud prostředek je nedodržují předpisy, existuje mnoho důvodů, proč je to možné. Zjistěte, jak zjistit, co způsobilo nedodržení předpisů.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2f856e9c42b26d4e286493e2eb5d019a8cff6c23
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868723"
---
# <a name="determine-causes-of-non-compliance"></a>Určení příčiny nedodržování předpisů

Při prostředku Azure je určena jako nevyhovující pravidla zásad, je vhodné pochopit, jaká část pravidla prostředek není kompatibilní s. Je také užitečné porozumět, jaké změny změnit dříve odpovídal požadavkům prostředek k němu nedodržující předpisy. Existují dva způsoby, jak najít tyto informace:

> [!div class="checklist"]
> - [Podrobnosti o dodržování předpisů](#compliance-details)
> - [Historie změn (Preview)](#change-history-preview)

## <a name="compliance-details"></a>Podrobnosti dodržování předpisů

Pokud prostředek je nedodržují předpisy, podrobnosti o dodržování předpisů pro tento prostředek jsou k dispozici **dodržování zásad** stránky. V podokně Podrobnosti dodržování předpisů zahrnuje následující informace:

- Podrobnosti o prostředku, jako je například název, typ, umístění a ID prostředku
- Stav dodržování předpisů a časové razítko posledního vyhodnocení na aktuální přiřazení zásad
- Seznam _důvodů_ prostředek nedodržení předpisů

> [!IMPORTANT]
> Jako podrobnosti o dodržování předpisů pro _nekompatibilní_ prostředků zobrazuje aktuální hodnota vlastnosti na tento prostředek, musí mít uživatel **čtení** operace **typ** z prostředek. Například pokud _nekompatibilní_ prostředek je **Microsoft.Compute/virtualMachines** musí mít uživatel **Microsoft.Compute/virtualMachines/read** operace. Pokud uživatel nemá potřebné operace, zobrazí se chyba přístupu.

Chcete-li zobrazit podrobnosti o dodržování předpisů, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na **přehled** nebo **dodržování předpisů** vyberte zásadu v **stavu dodržování předpisů** , který je _nekompatibilní_.

1. V části **dodržování předpisů prostředkem** karty **dodržování zásad** stránky, klikněte pravým tlačítkem myši nebo vyberte tři tečky prostředku v **stavu dodržování předpisů** , který je  _Nedodržující předpisy_. Potom vyberte **zobrazit podrobnosti o dodržování předpisů**.

   ![Zobrazit možnost Podrobnosti dodržování předpisů](../media/determine-non-compliance/view-compliance-details.png)

1. **Podrobnosti o dodržování předpisů** podokně se zobrazí informace z nejnovější vyhodnocení prostředku do aktuálního přiřazení zásad. V tomto příkladu pole **Microsoft.Sql/servers/version** bude zjištěna _12.0_ při definici zásad, byl očekáván _14.0_. Pokud prostředek je jako nevyhovující z několika důvodů, každý je uvedený v tomto podokně.

   ![Podokno Podrobnosti dodržování předpisů a důvody pro nedodržení předpisů](../media/determine-non-compliance/compliance-details-pane.png)

   Pro **auditIfNotExists** nebo **deployIfNotExists** údaje obsahují definice zásad **details.type** vlastnost a všechny volitelné vlastnosti. Seznam najdete v tématu [auditIfNotExists vlastnosti](../concepts/effects.md#auditifnotexists-properties) a [deployIfNotExists vlastnosti](../concepts/effects.md#deployifnotexists-properties). **Naposledy vyhodnoceno prostředků** souvisejících prostředků z je **podrobnosti** část definice.

   Příklad částečné **deployIfNotExists** definice:

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

   ![Podokno Podrobnosti dodržování předpisů – * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> K ochraně dat, pokud je hodnota vlastnosti _tajný kód_ hvězdičky z obou stran zobrazí aktuální hodnotu.

Tyto podrobnosti vysvětlit, proč prostředek aktuálně nedodržují předpisy, ale nezobrazovat při provedení změny na prostředek, který způsobil, že se jako nevyhovující. Informace najdete v tématu [změnit historii (Preview)](#change-history-preview) níže.

### <a name="compliance-reasons"></a>Dodržováním předpisů

Následující matice mapuje každé možné _důvod_ na příslušný [podmínku](../concepts/definition-structure.md#conditions) v definici zásad:

|Důvod | Podmínka |
|-|-|
|Aktuální hodnota musí jako klíč obsahovat cílovou hodnotu. |containsKey nebo **není** notContainsKey |
|Aktuální hodnota musí obsahovat cílovou hodnotu. |obsahuje nebo **není** notContains |
|Aktuální hodnota musí být shodná s cílovou hodnotou. |se rovná nebo **není** notEquals |
|Aktuální hodnota musí existovat. |Existuje |
|Aktuální hodnota musí být v cílové hodnotě. |v nebo **není** notIn |
|Aktuální hodnota musí být jako cílová hodnota. |například nebo **není** notLike |
|Aktuální hodnota musí s rozlišováním velikosti písmen odpovídat cílové hodnotě. |odpovídat nebo **není** notMatch |
|Aktuální hodnota musí bez rozlišování velikosti písmen odpovídat cílové hodnotě. |matchInsensitively nebo **není** notMatchInsensitively |
|Aktuální hodnota nesmí jako klíč obsahovat cílovou hodnotu. |notContainsKey nebo **není** containsKey|
|Aktuální hodnota nesmí obsahovat cílovou hodnotu. |notContains nebo **není** obsahuje |
|Aktuální hodnota nesmí být shodná s cílovou hodnotou. |notEquals nebo **není** rovná se |
|Aktuální hodnota nesmí existovat. |**není** existuje  |
|Aktuální hodnota nesmí být v cílové hodnotě. |notIn nebo **není** v |
|Aktuální hodnota nesmí být jako cílová hodnota. |notLike nebo **není** jako |
|Aktuální hodnota nesmí s rozlišováním velikosti písmen odpovídat cílové hodnotě. |notMatch nebo **není** odpovídat |
|Aktuální hodnota nesmí bez rozlišování velikosti písmen odpovídat cílové hodnotě. |notMatchInsensitively nebo **není** matchInsensitively |
|Žádné související prostředky neodpovídají podrobnostem účinku v definici zásad. |Prostředek typu definovaného v **then.details.type** a související na prostředek definovaný v **Pokud** část tohoto pravidla zásady neexistuje. |

## <a name="compliance-details-for-guest-configuration"></a>Podrobnosti o dodržování předpisů pro konfiguraci typu Host

Pro _auditu_ zásady v _hosta konfigurace_ kategorie, může být různá nastavení, vyhodnotí ve virtuálním počítači a budete potřebovat k zobrazení podrobností podle nastavení. Například pokud sledujete seznam nainstalovaných aplikací a stav přiřazení je _nekompatibilní_, bude potřeba zjistit, konkrétní aplikace, které nebyly nalezeny.

Také nemusí mít přístup k přihlášení k virtuálnímu počítači přímo, ale budete muset sestavy na to, proč tento virtuální počítač je _nekompatibilní_. Například může auditovat, že virtuální počítače jsou připojené ke správné doméně a součástí vytváření sestav o členství v aktuální doméně.

### <a name="azure-portal"></a>portál Azure

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na **přehled** nebo **dodržování předpisů** vyberte přiřazení zásady pro všechny iniciativu, který obsahuje definici zásady Konfigurace hosta, to _nekompatibilní_.

1. Vyberte _auditu_ zásad v rámci iniciativy, která _nekompatibilní_.

   ![Zobrazit podrobnosti o definici audit](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. Na **dodržování předpisů prostředkem** kartu, je k dispozici následující informace:

   - **Název** – název konfigurace přiřazení hosta.
   - **Nadřazený prostředek** – virtuální počítač _nekompatibilní_ stavu pro vybraný přiřazení konfigurace hosta.
   - **Typ prostředku** – _guestConfigurationAssignments_ jméno a příjmení.
   - **Naposledy vyhodnoceno** – čas poslední služba hosta konfigurace oznámení o stavu cílového virtuálního počítače Azure Policy.

   ![Podívejte se na podrobnosti dodržování zásad.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Vyberte název přiřazení konfigurace hostovaného v **název** sloupec, který se otevře **dodržování předpisů prostředkem** stránky.

1. Vyberte **zobrazení prostředků** tlačítko v horní části stránky otevřete **hosta přiřazení** stránky.

**Hosta přiřazení** stránka zobrazuje všechny podrobnosti o dodržování předpisů k dispozici. Každý řádek v zobrazení představuje zkušební verzi, která byla provedena ve virtuálním počítači. V **důvod** sloupce, věta popisující, proč je přiřazení typu Host _nekompatibilní_ se zobrazí. Pokud sledujete, že virtuální počítače by měl být připojený k doméně, třeba **důvod** sloupci zobrazí text včetně členství v aktuální doméně.

![Podívejte se na podrobnosti dodržování zásad.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Můžete také zobrazit podrobnosti o dodržování předpisů v Azure Powershellu. Nejprve ujistěte se, že máte modul Konfigurace hosta nainstalovaný.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Můžete zobrazit aktuální stav všech přiřazení hosta pro virtuální počítač pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Chcete-li zobrazit pouze _důvod_ slovní spojení, které popisuje, proč tento virtuální počítač je _nekompatibilní_, vrátí pouze z důvodu podřízené vlastnosti.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Historie dodržování předpisů můžete také výstup pro přiřazení hostovaného v oboru pro virtuální počítač. Výstup tohoto příkazu obsahuje podrobnosti o jednotlivých sestav pro virtuální počítač.

> [!NOTE]
> Velké objemy dat může vrátit výstup. Doporučujeme ukládat výstup do proměnné.

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

Pro zjednodušení tohoto zobrazení, použijte **ShowChanged** parametru. Výstup tohoto příkazu zahrnuje jenom sestavy a potom změnu stavu dodržování předpisů.

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

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Historie změn (Preview)

Jako součást nového **ve verzi public preview**, za posledních 14 dní historie změn jsou k dispozici pro všechny prostředky Azure, které podporují [dokončení odstranění režimu](../../../azure-resource-manager/complete-mode-deletion.md). Historie změn při byla zjištěna změna a poskytuje podrobnosti o _visual diff_ pro každou změnu. Detekce změn se aktivuje při přidání, odebrání nebo změnit vlastnosti správce prostředků.

1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

1. Na **přehled** nebo **dodržování předpisů** vyberte zásadu v libovolném **stavu dodržování předpisů**.

1. V části **dodržování předpisů prostředkem** karty **dodržování zásad** vyberte prostředek.

1. Vyberte **historii změn (preview)** kartě **dodržování předpisů prostředkem** stránky. Seznam zjistila změny, pokud jsou zobrazeny všechny existují.

   ![Karta historie změn zásad na stránce dodržování předpisů prostředkem](../media/determine-non-compliance/change-history-tab.png)

1. Vyberte jednu z nalezenými změnami. _Visual diff_ pro prostředek se zobrazí na **historii změn** stránky.

   ![Historie změn zásad na stránce historie změn Visual rozdíl.](../media/determine-non-compliance/change-history-visual-diff.png)

_Visual diff_ formě identifikovat změny prostředku. Byly zjištěny změny nemusí souviset s aktuální stav dodržování předpisů prostředku.

Poskytuje data o historii změn [Azure Graph prostředků](../../resource-graph/overview.md). Pro dotazy, tyto informace mimo na webu Azure portal, najdete v článku [získat změn prostředků](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md).
- Projděte si [strukturu definic zásad](../concepts/definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](../concepts/effects.md).
- Pochopit postup [programové vytváření zásad](programmatically-create.md).
- Zjistěte, jak [získat data o dodržování předpisů](getting-compliance-data.md).
- Zjistěte, jak [nápravě nekompatibilních prostředků](remediate-resources.md).
- Zkontrolujte, jaké skupiny pro správu je s [uspořádání prostředků se skupinami pro správu Azure](../../management-groups/overview.md).