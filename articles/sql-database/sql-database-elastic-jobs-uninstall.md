---
title: Tom, jak odinstalovat nástroj úlohy elastické databáze
description: Další informace o odinstalaci komponenty úlohy elastické databáze pomocí portálu Azure PowerShell.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: e1089db848b30945e5e61765c762262f5478450e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645884"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Odinstalace součástí úlohy elastické databáze
**Elastické databáze úlohy** součásti lze odinstalovat pomocí portálu Azure nebo Powershellu.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Odinstalace součástí úlohy elastické databáze pomocí portálu Azure
1. Otevřete web [Azure Portal](https://portal.azure.com/).
2. Přejděte na předplatné, které obsahuje **úlohy elastické databáze** součásti, konkrétně předplatné, ve které elastické databáze součásti úlohy nebyly nainstalovány.
3. Klikněte na tlačítko **Procházet** a klikněte na tlačítko **skupiny prostředků**.
4. Vyberte skupinu prostředků s názvem "__ElasticDatabaseJob".
5. Odstraňte skupinu prostředků.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Odinstalace součástí úlohy elastické databáze pomocí prostředí PowerShell
1. Spusťte příkazové okno Microsoft Azure PowerShell a přejděte na nástroje podadresář ve složce Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: typ **cd nástroje**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > disk cd nástroje
2. Spusťte.\UninstallElasticDatabaseJobs.ps1 skript prostředí PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Nebo jednoduše, spusťte následující skript, za předpokladu, že výchozí hodnoty, kde je použít pro instalaci součástí:

        $ResourceGroupName = "__ElasticDatabaseJob"
        Switch-AzureMode AzureResourceManager

        $resourceGroup = Get-AzureResourceGroup -Name $ResourceGroupName
        if(!$resourceGroup)
        {
            Write-Host "The Azure Resource Group: $ResourceGroupName has already been deleted.  Elastic database job components are uninstalled."
            return
        }

        Write-Host "Removing the Azure Resource Group: $ResourceGroupName.  This may take a few minutes.”
        Remove-AzureResourceGroup -Name $ResourceGroupName -Force
        Write-Host "Completed removing the Azure Resource Group: $ResourceGroupName.  Elastic database job compoennts are now uninstalled."

## <a name="next-steps"></a>Další postup
Pokud chcete znovu nainstalovat úlohy elastické databáze, přečtěte si téma [nainstalovat službu úlohy elastické databáze](sql-database-elastic-jobs-service-installation.md)

Přehled úlohy elastické databáze najdete v tématu [přehled úlohy elastické databáze](sql-database-elastic-jobs-overview.md).

<!--Image references-->


