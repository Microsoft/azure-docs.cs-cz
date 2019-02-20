---
title: Správa runbooků ve službě Azure Automation
description: Tento článek popisuje, jak spravovat sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: da3b09998d163ffcc16bfcbbf9f516467dd3311d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418541"
---
# <a name="manage-runbooks-in-azure-automation"></a>Správa runbooků ve službě Azure Automation

Sady runbook můžete přidat do Azure Automation. buď [vytvořením nového](#creating-a-new-runbook) nebo importováním existujícího runbooku ze souboru nebo [Galerie Runbooků](automation-runbook-gallery.md). Tento článek obsahuje informace o vytváření a import runbooků ze souboru.  Můžete získat všechny podrobnosti o přístupu k komunity runbooky a moduly v [Galerie modulů a Runbooků Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Vytvoření runbooku

Vytvořit nový runbook ve službě Azure Automation pomocí některé z Azure Portal nebo prostředí Windows PowerShell. Po vytvoření runbooku ho můžete upravit pomocí informací v [pracovním postupu Powershellu](automation-powershell-workflow.md) a [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Vytvoření sady runbook na portálu Azure portal

1. Na webu Azure Portal otevřete účet Automation.
2. Z centra, vyberte **sady Runbook** otevřete seznam runbooků.
3. Klikněte na **přidat runbook** tlačítko a pak **vytvořit nový runbook**.
4. Zadejte **název** pro sadu runbook a vyberte jeho [typ](automation-runbook-types.md). Název runbooku musí začínat písmenem a může obsahovat písmena, číslice, podtržítka a pomlčky.
5. Klikněte na tlačítko **vytvořit** k vytvoření sady runbook a otevřete editor.

### <a name="create-a-runbook-with-powershell"></a>Vytvoření sady runbook pomocí prostředí PowerShell

Můžete použít [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) rutina pro vytvoření prázdné [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Použití **typ** parametr určit jeden ze čtyř typů sad runbook.

Následující vzorové příkazy znázorňují postup vytvoření nové prázdné sady runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Import sady runbook

Importováním skript prostředí PowerShell nebo pracovního postupu Powershellu (s příponou .ps1), exportované grafický runbook (.graphrunbook) nebo skript Python 2 (s příponou .py) můžete vytvořit novou sadu runbook ve službě Azure Automation.  Je nutné zadat [typu runbook](automation-runbook-types.md) , který se vytvoří během importu, přičemž v úvahu následující aspekty.

* A `.graphrunbook` souboru může importovat jenom do nového [grafický runbook](automation-runbook-types.md#graphical-runbooks), a grafické runbooky můžou vytvářet jenom z `.graphrunbook` souboru.
* A `.ps1` soubor, který obsahuje pracovní postup prostředí PowerShell lze importovat pouze do [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks).  Pokud soubor obsahuje více pracovních postupů Powershellu, import se nezdaří. Musíte uložit každý pracovní postup do vlastního souboru a importovat každý zvlášť.
* A `.ps1` soubor, který neobsahuje pracovního postupu můžete importovat do buď [Powershellového runbooku](automation-runbook-types.md#powershell-runbooks) nebo [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks).  Pokud se importují do pracovních postupů Powershellu, pak je převeden do pracovního postupu a komentáře jsou zahrnuty v sadě runbook určující, které byly provedeny změny.

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

## <a name="test-a-runbook"></a>Testování runbooku

Při testování sady runbook [koncept](#publishing-a-runbook) spuštění a jsou dokončeny všechny akce, které aplikace provádí. Nevytvoří se žádná historie úlohy, ale [výstup](automation-runbook-output-and-messages.md#output-stream) a [upozornění a chyby](automation-runbook-output-and-messages.md#message-streams) datové proudy jsou zobrazeny v testovací podokno výstup. Zprávy [podrobné Stream](automation-runbook-output-and-messages.md#message-streams) se zobrazují v podokně výstupu pouze tehdy, pokud [proměnnou $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) je nastavena na pokračovat.

I když je právě spuštěna verze konceptu, sada runbook stále normálně spouští a dělá všechny akce s prostředky v prostředí. Z tohoto důvodu jste měli runbooky testovat jenom o neprodukční prostředky.

Postup pro každý test [typu runbook](automation-runbook-types.md) je stejný a neexistuje žádný rozdíl v testování mezi textový editor a grafickém editoru na webu Azure Portal.  

1. Otevřete jeho koncept runbooku buď [textový editor](automation-edit-textual-runbook.md) nebo [grafický editor](automation-graphical-authoring-intro.md).
1. Klikněte na tlačítko **Test** tlačítko otevřete stránku testu.
1. Pokud má runbook parametry, jsou vypsáni v levém podokně, ve kterém můžete zadat hodnoty, které se použijí pro test.
1. Pokud chcete spustit test [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), změňte **parametrů běhu** k **Hybrid Worker** a vyberte název cílové skupiny.  V opačném případě ponechte výchozí **Azure** pro spuštění testu v cloudu.
1. Klikněte na tlačítko **Start** tlačítko pro spuštění testu.
1. Pokud je sada runbook [pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) nebo [grafický](automation-runbook-types.md#graphical-runbooks), potom můžete ukončit nebo jej pozastavit, pokud je testován s tlačítky pod podoknem výstupu. Když runbook pozastavíte, dokončí aktuální aktivitu teprve pak se pozastaví. Po pozastavení sady runbook můžete ji zastavit nebo restartovat.
1. Zkontrolujte výstup z runbooku v podokně výstup.

## <a name="publish-a-runbook"></a>Publikování runbooku

Při vytváření nebo importovat nové sady runbook, musíte ji publikovat než budete moct spustit.  Každá sada runbook ve službě Automation má koncept a publikovanou verzi. Je možné spustit jenom publikovaná verze a lze upravovat pouze verzi konceptu. Publikovaná verze není ovlivněn změny konceptu. Když koncept by měla být k dispozici, pak ji publikujete, která publikovaná verze přepíše verzi konceptu.

### <a name="azure-portal"></a>portál Azure

1. Otevřete sadu runbook na portálu Azure portal.
2. Klikněte na tlačítko **upravit** tlačítko.
3. Klikněte na tlačítko **publikovat** tlačítko a pak **Ano** zprávy pro ověření.

### <a name="powershell"></a>PowerShell

Můžete použít [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) rutiny publikování sady runbook pomocí prostředí Windows PowerShell. Následující vzorové příkazy znázorňují postup při publikování ukázkové sady runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak můžete využívat výhod sady Runbook a galerii modulů Powershellu najdete v tématu [Galerie modulů a Runbooků Azure Automation](automation-runbook-gallery.md)
* Další informace o editaci v sadě textový editor sady runbook Powershellu a pracovních postupů Powershellu najdete v tématu [úpravy textových runbooků ve službě Azure Automation](automation-edit-textual-runbook.md)
* Chcete-li další informace o vytváření grafického runbooku, přečtěte si téma [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md)
