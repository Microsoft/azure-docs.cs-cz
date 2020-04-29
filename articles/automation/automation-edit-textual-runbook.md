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
ms.openlocfilehash: 2ef7db244057bc8b3b2e4d938b9f3bdd11c7940a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81406023"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Úpravy textových runbooků v Azure Automation

Textový editor v Azure Automation lze použít k úpravě runbooků [PowerShellu](automation-runbook-types.md#powershell-runbooks) a [runbooků powershellového pracovního postupu](automation-runbook-types.md#powershell-workflow-runbooks). Tento Editor obsahuje typické funkce jiných editorů kódu, jako je například IntelliSense. Obsahuje také barevné kódování s dalšími speciálními funkcemi, které vám pomůžou při přístupu k prostředkům, které jsou společné pro Runbooky. 

Textový Editor obsahuje funkci pro vložení kódu pro rutiny, prostředky a podřízené Runbooky do sady Runbook. Místo psaní v kódu si můžete vybrat ze seznamu dostupných prostředků a editor vloží příslušný kód do Runbooku.

Každá sada Runbook v Azure Automation má dvě verze, koncept a Publikováno. Upravíte koncept verze Runbooku a pak ho publikujete, aby se mohl spustit. Publikovaná verze se nedá upravit. Další informace najdete v tématu [publikování Runbooku](manage-runbooks.md#publishing-a-runbook).

Tento článek poskytuje podrobné pokyny pro provádění různých funkcí v tomto editoru. Neplatí to pro [grafické Runbooky](automation-runbook-types.md#graphical-runbooks). Pokud chcete s těmito sadami Runbook pracovat, přečtěte si téma [vytváření grafiky v Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Úprava Runbooku pomocí Azure Portal

Pomocí následujícího postupu otevřete Runbook pro úpravy v textovém editoru.

1. V Azure Portal vyberte svůj účet Automation.
2. V části **Automatizace procesu**vyberte **Runbooky** a otevřete seznam runbooků.
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
4. Klikněte pravým tlačítkem na název assetu, který chcete vložit, a vyberte **Přidat na plátno**. V případě [variabilních prostředků](automation-variables.md)vyberte **Přidat "získat proměnnou" na plátno** nebo **Přidat "nastavit proměnnou" na plátno**v závislosti na tom, zda chcete získat nebo nastavit proměnnou.
5. Všimněte si, že kód pro Asset je vložen do sady Runbook.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Úprava Azure Automation sady Runbook pomocí prostředí Windows PowerShell

Chcete-li upravit sadu Runbook pomocí prostředí Windows PowerShell, použijte Editor dle vlastního výběru a uložte sadu Runbook do souboru **. ps1** . K načtení obsahu Runbooku můžete použít rutinu [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) . Pomocí rutiny [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) můžete stávající koncept sady Runbook nahradit upravenou sadou.

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

Následující vzorové příkazy ukazují, jak nahradit stávající obsah Runbooku obsahem souboru skriptu. Jedná se o stejný vzorový postup jako v [nástroji pro import Runbooku ze souboru skriptu v prostředí Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
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
* [Reference k rutinám prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation)
