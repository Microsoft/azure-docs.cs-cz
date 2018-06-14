---
title: Azure ukázkový skript prostředí PowerShell - odstranění zálohy pro webovou aplikaci | Microsoft Docs
description: Azure ukázkový skript prostředí PowerShell - odstranění zálohy pro webovou aplikaci
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f4204cbb4aefe590b87d0a72675823321f280f33
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2017
ms.locfileid: "23941744"
---
# <a name="delete-a-backup-for-a-web-app"></a>Odstranit zálohy pro webovou aplikaci

Tento ukázkový skript vytvoří webovou aplikaci ve službě App Service se jeho souvisejících prostředků a potom vytvoří jednorázové zálohy pro ni. 

Pokud chcete spustit tento skript, musíte existující zálohy pro webovou aplikaci. Chcete-li vytvořit, přečtěte si téma [zálohování webovou aplikaci](app-service-powershell-backup-onetime.md) nebo [vytvoření naplánovaného zálohování pro webovou aplikaci](app-service-powershell-backup-scheduled.md).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázka skriptu, následující příkaz lze použít k odebrání skupiny prostředků, webové aplikace a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazy na dokumentaci konkrétní příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Získá seznam záloh pro webovou aplikaci. |
| [Odebrat AzureRmWebAppBackup](/powershell/module/azurerm.websites/remove-azurermwebappbackup) | Odebere zadané zálohy webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Další ukázky prostředí Azure Powershell pro Azure App Service Web Apps naleznete v [prostředí Azure PowerShell ukázky](../app-service-powershell-samples.md).
