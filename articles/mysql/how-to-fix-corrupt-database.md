---
title: Vyřešit poškození databáze Azure Database for MySQL
description: V tomto článku se dozvíte, jak opravit problémy s poškozením databáze v Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 21e4189e56f704129710da5b1d39613c4e1b1df5
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766886"
---
# <a name="troubleshoot-database-corruption-in-azure-database-for-mysql"></a>Řešení potíží s poškozením databáze v Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Poškození databáze může způsobit výpadky vaší aplikace. Je také důležité vyřešit problémy poškození v čase, aby nedošlo ke ztrátě dat. Když dojde k poškození databáze, zobrazí se v protokolech serveru Tato chyba: `InnoDB: Database page corruption on disk or a failed.`

V tomto článku se dozvíte, jak vyřešit problémy s poškozením databáze nebo tabulky. Azure Database for MySQL používá modul InnoDB. Nabízí automatizované operace kontroly a oprav poškození. InnoDB kontroluje poškozené stránky tím, že spouští kontrolní součty na každé stránce, kterou čte. Pokud najde rozdíl kontrolního součtu, server MySQL se automaticky zastaví.

Vyzkoušejte následující možnosti, které vám umožní rychle zmírnit problémy s poškozením databáze.

## <a name="restart-your-mysql-server"></a>Restartujte server MySQL.

Všimněte si, že když vaše aplikace přistupuje k tabulce nebo databázi, je obvykle poškozená databáze nebo tabulka. InnoDB obsahuje mechanismus obnovení po havárii, který dokáže vyřešit většinu problémů při restartování serveru. Proto restartování serveru může pomáhat při obnovení serveru z havárie, která způsobila, že databáze je v nesprávném stavu.

## <a name="use-the-dump-and-restore-method"></a>Použití metody výpisu a obnovení

Doporučujeme vyřešit problémy s poškozením pomocí metody *výpisu a obnovení* . Tato metoda zahrnuje:
1. Přístup k poškozené tabulce.
1. Vytvoření logické zálohy tabulky pomocí nástroje mysqldump Záloha bude uchovávat strukturu tabulky a data, která jsou v ní.
1. Opětovné načtení tabulky do databáze.

### <a name="back-up-your-database-or-tables"></a>Zálohování databáze nebo tabulek

> [!Important]
> - Ujistěte se, že jste nakonfigurovali pravidlo brány firewall pro přístup k serveru z klientského počítače. Další informace najdete v tématu [Konfigurace pravidla brány firewall na jednom serveru](howto-manage-firewall-using-portal.md) a [Konfigurace pravidla brány firewall na flexibilním serveru](flexible-server/how-to-connect-tls-ssl.md).
> - `--ssl-cert`Pokud máte povolený protokol SSL, použijte možnost SSL pro mysqldump.

Vytvořte záložní soubor z příkazového řádku pomocí mysqldump. Použijte tento příkaz:

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Popisy parametrů:
- `[ssl-cert=/path/to/pem]`: Cesta k certifikátu SSL. Stáhněte si certifikát SSL na klientském počítači a nastavte cestu k němu v příkazu. Tento parametr nepoužívejte, je-li zakázán protokol SSL.
- `[host]`: Server Azure Database for MySQL.
- `[uname]`: Uživatelské jméno správce serveru.
- `[pass]`: Heslo pro uživatele s oprávněními správce.
- `[dbname]`: Název vaší databáze.
- `[backupfile.sql]`: Název souboru zálohy databáze.

> [!Important]
> - V případě jednoho serveru použijte formát, `admin-user@servername` který chcete nahradit `myserveradmin` v následujících příkazech.
> - V případě flexibilního serveru použijte formát, `admin-user` který chcete nahradit `myserveradmin` v následujících příkazech.

Pokud je určitá tabulka poškozená, vyberte v databázi konkrétní tabulky k zálohování:
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Chcete-li zálohovat jednu nebo více databází, použijte `--database` přepínač a seznam názvů databází oddělené mezerami:

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

### <a name="restore-your-database-or-tables"></a>Obnovení databáze nebo tabulek

Následující kroky ukazují, jak obnovit databázi nebo tabulky. Po vytvoření záložního souboru můžete tabulky nebo databáze obnovit pomocí nástroje MySQL. Spusťte tento příkaz:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Tady je příklad, který se obnoví `testdb` ze záložního souboru vytvořeného pomocí mysqldump: 

> [!Important]
> - V případě jednoho serveru použijte formát, `admin-user@servername` který chcete nahradit `myserveradmin` v následujícím příkazu.
> - V případě flexibilního serveru použijte formát, ```admin-user``` který chcete nahradit `myserveradmin` v následujícím příkazu. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Další kroky
Pokud předchozí kroky problém nevyřeší, můžete vždy obnovit celý server:
- [Obnovit server v Azure Database for MySQL – jeden server](howto-restore-server-portal.md)
- [Obnovení serveru v Azure Database for MySQL-flexibilním serveru](flexible-server/how-to-restore-server-portal.md)



