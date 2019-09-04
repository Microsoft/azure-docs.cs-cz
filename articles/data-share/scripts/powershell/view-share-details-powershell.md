---
title: 'Skript prostředí PowerShell: Vypsat existující sdílené složky v Azure Data Share | Microsoft Docs'
description: Tento skript PowerShellu vypíše a zobrazí podrobnosti o sdílených složkách.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 53b44441397be6aaa690c75bf362fd16d5a34019
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242889"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Použití PowerShellu k zobrazení podrobností o odeslané sdílené složce dat

Tento skript PowerShellu vypíše sdílené složky dat z existujícího účtu a získá podrobnosti o konkrétní sdílené složce.


## <a name="sample-script"></a>Ukázkový skript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzDataShare](/powershell/module/az.resources/get-az) | Získá a zobrazí seznam sdílených složek v účtu. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
