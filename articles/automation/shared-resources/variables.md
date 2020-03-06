---
title: Variabilní prostředky v Azure Automation
description: Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v Azure Automation.  V tomto článku se dozvíte o podrobnostech proměnných a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ce56b64502904308f45c74a5471447d93419452
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303048"
---
# <a name="variable-assets-in-azure-automation"></a>Variabilní prostředky v Azure Automation

Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v účtu Automation. Můžete je spravovat z Azure Portal, z PowerShellu, v rámci sady Runbook nebo v konfiguraci DSC.

Proměnné služeb automatizace jsou užitečné v následujících scénářích:

- Sdílení hodnoty mezi několika Runbooky nebo konfiguracemi DSC.

- Sdílení hodnoty mezi několika úlohami ze stejné konfigurace sady Runbook nebo DSC.

- Správa hodnoty používané Runbooky nebo konfiguracemi DSC z portálu nebo z příkazového řádku PowerShellu. Příkladem je sada běžných položek konfigurace, například konkrétní seznam názvů virtuálních počítačů, konkrétní skupiny prostředků, název domény služby AD a další.  

Vzhledem k tomu, že proměnné automatizace jsou trvalé, jsou k dispozici i v případě, že dojde k chybě Runbooku nebo konfigurace DSC. Toto chování umožňuje, aby jedna konfigurace sady Runbook nebo DSC nastavila hodnotu, kterou používá jiná sada Runbook nebo stejná sada Runbook nebo konfigurace DSC při příštím spuštění.

Při vytváření proměnné můžete zadat její šifrování a úložiště Azure Automation jako zabezpečený prostředek. K dalším zabezpečeným prostředkům patří přihlašovací údaje, certifikáty a připojení. Azure Automation tyto prostředky šifruje a ukládá je pomocí jedinečného klíče, který se generuje pro každý účet Automation. Klíč je uložený v systému spravovaném Key Vault. Před uložením zabezpečeného prostředku Azure Automation načte klíč z Key Vault a pak ho použije k zašifrování assetu. 

Azure Automation ukládá každou šifrovanou proměnnou bezpečně. Její hodnotu nejde načíst pomocí rutiny [Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) , která se dodává jako součást modulu Azure PowerShell. Jediným způsobem, jak načíst šifrovanou hodnotu, je použití aktivity **Get-AutomationVariable** v sadě Runbook nebo v konfiguraci DSC.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](../automation-update-azure-modules.md).

## <a name="variable-types"></a>Typy proměnných

Když vytvoříte proměnnou pomocí Azure Portal, je nutné zadat datový typ z rozevíracího seznamu, aby portál mohl zobrazit příslušný ovládací prvek pro zadání hodnoty proměnné. Následující typy proměnných jsou k dispozici v Azure Automation:

* Řetězec
* Integer
* DateTime
* Logická hodnota
* Null

Proměnná není omezena na určený datový typ. Pokud chcete zadat hodnotu jiného typu, je nutné nastavit proměnnou pomocí prostředí Windows PowerShell. Pokud označíte, že **není definováno**, hodnota proměnné je nastavena na **hodnotu null**a je nutné nastavit hodnotu pomocí rutiny [set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0) nebo aktivity **set-AutomationVariable** .

Portál nelze použít k vytvoření nebo změně hodnoty pro komplexní typ proměnné. Pomocí Windows PowerShellu ale můžete zadat hodnotu libovolného typu. Komplexní typy jsou načteny jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Můžete uložit více hodnot do jedné proměnné tak, že vytvoříte pole nebo zatřiďovací tabulku a uložíte ji do proměnné.

## <a name="powershell-cmdlets-that-create-and-manage-variable-assets"></a>Rutiny PowerShellu, které vytvářejí a spravují variabilní prostředky

Pro modul AZ Module se k vytváření a správě prostředků proměnných automatizace pomocí Windows PowerShellu použijí rutiny v následující tabulce. Dodávají se jako součást [modulu AZ. Automation](/powershell/azure/overview), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

