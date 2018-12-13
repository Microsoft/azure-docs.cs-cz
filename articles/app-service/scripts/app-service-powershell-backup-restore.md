---
title: Ukázkový skript Azure Powershellu – obnovení webové aplikace ze zálohy | Dokumentace Microsoftu
description: Ukázkový skript Azure Powershellu – obnovení webové aplikace ze zálohy
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1f9e35c471ef846a77667eee94f0e048bd914710
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193344"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Obnovení webové aplikace ze zálohy pomocí Azure Powershellu

Tento ukázkový skript načte zálohu předchozí dokončená z existující webové aplikace a obnoví přepsáním jeho obsah. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzureRmAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud nepotřebujete webové aplikace, použijte následující příkaz k odebrání skupiny prostředků, webové aplikace a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [Obnovení AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Obnoví webovou aplikaci z předchozí dokončená zálohy. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../app-service-powershell-samples.md).
