---
title: 'Rychlý Start: připojení pomocí Pythonu-Azure Database for MySQL-flexibilního serveru'
description: V tomto rychlém startu najdete několik ukázek kódu Pythonu, které můžete použít k připojení a dotazování dat z Azure Database for MySQL-flexibilního serveru.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: ff14bb1daeef6fc54ee5d11632ad98a29db2a172
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946838"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Rychlý Start: použití Pythonu k připojení a dotazování dat v Azure Database for MySQL-flexibilním serveru

> [!IMPORTANT] 
> Azure Database for MySQL – flexibilní Server je momentálně ve verzi Public Preview.

V tomto rychlém startu se připojíte k Azure Database for MySQL flexibilnímu serveru pomocí Pythonu. Pak použijete příkazy SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi z platforem Mac, Ubuntu Linux a Windows. 

V tomto článku se předpokládá, že máte zkušenosti s vývojem pomocí Pythonu, ale začínáte pracovat s Azure Database for MySQL flexibilním serverem.

## <a name="prerequisites"></a>Předpoklady

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Azure Database for MySQL flexibilní Server. Pokud chcete vytvořit flexibilní Server, přečtěte si téma [vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure Portal](./quickstart-create-server-portal.md) nebo [Vytvoření Azure Database for MySQL flexibilního serveru pomocí Azure CLI](./quickstart-create-server-cli.md).

## <a name="preparing-your-client-workstation"></a>Příprava pracovní stanice klienta
- Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru. Přečtěte si téma [Vytvoření a správa Azure Database for MySQL flexibilní serverové virtuální sítě pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru. Přečtěte si téma [Vytvoření a správa Azure Database for MySQL flexibilních pravidel brány firewall serveru pomocí Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-python-and-the-mysql-connector"></a>Instalace Pythonu a konektoru MySQL

Nainstalujte Python a konektor MySQL pro Python do počítače pomocí následujících kroků: 

> [!NOTE]
> V tomto rychlém startu se k připojení k MySQL používá nezpracovaný přístup k dotazu SQL. Pokud používáte webové rozhraní, použijte doporučený konektor pro rozhraní, například [mysqlclient](https://pypi.org/project/mysqlclient/) pro Django.

1. Stáhněte a nainstalujte [Python 3,7 nebo novější](https://www.python.org/downloads/) pro váš operační systém. Nezapomeňte přidat Python k vašemu `PATH` , protože konektor MySQL vyžaduje.
   
1. Otevřete příkazový řádek nebo `bash` prostředí a podívejte se na verzi Pythonu spuštěním příkazu `python -V` s přepínačem Velká v.
   
1. `pip`Instalační program balíčku je zahrnutý v nejnovějších verzích Pythonu. Aktualizujte `pip` na nejnovější verzi spuštěním `pip install -U pip` . 
   
   Pokud `pip` není nainstalovaný, můžete si ho stáhnout a nainstalovat pomocí `get-pip.py` . Další informace najdete v tématu [instalace](https://pip.pypa.io/en/stable/installing/). 
   
1. Použijte `pip` k instalaci konektoru MySQL pro Python a jeho závislostí:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   Konektor Pythonu pro MySQL můžete také nainstalovat z [MySQL.com](https://dev.mysql.com/downloads/connector/python/). Další informace o konektoru MySQL pro Python najdete v tématu [Příručka pro vývojáře MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace o připojení, které potřebujete k připojení Azure Database for MySQL flexibilního serveru z Azure Portal. Potřebujete název serveru, název databáze a přihlašovací údaje pro přihlášení.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
   
1. Na panelu hledání na portálu vyhledejte a vyberte Azure Database for MySQL flexibilní Server, který jste vytvořili, například **mydemoserver**.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. Na stránce **Přehled** serveru si poznamenejte **název serveru** a **přihlašovací jméno správce serveru**. Pokud zapomenete heslo, můžete také resetovat heslo z této stránky.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Ukázky kódů

### <a name="run-below-mentioned-python-code-samples"></a>Spusťte níže uvedené ukázky kódu Pythonu.
Pro každý příklad kódu v tomto článku:

1. Vytvořte nový soubor v textovém editoru.
1. Přidejte do souboru příklad kódu. V kódu nahraďte `<mydemoserver>` `<myadmin>` `<mypassword>` `<mydatabase>` zástupné symboly,, a hodnotami pro server MySQL a databázi.
1. Uložte soubor do složky projektu s příponou *. py* , jako je například *C:\pythonmysql\createtable.py* nebo */home/username/pythonmysql/CreateTable.py*.
1. Chcete-li spustit kód, otevřete příkazový řádek nebo `bash` prostředí a změňte adresář na složku projektu, například `cd pythonmysql` . Zadejte `python` příkaz následovaný názvem souboru, například `python createtable.py` a stiskněte klávesu ENTER. 
   
   > [!NOTE]
   > Pokud se v systému Windows nenajde *python.exe* , možná budete muset přidat cestu Pythonu do proměnné prostředí PATH nebo zadat úplnou cestu pro *python.exe*, například `C:\python27\python.exe createtable.py` .

### <a name="create-a-table-and-insert-data"></a>Vytvoření tabulky a vložení dat

Pomocí následujícího kódu se připojte k serveru a databázi, vytvořte tabulku a načtěte data pomocí příkazu **INSERT** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k flexibilnímu serveru pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.exeroztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="read-data"></a>Čtení dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k flexibilnímu serveru pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.exeroztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

Kód přečte řádky dat pomocí metody [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , zachovává sadu výsledků dotazu v řádku kolekce a pomocí `for` iterátoru přeskočí řádky.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="update-data"></a>Aktualizace dat

Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k flexibilnímu serveru pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.exeroztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="delete-data"></a>Odstranění dat

Pomocí následujícího kódu se připojte a odeberte data s využitím příkazu **DELETE** jazyka SQL. 

Kód importuje knihovnu MySQL. Connector a pomocí funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) se připojí k flexibilnímu serveru pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. Kód používá kurzor na připojení a metoda [cursor.exeroztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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
- [Šifrované připojení pomocí TLS 1,2 (Transport Layer Security) v Azure Database for MySQL-flexibilním serveru](./how-to-connect-tls-ssl.md).
- Přečtěte si další informace o [sítích v Azure Database for MySQL flexibilním serveru](./concepts-networking.md).
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní pravidla brány firewall serveru pomocí Azure Portal](./how-to-manage-firewall-portal.md).
- [Vytvářejte a spravujte Azure Database for MySQL flexibilní serverovou virtuální síť pomocí Azure Portal](./how-to-manage-virtual-network-portal.md).
