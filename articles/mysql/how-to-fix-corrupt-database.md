---
title: Vyřešit poškození databáze Azure Database for MySQL
description: Informace o řešení problémů s poškozením databáze pro Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 815b24d25e674e5460cc50d7eb6871f740994893
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935937"
---
# <a name="troubleshoot-database-corruption-on-azure-database-for-mysql"></a>Řešení potíží s poškozením databáze na Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Poškození databáze může způsobit výpadky vaší aplikace a je také důležité vyřešit problém v čase, aby se předešlo ztrátě dat. Když dojde k poškození databáze, zobrazí se ve vašem serveru Tato chyba **InnoDB: poškození stránky databáze na disku nebo selhání**.

V této příručce se dozvíte, jak opravit, jestli je databáze nebo tabulka poškozená. Azure Database for MySQL používá modul InnoDB a poskytuje automatizované operace kontroly a oprav poškození. InnoDB kontroluje poškozené stránky tím, že provádí kontrolní součty na každé stránce, kterou čte, a pokud najde nesoulad kontrolního součtu, automaticky zastaví Server MySQL.

Vyzkoušejte některou z následujících možností, které vám pomůžou rychle zmírnit problémy s poškozením databáze.

## <a name="restart-your-mysql-server"></a>Restartujte server MySQL.

Obvykle si všimnete, že je databáze nebo tabulka poškozená, když aplikace přistupuje k databázi typu ro. Vzhledem k tomu, že InnoDB obsahuje mechanismus obnovení po havárii, který dokáže vyřešit většinu problémů při restartování serveru. Restartování serveru by proto mělo pomáhat při obnovení serveru z havárie, která způsobila, že databáze je v nesprávném stavu.

##  <a name="resolve-data-corruption-with-dump-and-restore-method"></a>Řešení poškození dat pomocí metody výpisu a obnovení

Doporučujeme vyřešit problém s poškozením pomocí **metody výpisu a obnovení**. To zahrnuje získání přístupu k poškozené tabulce pomocí nástroje **mysqldump** k vytvoření logické zálohy tabulky, která zachová strukturu tabulky a data v ní a následně znovu načte tabulku zpět do databáze.

### <a name="backup-your-database-or-tables"></a>Zálohování databáze nebo tabulek

> [!Important]
> - Nastavte konfiguraci pravidla brány firewall, abyste měli přístup k serveru z klientského počítače. Podívejte se, jak nakonfigurovat [pravidlo brány firewall na jednom serveru](howto-manage-firewall-using-portal.md) a [pravidlo brány firewall na flexibilním serveru](flexible-server/how-to-connect-tls-ssl.md).
> - Možnost SSL použijte ```--ssl-cert``` pro **mysqldump** , pokud je povolený protokol SSL.

Vytvoření záložního souboru z příkazového řádku pomocí mysqldump pomocí tohoto příkazu

```
$ mysqldump [--ssl-cert=/path/to/pem] -h [host] -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

K dispozici jsou následující parametry:
- [SSL-CERT =/path/to/PEM] Stáhněte si certifikát SSL na klientském počítači a nastavte cestu k němu v příkazu. Nepoužívejte toto: protokol SSL je zakázán.
- [hostitel] je váš server Azure Database for MySQL.
- [uname] je uživatelské jméno správce serveru.
- [Pass] je heslo pro uživatele s oprávněními správce.
- [dbname] je název vaší databáze.
- [souborzálohy. SQL] Pokud název souboru pro zálohování databáze

> [!Important]
> - V případě jednoho serveru použijte formát, ```admin-user@servername``` který chcete nahradit ```myserveradmin``` v následujících příkazech.
> - V případě flexibilního serveru použijte formát ```admin-user``` , který chcete nahradit ```myserveradmin``` v následujících příkazech.

Pokud je určitá tabulka poškozená, pak v tomto příkladu vyberte konkrétní tabulky v databázi, které chcete zálohovat.
```
$ mysqldump --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb table1 table2 > testdb_tables_backup.sql
```

Chcete-li zálohovat jednu nebo více databází, použijte přepínač--Database a seznamte se s názvy databází oddělenými mezerami.

```
$ mysqldump --ssl-cert=</path/to/pem>  -h mydemoserver.mysql.database.azure.com -u myserveradmin -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql
```

###  <a name="restore-your-database-or-tables"></a>Obnovení databáze nebo tabulek

Následující kroky ukazují, jak transakční program obnovuje databázi nebo tabulky. Po vytvoření záložního souboru můžete tabulku nebo databáze obnovit pomocí nástroje ***MySQL** . Spusťte příkaz, jak je znázorněno níže:

```
mysql  --ssl-cert=</path/to/pem> -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
Tady je příklad obnovení ```testdb``` ze záložního souboru vytvořeného pomocí **mysqldump**. 

> [!Important]
> - V případě jednoho serveru použijte formát, ```admin-user@servername``` který chcete nahradit ```myserveradmin``` v níže uvedeném příkazu.
> - V případě flexibilního serveru použijte formát ```admin-user``` , který chcete nahradit ```myserveradmin``` v níže uvedeném příkazu. 

```
$ mysql --ssl-cert=</path/to/pem> -h mydemoserver.mysql.database.azure.com -u myserveradmin -p testdb < testdb_backup.sql
```

## <a name="next-steps"></a>Další kroky
Pokud výše uvedené kroky nepomohly problém vyřešit, můžete vždy obnovit celý server.
- [Obnovení jednoho serveru Azure Database for MySQL](howto-restore-server-portal.md)
- [Obnovení Azure Database for MySQL flexibilního serveru](flexible-server/how-to-restore-server-portal.md)