| Rutina | Popis |
|:---|:---|
|[Get-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationvariable?view=azps-3.5.0) | Načte hodnotu existující proměnné.|
|[New-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationvariable?view=azps-3.5.0) | Vytvoří novou proměnnou a nastaví její hodnotu.|
|[Remove-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationvariable?view=azps-3.5.0)| Odstraní existující proměnnou.|
|[Set-AzAutomationVariable](https://docs.microsoft.com/powershell/module/az.automation/set-azautomationvariable?view=azps-3.5.0)| Nastaví hodnotu existující proměnné.|

## <a name="activities-to-access-variables"></a>Aktivity pro přístup k proměnným

Aktivity v následující tabulce se používají pro přístup k proměnným v sadách Runbook a konfiguracích DSC. Rozdíl mezi **Get-AzAutomationVariable** a **Get-AutomationVariable** je vysvětlen pro šifrované proměnné na začátku tohoto článku.

| Aktivita | Popis |
|:---|:---|
|**Get-AutomationVariable**|Načte hodnotu existující proměnné.|
|**Set-AutomationVariable**|Nastaví hodnotu existující proměnné.|

> [!NOTE]
> Vyhněte se použití proměnných v parametru název **Get-AutomationVariable** v sadě Runbook nebo konfiguraci DSC. Použití tohoto parametru může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a proměnných automatizace v době návrhu.

Funkce v následující tabulce se používají pro přístup k proměnným v sadě Runbook Python2 a k jejich načítání.

|Funkce Python2|Popis|
|:---|:---|
|automationassets.get_automation_variable|Načte hodnotu existující proměnné. |
|automationassets.set_automation_variable|Nastaví hodnotu existující proměnné. |

> [!NOTE]
> Abyste mohli získat přístup k funkcím assetu, musíte importovat modul **automationassets** v horní části sady Runbook Pythonu.

## <a name="creating-a-new-automation-variable"></a>Vytváří se nová proměnná automatizace.

### <a name="create-a-new-variable-using-the-azure-portal"></a>Vytvořit novou proměnnou pomocí Azure Portal

1. V účtu Automation klikněte na dlaždici **assety** a potom v okně **Assety (prostředky** ) vyberte **proměnné**.
2. Na dlaždici **proměnné** vyberte **přidat proměnnou**.
3. V okně **Nová proměnná** dokončete možnosti a potom kliknutím na **vytvořit** uložte novou proměnnou.

>[!NOTE]
>Chcete-li odebrat šifrování pro proměnnou, je nutné odstranit proměnnou a znovu ji vytvořit jako nezašifrovanou.

### <a name="create-a-new-variable-with-windows-powershell"></a>Vytvoření nové proměnné pomocí prostředí Windows PowerShell

Skript pomocí rutiny **New-AzAutomationVariable** vytvoří novou proměnnou a nastaví její počáteční hodnotu. Pak může hodnotu načíst pomocí **Get-AzAutomationVariable**. Pokud je hodnota jednoduchý typ, je načten stejný typ. Pokud se jedná o komplexní typ, načte se **PSCustomObject** typ.

Následující příklad ukazuje, jak vytvořit proměnnou typu String a pak vrátit její hodnotu.

```powershell
New-AzAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Následující příklad ukazuje, jak vytvořit proměnnou se složitým typem a poté načíst jeho vlastnosti. V takovém případě se použije objekt virtuálního počítače z [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) .

```powershell
$vm = Get-AzVM -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Použití proměnné v sadě Runbook nebo konfiguraci DSC

Aktivitu **set-AutomationVariable** můžete použít k nastavení hodnoty proměnné automatizace v Runbooku PowerShellu nebo v konfiguraci DSC a rutině **Get-AutomationVariable** pro její načtení. Nepoužívejte rutiny **set-AzAutomationVariable** a **Get-AzAutomationVariable** ani jejich ekvivalenty AzureRM modulu v sadě Runbook nebo konfiguraci DSC, protože jsou méně efektivní než aktivity pracovního postupu. 

Všimněte si, že hodnotu zabezpečené proměnné nelze načíst pomocí **Get-AzAutomationVariable** nebo jejího ekvivalentu modulu AzureRM. 

Jediným způsobem, jak vytvořit novou proměnnou v rámci sady Runbook nebo konfigurace DSC, je použít rutinu **New-AzAutomationVariable** .

### <a name="textual-runbook-samples"></a>Ukázky textových runbooků

#### <a name="set-and-retrieve-a-simple-value-from-a-variable"></a>Nastavení a načtení jednoduché hodnoty z proměnné

Následující vzorové příkazy ukazují, jak nastavit a načíst proměnnou v textovém Runbooku. Tato ukázka předpokládá vytvoření celočíselných proměnných s názvem *NumberOfIterations* a *NumberOfRunnings* a řetězcovou proměnnou s názvem *SampleMessage*.

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

#### <a name="set-and-retrieve-a-variable-in-a-python2-runbook"></a>Nastavení a načtení proměnné v Runbooku Python2

Následující příklad ukazuje, jak použít proměnnou, nastavit proměnnou a zpracovat výjimku pro neexistující proměnnou v sadě Python2 Runbook.

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

V grafickém Runbooku můžete přidat aktivitu **Get-AutomationVariable** nebo **set-AutomationVariable** . Stačí kliknout pravým tlačítkem myši na proměnnou v podokně Knihovna v grafickém editoru a vybrat aktivitu, kterou požadujete.

![Přidat proměnnou na plátno](../media/variables/runbook-variable-add-canvas.png)

#### <a name="set-values-in-a-variable"></a>Nastavení hodnot v proměnné

Následující obrázek ukazuje ukázkové aktivity pro aktualizaci proměnné s jednoduchou hodnotou v grafickém Runbooku. V této ukázce **Get-AzVM** načte jeden virtuální počítač Azure a uloží název počítače do existující proměnné Automation s typem řetězce. Nezáleží na tom, zda [je odkaz kanálem nebo sekvencí](../automation-graphical-authoring-intro.md#links-and-workflow) , protože kód očekává pouze jeden objekt ve výstupu.

![Nastavit jednoduchou proměnnou](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Další kroky

- Další informace o připojení aktivit při vytváření grafického obsahu najdete v tématu [odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow).
- Informace o tom, jak začít s grafickými Runbooky, najdete v tématu [můj první grafický Runbook](../automation-first-runbook-graphical.md).
