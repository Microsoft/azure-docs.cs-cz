---
title: Úpravy textových runbooků v Azure Automation
description: Tento článek popisuje různé postupy pro práci s PowerShellem a runbooky pracovního postupu PowerShellu v Azure Automation pomocí textového editoru.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a7eec0a7650f9c8e04a8d1062d32b6feb7d1d99
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850852"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Úpravy textových runbooků v Azure Automation

Textový editor v Azure Automation lze použít k úpravě runbooků [PowerShellu](automation-runbook-types.md#powershell-runbooks) a [runbooků powershellového pracovního postupu](automation-runbook-types.md#powershell-workflow-runbooks). To má typické funkce jiných editorů kódu, jako je IntelliSense a barevné kódování, s dalšími speciálními funkcemi, které vám pomůžou při přístupu k prostředkům, které jsou společné pro Runbooky. Tento článek poskytuje podrobné pokyny pro provádění různých funkcí v tomto editoru.

Textový Editor obsahuje funkci pro vložení kódu pro rutiny, prostředky a podřízené Runbooky do sady Runbook. Namísto psaní do kódu si můžete vybrat ze seznamu dostupných prostředků a nechat do Runbooku vložen příslušný kód.

Každá sada Runbook v Azure Automation má dvě verze, koncept a Publikováno. Upravíte koncept verze Runbooku a pak ho publikujete, aby se mohl spustit. Publikovaná verze nelze upravit. Další informace najdete v tématu [publikování Runbooku](manage-runbooks.md#publish-a-runbook).

Pokud chcete pracovat s [grafickými Runbooky](automation-runbook-types.md#graphical-runbooks), přečtěte si téma [vytváření grafického obsahu v Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Úprava Runbooku pomocí Azure Portal

Pomocí následujícího postupu otevřete Runbook pro úpravy v textovém editoru.

1. V Azure Portal vyberte svůj účet Automation.
2. V části **Automatizace procesu**vyberte **Runbooky** a otevřete seznam runbooků.
3. Vyberte sadu Runbook, kterou chcete upravit, a poté klikněte na tlačítko **Upravit** .
4. Upravte sadu Runbook.
5. Klikněte na tlačítko **Uložit** když se dokončí úpravy.
6. Klikněte na tlačítko **publikovat** potřebujete nejnovější verzi konceptu sady runbook, který má být publikován.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Vložení rutiny do sady Runbook

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete rutinu umístit.
2. Rozbalte uzel **rutiny** v ovládacím prvku knihovna.
3. Rozbalte modul obsahující rutinu, kterou chcete použít.
4. Klikněte pravým tlačítkem na rutinu, kterou chcete vložit, a vyberte **Přidat na plátno**. Pokud má rutina více než jednu sadu parametrů, bude přidána výchozí sada. Můžete také rozbalit rutinu a vybrat jinou sadu parametrů.
5. Kód rutiny je vložen s celým seznamem parametrů.
6. Zadejte odpovídající hodnotu namísto datového typu, který je obklopen složenými závorkami < > pro všechny požadované parametry. Odeberte všechny parametry, které nepotřebujete.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Vložení kódu pro podřízený Runbook do sady Runbook

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete umístit kód pro [podřízený Runbook](automation-child-runbooks.md).
2. Rozbalte uzel **Runbooky** v ovládacím prvku knihovna.
3. Klikněte pravým tlačítkem na Runbook, který chcete vložit, a vyberte **Přidat na plátno**.
4. Kód pro podřízenou sadu Runbook je vložen do libovolných zástupných symbolů pro všechny parametry sady Runbook.
5. Zástupné symboly nahraďte odpovídajícími hodnotami pro každý parametr.

### <a name="to-insert-an-asset-into-a-runbook"></a>Vložení assetu do Runbooku

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete umístit kód pro podřízený Runbook.
2. Rozbalte uzel **assets (prostředky** ) v ovládacím prvku knihovna.
3. Rozbalte uzel pro požadovaný typ prostředku.
4. Klikněte pravým tlačítkem na prostředek, který chcete vložit, a vyberte **Přidat na plátno**. V případě [variabilních prostředků](automation-variables.md)vyberte **Přidat "získat proměnnou" na plátno** nebo **Přidat "nastavenou proměnnou" na plátno** v závislosti na tom, zda chcete proměnnou získat nebo nastavit.
5. Kód assetu je vložen do sady Runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Úprava Azure Automation sady Runbook pomocí prostředí Windows PowerShell

Chcete-li upravit sadu Runbook pomocí prostředí Windows PowerShell, použijte Editor dle svého výběru a uložte jej do souboru `.ps1`. Pomocí rutiny [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) můžete načíst obsah Runbooku a pak rutinu [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) nahradit existující koncept sady Runbook upraveným.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Načtení obsahu Runbooku pomocí prostředí Windows PowerShell

Následující vzorové příkazy znázorňují postup načtení skriptu pro sadu runbook a uložte ho do souboru skriptu. V tomto příkladu se načte koncept. Je také možné načíst publikovanou verzi sady Runbook, i když tuto verzi nelze změnit.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Změna obsahu Runbooku pomocí prostředí Windows PowerShell

Následující vzorové příkazy znázorňují postup nahrazení stávajícího obsahu sady Runbook obsahem ze souboru skriptu. Jedná se o stejný vzorový postup jako v [nástroji pro import Runbooku ze souboru skriptu v prostředí Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Související články

* [Správa runbooků v Azure Automation](manage-runbooks.md)
* [Pracovní postup prostředí PowerShell pro učení](automation-powershell-workflow.md)
* [Grafické vytváření v Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikáty](automation-certificates.md)
* [Připojení](automation-connections.md)
* [Přihlašovací údaje](automation-credentials.md)
* [Plány](automation-schedules.md)
* [Proměnné](automation-variables.md)

