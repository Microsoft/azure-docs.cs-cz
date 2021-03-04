---
title: Vytvoření pracovního prostoru Log Analytics – Azure PowerShell
description: Ukázka skriptu Azure PowerShell – vytvoření pracovního prostoru Log Analytics
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: c04eb177eb8c52464be2ab7702a365d7a7b54e07
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040936"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Vytvoření pracovního prostoru Log Analytics pomocí prostředí PowerShell

Tento skript vám umožní rychle začít pracovat s pracovním prostorem Azure Log Analytics, který je nutný, pokud chcete začít shromažďovat, analyzovat a provádět akce s daty.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript pomocí následujících příkazů vytvoří nový pracovní prostor Log Analytics v předplatném. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Načte informace o existujícím pracovním prostoru. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Vytvoří pracovní prostor v zadané skupině prostředků a umístění. |


## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

