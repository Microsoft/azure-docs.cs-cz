---
title: 'Skript PowerShellu: Seznam existujících sdílených složek ve sdílené službě Azure | Dokumenty společnosti Microsoft'
description: Tento skript prostředí PowerShell uvádí a zobrazuje podrobnosti o sdílených složek.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307122"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Zobrazení podrobností o sdílené složce odeslaných dat pomocí prostředí PowerShell

Tento skript prostředí PowerShell uvádí sdílené složky dat z existujícího účtu a získá podrobnosti o konkrétní sdílené složce.


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
| [Získat-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Získá a seznamy sdílených položek v účtu. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Share PowerShellu najdete ve [ukázkách Prostředí PowerShell Azure Data Share](../../samples-powershell.md).
