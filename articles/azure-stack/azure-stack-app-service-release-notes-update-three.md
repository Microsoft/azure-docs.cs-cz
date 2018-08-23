---
title: Aktualizace 3 zpráva k vydání verze služby App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o tom, co je v aktualizaci 3 pro službu App Service ve službě Azure Stack, známé problémy a kde se stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/20/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 88a4bcf018387ac83b485ec9e2efac11f85ba97c
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2018
ms.locfileid: "42432285"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>App Service v Azure stacku zpráva k vydání verze update 3

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a oprav ve službě Azure App Service v Azure stacku Update 3 a známých problémech. Známé problémy jsou rozděleny do týkající se přímo k nasazení, aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Aktualizace 1807 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.3.
>
>

## <a name="build-reference"></a>Referenční informace o buildu

App Service na číslo sestavení aktualizace 3 pro Azure Stack je **74.0.13698.31**

### <a name="prerequisites"></a>Požadavky

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

Než zahájíte upgrade služby Azure App Service ve službě Azure Stack na 1.3, zkontrolujte připravené jsou všechny role ve správě Azure App Service v portálu pro správu Azure Stack

![Stav role služby App Service](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure stacku Update 3 zahrnuje následující vylepšení a opravy:

- Podpora použití SQL serveru Always On pro poskytovatele prostředků služby Azure App Service databází.

- Přidá nový parametr prostředí do skriptu pomocné rutiny vytvořit AADIdentityApp pomáhat cílení různých oblastech AAD.

- Aktualizace **aplikace služeb pro klienty, Admin, portály funkce a nástroje Kudu**. Konzistentní s verzí sady SDK portálu Azure Stack.

- Aktualizace základní službu, ke zlepšení spolehlivosti a chybových zpráv umožňuje snazší Diagnostika běžných problémů.

- **Aktualizace následujících aplikační architektury a nástroje**:
  - Přidání ASP.Net Core 2.1.2
  - Přidání NodeJS 10.0.0
  - Přidání Zulu OpenJDK 8.30.0.1
  - Přidání Tomcat 8.5.31 a 9.0.8
  - Přidání PHP verze:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Přidání Wincache 2.0.0.8
  - Aktualizované Git pro Windows do v 2.17.1.2
  - Aktualizované Kudu pro 74.10611.3437
  
- **Aktualizace základního operačního systému všech rolí**:
  - [Servisní aktualizace zásobníku pro Windows Server 2016 pro systémy x64 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [2018-07 kumulativní aktualizace pro Windows Server 2016 pro systémy x64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Aktualizace kroky po (volitelné)

Pro zákazníky, kteří chtějí migrovat do databáze s omezením pro stávající služby Azure App Service v nasazení Azure Stack proveďte tyto kroky po dokončení Azure App Service ve službě Azure Stack 1.3 update:

> [!IMPORTANT]
> Tento postup trvá přibližně 5 až 10 minut.  Tento postup zahrnuje ukončuje existující relace přihlášení databáze.  Plánování výpadku, migrace a ověření služby Azure App Service v Azure Stack po migraci
>
>

1. Přidat [databáze služby App Service (appservice_hosting a appservice_metering) do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)

1. Povolit obsažené databázi
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Probíhá převod databáze částečně obsaženy.  Tento krok bude mít za následek výpadek jako musí být ukončeny všechny aktivní relace

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate Logins to Contained Database Users

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

Ověření

1. Zkontrolujte, jestli SQL Server má povolené členství ve skupině

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Zkontrolujte stávající omezením chování
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure App Service ve službě Azure Stack

Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack 1807](azure-stack-update-1807.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na Přehled služby Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service ve službě Azure Stack najdete v tématu [před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md).
