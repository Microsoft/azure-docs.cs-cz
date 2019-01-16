---
title: Proměnných assetů ve službě Azure Automation
description: Proměnných assetů jsou hodnoty, které jsou k dispozici pro všechny runbooky a konfigurace DSC ve službě Azure Automation.  Tento článek vysvětluje podrobnosti proměnné a jak pracovat s nimi v textové a grafické vytváření obsahu.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: aaf8671ec4bfc4bcf6fecaa357f6ae983eb04499
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330513"
---
# <a name="variable-assets-in-azure-automation"></a>Proměnných assetů ve službě Azure Automation

Proměnných assetů jsou hodnoty, které jsou k dispozici pro všechny runbooky a konfigurace DSC ve vašem účtu automation. Může být vytvořen, upravit a načíst z portálu Azure portal, prostředí Windows PowerShell a z runbooku nebo konfigurace DSC. Proměnné služeb automatizace jsou užitečné v následujících scénářích:

- Sdílení hodnoty mezi několika runbooky a konfigurace DSC.

- Sdílení hodnoty mezi několika úlohami stejné runbooku nebo konfigurace DSC.

- Správa hodnoty z portálu nebo z příkazového řádku prostředí Windows PowerShell, který používá runbooky a konfigurace DSC, jako je například sada běžné položky konfigurace jako konkrétního seznamu názvy virtuálních počítačů, konkrétní skupině prostředků, název domény služby AD, atd.  

Proměnné služeb automatizace jsou trvalé, takže se budete mít k dispozici i v případě, runbooku nebo konfigurace DSC se nezdaří. Díky tomu také hodnotu nastavení jedné sady runbook, který se pak použije v jiném, nebo se používá ve stejném runbooku nebo konfigurace DSC při příštím spuštění.

Při vytváření proměnné je můžete zadat, že je uložen šifrované. Pokud je proměnná zašifrovaná, bezpečně se uloží ve službě Azure Automation a jeho hodnotu nelze načíst z [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable) rutinu, která je dodávána jako součást modulu Azure PowerShell. Jediným způsobem, že je možné načíst hodnotu zašifrovaných je z **Get-AutomationVariable** aktivitu v runbooku nebo konfigurace DSC.

>[!NOTE]
>Zabezpečené prostředky ve službě Azure Automation zahrnovat přihlašovací údaje, certifikátů, připojení a zašifrované proměnné. Tyto prostředky jsou zašifrované a uložené ve službě Azure Automation jednotlivých účtů automation pomocí jedinečný klíč, který je generován. Tento klíč je uložen v systému spravované služby Key Vault. Před uložením o zabezpečený prostředek, je klíč načíst ze služby Key Vault a použije k zašifrování assetu. Tento proces se spravuje přes Azure Automation.

## <a name="variable-types"></a>Tyto typy proměnných

Při vytváření proměnné pomocí webu Azure portal, musíte zadat datový typ z rozevíracího seznamu, tak na portálu může zobrazit příslušný ovládací prvek pro zadání hodnoty proměnné. Proměnná není omezen na tento typ dat, ale je nutné nastavit proměnnou pomocí Windows Powershellu, pokud chcete zadat hodnotu jiného typu. Pokud zadáte **není definována**, bude nastavena hodnotu proměnné **$null**, a hodnota se musí nastavit [Set-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable) rutiny nebo **Set-AutomationVariable** aktivity. Nejde vytvořit nebo změnit hodnotu pro proměnnou komplexní typ na portálu, ale můžete zadat hodnotu libovolného typu pomocí Windows Powershellu. Komplexní typy jsou vrácena jako [PSCustomObject](/dotnet/api/system.management.automation.pscustomobject).

Vytvořením pole nebo zatřiďovací tabulky a uložení do proměnné můžete ukládat víc hodnot do jedné proměnné.

Následuje seznam dostupných ve službě Automation proměnné typů:

* Řetězec
* Integer
* DateTime
* Logická hodnota
* Null

## <a name="azurerm-powershell-cmdlets"></a>Rutiny AzureRM Powershellu
Pro AzureRM rutiny v následující tabulce se používají k vytváření a správě prostředků přihlašovacích údajů automation pomocí prostředí Windows PowerShell. Se dodávají jako součást [modulu Azure RM.Automation](/powershell/azure/overview) která je k dispozici pro použití v runbooků služeb automatizace a konfigurace DSC.

| Rutiny | Popis |
|:---|:---|
|[Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable)|Načte hodnotu existující proměnné.|
|[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable)|Vytvoří novou proměnnou a nastaví její hodnotu.|
|[Remove-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Remove-AzureRmAutomationVariable)|Odebere existující proměnnou.|
|[Set-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Set-AzureRmAutomationVariable)|Nastaví hodnotu existující proměnné.|

