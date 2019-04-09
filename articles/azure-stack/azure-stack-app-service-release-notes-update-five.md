---
title: Aktualizace 5 zpráva k vydání verze služby App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o tom, co je v update 5 pro službu App Service ve službě Azure Stack, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 192ac256f013498e57ecf7939d29796af073b948
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59260557"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>App Service v Azure stacku zpráva k vydání verze update 5

*Týká se Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a oprav ve službě Azure App Service v Azure stacku Update 5 a známých problémech. Známé problémy jsou rozděleny do týkající se přímo k nasazení, aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Aktualizace 1901 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.5.


## <a name="build-reference"></a>Referenční informace o buildu

App Service v Azure stacku Update 5 číslo sestavení je **80.0.2.15**

### <a name="prerequisites"></a>Požadavky

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

Před zahájením upgradu služby Azure App Service ve službě Azure Stack 1.5:

- Zkontrolujte všechny role jsou připravené ve správě Azure App Service v portálu pro správu Azure Stack

- Zálohování služby App Service a hlavní databáze:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Zálohování sdílené složky souboru obsahu klientskou aplikaci

- Publikování **rozšíření vlastních skriptů** verze **1.9.1** z Marketplace

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure stacku aktualizaci 5 zahrnuje následující vylepšení a opravy:

- Aktualizace **aplikace služeb pro klienty, Admin, portály funkce a nástroje Kudu**. Konzistentní s verzí sady SDK portálu Azure Stack.

- Aktualizace **modul runtime služby Azure Functions** k **v1.0.12205**.

- Aktualizace **Kudu nástroje** kvůli řešení problémů s stylů a funkce pro zákazníky, kteří provozní **odpojení** Azure Stack. 

- Aktualizace základní službu, ke zlepšení spolehlivosti a chybových zpráv umožňuje snazší Diagnostika běžných problémů.

- **Aktualizace následujících aplikační architektury a nástroje**:
  - Přidání ASP.NET Core 2.1.6 a 2.2.0
  - Přidání NodeJS 10.14.1
  - Přidání NPM 6.4.1
  - Aktualizované Kudu pro 79.20129.3767
  
- **Aktualizace základního operačního systému všech rolí**:
  - [2019-02 kumulativní aktualizace pro Windows Server 2016 pro systémy x64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]  
> Pokud budete mít k dispozici poskytovatele prostředků App Service vždy na instanci SQL musíte [přidat do skupiny dostupnosti databáze appservice_hosting a appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizaci databází, aby nedocházelo ke Služba v případě selhání databáze.

### <a name="post-update-steps"></a>Postup po aktualizaci

Pro zákazníky, kteří chtějí migrovat do databáze s omezením pro stávající služby Azure App Service v nasazení Azure Stack proveďte tyto kroky po dokončení Azure App Service ve službě Azure Stack 1.5 update:

> [!IMPORTANT]
> Postup migrace trvá přibližně 5 až 10 minut.  Postup zahrnuje ukončuje existující relace přihlášení databáze.  Plánování výpadku, migrace a ověření služby Azure App Service v Azure Stack po migraci.  Pokud jste dokončili postup po aktualizaci na službě Azure App Service v Azure stacku 1.3, pak tyto kroky nejsou nutné.
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

1. Částečně obsaženy převodu databáze na databázi, převod bude mít za následek výpadek jako musí být ukončeny všechny aktivní relace

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

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pracovní procesy se nám kontaktovat souborového serveru při nasazení služby App Service v existující virtuální sítě a souborový server je dostupná pouze na privátní sítě, jak je uvedeno ve službě Azure App Service v dokumentaci k nasazení Azure Stack.

Pokud jste se rozhodli nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, je nutné přidat odchozí pravidlo zabezpečení, povolení provozu SMB mezi podsítě pracovního procesu a souborový server. Přejděte na WorkersNsg v portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:
 * Zdroj: Všechny
 * Zdrojový rozsah portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: Rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure App Service ve službě Azure Stack

Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na Přehled služby Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service ve službě Azure Stack najdete v tématu [před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md).
