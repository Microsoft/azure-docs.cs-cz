---
title: Správa runbooků v Azure Automation
description: Tento článek popisuje, jak spravovat Runbooky v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 42fbb224981123a7cb0f39f07c144b5d2ffbc587
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75417885"
---
# <a name="manage-runbooks-in-azure-automation"></a>Správa runbooků v Azure Automation

Runbook můžete přidat do Azure Automation tím, že [vytvoříte nový](#create-a-runbook) nebo naimportujete existující Runbook ze souboru nebo [Galerie runbooků](automation-runbook-gallery.md). Tento článek poskytuje informace o vytváření a importování runbooků ze souboru.  Všechny podrobnosti o přístupu k sadám Runbook a k modulům v galerii runbooků [a modulům](automation-runbook-gallery.md)můžete získat Azure Automation.

## <a name="create-a-runbook"></a>Vytvoření runbooku

Novou sadu Runbook můžete vytvořit v Azure Automation pomocí některého z portálů Azure nebo Windows PowerShellu. Až se Runbook vytvoří, můžete ho upravit pomocí informací v části [výuka pracovního postupu prostředí PowerShell](automation-powershell-workflow.md) a [vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Vytvoření Runbooku v Azure Portal

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra vyberte **Runbooky** a otevřete seznam runbooků.
3. Klikněte na tlačítko **Přidat Runbook** a pak **vytvořte novou sadu Runbook**.
4. Zadejte **název** Runbooku a vyberte jeho [typ](automation-runbook-types.md). Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
5. Kliknutím na **vytvořit** vytvořte Runbook a otevřete Editor.

### <a name="create-a-runbook-with-powershell"></a>Vytvoření Runbooku pomocí PowerShellu

Pomocí rutiny [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) můžete vytvořit prázdnou [sadu Runbook pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks). Pomocí parametru **typu** zadejte jeden ze čtyř typů sad Runbook.

Následující vzorové příkazy ukazují, jak vytvořit novou prázdnou sadu Runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Import Runbooku

Novou sadu Runbook můžete vytvořit v Azure Automation importem skriptu PowerShellu nebo pracovního postupu PowerShellu (přípona. ps1), exportovaného grafického Runbooku (. graphrunbook) nebo skriptu Pythonu 2 (. py Extension).  Je nutné zadat [typ Runbooku](automation-runbook-types.md) , který se vytvoří během importu, přičemž vezme v úvahu následující skutečnosti.

* `.graphrunbook` soubor lze importovat pouze do nového [grafického Runbooku](automation-runbook-types.md#graphical-runbooks)a grafické Runbooky lze vytvořit pouze ze souboru `.graphrunbook`.
* `.ps1` soubor obsahující pracovní postup PowerShellu se dá importovat jedině do [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks). Pokud soubor obsahuje několik pracovních postupů PowerShellu, import se nezdaří. Každý pracovní postup musíte uložit do vlastního souboru a naimportovat každý samostatně.
* `.ps1` soubor obsahující pracovní postup PowerShellu by se neměl importovat do [Runbooku PowerShellu](automation-runbook-types.md#powershell-runbooks), protože ho skriptovací stroj PowerShellu nedokáže rozpoznat.
* `.ps1` soubor, který neobsahuje pracovní postup, se dá importovat do Runbooku [PowerShellu](automation-runbook-types.md#powershell-runbooks) nebo [Runbooku pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks).  Pokud je importována do Runbooku pracovního postupu PowerShellu, je převedena na pracovní postup a komentáře jsou součástí sady Runbook, kde jsou zadány změny, které byly provedeny.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Import Runbooku ze souboru s Azure Portal

Pomocí následujícího postupu můžete do Azure Automation importovat soubor skriptu.

> [!NOTE]
> Všimněte si, že můžete importovat soubor. ps1 do Runbooku pracovního postupu PowerShellu pomocí portálu.

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra vyberte **Runbooky** a otevřete seznam runbooků.
3. Klikněte na tlačítko **Přidat Runbook** a potom na **importovat**.
4. Kliknutím na **soubor sady Runbook** vyberte soubor, který chcete importovat.
5. Pokud je pole **název** povolené, máte možnost ho změnit.  Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
6. [Typ Runbooku](automation-runbook-types.md) se vybere automaticky, ale po přijetí platných omezení do účtu můžete typ změnit.
7. Nová sada Runbook se zobrazí v seznamu sad Runbook pro účet Automation.
8. Runbook je nutné [publikovat](#publish-a-runbook) před tím, než je možné jej spustit.

> [!NOTE]
> Po importu grafického Runbooku nebo grafického Runbooku pracovního postupu PowerShellu máte možnost převést na jiný typ, pokud je to žádoucí. Nemůžete převést na textový Runbook.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Import Runbooku ze souboru skriptu v prostředí Windows PowerShell

Pomocí rutiny [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) můžete importovat soubor skriptu jako koncept Runbooku pracovního postupu PowerShellu. Pokud sada Runbook již existuje, import se nezdařil, pokud nepoužijete parametr *-Force* .

Následující vzorové příkazy ukazují, jak naimportovat soubor skriptu do sady Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testování runbooku

Při testování Runbooku se spustí [koncept verze](#publish-a-runbook) a všechny akce, které provádí, se dokončí. Nevytvoří se žádná historie úlohy, ale Stream [výstupů](automation-runbook-output-and-messages.md#output-stream) a [Upozornění a chyb](automation-runbook-output-and-messages.md#message-streams) se zobrazí v podokně výstup testu. Zprávy do [podrobného datového proudu](automation-runbook-output-and-messages.md#message-streams) se zobrazí v podokně výstup pouze v případě, že je [Proměnná $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) nastavena na pokračovat.

I když je verze konceptu spuštěna, sada Runbook se stále provádí normálně a provede všechny akce s prostředky v prostředí. Z tohoto důvodu byste měli testovat pouze Runbooky na neprodukčních prostředcích.

Postup testování každého [typu Runbooku](automation-runbook-types.md) je stejný a při testování mezi textovým editorem a grafickým editorem v Azure Portal nedochází k žádnému rozdílu.

1. Otevřete koncept sady Runbook buď v [textovém editoru](automation-edit-textual-runbook.md) , nebo v [grafickém editoru](automation-graphical-authoring-intro.md).
1. Kliknutím na tlačítko **test** otevřete stránku test.
1. Pokud má Runbook parametry, budou uvedeny v levém podokně, kde můžete zadat hodnoty, které mají být použity pro test.
1. Pokud chcete spustit test na [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), změňte **nastavení spuštění** na **Hybrid Worker** a vyberte název cílové skupiny.  V opačném případě ponechte výchozí **Azure** spustit test v cloudu.
1. Kliknutím na tlačítko **Spustit** spusťte test.
1. Pokud je sada Runbook [pracovní postup PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafická](automation-runbook-types.md#graphical-runbooks), můžete ji zastavit nebo pozastavit, když je testována pomocí tlačítek pod podoknem výstup. Když runbook pozastavíte, dokončí aktuální aktivitu teprve pak se pozastaví. Po pozastavení sady runbook můžete ji zastavit nebo restartovat.
1. Zkontrolujte výstup Runbooku v podokně výstup.

## <a name="publish-a-runbook"></a>Publikování runbooku

Když vytváříte nebo importujete novou sadu Runbook, je nutné ji publikovat, abyste ji mohli spustit.  Každá sada Runbook ve službě Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze není ovlivněn změny konceptu. Pokud má být k dispozici koncept verze, pak ho publikujete, čímž dojde k přepsání publikované verze verzí konceptu.

### <a name="azure-portal"></a>Portál Azure

1. Otevřete Runbook v Azure Portal.
2. Klikněte na tlačítko **Edit** (Upravit).
3. Klikněte na tlačítko **publikovat** a pak na ověřovací zprávu **Ano** .

### <a name="powershell"></a>PowerShell

K publikování Runbooku pomocí Windows PowerShellu můžete použít rutinu [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) . Následující vzorové příkazy ukazují, jak publikovat ukázkovou sadu Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak můžete využít galerii modulů Runbook a PowerShellu, najdete v tématu [Galerie runbooků a modulů pro Azure Automation](automation-runbook-gallery.md)
* Další informace o úpravách runbooků PowerShellu a PowerShellového pracovního postupu pomocí textového editoru najdete [v tématu Úprava textových runbooků v Azure Automation](automation-edit-textual-runbook.md)
* Další informace o vytváření grafického Runbooku najdete v tématu věnovaném [vytváření grafických postupů v Azure Automation](automation-graphical-authoring-intro.md) .
