---
title: Vytvoření pracovního prostoru Log Analytics – Azure PowerShell
description: Ukázka skriptu Azure PowerShell – vytvoření pracovního prostoru Log Analytics
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: f6bfb3a244874f6160d34c174b6d10b9a03ca437
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87024108"
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

