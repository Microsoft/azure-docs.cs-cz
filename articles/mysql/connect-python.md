---
title: Připojení k Azure Database for MySQL z Pythonu
description: V tomto rychlém startu najdete vzorový kód Pythonu, který můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 02/28/2018
ms.openlocfilehash: 08725493f9d050da762a6527363b77af6eeed6e6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="azure-database-for-mysql-use-python-to-connect-and-query-data"></a>Azure Database for MySQL: Použití Pythonu k připojení a dotazování dat
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL pomocí [Pythonu](https://python.org). Používá příkazy jazyka SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi z platforem Mac OS, Ubuntu Linux a Windows. Toto téma předpokládá, že máte zkušenosti s vývojem pomocí Pythonu a teprve začínáte pracovat se službou Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start jako výchozí bod využívá prostředky vytvořené v některém z těchto průvodců:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalace Pythonu a konektoru MySQL
Nainstalujte na svém počítači [Python](https://www.python.org/downloads/) a [konektor MySQL pro Python](https://dev.mysql.com/downloads/connector/python/). V závislosti na vaší platformě postupujte podle kroků v příslušné části:

### <a name="windows"></a>Windows
1. Z webu [python.org](https://www.python.org/downloads/windows/) stáhněte a nainstalujte Python 2.7. 
2. Spusťte příkazový řádek a zkontrolujte instalaci Pythonu. Spuštěním příkazu `C:\python27\python.exe -V` s použitím přepínače V (velké písmeno) zobrazte číslo verze.
3. Z webu [mysql.com](https://dev.mysql.com/downloads/connector/python/) nainstalujte konektor Pythonu pro MySQL odpovídající vaší verzi Pythonu.

### <a name="linux-ubuntu"></a>Linux (Ubuntu)
1. V systému Linux (Ubuntu) je Python obvykle nainstalován jako součást výchozí instalace.
2. Spusťte prostředí Bash a zkontrolujte instalaci Pythonu. Spuštěním příkazu `python -V` s použitím přepínače V (velké písmeno) zobrazte číslo verze.
3. Zkontrolujte instalaci PIP spuštěním příkazu `pip show pip -V`, který zobrazí číslo verze. 
4. PIP může být součástí některých verzí Pythonu. Pokud PIP není nainstalovaný, můžete balíček [PIP](https://pip.pypa.io/en/stable/installing/) nainstalovat spuštěním příkazu `sudo apt-get install python-pip`.
5. Aktualizujte PIP na nejnovější verzi spuštěním příkazu `pip install -U pip`.
6. Nainstalujte konektor MySQL pro Python a jeho závislosti pomocí tohoto příkazu PIP:

   ```bash
   sudo pip install mysql-connector-python-rf
   ```
 
### <a name="macos"></a>MacOS
1. V systému Mac OS je Python obvykle nainstalován jako součást výchozí instalace operačního systému.
2. Spusťte prostředí Bash a zkontrolujte instalaci Pythonu. Spuštěním příkazu `python -V` s použitím přepínače V (velké písmeno) zobrazte číslo verze.
3. Zkontrolujte instalaci PIP spuštěním příkazu `pip show pip -V`, který zobrazí číslo verze.
4. PIP může být součástí některých verzí Pythonu. Pokud PIP není nainstalovaný, můžete nainstalovat balíček [PIP](https://pip.pypa.io/en/stable/installing/).
5. Aktualizujte PIP na nejnovější verzi spuštěním příkazu `pip install -U pip`.
6. Nainstalujte konektor MySQL pro Python a jeho závislosti pomocí tohoto příkazu PIP:

   ```bash
   pip install mysql-connector-python-rf
   ```

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for MySQL](./media/connect-python/1_server-overview-name-login.png)
   

## <a name="run-python-code"></a>Spuštění kódu Pythonu
- Vložte kód do textového souboru a ten uložte s příponou .py do složky projektu (například C:\pythonmysql\createtable.py nebo /home/username/pythonmysql/createtable.py).
- Pokud chcete kód spustit, spusťte příkazový řádek nebo prostředí Bash. Změňte adresář na složku vašeho projektu pomocí příkazu `cd pythonmysql`. Zadejte příkaz python následovaný názvem souboru (`python createtable.py`) a spusťte aplikaci. Pokud se v operačním systému Windows nepodaří najít soubor python.exe, možná budete muset zadat úplnou cestu ke spustitelnému souboru nebo přidat cestu k Pythonu do proměnné prostředí PATH. `C:\python27\python.exe createtable.py`

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat
Pomocí následujícího kódu se připojte k serveru, vytvořte tabulku a nahrajte data s využitím příkazu **INSERT** jazyka SQL. 

V kódu se importuje knihovna mysql.connector. Funkce [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) slouží k připojení ke službě Azure Database for MySQL s použitím [argumentů připojení](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí příkaz jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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

V kódu se importuje knihovna mysql.connector. Funkce [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) slouží k připojení ke službě Azure Database for MySQL s použitím [argumentů připojení](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí příkaz jazyka SQL na databázi MySQL. Řádky dat se načítají pomocí metody [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html). Sada výsledků dotazu se uchovává v řádku kolekce a k procházení řádků ve smyčce se používá iterátor for.

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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

V kódu se importuje knihovna mysql.connector.  Funkce [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) slouží k připojení ke službě Azure Database for MySQL s použitím [argumentů připojení](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí příkaz jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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

V kódu se importuje knihovna mysql.connector.  Funkce [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) slouží k připojení ke službě Azure Database for MySQL s použitím [argumentů připojení](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí příkaz jazyka SQL na databázi MySQL. 

Parametry `host`, `user`, `password` a `database` nahraďte hodnotami, které jste zadali při vytváření serveru a databáze.

```Python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'mydemoserver.mysql.database.azure.com',
  'user':'myadmin@mydemoserver',
  'password':'yourpassword',
  'database':'quickstartdb'
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
