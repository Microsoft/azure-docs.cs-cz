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
ms.openlocfilehash: aca7f700dc7004f682430d0794f8bc9b024f41b3
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113494"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>Obnovení webové aplikace ze zálohy pomocí Azure Powershellu

Tento ukázkový skript načte zálohu předchozí dokončená z existující webové aplikace a obnoví přepsáním jeho obsah. 

V případě potřeby nainstalujte Azure PowerShell podle pokynů uvedených v [příručce k Azure PowerShellu](/powershell/azure/overview) a pak spuštěním rutiny `Connect-AzAccount` vytvořte připojení k Azure. 

## <a name="sample-script"></a>Ukázkový skript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pokud nepotřebujete webové aplikace, použijte následující příkaz k odebrání skupiny prostředků, webové aplikace a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Získá seznam záloh webové aplikace. |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Obnoví webovou aplikaci z předchozí dokončená zálohy. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky Azure PowerShellu pro Azure App Service Web Apps najdete v [ukázkách Azure PowerShellu](../samples-powershell.md).
