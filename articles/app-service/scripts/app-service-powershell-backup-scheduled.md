---
title: Ukázkový skript Azure PowerShellu – Vytvoření naplánovaného zálohování webové aplikace | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Vytvoření naplánovaného zálohování webové aplikace
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 4afa7c2b144fcb1d72d82f146719c26d7ceb4eb4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31586785"
---
# <a name="create-a-scheduled-backup-for-a-web-app"></a>Vytvoření naplánovaného zálohování webové aplikace

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak pro ni vytvoří naplánované zálohování. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzureRmAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Vytvoří účet úložiště. |
| [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer) | Vytvoří kontejner úložiště Azure. |
| [New-AzureStorageContainerSASToken](/powershell/module/azure.storage/new-azurestoragecontainersastoken) | Vygeneruje token SAS pro kontejner úložiště Azure. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Vytvoří webovou aplikaci. |
| [Edit-AzureRmWebAppBackupConfiguration](/powershell/module/azurerm.websites/edit-azurermwebappbackupconfiguration) | Upraví konfiguraci zálohování webové aplikace. |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [Get-AzureRmWebAppBackupConfiguration](/powershell/module/azurerm.websites/get-azurermwebappbackupconfiguration) | Získá konfiguraci zálohování webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../app-service-powershell-samples.md).
