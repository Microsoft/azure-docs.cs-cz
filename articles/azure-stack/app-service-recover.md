---
title: Obnovení služby App Service v Azure stacku | Dokumentace Microsoftu
description: Podrobné pokyny pro zotavení po havárii Azure Stack App Service
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: jeffgilb
ms.reviewer: apwestgarth
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b034259dde817c863d976384b08da17983ed9de7
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340240"
---
# <a name="recovery-of-app-service-on-azure-stack"></a>Obnovení služby App Service v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*  

Tento dokument obsahuje pokyny týkající se akcí pro zotavení po havárii služby App Service.

Obnovení ze zálohy služby App Service ve službě Azure Stack, musí být přijata následující akce:
1.  Obnovení databází služby App Service
2.  Obsah sdílené složky serveru soubor obnovit
3.  Obnovení role služby App Service a služby

Pokud úložiště služby Azure Stack se použil pro úložiště aplikací Function App, je nutné také provést kroky k obnovení aplikace Function App.

## <a name="restore-the-app-service-databases"></a>Obnovení databází služby App Service
V produkčním prostředí připravené instance systému SQL Server by měl obnovit databáze aplikace služby SQL Server. 

Po [Příprava instanci systému SQL Server](azure-stack-app-service-before-you-get-started.md#prepare-the-sql-server-instance) k hostování databází služby App Service, obnovení databáze ze zálohy pomocí následujících kroků:

1. Přihlaste se k serveru SQL Server, který bude hostitelem obnovené databáze služby App Service s oprávněními správce.
2. Následující příkazy použijte k obnovení databáze služby App Service z příkazového řádku s oprávněními správce:
    ```dos
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_hosting FROM DISK='<full path to backup>' WITH REPLACE"
    sqlcmd -U <SQL admin login> -P <SQL admin password> -Q "RESTORE DATABASE appservice_metering FROM DISK='<full path to backup>' WITH REPLACE"
    ```
3. Ověřte, že obě databáze služby App Service byly úspěšně obnoveny a ukončete SQL Server Management Studio.

> [!NOTE]
> K zotavení z chyby instance clusteru převzetí služeb při selhání, [tyto pokyny](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/recover-from-failover-cluster-instance-failure?view=sql-server-2017). 

## <a name="restore-the-app-service-file-share-content"></a>Obnovit obsah sdílené složky souboru služby App Service
Po [Příprava souborový server](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server) k hostování sdílené služby App Service, budete muset obnovit obsah sdílené složky souboru tenanta ze zálohy. Můžete použít jakoukoli metodu máte k dispozici ke zkopírování souborů do nově vytvořeného umístění sdílené složky souboru služby App Service. Spuštěním tohoto příkladu na souborovém serveru se pomocí Powershellu a nástroje robocopy pro připojení k vzdálené sdílené složce a zkopírujte soubory do sdílené složky:

```powershell
$source = "<remote backup storage share location>"
$destination = "<local file share location>"
net use $source /user:<account to use to connect to the remote share in the format of domain\username> *
robocopy /E $source $destination
net use $source /delete
```

Kromě zkopírování obsahu sdílené složky, musíte resetovat také oprávnění pro sdílenou složku souboru, samotné. Chcete-li to provést, otevřete příkazový řádek pro správu na souborovém serveru a spusťte **ReACL.cmd** souboru. **ReACL.cmd** soubor se nachází v instalační soubory služby App Service v **BCDR** adresáře.

## <a name="restore-app-service-roles-and-services"></a>Obnovení role služby App Service a služby
Po obnovení databáze služby App Service a obsah sdílené složky souboru, musíte dále použití Powershellu k obnovení role služby App Service a služby. Tyto kroky obnoví tajné kódy služby App Service a konfigurace služeb.  

1. Přihlaste se k řadiči služby App Service **CN0-VM** virtuálního počítače jako **roleadmin** pomocí hesla, které jste zadali při instalaci služby App Service. 
    > [!TIP]
    > Bude potřeba změnit skupinu zabezpečení sítě Virtuálního počítače umožňuje připojení RDP. 
2. Kopírovat **SystemSecrets.JSON** soubor místně do virtuálního počítače řadiče. Budete muset zadat cestu k tomuto souboru `$pathToExportedSecretFile` parametr v dalším kroku. 
3. Obnovení role služby App Service a služby pomocí následujících příkazů v okně konzoly Powershellu se zvýšenými oprávněními:

    ```powershell
    # Stop App Service services on the primary controller VM
    net stop WebFarmService
    net stop ResourceMetering
    net stop HostingVssService # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # Restore App Service secrets. Provide the path to the App Service secrets file copied from backup. For example, C:\temp\SystemSecrets.json.
    # Press ENTER when prompted to reconfigure App Service from backup 

    # If necessary, use -OverrideDatabaseServer <restored server> with Restore-AppServiceStamp when the restored database server has a different address than backed-up deployment.
    # If necessary, use -OverrideContentShare <restored file share path> with Restore-AppServiceStamp when the restored file share has a different path from backed-up deployment.
    Restore-AppServiceStamp -FilePath $pathToExportedSecretFile 

    # Restore App Service roles
    Restore-AppServiceRoles

    # Restart App Service services
    net start WebFarmService
    net start ResourceMetering
    net start HostingVssService  # This service was deprecated in the App Service 1.5 release and is not required after the App Service 1.4 release.

    # After App Service has successfully restarted, and at least one management server is in ready state, synchronize App Service objects to complete the restore
    # Enter Y when prompted to get all sites and again for all ServerFarm entities.
    Get-AppServiceSite | Sync-AppServiceObject
    Get-AppServiceServerFarm | Sync-AppServiceObject
    ```

> [!TIP]
> Důrazně doporučujeme tuto relaci Powershellu po dokončení příkazu zavřete.

## <a name="restore-function-apps"></a>Obnovení aplikace Function App 
App Service pro službu Azure Stack nepodporuje obnovení tenanta uživatele aplikace a data než obsah sdílené složky souboru. Proto tyto musí být zálohována a obnovena mimo službu App Service operací zálohování a obnovení. Úložiště služby Azure Stack se použil pro úložiště aplikací Function App, následující postup použitou k obnovení ztracených dat:

1. Vytvořte nový účet úložiště používané aplikace Function App. Toto úložiště může být úložiště, Azure storage nebo žádné kompatibilní úložiště Azure Stack.
2. Načtení připojovacího řetězce pro úložiště.
3. Otevřete portál funkci, přejděte do aplikace function app.
4. Přejděte **funkce platformy** kartě a klikněte na tlačítko **nastavení aplikace**.
5. Změna **AzureWebJobsDashboard** a **AzureWebJobsStorage** nový připojovací řetězec a klikněte na tlačítko **Uložit**.
6. Přepnout na **přehled**.
7. Restartujte aplikaci. Může trvat několik pokusí vymazat všechny chyby.

## <a name="next-steps"></a>Další postup
[Přehled App Service v Azure Stacku](azure-stack-app-service-overview.md)