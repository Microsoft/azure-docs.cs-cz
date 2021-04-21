---
title: Upravit textové Runbooky v Azure Automation
description: Tento článek popisuje, jak použít textový editor Azure Automation pro práci s Runbooky PowerShellu a powershellovým prostředím pracovních postupů.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 296d45fae4d59553b54a1b68923c91be4168d3a5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829381"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Upravit textové Runbooky v Azure Automation

Pomocí textového editoru v Azure Automation můžete upravit [powershellové Runbooky](automation-runbook-types.md#powershell-runbooks) a [Runbooky pracovních postupů PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks). Tento Editor obsahuje typické funkce jiných editorů kódu, jako je například IntelliSense. Také používá barevné kódování s dalšími speciálními funkcemi, které vám pomůžou při přístupu k prostředkům, které jsou společné pro Runbooky. 

Textový Editor obsahuje funkci pro vložení kódu pro rutiny, prostředky a podřízené Runbooky do sady Runbook. Místo psaní v kódu si můžete vybrat ze seznamu dostupných prostředků a editor vloží příslušný kód do Runbooku.

Každá sada Runbook v Azure Automation má dvě verze, koncept a Publikováno. Upravíte koncept verze Runbooku a pak ho publikujete, aby se mohl spustit. Publikovaná verze se nedá upravit. Další informace najdete v tématu [publikování Runbooku](manage-runbooks.md#publish-a-runbook).

Tento článek poskytuje podrobné pokyny pro provádění různých funkcí v tomto editoru. Neplatí to pro [grafické Runbooky](automation-runbook-types.md#graphical-runbooks). Pokud chcete s těmito sadami Runbook pracovat, přečtěte si téma [vytváření grafiky v Azure Automation](automation-graphical-authoring-intro.md).

## <a name="edit-a-runbook-with-the-azure-portal"></a>Úprava Runbooku pomocí Azure Portal

1. V Azure Portal vyberte svůj účet Automation.
2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.
3. Zvolte sadu Runbook, kterou chcete upravit, a klikněte na tlačítko **Upravit**.
4. Upravte sadu Runbook.
5. Po dokončení úprav klikněte na **Uložit** .
6. Pokud chcete publikovat nejnovější verzi sady Runbook, klikněte na **publikovat** .

### <a name="insert-a-cmdlet-into-a-runbook"></a>Vložení rutiny do Runbooku

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete rutinu umístit.
2. Rozbalte uzel **rutiny** v ovládacím prvku knihovna.
3. Rozbalte modul obsahující rutinu, která se má použít.
4. Klikněte pravým tlačítkem na název rutiny, který chcete vložit, a vyberte **Přidat na plátno**. Pokud má rutina více než jednu sadu parametrů, Editor přidá výchozí sadu. Můžete také rozbalit rutinu a vybrat jinou sadu parametrů.
5. Všimněte si, že kód pro rutinu je vložen s celým seznamem parametrů.
6. Zadejte odpovídající hodnotu místo hodnoty obklopené lomenými závorkami (<>) pro všechny požadované parametry. Odeberte všechny parametry, které nepotřebujete.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Vložení kódu pro podřízený Runbook do Runbooku

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete umístit kód pro [podřízený Runbook](automation-child-runbooks.md).
2. Rozbalte uzel **Runbooky** v ovládacím prvku knihovna.
3. Klikněte pravým tlačítkem na Runbook, který chcete vložit, a vyberte **Přidat na plátno**.
4. Kód pro podřízenou sadu Runbook je vložen do libovolných zástupných symbolů pro všechny parametry sady Runbook.
5. Zástupné symboly nahraďte odpovídajícími hodnotami pro každý parametr.

### <a name="insert-an-asset-into-a-runbook"></a>Vložení assetu do Runbooku

1. V ovládacím prvku plátno textového editoru umístěte kurzor na místo, kam chcete umístit kód pro podřízený Runbook.
2. Rozbalte uzel **assets (prostředky** ) v ovládacím prvku knihovna.
3. Rozbalte uzel pro požadovaný typ prostředku.
4. Klikněte pravým tlačítkem na název assetu, který chcete vložit, a vyberte **Přidat na plátno**. V případě [variabilních prostředků](./shared-resources/variables.md)vyberte **Přidat "získat proměnnou" na plátno** nebo **Přidat "nastavit proměnnou" na plátno** v závislosti na tom, zda chcete získat nebo nastavit proměnnou.
5. Všimněte si, že kód pro Asset je vložen do sady Runbook.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Úprava Azure Automation sady Runbook pomocí prostředí Windows PowerShell

Chcete-li upravit sadu Runbook pomocí prostředí Windows PowerShell, použijte Editor dle vlastního výběru a uložte sadu Runbook do souboru **. ps1** . K načtení obsahu Runbooku můžete použít rutinu [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) . Pomocí rutiny  [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) můžete stávající koncept sady Runbook nahradit upravenou sadou.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Načtení obsahu Runbooku pomocí Windows PowerShellu

Následující vzorové příkazy ukazují, jak načíst skript pro Runbook a uložit ho do souboru skriptu. V tomto příkladu se načte koncept. Je také možné načíst publikovanou verzi sady Runbook, i když tuto verzi nelze změnit.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Změna obsahu Runbooku pomocí Windows PowerShellu

Následující vzorové příkazy ukazují, jak nahradit stávající obsah Runbooku obsahem souboru skriptu. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

* [Umožňuje spravovat Runbooky v Azure Automation](manage-runbooks.md).
* [Školicí pracovní postup prostředí PowerShell](automation-powershell-workflow.md).
* [Grafické vytváření v Azure Automation](automation-graphical-authoring-intro.md).
* [Certifikáty](./shared-resources/certificates.md).
* [Připojení](automation-connections.md).
* [Přihlašovací údaje](./shared-resources/credentials.md).
* [Plány](./shared-resources/schedules.md).
* [Proměnné](./shared-resources/variables.md).
* [Reference k rutinám prostředí PowerShell](/powershell/module/az.automation).
