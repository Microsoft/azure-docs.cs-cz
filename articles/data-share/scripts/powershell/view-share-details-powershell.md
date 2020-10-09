---
title: 'PowerShellový skript: vypsání existujících sdílených složek v Azure Data Share | Microsoft Docs'
description: Tento skript PowerShellu vypíše a zobrazí podrobnosti o sdílených složkách.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 6314bd348c22c901001b88eda6875181a2f69df4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "70307122"
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
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare?view=azps-2.6.0) | Získá a zobrazí seznam sdílených složek v účtu. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
