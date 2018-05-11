---
title: Nastavení sady Runbook ve službě Azure Automation
description: Popisuje konfiguraci nastavení pro sady runbook v Azure Automation a jak ji pomocí portálu Azure a prostředí Windows PowerShell změnit.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/19/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 064fdc6b50bf21b78d122c650ae22f8b4f70c608
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="runbook-settings"></a>Nastavení runbooku
Každá sada runbook ve službě Azure Automation má několik nastavení, která pomáhají identifikovat a změnit její chování při protokolování. Každá z těchto nastavení je popsána níže následují procedury na tom, jak je upravit.

## <a name="settings"></a>Nastavení
### <a name="name-and-description"></a>Název a popis
Název sady runbook nelze změnit po jeho vytvoření. Popis je volitelný a může mít až 512 znaků.

### <a name="tags"></a>Značky
Značky umožňují přiřadit různá slova a slovní spojení, která pomáhají sadu runbook identifikovat. Například když odešlete sady runbook [Galerie prostředí PowerShell](https://www.powershellgallery.com/), zadejte konkrétní značky k identifikaci kategorie, které sada runbook by měl být uvedený v. Můžete určit více značek pro sadu runbook oddělte je čárkami.

### <a name="logging"></a>Protokolování
Ve výchozím nastavení nejsou podrobné a průběh záznamy zapisují do historie úlohy. Můžete změnit nastavení pro konkrétní runbook tyto záznamy protokolu. Další informace o tyto záznamy, najdete v části [výstup a zprávy Runbooku](automation-runbook-output-and-messages.md).

## <a name="changing-runbook-settings"></a>Změna nastavení sady runbook

### <a name="changing-runbook-settings-with-the-azure-portal"></a>Změna nastavení sady runbook pomocí portálu Azure
Můžete změnit nastavení sady runbook na portálu Azure z **nastavení** okno pro sadu runbook.

1. Na portálu Azure vyberte **automatizace** a pak klikněte na název účtu automation.
2. Vyberte **Runbooky** kartě.
3. Klikněte na název sady runbook a přejdete do okna nastavení pro sadu runbook. Odsud můžete zadat nebo změnit značky, popis sady runbook, nakonfigurovat nastavení trasování a protokolování a přístup k nástrojů podpory, které pomáhají při řešení problémů.     

### <a name="changing-runbook-settings-with-windows-powershell"></a>Změna nastavení sady runbook pomocí prostředí Windows PowerShell
Můžete použít [Set-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603786.aspx) rutiny změnit nastavení pro sady runbook. Pokud chcete zadat více značek, můžete buď zadat pole nebo jeden řetězec s čárkami hodnoty na parametr značky. Můžete získat aktuální značky s [Get-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603728.aspx).

Následující vzorové příkazy ukazují, jak nastavit vlastnosti pro sady runbook. Tato ukázka přidá tři značky pro existující značky a určuje, zda mají být protokolovány podrobných záznamů.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $tags = (Get-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName).Tags
    $tags += "Tag1,Tag2,Tag3"
    Set-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName $automationAccountName –Name $runbookName –LogVerbose $true –Tags $tags

## <a name="next-steps"></a>Další postup
* Další informace o vytvoření a ze sady runbook načíst výstupní a chybové zprávy naleznete v tématu [výstup a zprávy Runbooku](automation-runbook-output-and-messages.md) 
* Abyste pochopili, jak přidat sadu runbook, která již byla vyvinuta komunity nebo jiný zdroj, nebo vytvořte vlastní sady runbook viz [vytvoření nebo import Runbooku](automation-creating-importing-runbook.md) 

