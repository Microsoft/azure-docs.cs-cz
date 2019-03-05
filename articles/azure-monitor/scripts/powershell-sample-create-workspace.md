---
title: 'Skript Azure Powershellu ukázkový: vytvoření pracovního prostoru Log Analytics | Dokumentace Microsoftu'
description: Skript Azure Powershellu ukázkový – vytvořit pracovní prostor Log Analytics
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306965"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Vytvoření pracovního prostoru Log Analytics pomocí Powershellu

Tento skript vám pomůže zprovoznit rychle k pracovnímu prostoru Azure Log Analytics, která je vyžadována, pokud chcete spustit shromažďování, analýze a akce a současně s daty.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k vytvoření nového pracovního prostoru Log Analytics v rámci vašeho předplatného. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Získá informace o existujícího pracovního prostoru. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Vytvoří pracovní prostor v zadané skupině prostředků a umístění. |


## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

