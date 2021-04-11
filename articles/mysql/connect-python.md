---
title: 'Rychlý Start: připojení pomocí Pythonu-Azure Database for MySQL'
description: V tomto rychlém startu najdete vzorový kód Pythonu, který můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: a4391ecb7175b0e473b47cc3de43fd113795bc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889021"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití Pythonu k připojení a dotazování dat v Azure Database for MySQL

V tomto rychlém startu se připojíte k Azure Database for MySQL pomocí Pythonu. Pak použijete příkazy SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi z platforem Mac, Ubuntu Linux a Windows. 

## <a name="prerequisites"></a>Požadavky
Pro tento rychlý Start budete potřebovat:

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free).
- Vytvoření jednoho serveru Azure Database for MySQL pomocí [Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> nebo [Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md) , pokud ho ještě nemáte.
- Na základě toho, jestli používáte veřejný nebo privátní přístup, proveďte **jednu** z následujících akcí, aby se povolilo připojení.

   |Akce| Metoda připojení|Praktičtí průvodci|
   |:--------- |:--------- |:--------- |
   | **Konfigurace pravidel brány firewall** | Veřejná | [Azure Portal](./howto-manage-firewall-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-firewall-using-cli.md)|
   | **Konfigurace koncového bodu služby** | Veřejná | [Azure Portal](./howto-manage-vnet-using-portal.md) <br/> [Rozhraní příkazového řádku](./howto-manage-vnet-using-cli.md)| 
   | **Konfigurace privátního odkazu** | Privátní | [Azure Portal](./howto-configure-privatelink-portal.md) <br/> [Rozhraní příkazového řádku](./howto-configure-privatelink-cli.md) | 

- [Vytvoření databáze a uživatele bez role správce](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Instalace Pythonu a konektoru MySQL

Nainstalujte Python a konektor MySQL pro Python do počítače pomocí následujících kroků: 

> [!NOTE]
> Tento rychlý Start používá [příručku pro vývojáře konektoru MySQL/Python](https://dev.mysql.com/doc/connector-python/en/).

1. Stáhněte a nainstalujte [Python 3,7 nebo novější](https://www.python.org/downloads/) pro váš operační systém. Nezapomeňte přidat Python k vašemu `PATH` , protože konektor MySQL vyžaduje.
   
2. Otevřete příkazový řádek nebo `bash` prostředí a podívejte se na verzi Pythonu spuštěním příkazu `python -V` s přepínačem Velká v.
   
3. `pip`Instalační program balíčku je zahrnutý v nejnovějších verzích Pythonu. Aktualizujte `pip` na nejnovější verzi spuštěním `pip install -U pip` . 
   
   Pokud `pip` není nainstalovaný, můžete si ho stáhnout a nainstalovat pomocí `get-pip.py` . Další informace najdete v tématu [instalace](https://pip.pypa.io/en/stable/installing/). 
   
4. Použijte `pip` k instalaci konektoru MySQL pro Python a jeho závislostí:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Získání informací o připojení

Získejte informace o připojení, které potřebujete k připojení k Azure Database for MySQL z Azure Portal. Potřebujete název serveru, název databáze a přihlašovací údaje pro přihlášení.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
   
1. Na panelu hledání na portálu vyhledejte a vyberte server Azure Database for MySQL, který jste vytvořili, například **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Název serveru Azure Database for MySQL":::
   
1. Na stránce **Přehled** serveru si poznamenejte **název serveru** a **přihlašovací jméno správce serveru**. Pokud zapomenete heslo, můžete také resetovat heslo z této stránky.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Název serveru Azure Database for MySQL 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Krok 1: vytvoření tabulky a vložení dat

Pomocí následujícího kódu se připojte k serveru a databázi, vytvořte tabulku a načtěte data pomocí příkazu **INSERT** jazyka SQL. Kód importuje knihovnu MySQL. Connector a používá metodu:
- funkce [Connect ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) slouží k připojení k Azure Database for MySQL pomocí [argumentů](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) v kolekci konfigurace. 
- Metoda [cursor.exeroztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) provádí dotaz SQL na databázi MySQL. 
- [Cursor. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) , když jste hotovi pomocí kurzoru.
- [připojeno. Close ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) pro ukončení připojení připojení.

> [!IMPORTANT]
> - Protokol SSL je ve výchozím nastavení povolený. Možná budete muset stáhnout [certifikát SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) pro připojení z místního prostředí.
> - `<mydemoserver>`Zástupné znaky,, a nahraďte `<myadmin>` `<mypassword>` `<mydatabase>` hodnotami pro server MySQL a databázi.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [mysql.connector.ClientFlag.SSL],
  'ssl_ca': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
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

[Máte problémy? Dejte nám prosím jistotu](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Krok 2: čtení dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. Kód importuje knihovnu MySQL. Connector a používá [cursor.exemetoda roztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) SPUSTÍ dotaz SQL na databázi MySQL. 

Kód přečte řádky dat pomocí metody [fetchall ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html) , zachovává sadu výsledků dotazu v řádku kolekce a pomocí `for` iterátoru přeskočí řádky.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Krok 3: aktualizace dat

Pomocí následujícího kódu se připojte a aktualizujte data s využitím příkazu **UPDATE** jazyka SQL. Kód importuje knihovnu MySQL. Connector a používá [cursor.exemetoda roztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) SPUSTÍ dotaz SQL na databázi MySQL. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Krok 4: odstranění dat

Pomocí následujícího kódu se připojte a odeberte data s využitím příkazu **DELETE** jazyka SQL. Kód importuje knihovnu MySQL. Connector a používá [cursor.exemetoda roztomilá ()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) SPUSTÍ dotaz SQL na databázi MySQL. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [Správa serveru Azure Database for MySQL pomocí rozhraní příkazového řádku](./how-to-manage-single-server-cli.md)

[Nemůžete najít, co hledáte? Dejte nám prosím jistotu.](https://aka.ms/mysql-doc-feedback)
