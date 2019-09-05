---
title: 'Skript prostředí PowerShell: Vytvořit nový účet Azure Data Share | Microsoft Docs'
description: Tento skript PowerShellu vytvoří nový účet pro sdílení dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307274"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Použití PowerShellu k vytvoření účtu pro sdílení dat v Azure

Tento skript PowerShellu vytvoří nový účet pro sdílení dat. 

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
| [New-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Vytvoří účet pro sdílení dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
