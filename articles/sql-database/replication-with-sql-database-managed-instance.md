---
title: Replikace pomocí služby Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Další informace o použití replikace systému SQL Server s Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 09/25/2018
ms.openlocfilehash: 25d13ba53eb5a8b411a557b5eaf05d278faa3733
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48869308"
---
# <a name="replication-with-sql-database-managed-instance"></a>Replikace SQL Database Managed Instance

Replikace je dostupná ve veřejné verzi preview na [Azure SQL Database Managed Instance](sql-database-managed-instance.md). Managed Instance může hostovat databáze vydavatele, distributor a odběratele.

## <a name="common-configurations"></a>Obvyklé konfigurace

Obecně platí vydavatelem a distributorem musí být buď v cloudu nebo místně. Podporují se následující konfigurace:

- **Vydavatele s místní distributor na spravovanou instanci**

   ![Replication-with-Azure-SQL-DB-Single-Managed-instance-Publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Vydavateli a distributorovi databáze se konfiguruje v jedné spravované instance.

- **Vydavatele s vzdáleného distributora na spravovanou instanci**

   ![Replication-with-Azure-SQL-DB-separate-Managed-instances-Publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   Vydavateli a distributorovi konfigurují na dvou spravované instance. V této konfiguraci:

  - Obě spravované instance jsou ve stejné virtuální síti.

  - Obě spravované instance jsou ve stejném umístění.

- **Vydavateli a distributorovi místně pomocí odběratele na spravovanou instanci**

   ![Replication-from-On-Premises-to-Azure-SQL-DB-Subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   V této konfiguraci služby Azure SQL database je odběratel. Tato konfigurace podporuje migraci z místního do Azure. V roli odběratele SQL database nevyžaduje Managed Instance, ale můžete jako krok při migraci z místního Azure SQL Database Managed Instance. Další informace o Azure SQL Database předplatitele najdete v tématu [replikace do služby SQL Database](replication-to-sql-database.md).

## <a name="requirements"></a>Požadavky

Vyžaduje vydavatelem a distributorem pro službu Azure SQL Database:

- Spravovaná Instance Azure SQL Database.

   >[!NOTE]
   >Azure SQL Database, které nejsou nakonfigurované pro Managed Instance je možné pouze Odběratelé.

- Všechny instance systému SQL Server musí být ve stejné virtuální síti.

- Připojení pomocí ověřování SQL mezi účastníky replikace.

- Sdílenou účtu úložiště Azure pro replikaci pracovní adresář.

## <a name="features"></a>Funkce

Podporuje:

- Transakční replikace a replikace snímků směs místních a instance Azure SQL Database Managed Instance.

- Předplatitelé může být místní, izolované databáze ve službě Azure SQL Database nebo databáze ve fondu v elastických fondů Azure SQL Database.

- Jednosměrné nebo obousměrné replikace

## <a name="configure-publishing-and-distribution-example"></a>Konfigurace publikování a distribuci příklad

1. [Vytvoření Azure SQL Database Managed Instance](sql-database-managed-instance-create-tutorial-portal.md) na portálu.
2. [Vytvoření účtu služby Azure Storage](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) pro pracovní adresář.

   Nezapomeňte si zkopírovat klíče úložiště. Zobrazit [zobrazení a zkopírování přístupových klíčů úložiště](../storage/common/storage-account-manage.md#access-keys
).
3. Vytvoření databáze vydavatele.

   V níže uvedené příklady skriptů, nahraďte `<Publishing_DB>` s názvem této databáze.

4. Vytvořte uživatele databáze s ověřováním SQL pro daného distributora. Zobrazit, [vytváření uživatelů databáze](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Použijte zabezpečené heslo.

   V níže uvedené příklady skriptů pomocí `<SQL_USER>` a `<PASSWORD>` s tímto účtem serveru SQL databáze, uživatele a heslo.

5. [Připojení ke spravované instanci SQL Database](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

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

## <a name="limitations"></a>Omezení

Nejsou podporovány následující funkce:

- Aktualizovatelné odběry

- Aktivní geografická replikace

## <a name="see-also"></a>Viz také

- [Co je Managed Instance?](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)
