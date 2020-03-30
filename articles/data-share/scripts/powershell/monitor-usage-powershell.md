---
title: 'Skript PowerShellu: Monitorování využití sdílené složky dat Azure | Dokumenty společnosti Microsoft'
description: Tento skript prostředí PowerShell načte metriky využití odeslané sdílené složky dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0a4084d309dd0160970f1c03540705b310eb8e75
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307192"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Použití Prostředí PowerShell ke sledování využití odeslané sdílené složky dat

Tento skript prostředí PowerShell monitoruje využití dat tím, že uvádí synchronizace odeslané sdílené složky dat a získává podrobnosti o konkrétní synchronizaci.

## <a name="sample-script"></a>Ukázkový skript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Synchronizace Get-AzDataShare](/powershell/module/az.datashare/get-azdatasharesynchronization?view=azps-2.6.0) | Seznam synchronizací ve sdílené složce. |
| [Get-AzDataShareSynchronizationPodrobnosti](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail?view=azps-2.6.0) | Získá podrobnosti synchronizace synchronizace synchronizace. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Share PowerShellu najdete ve [ukázkách Prostředí PowerShell Azure Data Share](../../samples-powershell.md).
