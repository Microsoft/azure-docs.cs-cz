---
title: 'PowerShellový skript: vytvoření nové sdílené datové složky Azure'
description: Tento skript PowerShellu vytvoří novou datovou sdílenou složku v rámci stávajícího účtu pro sdílení dat.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221345"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Použití PowerShellu k vytvoření sdílené složky dat v Azure

Tento skript PowerShellu vytvoří novou datovou sdílenou složku v rámci stávajícího účtu pro sdílení dat.

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
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | Vytvoří sdílenou složku dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
