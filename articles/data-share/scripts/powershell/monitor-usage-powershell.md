---
title: 'Skript prostředí PowerShell: Monitorování využití sdílené složky Azure Data | Microsoft Docs'
description: Tento skript PowerShellu načte metriky využití odeslaného sdíleného data.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0a4084d309dd0160970f1c03540705b310eb8e75
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307192"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Použití PowerShellu k monitorování využití odeslané sdílené datové složky

Tento skript PowerShellu monitoruje využití dat seznamem synchronizací odeslaných datových sdílených složek a získáním podrobností o konkrétní synchronizaci.

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
| [Get-AzDataShareSynchronization](/powershell/module/az.datashare/get-azdatasharesynchronization?view=azps-2.6.0) | Vypíše synchronizace pro sdílenou složku. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail?view=azps-2.6.0) | Získá podrobnosti o synchronizaci sdílené synchronizace. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
