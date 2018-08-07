---
title: Vytvoření nebo import runbooku ve službě Azure Automation
description: Tento článek popisuje, jak vytvořit nový runbook ve službě Azure Automation nebo importovat ze souboru.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 2ccf9036d3701c710c6d3258f81390ed355c246c
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578027"
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Vytvoření nebo import runbooku ve službě Azure Automation

Sady runbook můžete přidat do Azure Automation. buď [vytvořením nového](#creating-a-new-runbook) nebo importováním existujícího runbooku ze souboru nebo z [Galerie Runbooků](automation-runbook-gallery.md). Tento článek obsahuje informace o vytváření a import runbooků ze souboru.  Můžete získat všechny podrobnosti o přístupu k komunity runbooky a moduly v [Galerie modulů a Runbooků Azure Automation](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Vytváří se nový runbook

Vytvořit nový runbook ve službě Azure Automation pomocí některé z Azure Portal nebo prostředí Windows PowerShell. Po vytvoření runbooku ho můžete upravit pomocí informací v [pracovním postupu Powershellu](automation-powershell-workflow.md) a [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Chcete-li vytvořit novou sadu runbook Azure Automation pomocí webu Azure portal

1. Na webu Azure Portal otevřete účet Automation.
1. Z centra, vyberte **sady Runbook** otevřete seznam runbooků.
1. Klikněte na **přidat runbook** tlačítko a pak **vytvořit nový runbook**.
1. Zadejte **název** pro sadu runbook a vyberte jeho [typ](automation-runbook-types.md). Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
1. Klikněte na tlačítko **vytvořit** k vytvoření sady runbook a otevřete editor.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Chcete-li vytvořit novou sadu runbook Azure Automation pomocí prostředí Windows PowerShell
Můžete použít [New-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationrunbook) rutina pro vytvoření prázdné [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks). **Typ** parametr by měl být rovněž k určení jedním ze čtyř typů sad runbook.

Následující vzorové příkazy znázorňují postup vytvoření nové prázdné sady runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Import runbooku ze souboru do Azure Automation

Importováním skript prostředí PowerShell nebo pracovního postupu Powershellu (s příponou .ps1), exportované grafický runbook (.graphrunbook) nebo skript Python 2 (s příponou .py) můžete vytvořit novou sadu runbook ve službě Azure Automation.  Je nutné zadat [typu runbook](automation-runbook-types.md) , který se vytvoří během importu, přičemž v úvahu následující aspekty.

* Soubor .graphrunbook může importovat jenom do nového [grafický runbook](automation-runbook-types.md#graphical-runbooks), a grafické runbooky, které mohou být vytvořeny pouze ze souboru .graphrunbook.
* Souboru s příponou .ps1 obsahující pracovní postup prostředí PowerShell lze importovat pouze do [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks).  Pokud soubor obsahuje více pracovních postupů Powershellu, import se nezdaří. Musíte uložit každý pracovní postup do vlastního souboru a importovat každý zvlášť.
* Soubor .ps1, která neobsahuje pracovní postup můžete importovat do buď [Powershellového runbooku](automation-runbook-types.md#powershell-runbooks) nebo [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks).  Pokud se importují do pracovních postupů Powershellu, pak je převeden do pracovního postupu a komentáře jsou zahrnuty v sadě runbook určující, které byly provedeny změny.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Import runbooku ze souboru pomocí webu Azure portal

Následující postup slouží k importu souboru skriptu do služby Azure Automation.  

> [!NOTE]
> Všimněte si, že můžete importovat pouze souboru s příponou .ps1 do pracovního postupu Powershellu runbooku pomocí portálu.

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra, vyberte **sady Runbook** otevřete seznam runbooků.
3. Klikněte na **přidat runbook** tlačítko a pak **Import**.
4. Klikněte na tlačítko **soubor sady Runbook** a vyberte soubor k importu
5. Pokud **název** pole je povolená, pak máte možnost ho změnit.  Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
6. [Typ runbooku](automation-runbook-types.md) se vybere automaticky, ale můžete změnit typ po provedení příslušných omezení v úvahu. 
7. Nová sada runbook se zobrazí v seznamu sad runbook pro účet Automation.
8. Je nutné [Publikovat sadu runbook](#publishing-a-runbook) než budete moct spustit.

> [!NOTE]
> Jakmile dokončíte import grafický runbook nebo grafický runbook pracovního postupu Powershellu, máte možnost převést na jiný typ, pokud je potřeba. Nelze převést na textový sady runbook.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Import runbooku ze souboru skriptu pomocí Windows Powershellu
Můžete použít [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) rutinu, která importuje soubor skriptu jako koncept runbooku pracovního postupu Powershellu. Pokud sada runbook již existuje, nebude import úspěšný, pokud nechcete použít *– platnost* parametru. 

Následující vzorové příkazy ukazují, jak importovat soubor skriptu do sady runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="publishing-a-runbook"></a>Publikování runbooku

Při vytváření nebo importovat nové sady runbook, musíte ji publikovat než budete moct spustit.  Každá sada runbook ve službě Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze není ovlivněn změny konceptu. Když koncept by měla být k dispozici, pak ji publikujete, která publikovaná verze přepíše verzi konceptu.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Publikování runbooku pomocí webu Azure portal

1. Otevřete sadu runbook na portálu Azure portal.
1. Klikněte na tlačítko **upravit** tlačítko.
1. Klikněte na tlačítko **publikovat** tlačítko a pak **Ano** zprávy pro ověření.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Publikování runbooku pomocí prostředí Windows PowerShell
Můžete použít [Publish-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/publish-azurermautomationrunbook) rutiny publikování sady runbook pomocí prostředí Windows PowerShell. Následující vzorové příkazy znázorňují postup při publikování ukázkové sady runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak můžete využívat výhod sady Runbook a galerii modulů Powershellu najdete v tématu [Galerie modulů a Runbooků Azure Automation](automation-runbook-gallery.md)
* Další informace o editaci v sadě textový editor sady runbook Powershellu a pracovních postupů Powershellu najdete v tématu [úpravy textových runbooků ve službě Azure Automation](automation-edit-textual-runbook.md)
* Chcete-li další informace o vytváření grafického runbooku, přečtěte si téma [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md)