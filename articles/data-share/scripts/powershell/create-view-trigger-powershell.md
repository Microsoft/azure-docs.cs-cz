---
title: 'PowerShellový skript: vytvoření a zobrazení triggerů snímků Azure Data Share | Microsoft Docs'
description: Tento skript PowerShellu vytvoří a získá triggery snímků sdílené složky.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 31d9754ddd9e9dfd0cca258901a6c8ab2c4a9cdb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86497161"
---
# <a name="use-powershell-to-create-and-share-snapshot-triggers"></a>Použití PowerShellu k vytvoření a sdílení triggerů snímků

Tento skript PowerShellu vytvoří a získá triggery snímků sdílené složky.

## <a name="sample-script"></a>Ukázkový skript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [New-AzDataShareTrigger](/powershell/module/az.datashare/new-azdatasharetrigger?view=azps-2.6.0) | Vytvořte Trigger snímku sdílené složky. |
| [Get-AzDataShareTrigger](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Získá nastavení synchronizace pro synchronizaci sdílené složky. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
