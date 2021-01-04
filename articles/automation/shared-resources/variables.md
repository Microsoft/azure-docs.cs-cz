---
title: Správa proměnných v Azure Automation
description: Tento článek popisuje, jak pracovat s proměnnými v sadách Runbook a konfiguracích DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/01/2020
ms.topic: conceptual
ms.openlocfilehash: d064eb0b748c361b76139b1a21d25cec8996e818
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734772"
---
# <a name="manage-variables-in-azure-automation"></a>Správa proměnných v Azure Automation

Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v účtu Automation. Můžete je spravovat z Azure Portal, z PowerShellu, v rámci sady Runbook nebo v konfiguraci DSC.

Proměnné automatizace jsou užitečné pro následující scénáře:

- Sdílení hodnoty mezi několika Runbooky nebo konfiguracemi DSC.

- Sdílení hodnoty mezi několika úlohami ze stejné konfigurace sady Runbook nebo DSC.

- Správa hodnoty používané Runbooky nebo konfiguracemi DSC z portálu nebo z příkazového řádku PowerShellu. Příkladem je sada běžných položek konfigurace, například konkrétní seznam názvů virtuálních počítačů, konkrétní skupiny prostředků, název domény služby AD a další.  

Azure Automation uchovává proměnné a zpřístupňuje je i v případě, že dojde k chybě Runbooku nebo konfigurace DSC. Toto chování umožňuje, aby jedna konfigurace sady Runbook nebo DSC nastavila hodnotu, kterou používá jiná sada Runbook nebo stejná sada Runbook nebo konfigurace DSC při příštím spuštění.

