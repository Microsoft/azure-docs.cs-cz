---
title: 'Skript PowerShellu: Vytvoření pozvánky ke sdílené složce Azure| Dokumenty společnosti Microsoft'
description: Tento skript prostředí PowerShell odešle pozvánku ke sdílení dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307232"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Použití Prostředí PowerShell ke sledování využití odeslané sdílené složky dat

Tento skript prostředí PowerShell vytvoří pozvánku ke sdílení dat.

## <a name="sample-script"></a>Ukázkový skript


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Nová pozvánka AzDataShare](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Vytvořte pozvánku ke sdílení dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Share PowerShellu najdete ve [ukázkách Prostředí PowerShell Azure Data Share](../../samples-powershell.md).