## <a name="activities"></a>Aktivity
Aktivity v následující tabulce se používají pro přístup k přihlašovacím údajům v runbooku a konfiguracích DSC.

| Aktivity | Popis |
|:---|:---|
|Get-AutomationVariable|Načte hodnotu existující proměnné.|
|Set-AutomationVariable|Nastaví hodnotu existující proměnné.|

> [!NOTE] 
> Měli byste se vyhnout používání proměnných v parametru – Name **Get-AutomationVariable** v runbooku nebo konfigurace DSC, protože to může zkomplikovat zjišťování závislostí mezi runbooky nebo konfigurace DSC a automatizace proměnné v době návrhu.

Funkce v následující tabulce se používají pro přístup a získání proměnné v sadě runbook Python2. 

|Funkce Python2|Popis|
|:---|:---|
|automationassets.get_automation_variable|Načte hodnotu existující proměnné. |
|automationassets.set_automation_variable|Nastaví hodnotu existující proměnné. |

> [!NOTE] 
> Je nutné naimportovat modul "automationassets" v horní části stránky sady Python runbook pro přístup k funkce asset.

## <a name="creating-a-new-automation-variable"></a>Vytváří se nová proměnná služby Automation

### <a name="to-create-a-new-variable-with-the-azure-portal"></a>Chcete-li vytvořit nové proměnné pomocí webu Azure portal

1. Ve svém účtu Automation, klikněte na tlačítko **prostředky** dlaždici a potom na **prostředky** okně vyberte **proměnné**.
2. Na **proměnné** dlaždice, vyberte **přidat proměnnou**.
3. Dokončete výběr možností na **novou proměnnou** okna a kliknutím na **vytvořit** uložení nové proměnné.

### <a name="to-create-a-new-variable-with-windows-powershell"></a>Vytvoření nové proměnné pomocí prostředí Windows PowerShell

[New-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) rutina vytvoří novou proměnnou a nastavuje její počáteční hodnotu. Můžete načíst hodnotu pomocí [Get-AzureRmAutomationVariable](/powershell/module/AzureRM.Automation/Get-AzureRmAutomationVariable). Pokud je hodnota jednoduchého typu, je vrácena stejného typu. Pokud se jedná o komplexní typ, o **PSCustomObject** je vrácena.

Následující vzorové příkazy ukazují, jak vytvořit proměnnou řetězcového typu a vrátíte se jeho hodnota.

    New-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" 
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable' `
    –Encrypted $false –Value 'My String'
    $string = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name 'MyStringVariable').Value

