---
title: 'Skript prostředí PowerShell: Přijmout pozvánku ze sdílené složky Azure Data | Microsoft Docs'
description: Tento skript PowerShellu přijímá pozvánky od stávající sdílené složky.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9ef35e0865a7c5bbfb4accc058a8d63bb6f1e80b
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70243071"
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
| [Get-AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | Načte a vypíše pozvánky pro sdílení odeslaných dat. |
| [New-AzDataShareSubscription](/powershell/module/az.resources/get-azdatashareinvitation) | Vytvořte předplatné pro sdílení dat. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).

