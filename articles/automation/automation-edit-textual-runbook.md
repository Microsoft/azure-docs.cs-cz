---
title: Úpravy textových runbooků v Azure Automation
description: Tento článek obsahuje různé postupy pro práci s runbooky PowerShell a PowerShell Workflow v Azure Automation pomocí textového editoru.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3bbfe26f336a25ee85f2223226d6eb513ae21736
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632145"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Úpravy textových runbooků v Azure Automation

Textový editor v Azure Automation lze použít k úpravám [runbooků prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) a [runbooků pracovního postupu prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Tento editor má typické funkce jiných editorů kódu, jako je například IntelliSense. Má také barevné kódování s dalšími speciálními funkcemi, které vám pomohou při přístupu k prostředkům běžným pro sady Runbook. 

Textový editor obsahuje funkci pro vložení kódu pro rutiny, datové zdroje a podřízené runbooky do sady Runbook. Místo psaní v kódu sami, můžete vybrat ze seznamu dostupných prostředků a editor vloží příslušný kód do runbooku.

Každý runbook v Azure Automation má dvě verze, Koncept a Publikováno. Upravíte koncept verze runbooku a pak ji publikovat tak, aby mohla být provedena. Publikovaná verze se nedá upravit. Další informace naleznete [v tématu Publishing a runbook](manage-runbooks.md#publishing-a-runbook).

Tento článek obsahuje podrobné kroky pro provádění různých funkcí s tímto editorem. Ty se nevztahují na [grafické sady Runbook](automation-runbook-types.md#graphical-runbooks). Informace o práci s těmito runbooky najdete [v tématu Grafické vytváření v Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Úprava sady Runbook pomocí portálu Azure

Pomocí následujícího postupu otevřete runbook pro úpravy v textovém editoru.

1. Na webu Azure Portal vyberte účet Automation.
2. V části **AUTOMATIZACE PROCESŮ**vyberte **Runbook,** chcete-li otevřít seznam runbooků.
3. Vyberte runbook, který chcete upravit, a klepněte na tlačítko **Upravit**.
4. Upravte runbook.
5. Po dokončení úprav klikněte na **Uložit.**
6. Pokud chcete publikovat nejnovější verzi runbooku, klikněte na **Publikovat.**

### <a name="insert-a-cmdlet-into-a-runbook"></a>Vložení rutiny do runbooku

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete umístit rutinu.
2. Rozbalte uzel **rutiny** v ovládacím prvku Knihovna.
3. Rozbalte modul obsahující rutinu, která má být používána.
4. Klepněte pravým tlačítkem myši na název rutiny, kterou chcete vložit, a vyberte **Přidat na plátno**. Pokud má rutina více než jednu sadu parametrů, editor přidá výchozí sadu. Můžete také rozbalit rutinu a vybrat jinou sadu parametrů.
5. Všimněte si, že kód pro rutinu je vložen s celý seznam parametrů.
6. Poskytněte vhodnou hodnotu namísto hodnoty obklopené úhlovými závorkami (<>) pro všechny požadované parametry. Odeberte všechny parametry, které nepotřebujete.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Vložení kódu pro podřízený runbook do runbooku

1. Na plátně textového editoru umístěte kurzor na místo, kam chcete umístit kód [podřízeného runbooku](automation-child-runbooks.md).
2. Rozbalte uzel **Runbook** v ovládacím prvku Knihovna.
3. Kliknutím pravým tlačítkem myši na runbook vložte a vyberte **Přidat na plátno**.
4. Kód pro podřízený runbook je vložen s libovolnými zástupnými symboly pro všechny parametry runbooku.
5. Nahraďte zástupné symboly odpovídajícími hodnotami pro každý parametr.

### <a name="insert-an-asset-into-a-runbook"></a>Vložení datového zdroje do runbooku

1. V ovládacím prvku Plátno textového editoru umístěte kurzor na místo, kam chcete umístit kód pro podřízený runbook.
2. Rozbalte uzel **datového zdroje** v ovládacím prvku Knihovna.
3. Rozbalte uzel pro požadovaný typ datového zdroje.
4. Klikněte pravým tlačítkem myši na název datového zdroje, který chcete vložit, a vyberte **Přidat na plátno**. U [proměnných datových zdrojů](automation-variables.md)vyberte buď **Přidat "Získat proměnnou" na plátno,** nebo **Přidat "Nastavit proměnnou" na plátno**, v závislosti na tom, zda chcete proměnnou získat nebo nastavit.
5. Všimněte si, že kód pro datový zdroj je vložen do runbooku.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Úprava runbooku Azure Automation pomocí Prostředí Windows PowerShell

Chcete-li upravit runbook pomocí prostředí Windows PowerShell, použijte editor podle vašeho výběru a uložte jej do souboru **PS1.** Rutina [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) můžete použít k načtení obsahu runbooku. Rutina [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) můžete nahradit existující koncept runbooku upravenou.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Načtení obsahu runbooku pomocí prostředí Windows PowerShell

Následující vzorové příkazy ukazují, jak načíst skript pro Runbook a uložit ho do souboru skriptu. V tomto příkladu se načte koncept. Je také možné načíst publikovanou verzi runbooku, i když tuto verzi nelze změnit.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Změna obsahu runbooku pomocí prostředí Windows PowerShell

Následující ukázkové příkazy ukazují, jak nahradit existující obsah runbooku obsahem souboru skriptu. Jedná se o stejný ukázkový postup jako v [části Import runbooku ze souboru skriptu pomocí prostředí Windows PowerShell](manage-runbooks.md#importing-a-runbook).

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
* [Pracovní postup Učení PowerShellu](automation-powershell-workflow.md)
* [Vytváření grafiky v Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikáty](automation-certificates.md)
* [Připojení](automation-connections.md)
* [Přihlašovací údaje](automation-credentials.md)
* [Plány](automation-schedules.md)
* [Proměnné](automation-variables.md)

