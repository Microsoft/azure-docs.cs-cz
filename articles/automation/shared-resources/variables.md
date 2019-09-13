---
title: Variabilní prostředky v Azure Automation
description: Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v Azure Automation.  V tomto článku se dozvíte o podrobnostech proměnných a o tom, jak s nimi pracovat v textovém i grafickém vytváření.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: bobbytreed
ms.author: robreed
ms.date: 05/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3fe008d20ab43636b59861bcc5a7914ba0fca17e
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910070"
---
# <a name="variable-assets-in-azure-automation"></a>Variabilní prostředky v Azure Automation

Proměnné assets jsou hodnoty, které jsou k dispozici pro všechny Runbooky a konfigurace DSC v účtu Automation. Je možné je spravovat z Azure Portal, PowerShellu, v rámci sady Runbook nebo konfigurace DSC. Proměnné služeb automatizace jsou užitečné v následujících scénářích:

- Sdílení hodnoty mezi několika Runbooky nebo konfiguracemi DSC.

- Sdílení hodnoty mezi několika úlohami ze stejné konfigurace sady Runbook nebo DSC.

- Spravujte hodnotu z portálu nebo z příkazového řádku PowerShellu, který používají Runbooky nebo konfigurace DSC, jako je například sada běžných položek konfigurace, například konkrétní seznam názvů virtuálních počítačů, konkrétní skupiny prostředků, název domény služby AD a další.  

Vzhledem k tomu, že proměnné automatizace jsou trvalé, jsou k dispozici i v případě, že dojde k chybě Runbooku nebo konfigurace DSC. Toto chování umožňuje nastavit hodnotu pomocí jedné sady Runbook, která je pak používána jiným systémem, nebo ji používá stejná sada Runbook nebo konfigurace DSC při příštím spuštění.

Při vytvoření proměnné můžete určit, že je uložený zašifrovaný. Šifrované proměnné jsou bezpečně uložené v Azure Automation a její hodnota se nedá načíst z rutiny [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) , která se dodává jako součást modulu Azure PowerShell. Jediný způsob, jak lze načíst šifrovanou hodnotu, je z aktivity **Get-AutomationVariable** v sadě Runbook nebo v konfiguraci DSC. Pokud chcete změnit šifrovanou proměnnou tak, aby byla zašifrovaná, můžete tuto proměnnou odstranit a znovu vytvořit jako nešifrované.

>[!NOTE]
>Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou zašifrované a uložené v Azure Automation pomocí jedinečného klíče, který se generuje pro každý účet Automation. Tento klíč je uložený v Key Vault spravovaném systémem. Před uložením zabezpečeného prostředku se klíč načte z Key Vault a pak se použije k zašifrování assetu. Tento proces je spravovaný pomocí Azure Automation.

## <a name="variable-types"></a>Typy proměnných

Když vytvoříte proměnnou pomocí Azure Portal, je nutné zadat datový typ z rozevíracího seznamu, aby portál mohl zobrazit příslušný ovládací prvek pro zadání hodnoty proměnné. Proměnná není omezena na tento datový typ. Pokud chcete zadat hodnotu jiného typu, je nutné nastavit proměnnou pomocí prostředí Windows PowerShell. Pokud zadáte **nedefinované**, pak hodnota proměnné nastaví na **$null**a musíte nastavit hodnotu pomocí rutiny [set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) nebo aktivity **set-AutomationVariable** . V portálu nemůžete vytvořit nebo změnit hodnotu komplexního typu proměnné, ale můžete zadat hodnotu libovolného typu pomocí Windows PowerShellu. Komplexní typy jsou vraceny jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Můžete uložit více hodnot do jedné proměnné tak, že vytvoříte pole nebo zatřiďovací tabulku a uložíte ji do proměnné.

Níže jsou uvedeny typy proměnných, které jsou k dispozici v automatizaci:

* Řetězec
* Integer
* DateTime
* Logická hodnota
* Null

## <a name="azurerm-powershell-cmdlets"></a>Rutiny PowerShellu pro AzureRM

Rutiny v následující tabulce pro AzureRM slouží k vytváření a správě assetů přihlašovacích údajů automatizace pomocí Windows PowerShellu. Dodávají se jako součást [modulu AzureRM. Automation](/powershell/azure/overview), který je k dispozici pro použití v sadách Automation a konfiguracích DSC.

| Rutiny | Popis |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Načte hodnotu existující proměnné.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Vytvoří novou proměnnou a nastaví její hodnotu.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Odstraní existující proměnnou.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Nastaví hodnotu existující proměnné.|

## <a name="activities"></a>Aktivity

Aktivity v následující tabulce se používají pro přístup k proměnným v sadě Runbook a konfiguracích DSC. Rozdíl mezi rutinami Get-AzureRmAutomationVariable a Get-AutomationVariable se na začátku tohoto dokumentu upřesňuje výše.

| Aktivity | Popis |
|:---|:---|
|Get-AutomationVariable|Načte hodnotu existující proměnné.|
|Set-AutomationVariable|Nastaví hodnotu existující proměnné.|

