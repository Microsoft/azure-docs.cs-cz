---
title: Prostředky proměnných ve službě Azure Automation
description: Proměnné prostředky jsou hodnoty, které jsou k dispozici pro všechny sady Runbook a Konfigurace DSC v Azure Automation.  Tento článek vysvětluje podrobnosti o proměnných a jak s nimi pracovat v textovéi a grafické authoring.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d4a4a92feb3e1b400c0f40076148f7898c4bdef1
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365824"
---
# <a name="variable-assets-in-azure-automation"></a>Prostředky proměnných ve službě Azure Automation

Proměnné datové zdroje jsou hodnoty, které jsou k dispozici pro všechny sady Runbook a Konfigurace DSC v účtu automatizace. Můžete je spravovat z portálu Azure, z Prostředí PowerShell, v rámci sady Runbook nebo v konfiguraci DSC.

Proměnné automatizace jsou užitečné pro následující scénáře:

- Sdílení hodnoty mezi více runbooky nebo Konfigurace DSC.

- Sdílení hodnoty mezi více úlohami ze stejné konfigurace runbooku nebo DSC.

- Správa hodnoty používané runbooky nebo Konfigurace DSC z portálu nebo z příkazového řádku Prostředí PowerShell. Příkladem je sada běžných položek konfigurace, jako je například konkrétní seznam názvů virtuálních počítačů, konkrétní skupina prostředků, název domény služby AD a další.  

Azure Automation zachová proměnné a zpřístupní je i v případě, že se nezdaří konfigurace sady Runbook nebo DSC. Toto chování umožňuje jedné konfiguraci sady Runbook nebo DSC nastavit hodnotu, která je pak použita jinou sadou runbook nebo stejnou konfigurací sady Runbook nebo DSC při příštím spuštění.

Azure Automation ukládá každou šifrovanou proměnnou bezpečně. Při vytváření proměnné můžete zadat její šifrování a úložiště pomocí Azure Automation jako zabezpečený prostředek. Mezi další zabezpečené datové zdroje patří pověření, certifikáty a připojení. Azure Automation šifruje tyto prostředky a ukládá je pomocí jedinečného klíče, který se generuje pro každý účet automatizace. Klíč je uložen v systémově spravovaném trezoru klíčů. Před uložením zabezpečeného datového zdroje azure automation načte klíč z trezoru klíčů a pak ho použije k šifrování datového zdroje. 

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Typy proměnných

Při vytváření proměnné s portálem Azure, musíte zadat datový typ z rozevíracího seznamu tak, aby portál můžete zobrazit příslušný ovládací prvek pro zadání hodnoty proměnné. V Azure Automation jsou k dispozici typy proměnných:

* Řetězec
* Integer
* DateTime
* Logická hodnota
* Null

Proměnná není omezena na určený datový typ. Chcete-li zadat hodnotu jiného typu, musíte proměnnou nastavit pomocí prostředí Windows PowerShell. Pokud zadáváte `Not defined`, hodnota proměnné je nastavena na Hodnotu Null a je nutné nastavit hodnotu rutinou [Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) nebo aktivitou. `Set-AutomationVariable`

Nemůžete použít portál Azure k vytvoření nebo změně hodnoty pro komplexní typ proměnné. Můžete však zadat hodnotu libovolného typu pomocí prostředí Windows PowerShell. Komplexní typy jsou načteny jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Více hodnot můžete uložit do jedné proměnné vytvořením pole nebo hodnoty hash a uložením do proměnné.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Rutiny prostředí PowerShell, které vytvářejí a spravují proměnné datové zdroje

Pro modul Az se rutiny v následující tabulce používají k vytváření a správě proměnných prostředků automatizace pomocí prostředí Windows PowerShell. Dodávají se jako součást [modulu Az.Automation](/powershell/azure/overview), který je k dispozici pro použití v runbookech automation a konfiguracích DSC.

