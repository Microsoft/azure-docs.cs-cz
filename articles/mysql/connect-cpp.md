---
title: 'Rychlý Start: připojení pomocí C++-Azure Database for MySQL'
description: V tomto rychlém startu najdete vzorový kód jazyka C++, který můžete použít k připojení a dotazování dat ze služby Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.devlang: cpp
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: 28957235dc5c90beebf834101a481187909ab6bb
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332215"
---
# <a name="quickstart-use-connectorc-to-connect-and-query-data-in-azure-database-for-mysql"></a>Rychlý Start: použití konektoru/C++ k připojení a dotazování dat v Azure Database for MySQL

Tento rychlý start ukazuje, jak se připojit ke službě Azure Database for MySQL s použitím aplikace v C++. Ukazuje, jak pomocí příkazů jazyka SQL dotazovat, vkládat, aktualizovat a odstraňovat data v databázi. V tomto tématu se předpokládá, že máte zkušenosti s vývojem pomocí jazyka C++ a začínáte pracovat s Azure Database for MySQL.

## <a name="prerequisites"></a>Předpoklady

Tento rychlý start využívá jako výchozí bod prostředky vytvořené v některém z následujících průvodců:
- [Vytvoření serveru Azure Database for MySQL pomocí webu Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Vytvoření serveru Azure Database for MySQL pomocí Azure CLI](./quickstart-create-mysql-server-database-using-azure-cli.md)

Budete také muset:
- Nainstalovat [.NET Framework](https://www.microsoft.com/net/download)
- Nainstalovat [Visual Studio](https://www.visualstudio.com/downloads/)
- Nainstalovat [MySQL Connector/C++](https://dev.mysql.com/downloads/connector/cpp/) 
- Nainstalovat [Boost](https://www.boost.org/)

> [!IMPORTANT] 
> Ujistěte se, že IP adresa, ze které se připojujete, přidala pravidla brány firewall serveru pomocí [Azure Portal](./howto-manage-firewall-using-portal.md) nebo rozhraní příkazového [řádku Azure CLI](./howto-manage-firewall-using-cli.md) .

## <a name="install-visual-studio-and-net"></a>Instalace sady Visual Studio a .NET
V krocích v této části se předpokládá, že máte zkušenosti s vývojem pomocí rozhraní .NET.

### <a name="windows"></a>**Windows**
- Nainstalujte Visual Studio 2019 Community. Visual Studio 2019 Community je plně vybavené, rozšiřitelné a bezplatné integrované vývojové prostředí (IDE). Pomocí tohoto integrovaného vývojového prostředí (IDE) můžete vytvářet moderní aplikace pro Android, iOS, Windows, webové a databázové aplikace a cloudové služby. Můžete nainstalovat úplné rozhraní .NET Framework nebo jenom .NET Core: fragmenty kódu v tomto rychlém startu fungují s oběma. Pokud už máte v počítači nainstalovanou sadu Visual Studio, přeskočte následující dva kroky.
   1. Stáhněte si [instalační program sady Visual Studio 2019](https://www.visualstudio.com/thank-you-downloading-visual-studio/?sku=Community&rel=15). 
   2. Spusťte instalační program a podle zobrazených pokynů instalaci dokončete.

### <a name="configure-visual-studio"></a>**Konfigurace sady Visual Studio**
1. V aplikaci Visual Studio, vlastnosti projektu >-> linker-> obecné > další adresáře knihoven, přidejte adresář "\lib\opt" (například C:\Program Files (x86) \MySQL\MySQL Connector C++ 1.1.9 \ lib\opt) konektoru C++.
2. V sadě Visual Studio v části Projekt -> Vlastnosti -> C/C++ > Obecné -> Další adresáře k zahrnutí:
   - Přidejte adresář "\include" konektoru c++ (například: C:\Program Files (x86) \MySQL\MySQL Connector C++ 1.1.9 \ include \) .
   - Přidejte kořenový adresář knihovny pro zvýšení úrovně (například: C:\ boost_1_64_0 \) .
3. V sadě Visual Studio v části Projekt -> Vlastnosti -> Linker -> Vstup -> Další závislosti přidejte do textového pole **mysqlcppconn.lib**.
4. Zkopírujte soubor **mysqlcppconn.dll** ze složky knihovny konektoru C++ z kroku 3 do stejného adresáře jako spustitelný soubor aplikace nebo ho přidejte do proměnné prostředí, aby ho vaše aplikace mohla najít.

## <a name="get-connection-information"></a>Získání informací o připojení
Získejte informace o připojení potřebné pro připojení ke službě Azure Database for MySQL. Potřebujete plně kvalifikovaný název serveru a přihlašovací údaje.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. V nabídce vlevo na webu Azure Portal klikněte na **Všechny prostředky** a vyhledejte vytvořený server (například **mydemoserver** ).
3. Klikněte na název serveru.
4. Na panelu **Přehled** serveru si poznamenejte **Název serveru** a **Přihlašovací jméno správce serveru**. Pokud zapomenete své heslo, můžete ho na tomto panelu také resetovat.
 :::image type="content" source="./media/connect-cpp/1_server-overview-name-login.png" alt-text="Název serveru Azure Database for MySQL":::

## <a name="connect-create-table-and-insert-data"></a>Připojení, vytvoření tabulky a vložení dat
Pomocí následujícího kódu se připojte a nahrajte data s využitím příkazů **CREATE TABLE** a **INSERT INTO** jazyka SQL. Tento kód pro navázání připojení k MySQL využívá třídu sql::Driver s metodou connect(). Potom kód použije metody createStatement() a execute() pro spuštění příkazů databáze. 

Nahraďte parametry hostitel, DBName, uživatel a heslo. Parametry můžete nahradit hodnotami, které jste zadali při vytváření serveru a databáze. 

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

Nahraďte parametry hostitel, DBName, uživatel a heslo. Parametry můžete nahradit hodnotami, které jste zadali při vytváření serveru a databáze. 

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

Nahraďte parametry hostitel, DBName, uživatel a heslo. Parametry můžete nahradit hodnotami, které jste zadali při vytváření serveru a databáze. 

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

Nahraďte parametry hostitel, DBName, uživatel a heslo. Parametry můžete nahradit hodnotami, které jste zadali při vytváření serveru a databáze. 

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit všechny prostředky používané v rámci tohoto rychlého startu, odstraňte skupinu prostředků pomocí následujícího příkazu:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Migrace databáze MySQL do služby Azure Database for MySQL pomocí výpisu a obnovení.](concepts-migrate-dump-restore.md)
