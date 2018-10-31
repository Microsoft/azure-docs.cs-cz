---
title: Postup odinstalace nástroje úlohy elastické databáze
description: Zjistěte, jak odinstalovat součásti úlohy elastické databáze pomocí webu Azure portal, prostředí PowerShell.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: 15313bf0d2d152a8295fb831e62452f8ad63bbeb
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50242445"
---
# <a name="uninstall-elastic-database-jobs-components"></a>Odinstalovat součásti úlohy elastické databáze


[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]


**Úlohy elastic Database** komponenty lze odinstalovat pomocí webu Azure portal nebo Powershellu.

## <a name="uninstall-elastic-database-jobs-components-using-the-azure-portal"></a>Odinstalovat součásti úlohy elastické databáze pomocí webu Azure portal
1. Otevřete web [Azure Portal](https://portal.azure.com/).
2. Přejděte na předplatné, které obsahuje **úlohy elastické databáze** komponent, konkrétně předplatné, ve které Elastic Database byly nainstalované komponenty úlohy.
3. Klikněte na tlačítko **Procházet** a klikněte na tlačítko **skupiny prostředků**.
4. Vyberte skupinu prostředků s názvem "__ElasticDatabaseJob".
5. Odstraníte skupinu prostředků.

## <a name="uninstall--elastic-database-jobs-components-using-powershell"></a>Odinstalovat součásti úlohy elastické databáze pomocí Powershellu
1. Spusťte příkazové okno Microsoft Azure Powershellu a přejděte na nástroje podadresář ve složce Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x: typ **nástroje průběžného nasazování**.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x* > Nástroje průběžného nasazování
2. Spusťte.\UninstallElasticDatabaseJobs.ps1 skript prostředí PowerShell.
   
     PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>Unblock-File .\UninstallElasticDatabaseJobs.ps1   PS C:\*Microsoft.Azure.SqlDatabase.Jobs.x.x.xxxx.x*\tools>.\UninstallElasticDatabaseJobs.ps1

Nebo jednoduše řečeno, spusťte následující skript, za předpokladu, že výchozí hodnoty, kde se používá na instalaci komponenty:

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
Znovu nainstalujte úlohy elastické databáze, najdete v článku [instalace úlohy služby Elastic Database](sql-database-elastic-jobs-service-installation.md)

Přehled úloh Elastických databází najdete v tématu [přehled úloh Elastických databází](sql-database-elastic-jobs-overview.md).

<!--Image references-->