Následující vzorové příkazy ukazují, jak vytvořit proměnnou s komplexní typ a vrátíte se jeho vlastnosti. V takovém případě objektu virtuálního počítače z **Get-AzureRmVm** se používá.

    $vm = Get-AzureRmVm -ResourceGroupName "ResourceGroup01" –Name "VM01"
    New-AzureRmAutomationVariable –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable" –Encrypted $false –Value $vm
    
    $vmValue = (Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" –Name "MyComplexVariable").Value
    $vmName = $vmValue.Name
    $vmIpAddress = $vmValue.IpAddress



## <a name="using-a-variable-in-a-runbook-or-dsc-configuration"></a>Použití proměnné v runbooku nebo konfigurace DSC

Použití **Set-AutomationVariable** aktivitu pro nastavení hodnoty proměnné Automation v Powershellovém runbooku nebo konfigurace DSC a **Get-AutomationVariable** ji moct načíst. Neměli byste používat **Set-AzureRMAutomationVariable** nebo **Get-AzureRMAutomationVariable** rutiny v runbooku nebo konfigurace DSC, protože jsou míň efektivní než aktivity pracovního postupu. Také nelze načíst hodnotu zabezpečené proměnné s **Get-AzureRMAutomationVariable**. Jediný způsob, jak vytvořit novou proměnnou z v rámci runbooku nebo konfigurace DSC se má používat [New-AzureRMAutomationVariable](/powershell/module/AzureRM.Automation/New-AzureRmAutomationVariable) rutiny.


### <a name="textual-runbook-samples"></a>Ukázky textových runbooků

#### <a name="setting-and-retrieving-a-simple-value-from-a-variable"></a>Nastavení nebo načtení jednoduchých hodnot z proměnné

Následující vzorové příkazy ukazují, jak nastavit a načíst proměnnou v textové sady runbook. V této ukázce se předpokládá, že proměnné celočíselného typu s názvem *NumberOfIterations* a *NumberOfRunnings* a proměnná řetězcového typu nazvaná *SampleMessage* mít již byly vytvořeny.

    $NumberOfIterations = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfIterations'
    $NumberOfRunnings = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" -Name 'NumberOfRunnings'
    $SampleMessage = Get-AutomationVariable -Name 'SampleMessage'
    
    Write-Output "Runbook has been run $NumberOfRunnings times."
    
    for ($i = 1; $i -le $NumberOfIterations; $i++) {
       Write-Output "$i`: $SampleMessage"
    }
    Set-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" –AutomationAccountName "MyAutomationAccount" –Name NumberOfRunnings –Value ($NumberOfRunnings += 1)

#### <a name="setting-and-retrieving-a-complex-object-in-a-variable"></a>Nastavení nebo načtení komplexní objekt v proměnné

Následující ukázkový kód ukazuje, jak aktualizovat proměnnou s komplexní hodnoty v textové sady runbook. V této ukázce se virtuální počítač Azure získáte pomocí **Get-AzureVM** a uloží do existující proměnná služby Automation.  Jak je vysvětleno v [tyto typy proměnných](#variable-types), to se ukládá jako objekt PSCustomObject.

    $vm = Get-AzureVM -ServiceName "MyVM" -Name "MyVM"
    Set-AutomationVariable -Name "MyComplexVariable" -Value $vm

V následujícím kódu je hodnota načíst z proměnné a používá ke spuštění virtuálního počítače.

    $vmObject = Get-AutomationVariable -Name "MyComplexVariable"
    if ($vmObject.PowerState -eq 'Stopped') {
       Start-AzureVM -ServiceName $vmObject.ServiceName -Name $vmObject.Name
    }


#### <a name="setting-and-retrieving-a-collection-in-a-variable"></a>Nastavení nebo načtení kolekce do proměnné

Následující ukázkový kód ukazuje, jak použít proměnné s kolekcí komplexních hodnot v textové sady runbook. V této ukázce se načítají několika virtuálních počítačů Azure s **Get-AzureVM** a uloží do existující proměnná služby Automation. Jak je vysvětleno v [tyto typy proměnných](#variable-types), to se ukládá jako kolekce PSCustomObjects.

    $vms = Get-AzureVM | Where -FilterScript {$_.Name -match "my"}     
    Set-AutomationVariable -Name 'MyComplexVariable' -Value $vms

V následujícím kódu je kolekce načíst z proměnné a používá ke spuštění každého virtuálního počítače.

    $vmValues = Get-AutomationVariable -Name "MyComplexVariable"
    ForEach ($vmValue in $vmValues)
    {
       if ($vmValue.PowerState -eq 'Stopped') {
          Start-AzureVM -ServiceName $vmValue.ServiceName -Name $vmValue.Name
       }
    }
    
#### <a name="setting-and-retrieving-a-variable-in-python2"></a>Nastavení nebo načtení proměnnou v Python2
Následující ukázkový kód ukazuje, jak použít proměnné, nastavit proměnnou a zpracování výjimky pro neexistující proměnnou v sadě runbook Python2.

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


### <a name="graphical-runbook-samples"></a>Ukázky grafického runbooku

V grafický runbook, přidáte **Get-AutomationVariable** nebo **Set-AutomationVariable** kliknutím pravým tlačítkem na proměnnou v podokně knihovna grafickém editoru a výběr aktivity, které Chcete.

![Přidání proměnné na plátno](media/automation-variables/runbook-variable-add-canvas.png)

#### <a name="setting-values-in-a-variable"></a>Nastavení hodnoty v proměnné
Následující obrázek ukazuje ukázkové aktivity se aktualizovat proměnnou s jednoduchou hodnotou v grafický runbook. V této ukázce je načíst jeden virtuální počítač Azure s **Get-AzureRmVM** a název počítače je uložen do existující automatizace proměnné typu řetězec. Nezáleží, jestli [odkaz je kanál nebo pořadí](automation-graphical-authoring-intro.md#links-and-workflow) od očekáváte pouze jeden objekt ve výstupu.

![Jednoduchá proměnná set](media/automation-variables/runbook-set-simple-variable.png)

## <a name="next-steps"></a>Další kroky

* Další informace o propojení aktivit ve vytváření grafického obsahu najdete v tématu [odkazy v vytváření grafického obsahu](automation-graphical-authoring-intro.md#links-and-workflow)
* První kroky s grafickými runbooky najdete v článku [Můj první grafický runbook](automation-first-runbook-graphical.md). 