> [!NOTE]
> Měli byste se vyhnout používání proměnných v parametru – name v sadě Runbook nebo konfiguraci DSC, protože to může zkomplikovat zjišťování závislostí mezi sadami Runbook a konfigurací DSC a proměnných automatizace v době návrhu.

Funkce v následující tabulce se používají pro přístup k proměnným v sadě Runbook Python2 a k jejich načítání.

|Funkce Python2|Popis|
|:---|:---|
|automationassets.get_automation_variable|Načte hodnotu existující proměnné. |
|automationassets.set_automation_variable|Nastaví hodnotu existující proměnné. |

> [!NOTE]
> Aby bylo možné získat přístup k funkcím assetu, musíte naimportovat modul "automationassets" v horní části Runbooku sady Python.

## <a name="creating-a-new-automation-variable"></a>Vytváří se nová proměnná automatizace.

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Vytvoření nové proměnné pomocí Azure Portal

1. V účtu Automation klikněte na dlaždici **assety** a potom v okně **Assety (prostředky** ) vyberte **proměnné**.
2. Na dlaždici **proměnné** vyberte **přidat proměnnou**.
3. V okně **Nová proměnná** dokončete možnosti a klikněte na **vytvořit** Uložit novou proměnnou.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Vytvoření nové proměnné pomocí prostředí Windows PowerShell

Rutina [New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) vytvoří novou proměnnou a nastaví její počáteční hodnotu. Hodnotu můžete načíst pomocí [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Pokud je hodnota jednoduchý typ, je vrácen stejný typ. Pokud se jedná o komplexní typ, vrátí se **PSCustomObject** .

Následující vzorové příkazy ukazují, jak vytvořit proměnnou typu String a pak vrátit její hodnotu.

```powershell
New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
–Encrypted $false –Value 'My String'
$string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value
```

Následující vzorové příkazy ukazují, jak vytvořit proměnnou se složitým typem a jak pak vracet své vlastnosti. V takovém případě se použije objekt virtuálního počítače z **Get-AzureRmVm** .

```powershell
$vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm

$vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
$vmName = $vmValue.Name
$vmIpAddress = $vmValue.IpAddress
```

## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Použití proměnné v sadě Runbook nebo konfiguraci DSC

Aktivitu **set-AutomationVariable** můžete použít k nastavení hodnoty proměnné automatizace v Runbooku PowerShellu nebo v konfiguraci DSC a rutině **Get-AutomationVariable** pro její načtení. Rutiny **set-AzureRMAutomationVariable** nebo **Get-AzureRMAutomationVariable** byste neměli používat v sadě RUNBOOK nebo v konfiguraci DSC, protože jsou méně efektivní než aktivity pracovního postupu. Nemůžete také načíst hodnotu zabezpečení proměnných pomocí **Get-AzureRMAutomationVariable**. Jediným způsobem, jak vytvořit novou proměnnou v rámci sady Runbook nebo konfigurace DSC, je použít rutinu [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) .

### <a name="textual-runbook-samples"></a>Ukázky textových runbooků

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Nastavení a načtení jednoduché hodnoty z proměnné

Následující vzorové příkazy ukazují, jak nastavit a načíst proměnnou v textovém Runbooku. V této ukázce se předpokládá, že byly vytvořeny proměnné typu Integer s názvem *NumberOfIterations* a *NumberOfRunnings* a proměnná typu String s názvem *SampleMessage* .

```powershell
$NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
$NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
$SampleMessage = Get-AutomationVariable -Name 'SampleMessage'

Write-Output "Runbook has been run $NumberOfRunnings times."

for ($i = 1; $i -le $NumberOfIterations; $i++) {
    Write-Output "$i`: $SampleMessage"
}
Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)
```

#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Nastavení a načtení proměnné v Python2

Následující vzorový kód ukazuje, jak použít proměnnou, nastavit proměnnou a zpracovat výjimku pro neexistující proměnnou v sadě Python2 Runbook.

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
    value = automationassets.get_automation_variable("non-existing variable")
except AutomationAssetNotFound:
    print "variable not found"
```

### <a name="graphical-runbook-samples"></a>Ukázky grafického Runbooku

V grafickém Runbooku přidáte pomocí příkazu **Get-AutomationVariable** nebo **set-AutomationVariable** kliknutím pravým tlačítkem myši na proměnnou v podokně Knihovna v grafickém editoru a vyberete aktivitu, kterou požadujete.

![Přidat proměnnou na plátno](../media/variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Nastavení hodnot v proměnné

Následující obrázek ukazuje ukázkové aktivity pro aktualizaci proměnné s jednoduchou hodnotou v grafickém Runbooku. V této ukázce **Get-AzureRmVM** načte jeden virtuální počítač Azure a název počítače uloží do existující proměnné Automation s typem řetězce. Nezáleží na tom, zda [se jedná o kanál nebo sekvenci](../automation-graphical-authoring-intro.md#links-and-workflow) , protože očekáváte pouze jeden objekt ve výstupu.

![Nastavit jednoduchou proměnnou](../media/variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Další kroky

- Další informace o propojení aktivit společně s grafickým vytvářením najdete v tématu [odkazy v grafickém vytváření](../automation-graphical-authoring-intro.md#links-and-workflow) .
- První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](../automation-first-runbook-graphical.md).
