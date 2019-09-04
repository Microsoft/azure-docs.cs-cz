---
title: 'PowerShellový skript: vytvoření nové sdílené datové složky Azure | Microsoft Docs'
description: Tento skript PowerShellu vytvoří novou datovou sdílenou složku v rámci stávajícího účtu pro sdílení dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c76dfea83d0f736dbd48239cc3be3496a88ec8a2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242993"
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
| [New-AzDataShare](/powershell/module/az.resources/new-azdatashare) | Vytvoří sdílenou složku dat. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
