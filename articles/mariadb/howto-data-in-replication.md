---
title: Konfigurace replikace dat – databáze Azure pro MariaDB
description: Tento článek popisuje, jak nastavit replikaci dat v databázi Azure pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 51b800dde140affd222f2bdb341c0fbf3a57d8cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530151"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurace replikace dat v databázi Azure pro MariaDB

Tento článek popisuje, jak nastavit replikaci dat v databázi Azure pro MariaDB konfigurací hlavních a replikových serverů. Tento článek předpokládá, že máte nějaké předchozí zkušenosti se servery MariaDB a databázemi.

Chcete-li vytvořit repliku ve službě Azure Database for MariaDB, data-in replikace synchronizuje data z hlavního serveru MariaDB místně, ve virtuálních počítačích (VM) nebo v cloudových databázových službách.

> [!NOTE]
> Pokud je hlavní server verze 10.2 nebo novější, doporučujeme nastavit replikaci dat pomocí [globálního ID transakce](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Vytvoření serveru MariaDB, který se použije jako replika

1. Vytvořte novou databázi Azure pro server MariaDB (například replica.mariadb.database.azure.com). Server je replika serveru v datové replikaci.

    Další informace o vytváření serveru najdete [v tématu Vytvoření databáze Azure pro server MariaDB pomocí portálu Azure](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Je nutné vytvořit databázi Azure pro mariadb server v obecné účely nebo optimalizace paměti cenové úrovně.

2. Vytvořte identické uživatelské účty a odpovídající oprávnění.
    
    Uživatelské účty nejsou replikovány z hlavního serveru na replikovací server. Chcete-li poskytnout uživatelský přístup k serveru replik, musíte ručně vytvořit všechny účty a odpovídající oprávnění na nově vytvořené databázi Azure pro server MariaDB.

## <a name="configure-the-master-server"></a>Konfigurace hlavního serveru

Následující kroky připravit a nakonfigurovat MariaDB server hostovaný místně, ve virtuálním počítači nebo v cloudové databázové službě pro replikaci dat. Server MariaDB je hlavním serverem replikace datového připojení.

1. Zapněte binární protokolování.
    
    Chcete-li zjistit, zda je v předloze povoleno binární protokolování, zadejte následující příkaz:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud proměnná [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) vrátí `ON`hodnotu , binární protokolování je povoleno na serveru.

   Pokud `log_bin` vrátí `OFF`hodnotu , upravte soubor `log_bin=ON` **my.cnf** tak, aby se zapínal binární protokolování. Chcete-li změnu změnit, restartujte server.

2. Konfigurace nastavení hlavního serveru.

    Replikace datového `lower_case_table_names` data vyžaduje, aby byl parametr konzistentní mezi hlavním a replikami serverů. Parametr `lower_case_table_names` je ve `1` výchozím nastavení nastavenna v Azure Database pro MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Vytvořte novou roli replikace a nastavte oprávnění.

   Vytvořte uživatelský účet na hlavním serveru, který je nakonfigurován s oprávněními replikace. Účet můžete vytvořit pomocí příkazů SQL nebo MySQL Workbench. Pokud plánujete replikovat pomocí ssl, musíte zadat při vytváření uživatelského účtu.
   
   Informace o přidání uživatelských účtů na hlavní server naleznete v [dokumentaci k aplikaci MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Pomocí následujících příkazů může nová role replikace přistupovat k hlavnímu serveru z libovolného počítače, nikoli pouze z počítače, který je hostitelem samotného hlavního serveru. Pro tento přístup zadejte **synchronizačního\@uživatele %'** v příkazu pro vytvoření uživatele.
   
   Další informace o dokumentaci KAMariaDB naleznete [v tématu určení názvů účtů](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Příkaz SQL**

   - Replikace pomocí ssl

       Chcete-li vyžadovat ssl pro všechna uživatelská připojení, zadejte následující příkaz pro vytvoření uživatele:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikace bez ssl

       Pokud není pro všechna připojení vyžadováno ssl, zadejte následující příkaz pro vytvoření uživatele:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Chcete-li vytvořit roli replikace v pracovní plochy MySQL, vyberte v podokně **Správa** **položku Uživatelé a oprávnění**. Pak vyberte **Přidat účet**.
 
   ![Uživatelé a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Do pole Přihlašovací **jméno** zadejte uživatelské jméno.

   ![Synchronizovat uživatele](./media/howto-data-in-replication/syncuser.png)
 
   Vyberte panel **Role správy** a v seznamu globálních oprávnění vyberte **Položku** **Slave replikace**. Vyberte **Použít,** chcete-li vytvořit roli replikace.

   ![Slave replikace](./media/howto-data-in-replication/replicationslave.png)


4. Nastavte hlavní server do režimu jen pro čtení.

   Před výpisem databáze musí být server umístěn v režimu jen pro čtení. V režimu jen pro čtení hlavní server nemůže zpracovat žádné transakce zápisu. Chcete-li se vyhnout dopadu na podnikání, naplánujte okno jen pro čtení v době mimo špičku.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Získejte aktuální název a posun souboru binárního protokolu.

   Chcete-li určit aktuální název a posun [`show master status`](https://mariadb.com/kb/en/library/show-master-status/)binárního souboru protokolu, spusťte příkaz .
    
   ```sql
   show master status;
   ```
   Výsledky by měly být podobné následující tabulce:
   
   ![Výsledky hlavního stavu](./media/howto-data-in-replication/masterstatus.png)

   Poznamenejte si název binárního souboru, protože bude použit v pozdějších krocích.
   
6. Získejte pozici GTID (volitelně, potřebné pro replikaci s GTID).

   Spuštěním [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) funkce získáte pozici GTID pro odpovídající název souboru binlogu a posun.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Výpis a obnovení hlavního serveru

1. Vypáčit všechny databáze z hlavního serveru.

   Pomocí mysqldump vyložit všechny databáze z hlavního serveru. Není nutné vypustit knihovnu MySQL a testovací knihovnu.

    Další informace naleznete v [tématu Dump and restore](howto-migrate-dump-restore.md).

2. Nastavte hlavní server na režim čtení a zápisu.

   Po vypsání databáze změňte hlavní server MariaDB zpět do režimu čtení a zápisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovte soubor s výpisem stavu paměti na nový server.

   Obnovte soubor výpisu stavu na server vytvořený ve službě Azure Database for MariaDB. Informace o obnovení souboru s výpisem stavu paměti na serveru MariaDB naleznete v tématu [Dump & Restore.](howto-migrate-dump-restore.md)

   Pokud je soubor s výpisem stavu paměti velký, nahrajte ho do virtuálního počítače v Azure ve stejné oblasti jako váš replikovací server. Obnovte ho do databáze Azure pro server MariaDB z virtuálního počítače.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Propojení hlavních a replikových serverů se spuštěním replikace datového připojení

1. Nastavte hlavní server.

   Všechny funkce replikace dat se provádějí pomocí uložených procedur. Všechny procedury najdete na [adrese Uložené procedury replikace dat](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench.

   Chcete-li propojit dva servery a zahájit replikaci, přihlaste se k cílovému serveru replik ve službě Azure DB for MariaDB. Dále nastavte externí instanci jako hlavní `mysql.az_replication_change_master` `mysql.az_replication_change_master_with_gtid` server pomocí nebo uložené procedury na serveru Azure DB pro MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   – nebo –
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: název hostitele hlavního serveru
   - master_user: uživatelské jméno hlavního serveru
   - master_password: heslo pro hlavní server
   - master_log_file: název binárního souboru protokolu ze spuštění`show master status`
   - master_log_pos: binární pozice protokolu ze spuštění`show master status`
   - master_gtid_pos: Pozice GTID z běhu`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Kontext certifikátu certifikační autority. Pokud nepoužíváte ssl, předajte prázdný řetězec.*
    
    
    *Doporučujeme předat parametr master_ssl_ca jako proměnnou. Další informace naleznete v následujících příkladech.

   **Příklady**

   - Replikace pomocí ssl

       Vytvořte `@cert` proměnnou spuštěním následujících příkazů:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikace pomocí ssl je nastavena mezi hlavním serverem hostovaným v companya.com domény a replikovým serverem hostovaným v Databázi Azure pro MariaDB. Tato uložená procedura je spuštěna v replice.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikace bez ssl

       Replikace bez ssl je nastavena mezi hlavním serverem hostovaným v companya.com domény a replikovým serverem hostovaným v Databázi Azure pro MariaDB. Tato uložená procedura je spuštěna v replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Spusťte replikaci.

   Volání `mysql.az_replication_start` uložené procedury zahájit replikaci.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Zkontrolujte stav replikace.

   Volání [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) příkazu na serveru repliky zobrazíte stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud `Slave_IO_Running` `Slave_SQL_Running` a jsou `yes`ve stavu a `Seconds_Behind_Master` `0`hodnota je , replikace funguje. `Seconds_Behind_Master`označuje, jak pozdě je replika. Pokud hodnota není `0`, replika zpracovává aktualizace.

4. Aktualizujte odpovídající proměnné serveru, aby byla replikace datového připojení bezpečnější (vyžadována pouze pro replikaci bez GTID).
    
    Z důvodu nativní omezení replikace [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) v [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) MariaDB, musíte nastavit a proměnné na replikaci bez scénáře GTID.

    Zkontrolujte podřízené `sync_master_info` servery a `sync_relay_log_info` proměnné, abyste se ujistili, že replikace dat je stabilní, a nastavte proměnné na `1`.
    
## <a name="other-stored-procedures"></a>Další uložené procedury

### <a name="stop-replication"></a>Zastavení replikace

Chcete-li zastavit replikaci mezi hlavním serverem a replikami, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Odebrání relace replikace

Chcete-li odebrat vztah mezi hlavním serverem a replikami, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Přeskočit chybu replikace

Chcete-li přeskočit chybu replikace a povolit replikaci, použijte následující uloženou proceduru:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další kroky
Další informace o [replikaci dat](concepts-data-in-replication.md) pro azure databázi pro MariaDB.
