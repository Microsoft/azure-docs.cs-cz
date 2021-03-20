---
title: 'PowerShellový skript: výpis požadavků na sdílení dat Azure odeslaných příjemci'
description: Přečtěte si, jak tento skript PowerShellu získá pozvánky odeslané příjemci a podívejte se na příklad skriptu, který můžete použít.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 5f75894094fa2a15dbc9e2809ed6c3631df96c3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221209"
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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation) | Načte a vypíše pozvánky pro sdílení odeslaných dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
