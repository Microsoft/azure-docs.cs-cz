---
title: Ukázkový skript Azure PowerShellu – Odstranění zálohy webové aplikace | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Odstranění zálohy webové aplikace
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 4a2abe05b6781358355cc264b66673671bc52557
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53185643"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Odstraňování zálohy pro web pomocí Azure Powershellu

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak pro ni vytvoří jednorázovou zálohu. 

Ke spuštění tohoto skriptu potřebujete existující zálohu webové aplikace. Pokyny k jejímu vytvoření najdete v tématu [Zálohování webové aplikace](app-service-powershell-backup-onetime.md) nebo [Vytvoření naplánovaného zálohování webové aplikace](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [Remove-AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Odebere zadanou zálohu webové aplikace. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../app-service-powershell-samples.md).
