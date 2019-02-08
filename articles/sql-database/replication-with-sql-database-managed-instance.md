---
title: Konfigurace replikace databáze spravované instance Azure SQL Database | Dokumentace Microsoftu
description: Další informace o konfiguraci transakční replikace databáze spravované instance Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 038d8c919e68e68f886525a6c78139496edef8e1
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55893007"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Konfigurace replikace databáze spravované instance Azure SQL Database

Transakční replikace umožňuje replikovat data do databáze spravované instance Azure SQL Database z databáze systému SQL Server nebo jiné instanci databáze. Transakční replikace můžete použít také tak, aby nabízel změny provedené v databázi instance ve spravované instanci Azure SQL Database k databázi SQL serveru do izolované databáze ve službě Azure SQL Database pro databázi ve fondu v elastickém fondu Azure SQL Database. Transakční replikace je ve verzi public preview na [Azure SQL Database managed instance](sql-database-managed-instance.md). Spravovaná instance může hostovat databáze vydavatele, distributor a odběratele. Zobrazit [transakční replikace konfigurace](sql-database-managed-instance-transactional-replication.md#common-configurations) pro dostupné konfigurace.

## <a name="requirements"></a>Požadavky

Konfigurace managed instance fungovat jako vydavatel nebo distributora vyžaduje:

- Spravované instance se teď neúčastní relace geografickou replikaci.

   >[!NOTE]
   >Předplatitelé mohou být pouze izolované databáze a databáze ve fondu ve službě Azure SQL Database.

- Všechny spravované instance musí být ve stejné virtuální síti.

- Připojení pomocí ověřování SQL mezi účastníky replikace.

- Sdílenou účtu úložiště Azure pro replikaci pracovní adresář.

- Port 445 (odchozí TCP) musí být otevřené v pravidlech zabezpečení v podsíti spravované instance pro přístup ke sdílené složce Azure

## <a name="features"></a>Funkce

Podporuje:

- Transakční replikace a replikace snímků kombinaci místní SQL Server a spravované instance Azure SQL Database.
- Předplatitelé může být v místních databází SQL serveru, izolované databáze ve službě Azure SQL Database nebo databáze ve fondu v elastických fondů Azure SQL Database.
- Jednosměrné nebo obousměrné replikace.

Ve službě Azure SQL Database managed instance nepodporuje následující funkce:

- Aktualizovatelné odběry.
- Aktivní geografickou replikaci.

## <a name="configure-publishing-and-distribution-example"></a>Konfigurace publikování a distribuci příklad

1. [Vytvoření spravované instance Azure SQL Database](sql-database-managed-instance-create-tutorial-portal.md) na portálu.
2. [Vytvoření účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář.

   Nezapomeňte si zkopírovat klíče úložiště. Zobrazit [zobrazení a zkopírování přístupových klíčů úložiště](../storage/common/storage-account-manage.md#access-keys
).
3. Vytvoření instance databáze vydavatele.

   V níže uvedené příklady skriptů, nahraďte `<Publishing_DB>` s názvem instance databáze.

4. Vytvořte uživatele databáze s ověřováním SQL pro daného distributora. Použijte zabezpečené heslo.

   V níže uvedené příklady skriptů pomocí `<SQL_USER>` a `<PASSWORD>` s tímto účtem serveru SQL databáze, uživatele a heslo.

5. [Připojení ke spravované instanci SQL Database](sql-database-connect-query-ssms.md).

6. Spusťte následující dotaz pro přidání distributora a distribuční databázi.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Konfigurace vydavatele použít zadaný distribuční databázi, aktualizovat a spusťte následující dotaz.

   Nahraďte `<SQL_USER>` a `<PASSWORD>` účet serveru SQL a heslo.

   Nahraďte `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` s použitím hodnoty z vašeho účtu úložiště.  

   Nahraďte `<STORAGE_CONNECTION_STRING>` připojovacím řetězcem z **přístupové klíče** kartu z účtu úložiště Microsoft Azure.

   Po aktualizaci následující dotaz, spusťte ji.

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

8. Konfigurace vydavatele pro replikaci.

    V následujícím dotazem, nahraďte `<Publishing_DB>` s názvem vaší databáze vydavatele.

    Nahraďte `<Publication_Name>` s názvem pro publikaci.

    Nahraďte `<SQL_USER>` a `<PASSWORD>` účet serveru SQL a heslo.

    Po aktualizaci dotazu jeho spuštěním vytvořte publikaci.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

9. Přidáte článek, předplatné a agenta nabízeného odběru.

   Pokud chcete přidat tyto objekty, aktualizujte následující skript.

   - Nahraďte `<Object_Name>` s názvem objektu publikace.
   - Nahraďte `<Object_Schema>` s názvem schématu zdroje.
   - Nahraďte parametry v lomených závorkách `<>` tak, aby odpovídaly hodnoty v předchozí skripty.

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   GO
   ```
   
## <a name="see-also"></a>Viz také

- [Transakční replikace](sql-database-managed-instance-transactional-replication.md)
- [Co je Managed Instance?](sql-database-managed-instance.md)
