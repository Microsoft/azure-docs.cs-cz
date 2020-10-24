---
title: 'Rychlý Start: připojení pomocí Pythonu-Azure Database for PostgreSQL-Single server'
description: V tomto rychlém startu najdete ukázky kódu v Pythonu, které můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL na jednom serveru.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: 2ecf5c540c3fce7a60ebf256d871993400a731ed
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481189"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Rychlý Start: použití Pythonu k připojení a dotazování dat v Azure Database for PostgreSQL-Single server

V tomto rychlém startu pracujete s Azure Database for PostgreSQL s využitím Pythonu na macOS, Ubuntu Linux nebo Windows. V tomto rychlém startu se dozvíte, jak se připojit k databázi a použít příkazy SQL k dotazování, vkládání, aktualizaci a odstraňování dat. Článek předpokládá, že jste obeznámeni s Pythonem, ale novinkou pro práci s Azure Database for PostgreSQL.

> [!TIP]
> Pokud chcete vytvořit aplikaci Django s PostgreSQL, pak ji vyřadíte [pomocí kurzu nasazení webové aplikace Django s](../app-service/tutorial-python-postgresql-app.md) využitím PostgreSQL.


## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Dokončení [rychlého startu: vytvoření serveru Azure Database for PostgreSQL v Azure Portal](quickstart-create-server-database-portal.md) nebo [rychlém startu: vytvoření Azure Database for POSTGRESQL pomocí Azure CLI](quickstart-create-server-database-azure-cli.md).
  
- [Python](https://www.python.org/downloads/) 2.7.9 + nebo 3.4 +.
  
- Poslední instalační program balíčku [PIP](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-python-libraries-for-postgresql"></a>Instalace knihoven Pythonu pro PostgreSQL
Modul [psycopg2](https://pypi.python.org/pypi/psycopg2/) umožňuje připojení k databázi PostgreSQL a dotazování na ni a je k dispozici jako balíček pro Linux, MacOS nebo Windows [kolo](https://pythonwheels.com/) . Nainstalujte binární verzi modulu včetně všech závislostí. Další informace o `psycopg2` instalaci a požadavcích najdete v tématu [instalace](http://initd.org/psycopg/docs/install.html). 

Chcete-li provést instalaci `psycopg2` , otevřete terminál nebo příkazový řádek a spusťte příkaz `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Získat informace o připojení databáze
Připojení k databázi Azure Database for PostgreSQL vyžaduje plně kvalifikovaný název serveru a přihlašovací údaje. Tyto informace můžete získat z Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte název vašeho Azure Database for PostgreSQL serveru. 
1. Na stránce **Přehled** serveru zkopírujte plně kvalifikovaný **název serveru** a **uživatelské jméno správce**. Plně kvalifikovaný **název serveru** je vždy ve tvaru * \<my-server-name> . Postgres.Database.Azure.com*a **uživatelské jméno správce** je vždy ve tvaru *\<my-admin-username>@\<my-server-name>* . 
   
   Budete také potřebovat heslo správce. Pokud ho zapomenete, můžete ho resetovat z této stránky. 
   
   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Název serveru Azure Database for PostgreSQL":::

## <a name="how-to-run-the-python-examples"></a>Jak spustit příklady Pythonu

Pro každý příklad kódu v tomto článku:

1. Vytvořte nový soubor v textovém editoru. 
   
1. Přidejte do souboru příklad kódu. V kódu nahraďte:
   - `<server-name>` a `<admin-username>` s hodnotami, které jste zkopírovali z Azure Portal.
   - `<admin-password>` se svým heslem na serveru.
   - `<database-name>` názvem vaší databáze Azure Database for PostgreSQL. Výchozí databáze s názvem *Postgres* byla automaticky vytvořena při vytvoření serveru. Tuto databázi můžete přejmenovat nebo vytvořit novou databázi pomocí příkazů SQL. 
   
1. Uložte soubor do složky vašeho projektu s příponou *. py* , například *Postgres-INSERT.py*. Pro Windows se ujistěte, že je při ukládání souboru vybraný kódování UTF-8. 
   
1. Chcete-li spustit soubor, přejděte do složky vašeho projektu v rozhraní příkazového řádku a zadejte `python` název následovaný názvem souboru, například `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Vytvoření tabulky a vložení dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL databázi pomocí funkce [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) a načítá data pomocí příkazu SQL **INSERT** . Funkce [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) provádí dotaz SQL na databázi. 

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

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Název serveru Azure Database for PostgreSQL":::

## <a name="read-data"></a>Čtení dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL databázi a používá [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Select** ke čtení dat. Tato funkce přijme dotaz a vrátí sadu výsledků pro iteraci pomocí [Cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Aktualizace dat
Následující příklad kódu se připojí k vaší Azure Database for PostgreSQL databázi a používá [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Update** k aktualizaci dat. 

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

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Odstranění dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL databázi a používá [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Delete** k odstranění položky inventáře, kterou jste předtím vložili. 

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

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./howto-migrate-using-export-and-import.md)