---
title: 'PowerShell: odstranění zálohy aplikace'
description: Naučte se používat Azure PowerShell k automatizaci nasazení a správy App Service. V této ukázce se dozvíte, jak odstranit zálohu aplikace.
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 93b53bedef291b9aa740f5bbe9c492185f035f89
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80044833"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Odstranění zálohy webu pomocí Azure PowerShell

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak pro ni vytvoří jednorázovou zálohu. 

Ke spuštění tohoto skriptu potřebujete existující zálohu webové aplikace. Pokyny k jejímu vytvoření najdete v tématu [Zálohování webové aplikace](powershell-backup-onetime.md) nebo [Vytvoření naplánovaného zálohování webové aplikace](powershell-backup-scheduled.md).

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | Odebere zadanou zálohu webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
