---
title: 'Rychlý Start: připojení pomocí Azure CLI-Azure Database for MySQL-flexibilního serveru'
description: V tomto rychlém startu najdete několik způsobů, jak se pomocí Azure CLI připojit k serveru Azure Database for MySQL flexibilnímu.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/01/2021
ms.openlocfilehash: 5abc71a4df8dd27e80a7590d53159be95d46e441
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883845"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-mysql---flexible-server"></a>Rychlý Start: připojení a dotazování pomocí Azure CLI pomocí Azure Database for MySQL-flexibilního serveru

> [!IMPORTANT]
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento rychlý Start ukazuje, jak se připojit k Azure Database for MySQL flexibilnímu serveru pomocí příkazu Azure CLI s ```az mysql flexible-server connect``` příkazem. Tento příkaz umožňuje testovat připojení k databázovému serveru a spouštět dotazy přímo na serveru.  Můžete také použít příkaz Spustit v interaktivním režimu pro spuštění více dotazů.

## <a name="prerequisites"></a>Požadavky

- Účet Azure: Pokud ho nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Nainstalovat nejnovější verzi [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 nebo vyšší)
- Přihlášení pomocí příkazu Azure CLI pomocí ```az login``` příkazu 
- Zapněte trvalost parametrů s ```az config param-persist on``` . Trvalost parametrů vám pomůže použít místní kontext bez nutnosti opakovat hodně argumentů, jako je skupina prostředků nebo umístění atd.

## <a name="create-an-mysql-flexible-server"></a>Vytvoření flexibilního serveru MySQL

Jako první vytvoříme spravovaný server MySQL. V [Azure Cloud Shell](https://shell.azure.com/)spusťte následující skript a poznamenejte si **název serveru**, **uživatelské jméno** a  **heslo** , které jste vygenerovali pomocí tohoto příkazu.

```azurecli
az mysql flexible-server create --public-access <your-ip-address>
```

Můžete zadat další argumenty pro tento příkaz pro přizpůsobení. Podívejte se na všechny argumenty pro [AZ MySQL flexibilní-Server Create](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_create).

## <a name="create-a-database"></a>Vytvoření databáze
Spusťte následující příkaz, který vytvoří databázi **newdatabase** , pokud jste ji ještě nevytvořili.

```azurecli
az mysql flexible-server db create -d newdatabase
```

## <a name="view-all-the-arguments"></a>Zobrazit všechny argumenty
Všechny argumenty tohoto příkazu můžete zobrazit pomocí ```--help``` argumentu. 

```azurecli
az mysql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Test připojení k serveru databáze
Spusťte následující skript, který otestuje a ověří připojení k databázi z vývojového prostředí.

```azurecli
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```

**Příklad:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```

Pro úspěšné připojení byste měli vidět následující výstup:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
Pokud selhalo připojení, zkuste Tato řešení:
- Ověřte, jestli je na klientském počítači otevřený port 3306.
- Pokud je uživatelské jméno a heslo správce serveru správné
- Pokud jste nakonfigurovali pravidlo brány firewall pro klientský počítač
- Pokud jste server nakonfigurovali pomocí privátního přístupu ve virtuální síti, ujistěte se, že je váš klientský počítač ve stejné virtuální síti.

## <a name="run-single-query"></a>Spustit jeden dotaz
Spuštěním následujícího příkazu spusťte jeden dotaz pomocí ```--querytext``` argumentu ```-q``` .

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**Příklad:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```

Zobrazí se výstup, jak je znázorněno níže:

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to mysqldemoserver1.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection to mysqldemoserver1
Local context is turned on. Its information is saved in working directory C:\Users\sumuth. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
Txt    Val
-----  -----
test   200
test   200
test   200
test   200
test   200
test   200
test   200
```

## <a name="run-multiple-queries-using-interactive-mode"></a>Spuštění více dotazů pomocí interaktivního režimu
Pomocí **interaktivního** režimu můžete spustit více dotazů. Pokud chcete povolit interaktivní režim, spusťte následující příkaz.

```azurecli
az mysql flexible-server connect -n <server-name> -u <username> -p <password> --interactive
```

**Příklad:**
```azurecli
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase --interactive
```

Zobrazí se prostředí **MySQL** , jak je znázorněno níže:

```bash
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password:
mysql 5.7.29-log
mycli 1.22.2
Chat: https://gitter.im/dbcli/mycli
Mail: https://groups.google.com/forum/#!forum/mycli-users
Home: http://mycli.net
Thanks to the contributor - Martijn Engler
newdatabase> CREATE TABLE table1 (id int NOT NULL, val int,txt varchar(200));
Query OK, 0 rows affected
Time: 2.290s
newdatabase1> INSERT INTO table1 values (1,100,'text1');
Query OK, 1 row affected
Time: 0.199s
newdatabase1> SELECT * FROM table1;
+----+-----+-------+
| id | val | txt   |
+----+-----+-------+
| 1  | 100 | text1 |
+----+-----+-------+
1 row in set
Time: 0.149s
newdatabase>exit;
Goodbye!
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
* [Připojení k Azure Database for MySQL-flexibilnímu serveru pomocí šifrovaných připojení](how-to-connect-tls-ssl.md)
* [Správa serveru](./how-to-manage-server-cli.md)

