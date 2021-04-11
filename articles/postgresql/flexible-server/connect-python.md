---
title: 'Rychlý Start: připojení pomocí Pythonu-Azure Database for PostgreSQL-flexibilního serveru'
description: V tomto rychlém startu najdete několik ukázek kódu Pythonu, které můžete použít k připojení a dotazování dat z Azure Database for PostgreSQL-flexibilního serveru.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 823dd6fc64da2c4900f5a0a35a6cd27a9a2b43ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227480"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Rychlý Start: použití Pythonu k připojení a dotazování dat v Azure Database for PostgreSQL-flexibilním serveru

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibilní Server je ve verzi Preview.

V tomto rychlém startu se připojíte k Azure Database for PostgreSQL flexibilnímu serveru pomocí Pythonu. Pak použijete příkazy SQL k dotazování, vkládání, aktualizaci a odstraňování dat v databázi z platforem Mac, Ubuntu Linux a Windows. 

V tomto článku se předpokládá, že máte zkušenosti s vývojem pomocí Pythonu, ale začínáte pracovat se Azure Database for PostgreSQLm flexibilním serverem.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Azure Database for PostgreSQL flexibilní Server. Pokud chcete vytvořit flexibilní Server, přečtěte si téma [vytvoření Azure Database for PostgreSQL-flexibilního serveru pomocí Azure Portal](./quickstart-create-server-portal.md).
* [Python](https://www.python.org/downloads/) 2,7 nebo 3.6 +.
* Poslední instalační program balíčku [PIP](https://pip.pypa.io/en/stable/installing/)

## <a name="preparing-your-client-workstation"></a>Příprava pracovní stanice klienta
- Pokud jste vytvořili flexibilní Server s *privátním přístupem (Integration VNET)*, budete se muset připojit k serveru z prostředku ve stejné virtuální síti jako váš server. Můžete vytvořit virtuální počítač a přidat ho do virtuální sítě vytvořené pomocí flexibilního serveru. Další informace najdete [v tématu Vytvoření a správa Azure Database for PostgreSQL – flexibilní serverová virtuální síť pomocí Azure CLI](./how-to-manage-virtual-network-cli.md).
- Pokud jste vytvořili flexibilní Server s *veřejným přístupem (povolenými IP adresami)*, můžete přidat místní IP adresu do seznamu pravidel brány firewall na serveru. Přečtěte si téma [Vytvoření a Správa pravidel brány firewall serveru Azure Database for PostgreSQL – flexibilní pomocí Azure CLI](./how-to-manage-firewall-cli.md).

## <a name="install-the-python-libraries-for-postgresql"></a>Instalace knihoven Pythonu pro PostgreSQL
Modul [psycopg2](https://pypi.python.org/pypi/psycopg2/) umožňuje připojení k databázi PostgreSQL a dotazování na ni a je k dispozici jako balíček pro Linux, MacOS nebo Windows [kolo](https://pythonwheels.com/) . Nainstalujte binární verzi modulu včetně všech závislostí. Další informace o `psycopg2` instalaci a požadavcích najdete v tématu [instalace](http://initd.org/psycopg/docs/install.html). 

Chcete-li provést instalaci `psycopg2` , otevřete terminál nebo příkazový řádek a spusťte příkaz `pip install psycopg2` .

## <a name="get-database-connection-information"></a>Získat informace o připojení databáze
Připojení k Azure Database for PostgreSQL flexibilnímu serveru vyžaduje plně kvalifikovaný název serveru a přihlašovací údaje. Tyto informace můžete získat z Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)vyhledejte a vyberte flexibilní název serveru. 
2. Na stránce **Přehled** serveru zkopírujte plně kvalifikovaný **název serveru** a **uživatelské jméno správce**. Plně kvalifikovaný **název serveru** je vždy ve tvaru *\<my-server-name> . Postgres.Database.Azure.com*.

   Budete také potřebovat heslo správce. Pokud ho zapomenete, můžete ho resetovat na stránce s přehledem. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Jak spustit příklady Pythonu

Pro každý příklad kódu v tomto článku:

1. Vytvořte nový soubor v textovém editoru. 

1. Přidejte do souboru příklad kódu. V kódu nahraďte:
   - `<server-name>` a `<admin-username>` s hodnotami, které jste zkopírovali z Azure Portal.
   - `<admin-password>` se svým heslem na serveru.
   - `<database-name>` s názvem vaší Azure Database for PostgreSQLově flexibilní serverové databáze. Výchozí databáze s názvem *Postgres* byla automaticky vytvořena při vytvoření serveru. Tuto databázi můžete přejmenovat nebo vytvořit novou databázi pomocí příkazů SQL. 

1. Uložte soubor do složky vašeho projektu s příponou *. py* , například *Postgres-INSERT.py*. Pro Windows se ujistěte, že je při ukládání souboru vybraný kódování UTF-8. 

1. Chcete-li spustit soubor, přejděte do složky vašeho projektu v rozhraní příkazového řádku a zadejte `python` název následovaný názvem souboru, například `python postgres-insert.py` .

## <a name="create-a-table-and-insert-data"></a>Vytvoření tabulky a vložení dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQLově flexibilní databázi serveru pomocí funkce [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html) a načítá data pomocí příkazu SQL **INSERT** . Funkce [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) provádí dotaz SQL na databázi. 

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

![Výstup příkazového řádku](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Čtení dat
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL flexibilní databázi serveru a používá [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Select** ke čtení dat. Tato funkce přijme dotaz a vrátí sadu výsledků pro iteraci pomocí [Cursor. fetchall ()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
Následující příklad kódu se připojuje k vaší Azure Database for PostgreSQL flexibilní databázi serveru a používá [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Update** k aktualizaci dat. 

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
Následující příklad kódu se připojí k vaší Azure Database for PostgreSQLově flexibilní databázi serveru a pomocí příkazu [cursor.exeroztomilá](http://initd.org/psycopg/docs/cursor.html#execute) s příkazem SQL **Delete** odstraní položku inventáře, kterou jste předtím vložili. 

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
> [Migrace databáze pomocí výpisu a obnovení](../howto-migrate-using-dump-and-restore.md)
