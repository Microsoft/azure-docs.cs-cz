---
title: Vytvoření nebo import runbooku ve službě Azure Automation
description: Tento článek popisuje, jak vytvořit nový runbook ve službě Azure Automation nebo importovat jeden ze souboru.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: c49b2f19da7f59e413dedbd757ca8b6fc6c7e674
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Vytvoření nebo import runbooku ve službě Azure Automation
Přidáním sady runbook do Azure Automation a to buď [vytvořením nového](#creating-a-new-runbook) nebo importováním existujícího runbooku ze souboru nebo z [Galerie Runbooků](automation-runbook-gallery.md). Tento článek obsahuje informace o vytvoření a import sad runbook ze souboru.  Můžete získat všechny podrobnosti o přístupu k komunity runbooky a moduly v [Galerie Runbooků a modulů pro Azure Automation](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Vytvoření nové sady runbook
Můžete vytvořit novou sadu runbook ve službě Azure Automation pomocí jedné z Azure portály nebo prostředí Windows PowerShell. Po vytvoření runbooku ho můžete upravit pomocí informací v [pracovního postupu Powershellu Learning](automation-powershell-workflow.md) a [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Chcete-li vytvořit novou sadu runbook automatizace Azure pomocí portálu Azure
1. Na webu Azure Portal otevřete účet Automation.
2. Z centra, vyberte **Runbooky** otevřete seznam runbooků.
3. Klikněte na **přidat runbook** tlačítko a potom **vytvořit nový runbook**.
4. Zadejte **název** pro sadu runbook a vyberte jeho [typu](automation-runbook-types.md). Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
5. Klikněte na tlačítko **vytvořit** vytvořit sadu runbook a otevřete editor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Chcete-li vytvořit novou sadu runbook automatizace Azure pomocí prostředí Windows PowerShell
Můžete použít [New-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) vytvořte prázdnou [runbook pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks). Můžete zadat **název** parametr vytvořit prázdnou sadu runbook, který můžete později upravit, nebo můžete zadat **cesta** parametr pro import souboru sady runbook. **Typ** parametr by měl být také k určení jedním ze čtyř typů sady runbook.

Následující vzorové příkazy ukazují, jak vytvořit novou prázdnou sadu runbook.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Import runbooku ze souboru do Azure Automation.
Můžete vytvořit novou sadu runbook ve službě Azure Automation importováním skript prostředí PowerShell nebo pracovního postupu Powershellu (soubory s příponou .ps1), exportovaný grafický runbook (.graphrunbook) nebo skript Python 2 (.py rozšíření).  Je nutné zadat [typ runbooku](automation-runbook-types.md) vytvořený během importu, vezme v úvahu následující aspekty.

* Soubor .graphrunbook může importovat jenom do nového [grafický runbook](automation-runbook-types.md#graphical-runbooks), a grafické runbooky, které mohou být vytvořeny pouze ze souboru .graphrunbook.
* Souboru s příponou .ps1 s pracovním postupem prostředí PowerShell lze importovat pouze do [runbook pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks).  Pokud soubor obsahuje více pracovních postupů prostředí PowerShell, se import nezdaří. Musíte uložit každý pracovní postup do vlastního souboru a každý importovat samostatně.
* Souboru s příponou .ps1, který neobsahuje pracovního postupu lze importovat do buď [Powershellový runbook](automation-runbook-types.md#powershell-runbooks) nebo [runbook pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks).  Po importu do sady runbook PowerShell Workflow, pak je převeden do pracovního postupu a komentáře, které jsou zahrnuté v sadě runbook zadání změny, které byly provedeny.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Import runbooku ze souboru pomocí portálu Azure
Následující postup slouží k importu souboru skriptu do Azure Automation.  

> [!NOTE]
> Všimněte si, že můžete importovat pouze souboru s příponou .ps1 do pracovního postupu Powershellu runbooku pomocí portálu.
> 
> 

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra, vyberte **Runbooky** otevřete seznam runbooků.
3. Klikněte na **přidat runbook** tlačítko a potom **Import**.
4. Klikněte na tlačítko **soubor sady Runbook** a vyberte soubor k importu
5. Pokud **název** pole je přístupné, pak máte možnost ji změnit.  Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
6. [Typ runbooku](automation-runbook-types.md) se vybere automaticky, ale můžete změnit typ vezmete platí omezení v úvahu. 
7. Nový runbook se zobrazí v seznamu sad runbook pro účet služby Automation.
8. Je nutné [Publikovat sadu runbook](#publishing-a-runbook) předtím, než můžete ji spustit.

> [!NOTE]
> Po importu grafický runbook nebo grafický runbook pracovního postupu prostředí PowerShell, máte možnost převést na jiný typ, pokud je potřeba. Nelze převést na textový.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Import runbooku ze souboru skriptu v prostředí Windows PowerShell
Můžete použít [Import AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) rutiny importovat soubor skriptu jako koncept runbook pracovního postupu Powershellu. Pokud sada runbook již existuje, nebude import úspěšný, pokud nechcete použít *-Force* parametr. 

Následující vzorové příkazy ukazují, jak importovat soubor skriptu do runbooku.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publikování runbooku
Při vytváření nebo importovat nové sady runbook, musíte publikovat předtím, než můžete ji spustit.  Každá sada runbook ve službě Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze neovlivní změny na verzi konceptu. Pokud má být k dispozici koncept, publikujete ho. publikovaná verze přepíše konceptem.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Publikování sady runbook pomocí portálu Azure
1. Otevřete sadu runbook na portálu Azure.
2. Klikněte **upravit** tlačítko.
3. Klikněte **publikovat** tlačítko a potom **Ano** zobrazení zprávy pro ověření.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Chcete-li publikovat sady runbook pomocí prostředí Windows PowerShell
Můžete použít [publikovat AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) rutiny publikování sady runbook pomocí prostředí Windows PowerShell. Následující vzorové příkazy znázorňují postup publikování ukázkové sady runbook.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Další kroky
* Další informace o tom, jak můžete využívat výhod sadu Runbook a modul Galerie prostředí PowerShell najdete v tématu [Galerie Runbooků a modulů pro Azure Automation.](automation-runbook-gallery.md)
* Další informace o úpravách prostředí PowerShell a pracovní postup prostředí PowerShell sad runbook s textový editor, najdete v části [úpravy textovou sady runbook ve službě Azure Automation](automation-edit-textual-runbook.md)
* Další informace o vytváření grafický runbook najdete v tématu [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md)

