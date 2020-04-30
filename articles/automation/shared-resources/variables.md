---
title: Správa proměnných v Azure Automation
description: Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v Azure Automation.  V tomto článku se dozvíte o podrobnostech proměnných a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4778e9b2c0d3b442b214966ab69810d2f42b70b8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732760"
---
# <a name="manage-variables-in-azure-automation"></a>Správa proměnných v Azure Automation

Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v účtu Automation. Můžete je spravovat z Azure Portal, z PowerShellu, v rámci sady Runbook nebo v konfiguraci DSC.

Proměnné automatizace jsou užitečné pro následující scénáře:

- Sdílení hodnoty mezi několika Runbooky nebo konfiguracemi DSC.

- Sdílení hodnoty mezi několika úlohami ze stejné konfigurace sady Runbook nebo DSC.

- Správa hodnoty používané Runbooky nebo konfiguracemi DSC z portálu nebo z příkazového řádku PowerShellu. Příkladem je sada běžných položek konfigurace, například konkrétní seznam názvů virtuálních počítačů, konkrétní skupiny prostředků, název domény služby AD a další.  

Azure Automation uchovává proměnné a zpřístupňuje je i v případě, že dojde k chybě Runbooku nebo konfigurace DSC. Toto chování umožňuje, aby jedna konfigurace sady Runbook nebo DSC nastavila hodnotu, kterou používá jiná sada Runbook nebo stejná sada Runbook nebo konfigurace DSC při příštím spuštění.

Azure Automation ukládá každou šifrovanou proměnnou bezpečně. Při vytváření proměnné můžete zadat její šifrování a úložiště Azure Automation jako zabezpečený prostředek. K dalším zabezpečeným prostředkům patří přihlašovací údaje, certifikáty a připojení. Azure Automation tyto prostředky šifruje a ukládá je pomocí jedinečného klíče, který se generuje pro každý účet Automation. Klíč je uložený v systému spravovaném Key Vault. Před uložením zabezpečeného prostředku Azure Automation načte klíč z Key Vault a pak ho použije k zašifrování assetu. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Typy proměnných

Když vytvoříte proměnnou pomocí Azure Portal, je nutné zadat datový typ z rozevíracího seznamu, aby portál mohl zobrazit příslušný ovládací prvek pro zadání hodnoty proměnné. Následující typy proměnných jsou k dispozici v Azure Automation:

* Řetězec
* Integer
* DateTime
* Logická hodnota
* Null

Proměnná není omezena na určený datový typ. Pokud chcete zadat hodnotu jiného typu, je nutné nastavit proměnnou pomocí prostředí Windows PowerShell. Pokud označíte `Not defined`, hodnota proměnné je nastavená na null. Je nutné nastavit hodnotu pomocí rutiny [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) nebo `Set-AutomationVariable` aktivity.

Nemůžete použít Azure Portal k vytvoření nebo změně hodnoty pro komplexní typ proměnné. Pomocí Windows PowerShellu ale můžete zadat hodnotu libovolného typu. Komplexní typy jsou načteny jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Můžete uložit více hodnot do jedné proměnné tak, že vytvoříte pole nebo zatřiďovací tabulku a uložíte ji do proměnné.

>[!NOTE]
>Proměnné názvu virtuálního počítače můžou být maximálně 80 znaků. Proměnné skupiny prostředků můžou mít maximálně 90 znaků. Viz [pravidla a omezení pojmenování pro prostředky Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-name-rules).

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Rutiny PowerShellu, které vytvářejí a spravují variabilní prostředky

Pro modul AZ Module se k vytváření a správě prostředků proměnných automatizace pomocí Windows PowerShellu použijí rutiny v následující tabulce. Dodávají se jako součást [modulu AZ. Automation](/powershell/azure/overview), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

| Rutina | Popis |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Načte hodnotu existující proměnné. Tuto rutinu nemůžete použít k načtení hodnoty šifrované proměnné. Jediným způsobem, jak to provést, `Get-AutomationVariable` je použít aktivitu v sadě Runbook nebo konfiguraci DSC. |
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Vytvoří novou proměnnou a nastaví její hodnotu.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Odstraní existující proměnnou.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Nastaví hodnotu pro existující proměnnou. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Aktivity pro přístup k proměnným v sadách Runbook a konfiguracích DSC

Aktivity v následující tabulce se používají pro přístup k proměnným v sadách Runbook a konfiguracích DSC. Rutiny pro tyto aktivity se dodávají s globálním modulem `Orchestrator.AssetManagement.Cmdlets`.

| Aktivita | Popis |
|:---|:---|
|`Get-AutomationVariable`|Načte hodnotu existující proměnné.|
|`Set-AutomationVariable`|Nastaví hodnotu pro existující proměnnou.|

> [!NOTE]
> Vyhněte se použití proměnných `Name` v parametru `Get-AutomationVariable` v sadě Runbook nebo konfiguraci DSC. Použití tohoto parametru může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a proměnných automatizace v době návrhu.

Všimněte si `Get-AutomationVariable` , že v prostředí PowerShell nefunguje, ale pouze v sadě Runbook nebo konfiguraci DSC. Chcete-li například zobrazit hodnotu zašifrované proměnné, můžete vytvořit sadu Runbook, která získá proměnnou a následně ji zapsat do výstupního datového proudu:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funkce pro přístup k proměnným v sadách Python 2 Runbooky

