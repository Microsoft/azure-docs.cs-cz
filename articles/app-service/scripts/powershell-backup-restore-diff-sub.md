---
title: Ukázka skriptu Azure PowerShell – obnovení zálohy aplikace do jiného předplatného | Microsoft Docs
description: Ukázka skriptu Azure PowerShell – obnovení webové aplikace ze zálohy v jiném předplatném
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 5f293f290bd0c8b6c5546d37a43dde64a5af4f82
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098407"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Obnovení webové aplikace ze zálohy v jiném předplatném pomocí PowerShellu

Tento ukázkový skript načte dříve dokončenou zálohu z existující webové aplikace a obnoví ji na webovou aplikaci v jiném předplatném. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud už webovou aplikaci nepotřebujete, pomocí následujícího příkazu odeberte skupinu prostředků, webovou aplikaci a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Přidá ověřený účet, který se má použít pro Azure Resource Manager požadavky rutiny.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Obnoví webovou aplikaci z předchozího dokončeného zálohování. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
