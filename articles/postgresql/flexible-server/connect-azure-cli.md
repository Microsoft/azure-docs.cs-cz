---
title: 'Rychlý Start: připojení pomocí Azure CLI-Azure Database for PostgreSQL-flexibilního serveru'
description: V tomto rychlém startu najdete několik způsobů, jak se pomocí Azure CLI připojit k serveru Azure Database for PostgreSQL flexibilnímu.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurecli
ms.topic: quickstart
ms.date: 03/06/2021
ms.openlocfilehash: 3017d10abc910233e0627037349c0dfd966fd88e
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107883791"
---
# <a name="quickstart-connect-and-query-with-azure-cli--with-azure-database-for-postgresql---flexible-server"></a>Rychlý Start: připojení a dotazování pomocí Azure CLI pomocí Azure Database for PostgreSQL-flexibilního serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je momentálně ve verzi Public Preview.

Tento rychlý Start ukazuje, jak se připojit k Azure Database for PostgreSQL flexibilnímu serveru pomocí příkazu Azure CLI s ```az postgres flexible-server connect``` příkazem. Tento příkaz umožňuje testovat připojení k databázovému serveru a spouštět dotazy. Můžete také spustit více dotazů pomocí interaktivního režimu. 

## <a name="prerequisites"></a>Požadavky
- Účet Azure: Pokud ho nemáte, [Získejte bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
- Nainstalovat nejnovější verzi [Azure CLI](/cli/azure/install-azure-cli) (2.20.0 nebo vyšší)
- Přihlášení pomocí příkazu Azure CLI pomocí ```az login``` příkazu 
- Zapněte trvalost parametrů s ```az config param-persist on``` . Trvalost parametrů vám pomůže použít místní kontext bez nutnosti opakovat spoustu argumentů, jako je skupina prostředků nebo umístění.

## <a name="create-an-postgresql-flexible-server"></a>Vytvoření PostgreSQL flexibilního serveru

První věc, kterou vytvoříme, je spravovaný PostgreSQL Server. V [Azure Cloud Shell](https://shell.azure.com/)spusťte následující skript a poznamenejte si **název serveru**, **uživatelské jméno** a  **heslo** , které jste vygenerovali pomocí tohoto příkazu.

```azurecli
az postgres flexible-server create --public-access <your-ip-address>
```
Můžete zadat další argumenty pro tento příkaz pro přizpůsobení. Podívejte se na všechny argumenty pro [AZ Postgres flexibilní-Server Create](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create).

## <a name="view-all-the-arguments"></a>Zobrazit všechny argumenty
Všechny argumenty tohoto příkazu můžete zobrazit pomocí ```--help``` argumentu. 

```azurecli
az postgresql flexible-server connect --help
```

## <a name="test-database-server-connection"></a>Test připojení k serveru databáze
Pomocí příkazu můžete testovat a ověřit připojení k databázi z vývojového prostředí.

```azurecli
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```
**Příklad:** 
```azurecli
az postgres flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d postgres
```
Po úspěšném připojení se zobrazí výstup.
```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```

Pokud selhalo připojení, zkuste Tato řešení:
- Ověřte, jestli je na klientském počítači otevřený port 5432.
- Pokud je uživatelské jméno a heslo správce serveru správné
- Pokud jste nakonfigurovali pravidlo brány firewall pro klientský počítač
- Pokud jste server nakonfigurovali pomocí privátního přístupu ve virtuální síti, ujistěte se, že je váš klientský počítač ve stejné virtuální síti.

## <a name="run-single-query"></a>Spustit jeden dotaz
Můžete spustit jeden dotaz pomocí příkazu s ```--querytext``` argumentem, ```-q``` .

```azurecli
az postgres flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> -q "<query-text>"
```

**Příklad:** 
```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb -q "select * from table1;" --output table
```

Zobrazí se výstup, jak je znázorněno níže:

```output
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Successfully connected to postgresdemoserver.
Ran Database Query: 'select * from table1;'
Retrieving first 30 rows of query output, if applicable.
Closed the connection postgresdemoserver.
Local context is turned on. Its information is saved in working directory C:\mydir. You can run `az local-context off` to turn it off.
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
az postgres flexible-server connect -n <servername> -u <username> -p "<password>" -d <databasename>
```

**Příklad:**

```azurecli
az postgresql flexible-server connect -n postgresdemoserver -u dbuser -p "dbpassword" -d flexibleserverdb --interactive
```

Zobrazí se prostředí **psql** , jak je znázorněno níže:

```bash
Command group 'postgres flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Password for earthyTurtle7:
Server: PostgreSQL 12.5
Version: 3.0.0
Chat: https://gitter.im/dbcli/pgcli
Home: http://pgcli.com
postgres> create database pollsdb;
CREATE DATABASE
Time: 0.308s
postgres> exit
Goodbye!
Local context is turned on. Its information is saved in working directory C:\sunitha. You can run `az local-context off` to turn it off.
Your preference of  are now saved to local context. To learn more, type in `az local-context --help`
```


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Správa serveru](./how-to-manage-server-cli.md)
