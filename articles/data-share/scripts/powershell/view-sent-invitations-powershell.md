---
title: 'PowerShellový skript: vypsat pozvánky ke sdílení dat Azure odeslané příjemci | Microsoft Docs'
description: Přečtěte si, jak tento skript PowerShellu získá pozvánky odeslané příjemci a podívejte se na příklad skriptu, který můžete použít.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 3f0374ba5b98c145359d18143584e23fc543c552
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90985628"
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
| [Get-AzDataShareInvitation](/powershell/module/az.datashare/get-azdatashareinvitation?view=azps-2.6.0) | Načte a vypíše pozvánky pro sdílení odeslaných dat. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro sdílení dat Azure najdete v [ukázkách Azure Data Share PowerShell](../../samples-powershell.md).
