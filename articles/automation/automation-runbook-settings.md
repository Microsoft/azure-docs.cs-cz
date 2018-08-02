---
title: Nastavení sady Runbook ve službě Azure Automation
description: Popisuje nastavení konfigurace pro sady runbook ve službě Azure Automation, jak změnit pomocí webu Azure portal a prostředí Windows PowerShell.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 589df59e02a18629d5f405ff1ce8870333f2228e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397709"
---
# <a name="runbook-settings"></a>Nastavení runbooku
Každá sada runbook ve službě Azure Automation má několik nastavení, která pomáhají identifikovat a změnit její chování při protokolování. Každá z těchto nastavení je popsána níže následuje postup jeho změny.

## <a name="settings"></a>Nastavení
### <a name="name-and-description"></a>Název a popis
Název sady runbook nelze změnit po jeho vytvoření. Popis je volitelný a může mít až 512 znaků.

### <a name="tags"></a>Značky
Značky umožňují přiřadit různá slova a slovní spojení, která pomáhají sadu runbook identifikovat. Například při odeslání sady runbook [Galerie prostředí PowerShell](https://www.powershellgallery.com/), můžete zadat konkrétní značky k identifikaci kategorií, které sada runbook by měly být uvedeny v. Můžete určit více značek pro sadu runbook tak, že je oddělíte čárkami.

### <a name="logging"></a>Protokolování
Ve výchozím nastavení podrobný nebo průběh záznamy nejsou zapsány do historie úlohy. Můžete změnit nastavení pro konkrétní runbook, aby se protokolovaly tyto záznamy. Další informace o těchto záznamech najdete v tématu [Runbook Output and Messages](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Změna nastavení sady runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Změna nastavení sady runbook pomocí webu Azure portal
Můžete změnit nastavení na webu Azure Portal ze sady runbook **nastavení** okno pro sadu runbook.

1. Na webu Azure Portal, vyberte **automatizace** a pak klikněte na název účtu automation.
2. Vyberte **sady Runbook** kartu.
3. Klikněte na název sady runbook a přejdete do okna nastavení pro sadu runbook. Odsud můžete zadat nebo upravit značky, popis sady runbook, konfigurace nastavení trasování a protokolování a přístup k odborné pomoci nástroje, které pomáhají při řešení problémů.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Změna nastavení sady runbook pomocí prostředí Windows PowerShell
Můžete použít [Set-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/set-azurermautomationrunbook) rutiny, chcete-li změnit nastavení sady runbook. Pokud chcete zadat více značek, je buď zadat pole nebo jednoho řetězce s hodnotami oddělený čárkami do parametru Tags. Můžete získat aktuální značku s [Get-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/get-azurermautomationrunbook).

Následující vzorové příkazy znázorňují postup nastavení vlastností pro sadu runbook. Tato ukázka přidá tři klíčová slova do existující značky a určuje, zda mají být protokolovány podrobných záznamů.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Další postup
* Informace o vytvoření a načtení výstupu a chybové zprávy z runbooků najdete v tématu [výstup a zprávy Runbooku](automation-runbook-output-and-messages.md) 
* Pochopit, jak přidat sadu runbook, která byla již vyvinutý komunitou nebo jiný zdroj, nebo k vytvoření vlastní sady runbook viz [vytvoření nebo import Runbooku](automation-creating-importing-runbook.md) 

