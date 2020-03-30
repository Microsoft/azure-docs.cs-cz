---
title: 'Skript PowerShellu: Přijetí pozvánky ze sdílené složky dat Azure | Dokumenty společnosti Microsoft'
description: Tento skript prostředí PowerShell přijímá pozvánky z existující sdílené složky dat.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 246e3550650dfd458b4aeecda3b5b7733e49f017
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307335"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Přijetí pozvánky ke sdílené složce dat pomocí PowerShellu

Tento skript prostředí PowerShell přijímá pozvánky odeslané spotřebiteli.

## <a name="sample-script"></a>Ukázkový skript
```powershell
#List invitations sent to a consumer
Get-AzDataShareInvitation

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$invitationId = "<Invitation id>"

#Accept a specific invitation by creating a share subscription
New-AzDataShareSubscription -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName -InvitationId $invitationId

```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Získejte a seznam odeslaných pozvánek ke sdílení dat. |
| [New-AzDataSharePředplatné](/powershell/module/az.datashare/get-azdatasharesubscription?view=azps-2.6.0) | Vytvořte odběr sdílení dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Share PowerShellu najdete ve [ukázkách Prostředí PowerShell Azure Data Share](../../samples-powershell.md).

