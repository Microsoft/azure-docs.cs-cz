---
title: 'PowerShell: Obnovení zálohy do jiného předplatného'
description: Zjistěte, jak pomocí Azure PowerShellu automatizovat nasazení a správu služby App Service. Tato ukázka ukazuje, jak obnovit zálohu v jiném předplatném.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: 4a22148d4151fd3c80c7be439b5dd281200e5456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80044578"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Obnovení webové aplikace ze zálohy v jiném předplatném pomocí PowerShellu

Tento ukázkový skript načte dříve dokončenou zálohu z existující webové aplikace a obnoví ji do webové aplikace v jiném předplatném. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud webovou aplikaci už nepotřebujete, odeberte skupinu prostředků, webovou aplikaci a všechny související prostředky pomocí následujícího příkazu.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Přidat-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Přidá ověřený účet pro použití se žádostmi rutiny Azure Resource Manageru.  |
| [Seznam Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [Nový-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci |
| [Obnovit-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Obnoví webovou aplikaci z dříve dokončené zálohy. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
