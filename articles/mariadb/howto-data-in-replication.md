---
title: Konfigurace replikace dat – Azure Database for MariaDB
description: Tento článek popisuje, jak nastavit Replikace vstupních dat v Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/30/2020
ms.openlocfilehash: 332feffead74174ba0b9b278d8de1c5957d5b9e6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422475"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Konfigurace Replikace vstupních dat v Azure Database for MariaDB

Tento článek popisuje, jak nastavit Replikace vstupních dat v Azure Database for MariaDB konfigurací serverů hlavní servery a repliky. V tomto článku se předpokládá, že máte několik předchozích zkušeností s MariaDB servery a databázemi.

Pokud chcete vytvořit repliku ve službě Azure Database for MariaDB, Replikace vstupních dat synchronizuje data z hlavního serveru MariaDB místně, na virtuálních počítačích (VM) nebo v cloudových databázových službách.

Před provedením kroků v tomto článku zkontrolujte [omezení a požadavky](concepts-data-in-replication.md#limitations-and-considerations) na replikaci dat.

> [!NOTE]
> Pokud je váš hlavní server verze 10,2 nebo novější, doporučujeme nastavit Replikace vstupních dat pomocí [globálního ID transakce](https://mariadb.com/kb/en/library/gtid/).


## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Vytvoření serveru MariaDB pro použití jako repliky

1. Vytvořte nový Azure Database for MariaDB Server (například replica.mariadb.database.azure.com). Server je server repliky v Replikace vstupních dat.

    Další informace o vytváření serverů najdete v tématu [vytvoření serveru Azure Database for MariaDB pomocí Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

   > [!IMPORTANT]
   > Je nutné vytvořit server Azure Database for MariaDB v cenové úrovni optimalizované pro Pro obecné účely nebo paměť.

2. Vytváření identických uživatelských účtů a odpovídajících oprávnění
    
    Uživatelské účty se nereplikují z hlavního serveru na server repliky. Chcete-li poskytnout uživatelům přístup k serveru repliky, je nutné ručně vytvořit všechny účty a odpovídající oprávnění na nově vytvořeném serveru Azure Database for MariaDB.

3. Přidejte IP adresu hlavního serveru k pravidlům brány firewall repliky. 

   Pomocí webu [Azure Portal](howto-manage-firewall-portal.md) nebo [Azure CLI](howto-manage-firewall-cli.md) aktualizujte pravidla brány firewall.

## <a name="configure-the-master-server"></a>Konfigurace hlavního serveru

Následující kroky připravují a konfigurují místně hostovaný Server MariaDB, na virtuálním počítači nebo v cloudové databázové službě pro Replikace vstupních dat. Server MariaDB je hlavním serverem v Replikace vstupních dat.

1. Než budete pokračovat, zkontrolujte [požadavky hlavního serveru](concepts-data-in-replication.md#requirements) . 

   Ujistěte se například, že hlavní server umožňuje příchozí i odchozí provoz na portu 3306 a že má hlavní server **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN). 
   
   Otestujte připojení k hlavnímu serveru tím, že se pokusíte připojit z nástroje, jako je například příkaz MySQL hostovaný na jiném počítači nebo [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) k dispozici v Azure Portal.

2. Zapněte binární protokolování.
    
    Pokud chcete zjistit, jestli je v hlavní části povolené binární protokolování, zadejte následující příkaz:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Pokud proměnná [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) vrátí hodnotu `ON`, binární protokolování je na vašem serveru povolené.

   Pokud `log_bin` vrátí hodnotu `OFF`, upravte soubor **My. CNF** tak, `log_bin=ON` aby se zapíná binární protokolování. Aby se změna projevila, restartujte server.

3. Nakonfigurujte nastavení hlavního serveru.

    Replikace vstupních dat vyžaduje, aby `lower_case_table_names` byl parametr konzistentní mezi hlavním serverem a serverem repliky. `lower_case_table_names` Parametr je ve výchozím nastavení `1` nastaven na Azure Database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

4. Vytvořte novou roli replikace a nastavte oprávnění.

   Vytvořte uživatelský účet na hlavním serveru, který je nakonfigurovaný s oprávněními replikace. Účet můžete vytvořit pomocí příkazů SQL nebo MySQL Workbench. Pokud plánujete replikaci s protokolem SSL, je nutné tuto specifikaci zadat při vytváření uživatelského účtu.
   
   Informace o tom, jak přidat uživatelské účty na hlavní server, najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/create-user/).

   Pomocí následujících příkazů má nová role replikace přístup k hlavnímu serveru z libovolného počítače, nikoli jenom k počítači, který hostuje hlavní server. Pro tento přístup zadejte **syncuser\@'% '** v příkazu pro vytvoření uživatele.
   
   Další informace o dokumentaci k MariaDB najdete v tématu [Určení názvů účtů](https://mariadb.com/kb/en/library/create-user/#account-names).

   **Příkaz SQL**

   - Replikace s protokolem SSL

       Chcete-li pro všechna připojení uživatelů vyžadovat protokol SSL, zadejte následující příkaz pro vytvoření uživatele:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replikace bez SSL

       Pokud pro všechna připojení není vyžadován protokol SSL, zadejte následující příkaz pro vytvoření uživatele:
    
       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Pokud chcete vytvořit roli replikace v aplikaci MySQL Workbench, vyberte v podokně **Správa** možnost **Uživatelé a oprávnění**. Pak vyberte **Přidat účet**.
 
   ![Uživatelé a oprávnění](./media/howto-data-in-replication/users_privileges.png)

   Do pole **přihlašovací jméno** zadejte uživatelské jméno.

   ![Synchronizovat uživatele](./media/howto-data-in-replication/syncuser.png)
 
   Vyberte panel **role pro správu** a potom v seznamu **globálních oprávnění**vyberte **replikace podřízená**. Pokud chcete vytvořit roli replikace, vyberte **použít** .

   ![Replikace podřízených](./media/howto-data-in-replication/replicationslave.png)


5. Nastavte hlavní server na režim jen pro čtení.

   Než vypíšete databázi, musí být server umístěn v režimu jen pro čtení. V režimu jen pro čtení nemůže hlavní server zpracovat žádné transakce zápisu. Aby se zabránilo dopadu na firmu, naplánujte v době mimo špičku okno s oprávněním jen pro čtení.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

6. Získá aktuální binární název a posun souboru protokolu.

   Chcete-li určit aktuální binární název a posun souboru protokolu, spusťte příkaz [`show master status`](https://mariadb.com/kb/en/library/show-master-status/).
    
   ```sql
   show master status;
   ```
   Výsledky by měly vypadat podobně jako v následující tabulce:
   
   ![Výsledky hlavního stavu](./media/howto-data-in-replication/masterstatus.png)

   Poznamenejte si název binárního souboru, protože se bude používat v pozdějších krocích.
   
7. Získejte GTID pozici (volitelné, nutné pro replikaci s GTID).

   Spusťte funkci [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) , abyste získali GTID pozici pro odpovídající název a posun souboru binlog.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```
 

## <a name="dump-and-restore-the-master-server"></a>Výpis a obnovení hlavního serveru

1. Vypíše všechny databáze z hlavního serveru.

   Pomocí mysqldump vypíšete všechny databáze z hlavního serveru. Není nutné vypsat knihovnu MySQL a knihovnu testů.

    Další informace najdete v tématu [Výpis a obnovení](howto-migrate-dump-restore.md).

2. Nastavte hlavní server na režim pro čtení a zápis.

   Po dokončení databáze změňte hlavní server MariaDB zpět na režim čtení/zápisu.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Obnovte soubor s výpisem paměti na novém serveru.

   Obnovte soubor s výpisem paměti na server vytvořený ve službě Azure Database for MariaDB. Postup obnovení souboru s výpisem paměti na server MariaDB najdete v tématu [Výpis paměti & obnovení](howto-migrate-dump-restore.md) .

   Pokud je soubor s výpisem paměti velký, nahrajte ho do virtuálního počítače v Azure ve stejné oblasti jako server repliky. Obnovte ji na Azure Database for MariaDB Server z virtuálního počítače.

## <a name="link-the-master-and-replica-servers-to-start-data-in-replication"></a>Propojte hlavní server a server repliky a spusťte Replikace vstupních dat

1. Nastavte hlavní server.

   Všechny funkce Replikace vstupních dat jsou prováděny uloženými procedurami. Všechny postupy najdete v [replikace vstupních dat uložených procedurách](reference-data-in-stored-procedures.md). Uložené procedury lze spustit v prostředí MySQL nebo MySQL Workbench.

   Pokud chcete propojit dva servery a spustit replikaci, přihlaste se k cílovému serveru repliky ve službě Azure DB pro MariaDB. Potom nastavte externí instanci jako hlavní server pomocí uložené procedury `mysql.az_replication_change_master` nebo `mysql.az_replication_change_master_with_gtid` na serveru Azure DB pro MariaDB.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```
   
   – nebo –
   
   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: název hostitele hlavního serveru
   - master_user: uživatelské jméno pro hlavní server
   - master_password: heslo pro hlavní server
   - master_log_file: název souboru binárního protokolu se spustí.`show master status`
   - master_log_pos: binární umístění protokolu se spouští.`show master status`
   - master_gtid_pos: pozice GTID ze spuštění`select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: kontext certifikátu certifikační autority. Pokud nepoužíváte protokol SSL, předejte prázdný řetězec. *
    
    
    * Doporučujeme předat parametr master_ssl_ca jako proměnnou. Další informace najdete v následujících příkladech.

   **Příklady**

   - Replikace s protokolem SSL

       Vytvořte proměnnou `@cert` spuštěním následujících příkazů:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replikace s protokolem SSL je nastavená mezi hlavním serverem hostovaným v doméně companya.com a serverem repliky hostovaným v Azure Database for MariaDB. Tato uložená procedura se spouští na replice.
    
       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```
   - Replikace bez SSL

       Replikace bez protokolu SSL je nastavená mezi hlavním serverem hostovaným v doméně companya.com a serverem repliky hostovaným v Azure Database for MariaDB. Tato uložená procedura se spouští na replice.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Spusťte replikaci.

   Pro spuštění `mysql.az_replication_start` replikace zavolejte uloženou proceduru.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Zkontroluje stav replikace.

   Voláním [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) příkazu na serveru repliky zobrazte stav replikace.
    
   ```sql
   show slave status;
   ```

   Pokud `Slave_IO_Running` a `Slave_SQL_Running` jsou ve stavu `yes`a hodnota `Seconds_Behind_Master` je `0`, replikace funguje. `Seconds_Behind_Master`Určuje, jak pozdě je replika. Pokud hodnota není `0`, replika zpracovává aktualizace.

4. Aktualizujte odpovídající proměnné serveru pro zajištění bezpečnější replikace dat (vyžaduje se jenom pro replikaci bez GTID).
    
    Z důvodu nativního omezení replikace v MariaDB je nutné nastavit [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) a [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) proměnné pro replikaci bez scénáře GTID.

    Zkontrolujte, jestli jsou na `sync_master_info` podřízeném serveru a `sync_relay_log_info` proměnné, aby se zajistila stabilita replikace dat, a nastavte proměnné `1`na.
    
## <a name="other-stored-procedures"></a>Jiné uložené procedury

### <a name="stop-replication"></a>Zastavení replikace

Pokud chcete zastavit replikaci mezi hlavním serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>Odebrat vztah replikace

Chcete-li odebrat relaci mezi hlavním serverem a serverem repliky, použijte následující uloženou proceduru:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>Přeskočit chybu replikace

Pokud chcete přeskočit chybu replikace a povolení replikace, použijte následující uloženou proceduru:
    
```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [replikace vstupních dat](concepts-data-in-replication.md) pro Azure Database for MariaDB.