Funkce v následující tabulce se používají pro přístup k proměnným v sadě Runbook Python 2.

|Python 2 – funkce|Popis|
|:---|:---|
|`automationassets.get_automation_variable`|Načte hodnotu existující proměnné. |
|`automationassets.set_automation_variable`|Nastaví hodnotu pro existující proměnnou. |

> [!NOTE]
> Aby bylo možné získat `automationassets` přístup k funkcím assetu, musíte importovat modul v horní části Runbooku sady Python.

## <a name="working-with-automation-variables"></a>Práce s proměnnými automatizace

>[!NOTE]
>Chcete-li odebrat šifrování pro proměnnou, je nutné odstranit proměnnou a znovu ji vytvořit jako nezašifrovanou.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Vytvořit novou proměnnou pomocí Azure Portal

1. Z účtu Automation klikněte na dlaždici **assety** , pak na okno **assety** a vyberte **proměnné**.
2. Na dlaždici **proměnné** vyberte **přidat proměnnou**.
3. V okně **Nová proměnná** dokončete možnosti a potom kliknutím na **vytvořit** uložte novou proměnnou.

> [!NOTE]
> Jakmile uložíte zašifrovanou proměnnou, nelze ji zobrazit na portálu. Dá se aktualizovat jenom.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Vytvoření a použití proměnné ve Windows PowerShellu

PowerShellový skript používá `New-AzAutomationVariable` rutinu nebo její ekvivalent modulu AzureRM, aby vytvořil novou proměnnou a nastavil její počáteční hodnotu. Pokud je proměnná zašifrovaná, volání by mělo použít `Encrypted` parametr.

Skript načte hodnotu proměnné pomocí [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Pokud je hodnota jednoduchý typ, rutina načte stejný typ. Pokud se jedná o komplexní typ, načte `PSCustomObject` se typ.

>[!NOTE]
>Skript PowerShellu nemůže načíst šifrovanou hodnotu. Jediným způsobem, jak to provést, je použít `Get-AutomationVariable` aktivitu v sadě Runbook nebo konfiguraci DSC.

Následující příklad ukazuje, jak vytvořit proměnnou typu String a pak vrátit její hodnotu.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Následující příklad ukazuje, jak vytvořit proměnnou se složitým typem a pak načíst jeho vlastnosti. V takovém případě se použije objekt virtuálního počítače z [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Vytvoření a použití proměnné v sadě Runbook nebo konfiguraci DSC

Jediným způsobem, jak vytvořit novou proměnnou v rámci sady Runbook nebo konfigurace DSC, je použití `New-AzAutomationVariable` rutiny nebo jejího ekvivalentu modulu AzureRM. Skript pomocí této rutiny nastaví počáteční hodnotu proměnné. Skript pak může hodnotu načíst pomocí `Get-AzAutomationVariable`. Pokud je hodnota jednoduchý typ, je načten stejný typ. Pokud se jedná o komplexní typ, načte se `PSCustomObject` typ.

>[!NOTE]
>Jediným způsobem, jak načíst šifrovanou hodnotu, je použití `Get-AutomationVariable` aktivity v sadě Runbook nebo konfiguraci DSC. 

### <a name="textual-runbook-samples"></a>Ukázky textových runbooků

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Nastavení a načtení jednoduché hodnoty z proměnné

Následující vzorové příkazy ukazují, jak nastavit a načíst proměnnou v textovém Runbooku. Tato ukázka předpokládá vytvoření celočíselných proměnných s názvem `NumberOfIterations` a `NumberOfRunnings` a řetězcovou proměnnou s `SampleMessage`názvem.

```powershell
$NumberOfIterations = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Nastavení a načtení proměnné v sadě Runbook Python 2

Následující příklad ukazuje, jak použít proměnnou, nastavit proměnnou a zpracovat výjimku pro neexistující proměnnou v sadě Runbook Python 2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a variable
value = automationassets.get_automation_variable("test-variable")
print value

# set a variable (value can be int/bool/string)
automationassets.set_automation_variable("test-variable", True)
automationassets.set_automation_variable("test-variable", 4)
automationassets.set_automation_variable("test-variable", "test-string")

# handle a non-existent variable exception
try:
    value = automationassets.get_automation_variable("nonexisting variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Ukázky grafického Runbooku

V grafickém Runbooku můžete přidat aktivitu `Get-AutomationVariable` nebo. `Set-AutomationVariable` Stačí kliknout pravým tlačítkem myši na proměnnou v podokně Knihovna v grafickém editoru a vybrat aktivitu, kterou požadujete.

![Přidat proměnnou na plátno](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Nastavení hodnot v proměnné

Následující obrázek ukazuje ukázkové aktivity pro aktualizaci proměnné s jednoduchou hodnotou v grafickém Runbooku. V této ukázce `Get-AzVM` načte jeden virtuální počítač Azure a uloží název počítače do existující proměnné řetězce Automation. Nezáleží na tom, zda [je odkaz kanálem nebo sekvencí](../automation-graphical-authoring-intro.md#links-and-workflow) , protože kód očekává pouze jeden objekt ve výstupu.

![Nastavit jednoduchou proměnnou](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Další kroky

- Další informace o připojení aktivit při vytváření grafického obsahu najdete v tématu [odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow).
- Informace o tom, jak začít s grafickými Runbooky, najdete v tématu [můj první grafický Runbook](../automation-first-runbook-graphical.md).
