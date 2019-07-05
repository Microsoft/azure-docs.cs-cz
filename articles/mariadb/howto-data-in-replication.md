---
title: Konfigurace replikace dat ve službě Azure Database pro MariaDB | Dokumentace Microsoftu
description: Tento článek popisuje, jak nastavit Data replikace ve službě Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 21e8a88cc6f03b4d54a6c5299b0b6be36cc32d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444795"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurace replikace dat ve službě Azure Database pro MariaDB

Tento článek popisuje, jak nastavit Data replikace ve službě Azure Database pro MariaDB díky konfiguraci serverů master a repliky. Tento článek předpokládá, že máte zkušenosti s MariaDB serverům a databázím.

Replikace dat ve službě Azure Database pro MariaDB službu vytvoření repliky, synchronizuje data z hlavní MariaDB místní server, na virtuálních počítačích (VM), nebo v cloudové databázové služby.

> [!NOTE]
> Pokud je hlavní server verze 10.2 nebo novější, doporučujeme nastavit replikace dat pomocí [globální ID transakce](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Vytvoření serveru použít jako replika MariaDB

1. Vytvořte novou službu Azure Database pro MariaDB server (například replica.mariadb.database.azure.com). Server je server repliky v dat replikace.

    Další informace o vytvoření serveru, najdete v článku [vytvoření serveru Azure Database for MariaDB pomocí webu Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Azure Database pro MariaDB server musí vytvořit v obecné účely nebo k paměťově optimalizovaným cenové úrovně.

2. Vytvoření stejné uživatelské účty a odpovídající oprávnění.
    
    Uživatelské účty se nereplikují z hlavního serveru serverem repliky. Pokud chcete poskytnout přístup uživatelů k serveru repliky, musíte ručně vytvořit všechny účty a odpovídající oprávnění na nově vytvořený Azure Database pro MariaDB server.

## <a name="configure-the-master-server"></a>Konfigurace hlavního serveru

Následující kroky připravte a nakonfigurujte MariaDB server hostovaný místně na virtuálním počítači, nebo Cloudová databázová služba pro Data replikace. MariaDB server je hlavním v dat replikace.

1. Zapnutí v binární protokolování.
    
    Pokud chcete zobrazit, pokud je binární protokolování povoleno na hlavním serveru, zadejte následující příkaz:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud proměnná [ `log_bin` ](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) vrátí hodnotu `ON`, je na serveru zapnuté binární protokolování.

   Pokud `log_bin` vrátí hodnotu `OFF`, upravit **my.cnf** souboru tak, aby `log_bin=ON` zapne binární protokolování. Restartujte server, aby se změna projevila.

2. Konfigurace nastavení hlavního serveru.

    Replikace dat vyžaduje, aby parametr `lower_case_table_names` bude konzistentní napříč servery master a repliky. `lower_case_table_names` Parametr je nastaven na `1` ve výchozím nastavení ve službě Azure Database pro MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

3. Vytvoření nové role replikace a nastavit oprávnění.

   Vytvoření uživatelského účtu na hlavním serveru, který je nakonfigurovaný s oprávněními pro replikaci. Účet můžete vytvořit pomocí příkazů SQL nebo MySQL Workbench. Pokud máte v plánu replikovat pomocí protokolu SSL, je nutné zadat toto při vytváření uživatelského účtu.
   
   Zjistěte, jak přidat uživatelské účty na hlavní server, najdete v článku [MariaDB dokumentaci](https://mariadb.com/kb/en/library/create-user/).

   Pomocí následujících příkazů nové role replikace můžete přístup k hlavnímu serveru z libovolného počítače, ne jenom počítač, který hostuje hlavní server sám. Tento přístup, zadejte **syncuser\@'%'** v příkazu pro vytvoření uživatele.
   
   Další informace o dokumentaci MariaDB, naleznete v tématu [určující názvy účtů](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Příkaz SQL**

   - Replikace s protokolem SSL

       Pokud chcete vyžadovat protokol SSL pro všechna připojení uživatele, zadejte následující příkaz pro vytvoření uživatele:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikace bez protokolu SSL

       Pokud není vyžadována pro všechna připojení SSL, zadejte následující příkaz pro vytvoření uživatele:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Chcete-li vytvořit role replikace v aplikaci MySQL Workbench v **správu** vyberte **uživatelů a oprávnění**. Potom vyberte **přidat účet**.
 
   ![Uživatele a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Zadejte uživatelské jméno v **přihlašovací jméno** pole.

   ![Synchronizovat uživatele](./media/howto-data-in-replication/syncuser.png)
 
   Vyberte **správních rolí** panelu a pak v seznamu **globální oprávnění**vyberte **podřízený server replikace**. Vyberte **použít** vytvoření role replikace.

   ![Podřízený server replikace](./media/howto-data-in-replication/replicationslave.png)


4. Hlavní server nastavte do režimu jen pro čtení.

   Předtím, než je vypsat databáze, musí být umístěn na serveru v režimu jen pro čtení. V režimu jen pro čtení, hlavní server nemůže zpracovat žádné transakce zápisu. Chcete-li zabránit dopad na chod firmy, naplánování okna jen pro čtení během dobu mimo špičku.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

5. Získejte aktuální název souboru binárního protokolu a posun.

   Pokud chcete zjistit aktuální název souboru binárního protokolu a posun, spusťte příkaz [ `show master status` ](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Výsledky by měl vypadat přibližně v následující tabulce:
   
   ![Hlavní stav výsledky](./media/howto-data-in-replication/masterstatus.png)

   Poznámka: název binárního souboru, protože ho budou používat v dalších krocích.
   
6. Získá pozici GTID (volitelné, potřebné pro replikaci s GTID).

   Spuštění funkce [ `BINLOG_GTID_POS` ](https://mariadb.com/kb/en/library/binlog_gtid_pos/) získat GTID pozici pro odpovídající název souboru binlog a posun.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Výpisu a obnovení hlavního serveru

1. Vypsat všechny databáze z hlavního serveru.

   Pomocí mysqldump pro výpis všech databází z hlavního serveru. Není nutné vypsat knihovny MySQL a testovací knihovnou.

    Další informace najdete v tématu [výpisu a obnovení](howto-migrate-dump-restore.md).

2. Nastavte hlavní server do režimu pro čtení a zápisu.

   Poté, co byly zálohované databáze, změnit hlavní MariaDB server zpátky do režimu pro čtení a zápisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovte soubor s výpisem paměti na nový server.

   Obnovte soubor s výpisem paměti na server, který vytvořili ve službě Azure Database pro MariaDB službu. Zobrazit [výpisu a obnovení](howto-migrate-dump-restore.md) pro obnovení serveru MariaDB soubor s výpisem paměti.

   Pokud je soubor s výpisem paměti velký, nahrajte ho do virtuálního počítače v Azure v rámci stejné oblasti jako server repliky. Obnovení k Azure Database pro MariaDB server z virtuálního počítače.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Propojte servery s těmito master a repliky ke spuštění replikace dat

1. Nastavte hlavní server.

   Všechny funkce replikace dat provádí uložené procedury. Můžete najít všechny postupy v [Data v uložené procedury replikace](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench.

   K propojení dvou serverů a spuštění replikace, přihlaste se k cílovému serveru repliky v Azure DB pro službu MariaDB. Dále nastavte externí instanci jako hlavní server s použitím `mysql.az_replication_change_master` nebo `mysql.az_replication_change_master_with_gtid` uloženou proceduru v Azure DB for MariaDB serveru.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   nebo
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: název hostitele hlavního serveru
   - master_user: uživatelské jméno nadřazeného serveru
   - master_password: heslo nadřazeného serveru
   - master_log_file: název souboru binárního protokolu spuštění `show master status`
   - master_log_pos: pozice binární protokol spuštění `show master status`
   - master_gtid_pos: Pozice GTID spuštění `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: Certifikát certifikační Autority kontextu. Pokud nepoužíváte protokol SSL, předejte prázdné string.*
    
    
    \* Doporučujeme předávání parametru master_ssl_ca jako proměnnou. Další informace najdete v tématu v následujících příkladech.

   **Příklady**

   - Replikace s protokolem SSL

       Vytvořte proměnnou `@cert` spuštěním následujících příkazů:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE’S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikace s protokolem SSL je nastavená mezi hlavní server, který je hostovaný na SpolecnostA.cz domény a server repliky hostované ve službě Azure Database pro MariaDB. Tuto uloženou proceduru se spouští v replice.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikace bez protokolu SSL

       Replikace bez SSL je nastavená mezi hlavní server, který je hostovaný na SpolecnostA.cz domény a server repliky hostované ve službě Azure Database pro MariaDB. Tuto uloženou proceduru se spouští v replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Spuštění replikace.

   Volání `mysql.az_replication_start` uloženou proceduru spustit replikaci.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Zkontrolujte stav replikace.

   Volání [ `show slave status` ](https://mariadb.com/kb/en/library/show-slave-status/) příkaz na serveru repliky, chcete-li zobrazit stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud `Slave_IO_Running` a `Slave_SQL_Running` jsou ve stavu `yes`a hodnota `Seconds_Behind_Master` je `0`, funguje replikace. `Seconds_Behind_Master` Určuje, jak pozdní repliky. Pokud hodnota není `0`, pak replika je zpracování aktualizací.

4. Aktualizujte odpovídající proměnných serveru, aby se data replikace bezpečnější (vyžadováno pouze u replikace bez GTID).
    
    Kvůli omezení nativní replikace MariaDB, je nutné nastavit [ `sync_master_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) a [ `sync_relay_log_info` ](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) proměnné na replikaci bez GTID scénáře.

    Zkontrolujte podřízený server serveru `sync_master_info` a `sync_relay_log_info` proměnné zkontrolujte, zda je replikace dat stabilní a nastavte proměnné na `1`.
    
## <a name="other-stored-procedures"></a>Dalších uložených procedur

### <a name="stop-replication"></a>Zastavení replikace

Pokud chcete zastavit replikaci mezi serverem pro hlavní a repliku, použijte následující uložené procedury:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Odeberte vztah replikace

Pokud chcete odebrat vztah mezi serverem pro hlavní a repliky, použijte následující uložené procedury:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Přeskočit replikace – chyba

Chcete-li přeskočit Chyba replikace a povolení replikace, použijte následující uložené procedury:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další postup
Další informace o [replikace dat](concepts-data-in-replication.md) pro službu Azure Database pro MariaDB.
