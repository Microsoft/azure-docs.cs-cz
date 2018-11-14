---
title: Aktualizace 4 zpráva k vydání verze služby App Service ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o tom, co je v aktualizaci 4 pro službu App Service ve službě Azure Stack, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 11/13/2018
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 80948b973e6d20b4760e97311c5a65886cf91f8f
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617008"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>App Service v Azure stacku zpráva k vydání verze update 4

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a oprav ve službě Azure App Service v Azure stacku Update 4 a známých problémech. Známé problémy jsou rozděleny do týkající se přímo k nasazení, aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Aktualizace 1809 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.4.
>
>

## <a name="build-reference"></a>Referenční informace o buildu

App Service v Azure stacku Update 4 číslo sestavení je **78.0.13698.5**

### <a name="prerequisites"></a>Požadavky

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

Před zahájením upgradu služby Azure App Service ve službě Azure Stack 1.4:

- Zkontrolujte všechny role jsou připravené ve správě Azure App Service v portálu pro správu Azure Stack

- Zálohování služby App Service a hlavní databáze:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Zálohování sdílené složky souboru obsahu klientskou aplikaci

- Zajistěte syndikaci rozšíření vlastních skriptů verze 1.9 z Marketplace

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure stacku Update 4 zahrnuje následující vylepšení a opravy:

- Řešení pro [CVE 2018 8600](https://aka.ms/CVE20188600) různé lokality skriptování ohrožení zabezpečení.

- Přidání podpory pro verzi rozhraní API služby App Service 2018-02-01

- Aktualizace **aplikace služeb pro klienty, Admin, portály funkce a nástroje Kudu**. Konzistentní s verzí sady SDK portálu Azure Stack.

- Aktualizace základní službu, ke zlepšení spolehlivosti a chybových zpráv umožňuje snazší Diagnostika běžných problémů.

- **Aktualizace následujících aplikační architektury a nástroje**:
  - Přidání NodeJS 10.6.0
  - Přidání NPM 6.1.0
  - Přidání Zulu OpenJDK 8.31.0.2
  - Přidání Tomcat 8.5.34 a 9.0.12
  - Přidání PHP verze:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Aktualizovat na verze Pythonu:
    - 2.7.15
    - 3.6.6
  - Aktualizované Git pro Windows do v 2.17.1.2
  - Aktualizované Kudu pro 78.11022.3613
  
- **Aktualizace základního operačního systému všech rolí**:
  - [2018 10 kumulativní aktualizace pro Windows Server 2016 pro systémy x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Při nasazování Wordpress; vyřešen problém s ověřováním šablony DNN; a položky galerie Orchard CMS

- Vyřešit problém s konfigurací, když Azure Stack otočí klientský certifikát Azure Resource Manageru

- Obnovit funkci v nastavení mezi sdílení zdrojů Origin na portálu pro tenanta služby App Service

- Zobrazí chybovou zprávu v prostředí pro správu aplikace App Service při rovina řízení poskytovatele prostředků se nemůže připojit k nakonfigurovanou instanci systému SQL Server

- Ujistěte se, že koncový bod je zadán v připojovacím řetězci vlastního úložiště-li zadána v nové aplikaci – funkce

### <a name="post-update-steps-optional"></a>Postup aktualizace Post (volitelné)

Pro zákazníky, kteří chtějí migrovat do databáze s omezením pro stávající služby Azure App Service v nasazení Azure Stack proveďte tyto kroky po dokončení Azure App Service ve službě Azure Stack 1.4 update:

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
 * Zdroj: žádné
 * Zdrojový rozsah portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protocol: TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure App Service ve službě Azure Stack

Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack 1809](azure-stack-update-1809.md)

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na Přehled služby Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service ve službě Azure Stack najdete v tématu [před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md).
