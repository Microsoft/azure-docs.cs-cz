---
title: Úprava textové sady runbook ve službě Azure Automation
description: Tento článek obsahuje různé postupy pro práci se sadami runbook Powershellu a pracovní postup prostředí PowerShell ve službě Azure Automation pomocí textový editor.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 055bd2a7607b8cab9c7ca417c7c3f57c2e569f77
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Úprava textové sady runbook ve službě Azure Automation

Textový editor ve službě Azure Automation lze upravit [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks) a [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks). Tato akce nemá typické funkce editory další kód například intellisense a barevné kódování s další speciální funkce pomoc při přístupu k prostředkům, které jsou společné pro sady runbook. Tento článek poskytuje podrobné pokyny pro provádění různé funkce pomocí tohoto editoru.

Textový editor obsahuje funkci pro vložení kódu pro rutiny, prostředky a podřízené runbooky do runbooku. Nemusíte psát kód sami, můžete vybrat ze seznamu dostupných zdrojů a odpovídající kódu vloží do runbooku.

Každá sada runbook ve službě Azure Automation má dvě verze: koncept a publikovaný. Můžete upravovat verzi konceptu sady runbook a pak ho publikujete, aby se Dal spustit. Publikovaná verze nelze upravit. V tématu [publikování runbooku](automation-creating-importing-runbook.md#publishing-a-runbook) Další informace.

Pro práci s [grafické Runbooky](automation-runbook-types.md#graphical-runbooks), najdete v části [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Chcete-li upravit sadu runbook pomocí portálu Azure

Otevřete sadu runbook pro úpravy v textový editor, použijte následující postup.

1. Na portálu Azure vyberte svůj účet automation.
2. V části **AUTOMATIZACE procesu**, vyberte **Runbooky** otevřete seznam runbooků.
3. Vyberte sadu runbook, kterou chcete upravit a pak klikněte na **upravit** tlačítko.
4. Udělejte požadované úpravy.
5. Klikněte na tlačítko **Uložit** při dokončení provedené úpravy se.
6. Klikněte na tlačítko **publikovat** Pokud chcete, aby nejnovější verzi konceptu sady runbook publikovat.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Chcete-li vložit rutiny do runbooku

1. Na plátně textový editor umístěte kurzor, kam chcete umístit rutinu.
2. Rozbalte **rutiny** uzlu v ovládacím prvku knihovna.
3. Rozbalte modul, který obsahuje rutinu, kterou chcete použít.
4. Klikněte pravým tlačítkem na rutinu k vložení a vyberte **přidat na plátno**. Pokud rutina má více než jeden parametr nastavit, se přidá výchozí sadu. Můžete také rozšířit rutinu pro výběr sady jiným parametrem.
5. Kód pro rutinu vložena s jeho celý seznam parametrů.
6. Zadejte odpovídající hodnotu namísto datového typu v závorkách <> pro požadované parametry. Odeberte všechny parametry, které nepotřebujete.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Vložení kódu pro podřízený runbook do runbooku

1. Na plátně textový editor, umístěte kurzor kam chcete umístit kód [podřízeného runbooku](automation-child-runbooks.md).
2. Rozbalte **Runbooky** uzlu v ovládacím prvku knihovna.
3. Klikněte pravým tlačítkem na runbook pro vložení a vyberte **přidat na plátno**.
4. Kód pro podřízený runbook vložena s zástupné symboly pro všechny parametry runbooku.
5. Zástupné názvy nahraďte příslušnými hodnotami pro jednotlivé parametry.

### <a name="to-insert-an-asset-into-a-runbook"></a>Chcete-li vložit prostředek do runbooku

1. Na plátně textový editor umístěte kurzor, kam chcete umístit kód pro podřízený runbook.
2. Rozbalte **prostředky** uzlu v ovládacím prvku knihovna.
3. Rozbalte uzel pro typ prostředku, který má být.
4. Klikněte pravým tlačítkem na prostředek, který chcete vložit a vyberte **přidat na plátno**. Pro [proměnných assetů](automation-variables.md), vyberte buď **přidat "Získat proměnnou" na plátno** nebo **přidat "Nastavit proměnnou" na plátno** v závislosti na tom, jestli chcete získat nebo nastavit proměnnou.
5. Kód pro asset budou vložena do sady runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Chcete-li upravit runbook služby Azure Automation pomocí prostředí Windows PowerShell

Postup úpravy sady runbook pomocí prostředí Windows PowerShell, pomocí editoru podle své volby a uložte ho do souboru s příponou .ps1. Můžete použít [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) rutiny můžete načíst obsah runbooku a potom [Set-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) rutiny nahradit existující koncept runbooku upravili.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>K načtení obsahu runbooku pomocí prostředí Windows PowerShell

Následující vzorové příkazy znázorňují postup načtení skriptu pro sadu runbook a uložit ho do souboru skriptu. V tomto příkladu se načte koncept. Je také možné načíst publikovanou verzi runbooku, i když tato verze nedá změnit.

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

$runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
$runbookContent = $runbookDefinition.Content

Out-File -InputObject $runbookContent -FilePath $scriptPath
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Chcete-li změnit obsah sady Runbook pomocí prostředí Windows PowerShell

Následující vzorové příkazy ukazují, jak nahradit existující obsah runbooku obsahem souboru skriptu. Všimněte si, že toto je stejným způsobem jako v ukázkové [import runbooku ze souboru skriptu v prostředí Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName
```

## <a name="related-articles"></a>Související články

* [Vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md)
* [Učení pracovního postupu Powershellu](automation-powershell-workflow.md)
* [Grafické vytváření obsahu v Azure Automation.](automation-graphical-authoring-intro.md)
* [Certifikáty](automation-certificates.md)
* [Připojení](automation-connections.md)
* [Přihlašovací údaje](automation-credentials.md)
* [Plány](automation-schedules.md)
* [Proměnné](automation-variables.md)
