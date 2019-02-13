---
title: Ukázkový skript Azure PowerShellu – Zálohování webové aplikace | Microsoft Docs
description: Ukázkový skript Azure PowerShellu – Zálohování webové aplikace
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 13d985b95d971ea28ec5345dbb86df7a3271b716
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115177"
---
# <a name="back-up-a-web-app-using-powershell"></a>Zálohování webové aplikace pomocí Powershellu

Tento ukázkový skript vytvoří ve službě App Service webovou aplikaci se souvisejícími prostředky a pak pro ni vytvoří jednorázovou zálohu. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-onetime/backup-onetime.ps1?highlight=1-5 "Back up a web app")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Vytvoří účet úložiště. |
| [New-AzStorageContainer](/powershell/module/azure.storage/new-AzStoragecontainer) | Vytvoří kontejner úložiště Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/azure.storage/new-AzStoragecontainersastoken) | Vygeneruje token SAS pro kontejner úložiště Azure.  |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [New-AzWebAppBackup](/powershell/module/az.websites/new-azwebappbackup) | Vytvoří zálohu webové aplikace. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Získá seznam záloh webové aplikace. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
