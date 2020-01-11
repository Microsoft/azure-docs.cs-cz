---
title: Připojení pomocí Pythonu – Azure Database for MySQL
description: V tomto rychlém startu najdete vzorový kód Pythonu, který můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
ms.date: 01/09/2020
ms.openlocfilehash: 1550d8748d6c7c0e35796d2950d02d774fe52822
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896253"
---
# <a name="quickstart-use-python-to-connect-and-query-data-with-azure-database-for-mysql"></a>Rychlý Start: použití Pythonu k připojení a dotazování dat pomocí Azure Database for MySQL
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí [Pythonu](https://python.org). Příkazy SQL můžete použít k dotazování, vkládání, aktualizaci a odstraňování dat v databázi z platforem Mac OS, Ubuntu Linux a Windows. 

V tomto článku se předpokládá, že máte zkušenosti s vývojem pomocí Pythonu, ale začínáte pracovat s Azure Database for MySQL.

## <a name="create-an-azure-database-for-mysql"></a>Vytvoření Azure Database for MySQL 
Vytvořte Azure Database for MySQL server a databázi podle pokynů v tématu 
- [Vytvoření serveru Azure Database for MySQL pomocí Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md) nebo 
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md) 

## <a name="install-python-and-the-mysql-connector"></a>Instalace Pythonu a konektoru MySQL
Nainstalujte Python a konektor MySQL pro Python do počítače pomocí následujících kroků: 

> [!NOTE]
> V tomto rychlém startu se k připojení k MySQL používá nezpracovaný přístup k dotazu SQL. Pokud používáte webové rozhraní, použijte doporučený konektor pro rozhraní, například [mysqlclient](https://pypi.org/project/mysqlclient/) pro Django.

1. Stáhněte a nainstalujte [Python 3,7 nebo novější](https://www.python.org/downloads/) pro váš operační systém. Nezapomeňte přidat Python k vašemu `PATH`, protože konektor MySQL vyžaduje.
   
1. Otevřete příkazový řádek nebo `bash` Shell a podívejte se na verzi Pythonu spuštěním `python -V` s přepínačem Velká V.
   
1. Instalační program balíčku `pip` je součástí nejnovějších verzí Pythonu. Aktualizujte `pip` na nejnovější verzi spuštěním `pip install -U pip`. 
   
   Pokud `pip` není nainstalovaná, můžete si ho stáhnout a nainstalovat pomocí `get-pip.py`. Další informace najdete v tématu [instalace](https://pip.pypa.io/en/stable/installing/). 
   
1. K instalaci konektoru MySQL pro Python a jeho závislostí použijte `pip`:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Konektor Pythonu pro MySQL můžete také nainstalovat z [MySQL.com](https://dev.mysql.com/downloads/connector/python/). Další informace o konektoru MySQL pro Python najdete v tématu [Příručka pro vývojáře MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení, které potřebujete k připojení k Azure Database for MySQL z Azure Portal. Potřebujete název serveru, název databáze a přihlašovací údaje pro přihlášení.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
   
1. Na panelu hledání na portálu vyhledejte a vyberte server Azure Database for MySQL, který jste vytvořili, například **mydemoserver**.
   
   ![Název serveru Azure Database for MySQL](./media/connect-python/1_server-overview-name-login.png)
   
1. Na stránce **Přehled** serveru si poznamenejte **název serveru** a **přihlašovací jméno správce serveru**. Pokud zapomenete heslo, můžete také resetovat heslo z této stránky.
   
   ![Název serveru Azure Database for MySQL](./media/connect-python/azure-database-for-mysql-server-overview-name-login.png)

## <a name="run-the-python-examples"></a>Spuštění příkladů jazyka Python
Pro každý příklad kódu v tomto článku:

1. Vytvořte nový soubor v textovém editoru.
1. Přidejte do souboru příklad kódu. V kódu nahraďte `<mydemoserver>`, `<myadmin>`, `<mypassword>`a `<mydatabase>` zástupné symboly hodnotami pro server MySQL a databázi.
1. Uložte soubor do složky projektu s příponou *. py* , jako je například *C:\pythonmysql\createtable.py* nebo */home/username/pythonmysql/CreateTable.py*.
1. Chcete-li spustit kód, otevřete příkazový řádek nebo `bash` Shell a změňte adresář na složku projektu, například `cd pythonmysql`. Zadejte `python` příkaz následovaný názvem souboru, například `python createtable.py`a stiskněte klávesu ENTER. 
   
   > [!NOTE]
   > Pokud se v systému Windows nenajde soubor *Python. exe* , možná budete muset přidat cestu k Pythonu do proměnné prostředí PATH nebo zadat úplnou cestu k souboru *Python. exe*, například `C:\python27\python.exe createtable.py`.

## <a name="create-a-table-and-insert-data"></a>Vytvoření tabulky a vložení dat
Pomocí následujícího kódu se připojte k serveru a databázi, vytvořte tabulku a načtěte data pomocí příkazu **INSERT** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k Azure Database for MySQL pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [Cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Čtení dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k Azure Database for MySQL pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [Cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

Kód přečte řádky dat pomocí metody [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , zachovává sadu výsledků dotazu v řádku kolekce a pomocí `for` iterátoru přeskočí řádky.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k Azure Database for MySQL pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [Cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a odeberte data s využitím příkazu **DELETE** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k Azure Database for MySQL pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [Cursor. Execute ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace vaší databáze pomocí exportu a importu](./concepts-migrate-import-export.md)
