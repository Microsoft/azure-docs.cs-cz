---
title: Azure skript prostředí PowerShell ukázkový – vytvořit pracovní prostor analýzy protokolů | Microsoft Docs
description: Azure skript prostředí PowerShell ukázkový – vytvořit pracovní prostor analýzy protokolů pro
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 30d036ae56acc3a798d2776f292243f65cbea43d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23855436"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Pomocí prostředí PowerShell vytvořit pracovní prostor analýzy protokolů

Tento skript získá je spuštěná rychle se pracovní prostor analýzy protokolů Azure, který je nutný, pokud chcete spustit shromažďování, analýze a pořízení akce na data.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření nové pracovní prostor analýzy protokolů v rámci vašeho předplatného. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Získá informace o existujícímu pracovnímu prostoru. |
| [Nové AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Vytvoří pracovního prostoru v zadaná skupina prostředků a umístění. |


## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

