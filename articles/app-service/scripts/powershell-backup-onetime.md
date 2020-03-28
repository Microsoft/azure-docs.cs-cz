---
title: 'PowerShell: Zálohování aplikace'
description: Zjistěte, jak pomocí Azure PowerShellu automatizovat nasazení a správu služby App Service. Tato ukázka ukazuje, jak zálohovat aplikaci.
author: msangapu-msft
tags: azure-service-management
ms.assetid: fc755f82-ca3e-4532-b251-690b699324d6
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: c17cfcd2e55b98be9d86a73f6388d921336f993b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80057667"
---
# <a name="back-up-a-web-app-using-powershell"></a>Zálohování webové aplikace pomocí PowerShellu

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
| [Nový kontejner AzStorage](/powershell/module/az.storage/new-AzStoragecontainer) | Vytvoří kontejner úložiště Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Vygeneruje token SAS pro kontejner úložiště Azure.  |
| [Nový-AzAppServicePlán](/powershell/module/az.websites/new-azappserviceplan) | Vytvoří plán služby App Service. |
| [Nový-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Vytvoří webovou aplikaci. |
| [Nový-AzWebAppBackup](/powershell/module/az.websites/new-azwebappbackup) | Vytvoří zálohu webové aplikace. |
| [Seznam Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Získá seznam záloh webové aplikace. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
