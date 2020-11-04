---
title: 'Rychlý Start: připojení pomocí Pythonu-Azure Database for PostgreSQL-Single server'
description: V tomto rychlém startu najdete ukázky kódu v Pythonu, které můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL na jednom serveru.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93331773"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rychlý Start: použití Pythonu k připojení a dotazování dat v Azure Database for PostgreSQL-Single server

V tomto rychlém startu se dozvíte, jak se připojit k databázi na Azure Database for PostgreSQL jednom serveru a spustit příkazy SQL pro dotazování pomocí Pythonu v macOS, Ubuntu Linux nebo Windows.

> [!TIP]
> Pokud chcete vytvořit aplikaci Django s PostgreSQL, pak ji vyřadíte [pomocí kurzu nasazení webové aplikace Django s](../app-service/tutorial-python-postgresql-app.md) využitím PostgreSQL.


## <a name="prerequisites"></a>Předpoklady
Pro tento rychlý Start budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- Vytvoření jednoho serveru Azure Database for PostgreSQL pomocí [Azure Portal](./quickstart-create-server-database-portal.md) <br/> nebo [Azure CLI](./quickstart-create-server-database-azure-cli.md) , pokud ho ještě nemáte.
- Na základě toho, jestli používáte veřejný nebo privátní přístup, proveďte **jednu** z následujících akcí, aby se povolilo připojení.

  |Akce| Metoda připojení|Praktičtí průvodci|
  |:--------- |:--------- |:--------- |
  | **Konfigurace pravidel brány firewall** | Veřejná | [Azure Portal](./howto-manage-firewall-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-firewall-using-cli.md)|
  | **Konfigurace koncového bodu služby** | Veřejná | [Azure Portal](./howto-manage-vnet-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-vnet-using-cli.md)|
  | **Konfigurace privátního odkazu** | Privátní | [Azure Portal](./howto-configure-privatelink-portal.md) <br/> [Rozhraní příkazového řádku](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9 + nebo 3.4 +.

- Poslední instalační program balíčku [PIP](https://pip.pypa.io/en/stable/installing/)
- Nainstalujte [psycopg2](https://pypi.python.org/pypi/psycopg2/) pomocí `pip install psycopg2` okna terminálu nebo příkazového řádku. Další informace najdete v tématu [instalace `psycopg2` ](http://initd.org/psycopg/docs/install.html)nástroje.

## <a name="get-database-connection-information"></a>Získat informace o připojení databáze
Připojení k databázi Azure Database for PostgreSQL vyžaduje plně kvalifikovaný název serveru a přihlašovací údaje. Tyto informace můžete získat z Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte název vašeho Azure Database for PostgreSQL serveru.
1. Na stránce **Přehled** serveru zkopírujte plně kvalifikovaný **název serveru** a **uživatelské jméno správce**. Plně kvalifikovaný **název serveru** je vždy ve tvaru *\<my-server-name> . Postgres.Database.Azure.com* a **uživatelské jméno správce** je vždy ve tvaru *\<my-admin-username>@\<my-server-name>* .

   Budete také potřebovat heslo správce. Pokud ho zapomenete, můžete ho resetovat z této stránky.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Název serveru Azure Database for PostgreSQL":::

> [!IMPORTANT]
>  Nahraďte následující hodnoty:
>   - `<server-name>` a `<admin-username>` s hodnotami, které jste zkopírovali z Azure Portal.
>   - `<admin-password>` se svým heslem na serveru.
>   - `<database-name>` výchozí databáze s názvem *Postgres* byla automaticky vytvořena při vytvoření serveru. Tuto databázi můžete přejmenovat nebo [vytvořit novou databázi](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) pomocí příkazů SQL.

## <a name="step-1-connect-and-insert-data"></a>Krok 1: připojení a vložení dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL databázi pomocí
-  [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) – funkce a načte data pomocí příkazu SQL **INSERT** .
- [cursor.exefunkce roztomilá](http://initd.org/psycopg/docs/cursor.html#execute) SPUSTÍ dotaz SQL na databázi.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Po úspěšném spuštění kódu se vytvoří následující výstup:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Výstup příkazového řádku":::


[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Krok 2: čtení dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL databázi a používá
- Pro čtení dat [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) pomocí příkazu SQL **Select** .
- [Cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall) přijme dotaz a vrátí sadu výsledků pro iteraci pomocí

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Krok 3: aktualizace dat
Následující příklad kódu používá [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Update** k aktualizaci dat.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Krok 5: odstranění dat
Následující příklad kódu se spustí [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Delete** k odstranění položky inventáře, kterou jste předtím vložili.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Správa serveru Azure Database for MySQL pomocí portálu](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Správa serveru Azure Database for MySQL pomocí rozhraní příkazového řádku](./how-to-manage-server-cli.md)<br/>

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/postgres-doc-feedback)
