---
title: 'PowerShellový skript: přijetí pozvánky ze sdílené složky Azure'
description: Tento skript PowerShellu přijímá pozvánky od stávající sdílené složky.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 24091bafac7ad5c558b975d52064f12715b3d622
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221396"
---
# <a name="use-powershell-to-accept-a-data-share-invitation"></a>Použití PowerShellu k přijetí pozvánky ke sdílení dat

Tento skript PowerShellu přijímá pozvánky odeslané příjemci.

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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Načte a vypíše pozvánky pro sdílení odeslaných dat. |
| [New-AzDataShareSubscription](/powershell/module/az.datashare/get-azdatasharesubscription) | Vytvořte předplatné pro sdílení dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
