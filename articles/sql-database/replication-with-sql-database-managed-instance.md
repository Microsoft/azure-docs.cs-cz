---
title: Konfigurace replikace v Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Další informace o konfiguraci replikace transakcí do Azure SQL Database Managed Instance
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
ms.date: 01/16/2019
ms.openlocfilehash: 568b239cf41c802cc5d25b638f6d1501f58eccdf
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360084"
---
# <a name="configure-replication-in-azure-sql-database-managed-instance"></a>Konfigurace replikace v Azure SQL Database Managed Instance

Transakční replikace umožňuje replikovat data z databáze systému SQL Server nebo Azure SQL Database Managed Instance, do spravované Instance nebo push změny provedené ve vašich databázích ve spravované instanci jiné systému SQL Server, izolovaná databáze Azure nebo jiné Managed Instance. Replikace je ve verzi public preview na [Azure SQL Database Managed Instance](sql-database-managed-instance.md). Managed Instance může hostovat databáze vydavatele, distributor a odběratele. Zobrazit [transakční replikace konfigurace](sql-database-managed-instance-transactional-replication.md#common-configurations) pro dostupné konfigurace.

## <a name="requirements"></a>Požadavky

Vyžaduje vydavatelem a distributorem pro službu Azure SQL Database:

- Azure SQL Database Managed Instance, který není v konfiguraci Geo-DR.

   >[!NOTE]
   >Azure SQL Database, které nejsou nakonfigurované pro Managed Instance je možné pouze Odběratelé.

- Všechny instance systému SQL Server musí být ve stejné virtuální síti.

- Připojení pomocí ověřování SQL mezi účastníky replikace.

- Sdílenou účtu úložiště Azure pro replikaci pracovní adresář.

- Port 445 (odchozí TCP) musí být otevřené v pravidlech zabezpečení podsíť Managed Instance pro přístup ke sdílené složce Azure

## <a name="features"></a>Funkce

Podporuje:

- Transakční replikace a replikace snímků směs místních a instance Azure SQL Database Managed Instance.

- Předplatitelé může být místní, izolované databáze ve službě Azure SQL Database nebo databáze ve fondu v elastických fondů Azure SQL Database.

- Jednosměrné nebo obousměrné replikace.

Nejsou podporovány následující funkce:

- Aktualizovatelné odběry.

- Aktivní geografickou replikaci.

## <a name="configure-publishing-and-distribution-example"></a>Konfigurace publikování a distribuci příklad

1. [Vytvoření Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md) na portálu.
2. [Vytvoření účtu služby Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář.

   Nezapomeňte si zkopírovat klíče úložiště. Zobrazit [zobrazení a zkopírování přístupových klíčů úložiště](../storage/common/storage-account-manage.md#access-keys
).
3. Vytvoření databáze vydavatele.

   V níže uvedené příklady skriptů, nahraďte `<Publishing_DB>` s názvem této databáze.

4. Vytvořte uživatele databáze s ověřováním SQL pro daného distributora. Použijte zabezpečené heslo.

   V níže uvedené příklady skriptů pomocí `<SQL_USER>` a `<PASSWORD>` s tímto účtem serveru SQL databáze, uživatele a heslo.

5. [Připojení ke spravované instanci SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

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
- [Co je Managed Instance?](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
