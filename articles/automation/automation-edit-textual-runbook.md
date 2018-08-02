---
title: Úpravy textových runbooků ve službě Azure Automation
description: Tento článek obsahuje jiné postupy pro práci se sadami runbook Powershellu a pracovní postup prostředí PowerShell ve službě Azure Automation pomocí textového editoru.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b17fc82d6e9cbda6ffa94ac2ee5c97835b089a7e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399715"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Úpravy textových runbooků ve službě Azure Automation

Textový editor ve službě Azure Automation je možné upravit [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks) a [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks). Tato akce nemá typické funkce ostatní editory kódu jako je například technologie intellisense a barevného kódování s další speciální funkce usnadňující přístup k prostředkům, které jsou společné pro sady runbook. Tento článek obsahuje podrobné pokyny pro provádění různých funkcí v tomto editoru.

Textový editor obsahuje funkci pro vložení kódu pro rutiny, prostředky a podřízené runbooky do runbooku. Nemusíte psát kód sami, můžete vybrat ze seznamu dostupných prostředků a mít odpovídající kód vloží do runbooku.

Každá sada runbook ve službě Azure Automation má dvě verze: koncept a publikovaný. Můžete upravovat verzi konceptu sady runbook a pak ho publikujete, aby se Dal spustit. Publikovaná verze nelze upravit. Zobrazit [publikování runbooku](automation-creating-importing-runbook.md#publishing-a-runbook) Další informace.

Pro práci s [grafické Runbooky](automation-runbook-types.md#graphical-runbooks), naleznete v tématu [vytváření grafického obsahu ve službě Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Postup úpravy sady runbook pomocí webu Azure portal

Pomocí následujícího postupu otevřete sadu runbook pro úpravy v textový editor.

1. Na webu Azure Portal vyberte svůj účet automation.
2. V části **AUTOMATIZACE PROCESŮ**vyberte **sady Runbook** otevřete seznam runbooků.
3. Vyberte sadu runbook, kterou chcete upravit a potom klikněte na tlačítko **upravit** tlačítko.
4. Udělejte požadované úpravy.
5. Klikněte na tlačítko **Uložit** když se dokončí úpravy.
6. Klikněte na tlačítko **publikovat** potřebujete nejnovější verzi konceptu sady runbook, který má být publikován.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Chcete-li vložit rutiny do sady runbook

1. Na plátně textový editor umístěte kurzor, kam chcete umístit rutinu.
2. Rozbalte **rutiny** uzel v ovládacím prvku knihovna.
3. Rozbalte modulu, který obsahuje rutinu, kterou chcete použít.
4. Klikněte pravým tlačítkem na rutiny pro vložení a vyberte **přidat na plátno**. Pokud rutina má více než jeden parametr nastavena, bude výchozí sadu přidán. Můžete také rozšířit rutinu k výběru sada různých parametrů.
5. Kód pro tuto rutinu je vložen s jeho celý seznam parametrů.
6. Zadejte odpovídající hodnotu namísto datového typu v závorkách <> pro požadované parametry. Odeberte všechny parametry, které nepotřebujete.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Chcete-li vložit kód pro podřízený runbook do runbooku

1. Na plátně textový editor, umístěte kurzor myši kam chcete umístit kód [podřízeného runbooku](automation-child-runbooks.md).
2. Rozbalte **sady Runbook** uzel v ovládacím prvku knihovna.
3. Klikněte pravým tlačítkem na sadu runbook pro vložení a vyberte **přidat na plátno**.
4. Kód pro podřízený runbook je vložen s zástupné symboly pro všechny parametry sady runbook.
5. Nahraďte zástupné symboly příslušnými hodnotami pro jednotlivé parametry.

### <a name="to-insert-an-asset-into-a-runbook"></a>Chcete-li vložit prostředek do sady runbook

1. Na plátně textový editor umístěte kurzor, kam chcete umístit kód podřízeného runbooku.
2. Rozbalte **prostředky** uzel v ovládacím prvku knihovna.
3. Rozbalte uzel pro typ prostředku, který chcete.
4. Klikněte pravým tlačítkem myši klikněte na prostředek pro vložení a vyberte **přidat na plátno**. Pro [proměnných assetů](automation-variables.md), vyberte buď **"Získat proměnnou" na plátno přidejte** nebo **"Nastavit proměnnou" na plátno přidejte** v závislosti na tom, jestli chcete získat nebo nastavit proměnnou.
5. Kód pro prostředek je vložen do sady runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Úprava runbooku Azure Automation pomocí prostředí Windows PowerShell

Postup úpravy sady runbook pomocí prostředí Windows PowerShell, použijte editor podle vaší volby a uložte ho do souboru s příponou .ps1. Můžete použít [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) rutina pro načtení obsahu runbooku a potom [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) rutiny nahradit existující koncept runbooku se změnit jednu.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>K načtení obsahu runbooku pomocí prostředí Windows PowerShell

Následující vzorové příkazy znázorňují postup načtení skriptu pro sadu runbook a uložte ho do souboru skriptu. V tomto příkladu se načte koncept. Je také možné načíst publikovanou verzi runbooku, i když tato verze nedá změnit.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Chcete-li změnit obsah sady Runbook pomocí prostředí Windows PowerShell

Následující vzorové příkazy ukazují, jak nahradit existující obsah runbooku obsahem souboru skriptu. Všimněte si, že se stejným způsobem jako v ukázkové [import runbooku ze souboru skriptu prostředí Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Související články

* [Vytvoření nebo import runbooku ve službě Azure Automation](automation-creating-importing-runbook.md)
* [O pracovním postupu Powershellu](automation-powershell-workflow.md)
* [Grafické vytváření obsahu v Azure Automation.](automation-graphical-authoring-intro.md)
* [Certifikáty](automation-certificates.md)
* [Připojení](automation-connections.md)
* [Přihlašovací údaje](automation-credentials.md)
* [Plány](automation-schedules.md)
* [Proměnné](automation-variables.md)
