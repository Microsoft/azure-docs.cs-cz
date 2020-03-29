---
title: 'Skript PowerShellu: vytvoření nové sdílené složky dat Azure | Dokumenty společnosti Microsoft'
description: Tento skript prostředí PowerShell vytvoří novou sdílenou složku dat v rámci existujícího účtu sdílení dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307246"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Vytvoření sdílené datové složky v Azure pomocí PowerShellu

Tento skript prostředí PowerShell vytvoří novou sdílenou složku dat v rámci existujícího účtu sdílení dat.

## <a name="sample-script"></a>Ukázkový skript

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Nový-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Vytvoří sdílenou složku dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Share PowerShellu najdete ve [ukázkách Prostředí PowerShell Azure Data Share](../../samples-powershell.md).
