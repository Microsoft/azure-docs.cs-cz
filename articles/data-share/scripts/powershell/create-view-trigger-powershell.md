---
title: 'PowerShellový skript: vytvoření a zobrazení triggerů snímků Azure Data Share'
description: Tento skript PowerShellu vytvoří a získá triggery snímků sdílené složky.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 05b70641873ab352ca92ac199d99c05b2fe0f4e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221294"
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
| [New-AzDataShareTrigger](/powershell/module/az.datashare/new-azdatasharetrigger) | Vytvořte Trigger snímku sdílené složky. |
| [Get-AzDataShareTrigger](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting) | Získá nastavení synchronizace pro synchronizaci sdílené složky. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
