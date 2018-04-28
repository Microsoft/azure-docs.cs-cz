---
title: Připojení k Azure Database for MySQL z jazyka C++
description: V tomto rychlém startu najdete vzorový kód jazyka C++, který můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.devlang: C++
ms.topic: quickstart
ms.date: 04/12/2018
ms.openlocfilehash: 8394fbf5146a268bad464dc1a11d0772359c9acb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-database-for-mysql-use-connectorc-to-connect-and-query-data"></a>Azure Database for MySQL: Připojení a dotazování dat pomocí konektoru Connector/C++
Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL s použitím aplikace v C++. Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. Toto téma předpokládá, že máte zkušenosti s vývojem pomocí C++ a teprve začínáte pracovat se službou Azure Database for MySQL.

## <a name="prerequisites"></a>Požadavky
Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z následujících průvodců:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Budete také muset:
- Nainstalovat [.NET Framework](https://www.microsoft.com/net/download)
- Nainstalovat [Visual Studio](https://www.visualstudio.com/downloads/)
- Nainstalovat [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Nainstalovat [Boost](http://www.boost.org/)

## <a name="install-visual-studio-and-net"></a>Instalace sady Visual Studio a .NET
Kroky v této části předpokládají, že máte zkušenosti s vývojem pomocí rozhraní .NET.

### <a name="windows"></a>**Windows**
- Nainstalujte sadu Visual Studio 2017 Community, což je plně vybavené, rozšiřitelné a bezplatné integrované vývojové prostředí (IDE) pro vytváření moderních aplikací pro Android, iOS a Windows, stejně jako webových a databázových aplikací a cloudových služeb. Můžete nainstalovat úplné rozhraní .NET Framework nebo jenom .NET Core: fragmenty kódu v tomto rychlém startu fungují s oběma. Pokud už máte v počítači nainstalovanou sadu Visual Studio, přeskočte následující dva kroky.
   1. Stáhněte si [instalační program sady Visual Studio 2017](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Spusťte instalační program a podle zobrazených pokynů instalaci dokončete.

### <a name="configure-visual-studio"></a>**Konfigurace sady Visual Studio**
1. V sadě Visual Studio v části Projekt -> Vlastnosti -> Linker > Obecné > Další adresáře knihoven přidejte adresář \lib\opt (tj.: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\lib\opt) konektoru C++.
2. V sadě Visual Studio v části Projekt -> Vlastnosti -> C/C++ > Obecné -> Další adresáře k zahrnutí:
   - Přidejte adresář \include konektoru C++ (tj.: C:\Program Files (x86)\MySQL\MySQL Connector C++ 1.1.9\include\).
   - Přidejte kořenový adresář knihovny Boost (tj.: C:\boost_1_64_0\).
3. V sadě Visual Studio v části Projekt -> Vlastnosti -> Linker -> Vstup -> Další závislosti přidejte do textového pole **mysqlcppconn.lib**.
4. Zkopírujte soubor **mysqlcppconn.dll** ze složky knihovny konektoru C++ z kroku 3 do stejného adresáře jako spustitelný soubor aplikace nebo ho přidejte do proměnné prostředí, aby ho vaše aplikace mohla najít.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver**).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 ![Název serveru Azure Database for MySQL](./media/connect-cpp/1_server-overview-name-login.png)

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat
Pomocí následujícího kódu se připojte a nahrajte data s využitím příkazů **CREATE TABLE** a **INSERT INTO** jazyka SQL. Tento kód pro navázání připojení k MySQL využívá třídu sql::Driver s metodou connect(). Potom kód použije metody createStatement() a execute() pro spuštění příkazů databáze. 

Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::Statement *stmt;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    //please create database "quickstartdb" ahead of time
    con->setSchema("quickstartdb");

    stmt = con->createStatement();
    stmt->execute("DROP TABLE IF EXISTS inventory");
    cout << "Finished dropping table (if existed)" << endl;
    stmt->execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
    cout << "Finished creating table" << endl;
    delete stmt;

    pstmt = con->prepareStatement("INSERT INTO inventory(name, quantity) VALUES(?,?)");
    pstmt->setString(1, "banana");
    pstmt->setInt(2, 150);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "orange");
    pstmt->setInt(2, 154);
    pstmt->execute();
    cout << "One row inserted." << endl;

    pstmt->setString(1, "apple");
    pstmt->setInt(2, 100);
    pstmt->execute();
    cout << "One row inserted." << endl;

    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="read-data"></a>Čtení dat

Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **SELECT** jazyka SQL. Tento kód pro navázání připojení k MySQL využívá třídu sql::Driver s metodou connect(). Potom kód použije metody prepareStatement() a executeQuery() pro spuštění příkazů select. Dále kód použije metodu next() k přechodu na záznamy ve výsledcích. Nakonec kód použije metody getInt() a getString() k parsování hodnot v záznamu.

Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }

    con->setSchema("quickstartdb");

    //select  
    pstmt = con->prepareStatement("SELECT * FROM inventory;");
    result = pstmt->executeQuery();

    while (result->next())
        printf("Reading from table=(%d, %s, %d)\n", result->getInt(1), result->getString(2).c_str(), result->getInt(3));

    delete result;
    delete pstmt;
    delete con;
    system("pause");
    return 0;
}
```

## <a name="update-data"></a>Aktualizace dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **UPDATE** jazyka SQL. Tento kód pro navázání připojení k MySQL využívá třídu sql::Driver s metodou connect(). Potom kód použije metody prepareStatement() a executeQuery() pro spuštění příkazů update. 

Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");

    //update
    pstmt = con->prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?");
    pstmt->setInt(1, 200);
    pstmt->setString(2, "banana");
    pstmt->executeQuery();
    printf("Row updated\n");

    delete con;
    delete pstmt;
    system("pause");
    return 0;
}
```


## <a name="delete-data"></a>Odstranění dat
Pomocí následujícího kódu se připojte a načtěte data s využitím příkazu **DELETE** jazyka SQL. Tento kód pro navázání připojení k MySQL využívá třídu sql::Driver s metodou connect(). Potom kód použije metody prepareStatement() a executeQuery() pro spuštění příkazů delete.

Nahraďte parametry Host (hostitel), DBName (název databáze), User (uživatel) a Password (heslo) hodnotami, které jste zadali při vytváření vlastního serveru a databáze. 

```c++
#include <stdlib.h>
#include <iostream>
#include "stdafx.h"

#include "mysql_connection.h"
#include <cppconn/driver.h>
#include <cppconn/exception.h>
#include <cppconn/resultset.h>
#include <cppconn/prepared_statement.h>
using namespace std;

//for demonstration only. never save your password in the code!
const string server = "tcp://yourservername.mysql.database.azure.com:3306";
const string username = "username@servername";
const string password = "yourpassword";

int main()
{
    sql::Driver *driver;
    sql::Connection *con;
    sql::PreparedStatement *pstmt;
    sql::ResultSet *result;

    try
    {
        driver = get_driver_instance();
        //for demonstration only. never save password in the code!
        con = driver->connect(server, username, password);
    }
    catch (sql::SQLException e)
    {
        cout << "Could not connect to server. Error message: " << e.what() << endl;
        system("pause");
        exit(1);
    }
    
    con->setSchema("quickstartdb");
        
    //delete
    pstmt = con->prepareStatement("DELETE FROM inventory WHERE name = ?");
    pstmt->setString(1, "orange");
    result = pstmt->executeQuery();
    printf("Row deleted\n");    
    
    delete pstmt;
    delete con;
    delete result;
    system("pause");
    return 0;
}
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.](concepts-migrate-dump-restore.md)
