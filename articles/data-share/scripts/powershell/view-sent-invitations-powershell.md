---
title: 'Skript prostředí PowerShell: Vypsat pozvánky ke sdílení dat Azure odeslané příjemci | Microsoft Docs'
description: Tento skript PowerShellu přijímá pozvánky od stávající sdílené složky.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 1304b478bd07ed61293b668badee56338a9cab5a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242902"
---
# <a name="use-powershell-to-get-a-data-share-invitation"></a>Použití PowerShellu k získání pozvánky ke sdílení dat

Tento skript PowerShellu získá pozvánky odeslané příjemci.

## <a name="sample-script"></a>Ukázkový skript
```powershell
# Set variables with your own values
$invitationId = "<invitationId>"
$location = "<location>"

#List invitations sent to a consumer
Get-AzDataShareInvitation

#Get a specific invitation sent to a consumer
Get-AzDataShareInvitation -location -invitationId 

```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [Get-AzDataShareInvitation](/powershell/module/az.resources/get-azdatashareinvitation) | Načte a vypíše pozvánky pro sdílení odeslaných dat. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
