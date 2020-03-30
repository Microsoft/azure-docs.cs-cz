---
title: 'Skript PowerShellu: Vytvoření nového účtu Azure Data Share | Dokumenty společnosti Microsoft'
description: Tento skript prostředí PowerShell vytvoří nový účet Sdílení dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307274"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Vytvoření účtu sdílení dat v Azure pomocí PowerShellu

Tento skript prostředí PowerShell vytvoří nový účet Sdílení dat. 

## <a name="sample-script"></a>Ukázkový skript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Nový účet AzDataShare](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Vytvoří účet sdílení dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Share PowerShellu najdete ve [ukázkách Prostředí PowerShell Azure Data Share](../../samples-powershell.md).