| Rutina | Popis |
|:---|:---|
|[Proměnná Get-AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Načte hodnotu existující proměnné. Tuto rutinu nelze použít k načtení hodnoty šifrované proměnné. Jediný způsob, jak to udělat, je pomocí aktivity `Get-AutomationVariable` v konfiguraci runbook nebo DSC. |
|[Proměnná nová azautomation](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Vytvoří novou proměnnou a nastaví její hodnotu.|
|[Odebrat proměnnou AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Odebere existující proměnnou.|
|[Proměnná set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Nastaví hodnotu pro existující proměnnou. |

## <a name="activities-to-access-variables-in-runbooks-and-dsc-configurations"></a>Aktivity pro přístup k proměnným v sadách Runbook a DSC konfigurace

Aktivity v následující tabulce se používají pro přístup k proměnným v konfiguracích runbooků a DSC. Rutiny pro tyto aktivity jsou dodávány s globálním modulem `Orchestrator.AssetManagement.Cmdlets`.

| Aktivita | Popis |
|:---|:---|
|`Get-AutomationVariable`|Načte hodnotu existující proměnné.|
|`Set-AutomationVariable`|Nastaví hodnotu pro existující proměnnou.|

> [!NOTE]
> Nepoužívejte proměnné v `Name` parametru `Get-AutomationVariable` v konfiguraci runbooku nebo DSC. Použití tohoto parametru může komplikovat zjišťování závislostí mezi runbooky nebo konfiguracemi DSC a proměnnými automatizace v době návrhu.

Všimněte `Get-AutomationVariable` si, že nefunguje v Prostředí PowerShell, ale pouze v konfiguraci runbook nebo DSC. Chcete-li například zobrazit hodnotu šifrované proměnné, můžete vytvořit runbook, který získá proměnnou a pak ji zapíše do výstupního datového proudu:
 
```powershell
$mytestencryptvar = Get-AutomationVariable -Name TestVariable
Write-output "The encrypted value of the variable is: $mytestencryptvar"
```

## <a name="functions-to-access-variables-in-python-2-runbooks"></a>Funkce pro přístup k proměnným v runbookech Pythonu 2

Funkce v následující tabulce se používají pro přístup k proměnným v pythonu 2 runbook.

|Funkce Pythonu 2|Popis|
|:---|:---|
|`automationassets.get_automation_variable`|Načte hodnotu existující proměnné. |
|`automationassets.set_automation_variable`|Nastaví hodnotu pro existující proměnnou. |

> [!NOTE]
> Chcete-li `automationassets` získat přístup k funkcím datových zdrojů, musíte importovat modul v horní části sady Runbook pythonu.

## <a name="working-with-automation-variables"></a>Práce s proměnnými automatizace

>[!NOTE]
>Pokud chcete odebrat šifrování proměnné, musíte proměnnou odstranit a znovu ji vytvořit jako nezašifrovanou.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Vytvoření nové proměnné pomocí portálu Azure

1. Na účtu Automation klikněte na dlaždici **Prostředky,** potom na okno **Prostředky** a vyberte **Proměnné**.
2. Na dlaždici **Proměnné** vyberte **Přidat proměnnou**.
3. Vyplňte možnosti v okně **Nová proměnná** a kliknutím na **Vytvořit** uložte novou proměnnou.

> [!NOTE]
> Po uložení šifrované proměnné ji nelze na portálu zobrazit. Může být pouze aktualizován.

### <a name="create-and-use-a-variable-in-windows-powershell"></a>Vytvoření a použití proměnné v prostředí Windows PowerShell

Skript prostředí PowerShell `New-AzAutomationVariable` používá rutinu nebo jeho ekvivalent modulu AzureRM k vytvoření nové proměnné a nastavení její počáteční hodnoty. Pokud je proměnná zašifrována, volání by mělo použít `Encrypted` parametr.

Skript načte hodnotu proměnné pomocí [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0). Pokud je hodnota jednoduchý typ, rutina načte stejný typ. Pokud se jedná o komplexní `PSCustomObject` typ, je načten typ.

>[!NOTE]
>Skript prostředí PowerShell nemůže načíst šifrovanou hodnotu. Jediný způsob, jak to udělat, je použít aktivitu `Get-AutomationVariable` v konfiguraci runbooknebo DSC.

Následující příklad ukazuje, jak vytvořit proměnnou typu String a potom vrátit její hodnotu.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Následující příklad ukazuje, jak vytvořit proměnnou se složitým typem a potom načíst její vlastnosti. V tomto případě se používá objekt virtuálního počítače z [Get-AzVM.](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0)

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

### <a name="create-and-use-a-variable-in-a-runbook-or-dsc-configuration"></a>Vytvoření a použití proměnné v konfiguraci runbooku nebo DSC

Jediný způsob, jak vytvořit novou proměnnou z konfigurace sady Runbook `New-AzAutomationVariable` nebo DSC, je použití rutiny nebo jejího ekvivalentu modulu AzureRM. Skript používá tuto rutinu k nastavení počáteční hodnoty proměnné. Skript pak může načíst `Get-AzAutomationVariable`hodnotu pomocí aplikace . Pokud je hodnota jednoduchý typ, stejný typ je načten. Pokud se jedná o komplexní `PSCustomObject` typ, je načten typ.

>[!NOTE]
>Jediný způsob, jak načíst šifrovanou hodnotu, je použití aktivity `Get-AutomationVariable` v konfiguraci runbooku nebo DSC. 

### <a name="textual-runbook-samples"></a>Textové ukázky runbooku

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Nastavení a načtení jednoduché hodnoty z proměnné

Následující ukázkové příkazy ukazují, jak nastavit a načíst proměnnou v textové sadě Runbook. Tato ukázka předpokládá vytvoření celých proměnných s názvem `NumberOfIterations` a `NumberOfRunnings` a proměnné řetězce s názvem `SampleMessage`.

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

#### <a name="set-and-retrieve-a-variable-in-a-python-2-runbook"></a>Nastavení a načtení proměnné v sadě Runbook pythonu 2

Následující ukázka ukazuje, jak použít proměnnou, nastavit proměnnou a zpracovat výjimku pro neexistující proměnnou v sadě Runbook Python 2.

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

### <a name="graphical-runbook-samples"></a>Grafické ukázky runbooků

V grafickém runbooku můžete `Get-AutomationVariable` `Set-AutomationVariable` přidat aktivitu nebo. Jednoduše klikněte pravým tlačítkem myši na proměnnou v podokně Knihovna grafického editoru a vyberte požadovanou aktivitu.

![Přidání proměnné na plátno](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Nastavení hodnot v proměnné

Následující obrázek znázorňuje ukázkové aktivity pro aktualizaci proměnné s jednoduchou hodnotou v grafickém runbooku. V této `Get-AzVM` ukázce načte jeden virtuální počítač Azure a uloží název počítače do existující proměnné řetězce automatizace. Nezáleží na tom, zda [je propojení kanálu nebo sekvence,](../automation-graphical-authoring-intro.md#links-and-workflow) protože kód očekává pouze jeden objekt ve výstupu.

![Nastavit jednoduchou proměnnou](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Další kroky

- Další informace o propojení aktivit ve vytváření grafiky naleznete v [tématu Odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow).
- Informace o tom, jak začít s grafickými runbooky, najdete [v tématu Moje první grafická kniha runbook](../automation-first-runbook-graphical.md).