Azure Automation ukládá každou šifrovanou proměnnou bezpečně. Když vytvoříte proměnnou, můžete její šifrování a úložiště zadat Azure Automation jako zabezpečený prostředek. Po vytvoření proměnné nemůžete změnit její stav šifrování, aniž byste museli znovu vytvořit proměnnou. Pokud máte proměnné účtu Automation, které ukládají citlivé údaje, které ještě nejsou zašifrované, musíte je odstranit a znovu vytvořit jako šifrované proměnné. Azure Security Center doporučení je šifrovat všechny Azure Automation proměnné, jak je popsané v tématu [proměnné účtu Automation by měly být šifrované](../../security-center/recommendations-reference.md#recs-computeapp). Pokud máte nešifrované proměnné, které chcete vyloučit z tohoto doporučení zabezpečení, přečtěte si téma [vyloučení prostředku z doporučení a zabezpečeného skóre](../../security-center/exempt-resource.md) pro vytvoření pravidla výjimky.

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Azure Automation ukládá klíč do Key Vault spravovaném systémem. Před uložením zabezpečeného assetu Automation načte klíč z Key Vault a pak ho použije k zašifrování prostředku.

## <a name="variable-types"></a>Typy proměnných

Když vytvoříte proměnnou pomocí Azure Portal, je nutné zadat datový typ z rozevíracího seznamu, aby portál mohl zobrazit příslušný ovládací prvek pro zadání hodnoty proměnné. Následující typy proměnných jsou k dispozici v Azure Automation:

* Řetězec
* Integer
* DateTime
* Logická hodnota
* Null

Proměnná není omezena na zadaný datový typ. Pokud chcete zadat hodnotu jiného typu, je nutné nastavit proměnnou pomocí prostředí Windows PowerShell. Pokud označíte `Not defined` , hodnota proměnné je nastavená na null. Hodnotu je nutné nastavit pomocí rutiny [set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable) nebo interní `Set-AutomationVariable` rutiny.

Nemůžete použít Azure Portal k vytvoření nebo změně hodnoty pro komplexní typ proměnné. Pomocí Windows PowerShellu ale můžete zadat hodnotu libovolného typu. Komplexní typy jsou načteny jako [Newtonsoft.Jsna. LINQ. JProperty](https://www.newtonsoft.com/json/help/html/N_Newtonsoft_Json_Linq.htm) pro komplexní typ objektu místo PSObject typu [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Můžete uložit více hodnot do jedné proměnné tak, že vytvoříte pole nebo zatřiďovací tabulku a uložíte ji do proměnné.

>[!NOTE]
>Proměnné názvu virtuálního počítače můžou být maximálně 80 znaků. Proměnné skupiny prostředků můžou mít maximálně 90 znaků. Viz [pravidla a omezení pojmenování pro prostředky Azure](../../azure-resource-manager/management/resource-name-rules.md).

## <a name="powershell-cmdlets-to-access-variables"></a>Rutiny PowerShellu pro přístup k proměnným

Rutiny v následující tabulce vytvářejí a spravují proměnné automatizace pomocí PowerShellu. Dodávají se jako součást [AZ moduls](modules.md#az-modules).

| Rutina | Popis |
|:---|:---|
|[Get-AzAutomationVariable](/powershell/module/az.automation/get-azautomationvariable) | Načte hodnotu existující proměnné. Pokud je hodnota jednoduchý typ, je načten stejný typ. Pokud se jedná o komplexní typ, `PSCustomObject` načte se typ. <br>**Poznámka:** Tuto rutinu nemůžete použít k načtení hodnoty šifrované proměnné. Jediným způsobem, jak to provést, je použít interní `Get-AutomationVariable` rutinu v konfiguraci sady Runbook nebo DSC. Viz [interní rutiny pro přístup k proměnným](#internal-cmdlets-to-access-variables). |
|[New-AzAutomationVariable](/powershell/module/az.automation/new-azautomationvariable) | Vytvoří novou proměnnou a nastaví její hodnotu.|
|[Remove-AzAutomationVariable](/powershell/module/az.automation/remove-azautomationvariable)| Odstraní existující proměnnou.|
|[Set-AzAutomationVariable](/powershell/module/az.automation/set-azautomationvariable)| Nastaví hodnotu pro existující proměnnou. |

## <a name="internal-cmdlets-to-access-variables"></a>Interní rutiny pro přístup k proměnným

Interní rutiny v následující tabulce se používají pro přístup k proměnným v sadách Runbook a konfiguracích DSC. Tyto rutiny se dodávají s globálním modulem `Orchestrator.AssetManagement.Cmdlets` . Další informace najdete v tématu [interní rutiny](modules.md#internal-cmdlets).

| Interní rutina | Popis |
|:---|:---|
|`Get-AutomationVariable`|Načte hodnotu existující proměnné.|
|`Set-AutomationVariable`|Nastaví hodnotu pro existující proměnnou.|

> [!NOTE]
> Vyhněte se použití proměnných v `Name` parametru `Get-AutomationVariable` v sadě Runbook nebo konfiguraci DSC. Použití proměnných může zkomplikovat zjišťování závislostí mezi sadami Runbook a proměnnými automatizace v době návrhu.

`Get-AutomationVariable` nefunguje v prostředí PowerShell, ale pouze v sadě Runbook nebo konfiguraci DSC. Chcete-li například zobrazit hodnotu zašifrované proměnné, můžete vytvořit sadu Runbook, která získá proměnnou a následně ji zapsat do výstupního datového proudu:

```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="python-functions-to-access-variables"></a>Funkce Pythonu pro přístup k proměnným

Funkce v následující tabulce se používají pro přístup k proměnným v sadě Runbook Python 2 a 3. Sady Python 3 Runbooky jsou momentálně ve verzi Preview.

|Funkce Pythonu|Popis|
|:---|:---|
|`automationassets.get_automation_variable`|Načte hodnotu existující proměnné. |
|`automationassets.set_automation_variable`|Nastaví hodnotu pro existující proměnnou. |

> [!NOTE]
> Aby bylo možné `automationassets` získat přístup k funkcím assetu, musíte importovat modul v horní části Runbooku sady Python.

## <a name="create-and-get-a-variable"></a>Vytvoření a získání proměnné

>[!NOTE]
>Chcete-li odebrat šifrování pro proměnnou, je nutné odstranit proměnnou a znovu ji vytvořit jako nezašifrovanou.

### <a name="create-and-get-a-variable-using-the-azure-portal"></a>Vytvoření a získání proměnné pomocí Azure Portal

1. Z účtu Automation v levém podokně vyberte **proměnné** v části **sdílené prostředky**.
2. Na stránce **proměnné** vyberte **přidat proměnnou**.
3. Dokončete možnosti na stránce **Nová proměnná** a pak výběrem možnosti **vytvořit** uložte novou proměnnou.

> [!NOTE]
> Jakmile uložíte zašifrovanou proměnnou, nelze ji zobrazit na portálu. Dá se aktualizovat jenom.

### <a name="create-and-get-a-variable-in-windows-powershell"></a>Vytvoření a získání proměnné v prostředí Windows PowerShell

Vaše sada Runbook nebo konfigurace DSC pomocí `New-AzAutomationVariable` rutiny vytvoří novou proměnnou a nastaví její počáteční hodnotu. Pokud je proměnná zašifrovaná, volání by mělo použít `Encrypted` parametr. Váš skript může načíst hodnotu proměnné pomocí `Get-AzAutomationVariable` .

>[!NOTE]
>Skript PowerShellu nemůže načíst šifrovanou hodnotu. Jediným způsobem, jak to provést, je použití interní `Get-AutomationVariable` rutiny.

Následující příklad ukazuje, jak vytvořit řetězcovou proměnnou a poté vrátit její hodnotu.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Následující příklad ukazuje, jak vytvořit proměnnou se složitým typem a pak načíst jeho vlastnosti. V tomto případě se používá objekt virtuálního počítače z [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) , který určuje podmnožinu svých vlastností.

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01" | Select Name, Location, Extensions
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable"

$vmName = $vmValue.Name
$vmExtensions = $vmValue.Extensions
```

## <a name="textual-runbook-examples"></a>Příklady textových runbooků

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Následující příklad ukazuje, jak nastavit a načíst proměnnou v textovém Runbooku. Tento příklad předpokládá vytvoření celočíselných proměnných s názvem `NumberOfIterations` a `NumberOfRunnings` a řetězcovou proměnnou s názvem `SampleMessage` .

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

# <a name="python-2"></a>[Python 2](#tab/python2)

Následující příklad ukazuje, jak získat proměnnou, nastavit proměnnou a zpracovat výjimku pro neexistující proměnnou v sadě Runbook Python 2.

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

# <a name="python-3"></a>[Python 3](#tab/python3)

Následující příklad ukazuje, jak získat proměnnou, nastavit proměnnou a zpracovat výjimku pro neexistující proměnnou v sadě Runbook Python 3 (Preview).

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
    print ("variable not found")
```

---

## <a name="graphical-runbook-examples"></a>Příklady grafického Runbooku

V grafickém Runbooku můžete přidat aktivity pro interní rutiny `Get-AutomationVariable` nebo `Set-AutomationVariable` . Stačí kliknout pravým tlačítkem na každou proměnnou v podokně Knihovna v grafickém editoru a vybrat aktivitu, kterou požadujete.

![Přidat proměnnou na plátno](../media/variables/runbook-variable-add-canvas.png)

Následující obrázek ukazuje ukázkové aktivity pro aktualizaci proměnné s jednoduchou hodnotou v grafickém Runbooku. V tomto příkladu aktivita `Get-AzVM`  načte jeden virtuální počítač Azure a uloží název počítače do existující proměnné řetězce Automation. Nezáleží na tom, zda [je odkaz kanálem nebo sekvencí](../automation-graphical-authoring-intro.md#use-links-for-workflow) , protože kód očekává pouze jeden objekt ve výstupu.

![Nastavit jednoduchou proměnnou](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Další kroky

* Další informace o rutinách, které se používají pro přístup k proměnným, najdete v tématu [Správa modulů v Azure Automation](modules.md).
* Obecné informace o sadách Runbook naleznete [v tématu Spuštění Runbooku v Azure Automation](../automation-runbook-execution.md).
* Podrobnosti o konfiguracích DSC najdete v tématu [Přehled konfigurace stavu Azure Automation](../automation-dsc-overview.md).
