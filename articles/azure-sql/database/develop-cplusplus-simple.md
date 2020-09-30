---
title: Připojení k SQL Database pomocí jazyka C a C++
description: Vzorový kód v tomto rychlém startu použijte k vytvoření moderní aplikace v jazyce C++ a zajištěné pomocí výkonné relační databáze v cloudu s Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: 38e4839a41ad8e58e575e552e877303a5105ff36
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91443659"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Připojení k SQL Database pomocí jazyka C a C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento příspěvek je zaměřený na vývojáře v jazyce C a C++, kteří se pokoušejí připojit k Azure SQL Database. Rozdělí se na oddíly, takže můžete přejít na oddíl, který nejlépe zaznamená váš zájem.

## <a name="prerequisites-for-the-cc-tutorial"></a>Předpoklady pro kurz pro C/C++

Ujistěte se, že máte následující položky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Chcete-li sestavit a spustit tuto ukázku, je nutné nainstalovat komponenty jazyka C++.
* [Vývoj pro Linux v systému Visual Studio](https://docs.microsoft.com/cpp/linux/?view=vs-2019). Pokud vyvíjíte v systému Linux, je nutné také nainstalovat rozšíření sady Visual Studio Linux.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database a SQL Server na virtuálních počítačích

Azure SQL Database je postavená na Microsoft SQL Server a je navržená tak, aby poskytovala vysokou dostupnost, výkonnou a škálovatelnou službu. Používáte spoustu výhod používání Azure SQL prostřednictvím vaší speciální databáze běžící místně. V případě Azure SQL nemusíte instalovat, nastavovat, udržovat nebo spravovat vaši databázi, ale jenom obsah a strukturu databáze. Mezi běžné věci, které se obáváme s databázemi, jako je odolnost proti chybám a redundance, jsou integrované.

Azure má v současné době pro hostování úloh SQL serveru dvě možnosti: Azure SQL Database, databáze jako služba a SQL Server v Virtual Machines (VM). Nebudeme se k podrobnostem rozdílů mezi těmito dvěma výjimkami zobrazovat, protože Azure SQL Database nejlepším řešením pro nové cloudové aplikace, které využívají úspory nákladů a optimalizaci výkonu, které poskytují cloudové služby. Pokud zvažujete migraci nebo rozšiřování místních aplikací do cloudu, může být pro vás vhodnější SQL Server na virtuálním počítači Azure. Abychom vám pomohli něco jednoduchého pro tento článek, vytvoříme Azure SQL Database.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologie pro přístup k datům: ODBC a OLE DB

Připojení k Azure SQL Database není jiné a v současné době existují dva způsoby, jak se připojit k databázím: rozhraní ODBC (Open Database Connectivity) a OLE DB (objekt propojování a vkládání databáze). V posledních letech společnost Microsoft zarovnala [rozhraní ODBC pro nativní přístup k relačním datům](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). Rozhraní ODBC je relativně jednoduché a také mnohem rychlejší než OLE DB. Jediným aspektem upozornění je, že rozhraní ODBC používá staré rozhraní API ve stylu jazyka C.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Krok 1: vytvoření Azure SQL Database

Informace o tom, jak vytvořit ukázkovou databázi, najdete na [stránce Začínáme](single-database-create-quickstart.md) .  Alternativně můžete pomocí tohoto [krátkého e-](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) mailového videa vytvořit Azure SQL Database pomocí Azure Portal.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Krok 2: získání připojovacího řetězce

Po zřízení Azure SQL Database musíte provést následující kroky, abyste zjistili informace o připojení a přidali IP adresu klienta pro přístup k bráně firewall.

V [Azure Portal](https://portal.azure.com/)do Azure SQL Database připojovacího řetězce ODBC použijte připojovací **řetězce pro zobrazení databáze** uvedené jako součást přehledu pro vaši databázi:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Zkopírujte obsah **rozhraní ODBC (zahrnuje Node.js) řetězec [ověřování SQL]** . Tento řetězec používáme později pro připojení z našeho překladače příkazového řádku C++ ODBC. Tento řetězec poskytuje podrobnosti, jako jsou například ovladače, server a další parametry připojení databáze.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Krok 3: Přidání IP adresy do brány firewall

V části Brána firewall pro váš server přidejte [IP adresu klienta k bráně firewall pomocí těchto kroků](firewall-configure.md) , abyste se ujistili, že je možné navázat úspěšné připojení:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

V tomto okamžiku jste nakonfigurovali Azure SQL Database a jste připraveni se připojit z kódu jazyka C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Krok 4: připojení z aplikace Windows C/C++

Pomocí této ukázky, která se používá v aplikaci Visual Studio, se můžete snadno připojit k vašemu [Azure SQL Database pomocí rozhraní ODBC ve Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) . Ukázka implementuje překladač příkazového řádku ODBC, který se dá použít pro připojení k naší Azure SQL Database. Tato ukázka přebírá jako argument příkazového řádku buď soubor názvu zdroje databáze (DSN), nebo podrobný připojovací řetězec, který jste zkopírovali dříve z Azure Portal. Zobrazte stránku vlastností tohoto projektu a vložte připojovací řetězec jako argument příkazu, jak je znázorněno zde:

![Propsfile DSN](./media/develop-cplusplus-simple/props.png)

Ujistěte se, že jste zadali správné ověřovací údaje pro vaši databázi jako součást databázového připojovacího řetězce.

Spusťte aplikaci a sestavte ji. Mělo by se zobrazit následující okno ověřování úspěšného připojení. Můžete dokonce spustit některé základní příkazy SQL jako **Create Table** k ověření připojení k databázi:

![Příkazy SQL](./media/develop-cplusplus-simple/sqlcommands.png)

Případně můžete vytvořit soubor DSN pomocí průvodce, který se spustí, když nejsou k dispozici žádné argumenty příkazu. Doporučujeme vyzkoušet také tuto možnost. Tento soubor DSN můžete použít pro automatizaci a ochranu nastavení ověřování:

![Vytvořit soubor DSN](./media/develop-cplusplus-simple/datasource.png)

Gratulujeme! Úspěšně jste se připojili k Azure SQL pomocí C++ a ODBC ve Windows. Můžete pokračovat v čtení, aby to bylo stejné i pro Linux Platform.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Krok 5: připojení z linuxové aplikace C/C++

V případě, že jste zprávy ještě neslyšeli, Visual Studio teď umožňuje vyvíjet i aplikaci C++ Linux. O tomto novém scénáři si můžete přečíst na blogu [Visual C++ pro vývoj pro Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . K sestavení pro Linux budete potřebovat vzdálený počítač, ve kterém je spuštěný distribuce se systémem Linux. Pokud nemáte k dispozici žádný obsah, můžete ho rychle nastavit pomocí [virtuálních počítačů Azure s Linux](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pro účely tohoto kurzu můžeme předpokládat, že máte nastavenou distribuci Ubuntu 16,04 Linux. Tento postup by se měl vztahovat taky na Ubuntu 15,10, Red Hat 6 a Red Hat 7.

Následující postup nainstaluje knihovny potřebné pro SQL a ODBC pro distribuce:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Spusťte Visual Studio. V nabídce nástroje – > možnosti – > Správce připojení pro různé platformy > přidejte připojení do pole Linux:

![Možnosti nástrojů](./media/develop-cplusplus-simple/tools.png)

Po navázání připojení přes SSH vytvořte prázdnou šablonu projektu (Linux):

![Nová šablona projektu](./media/develop-cplusplus-simple/template.png)

Pak můžete přidat [nový zdrojový soubor C a nahradit ho tímto obsahem](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Pomocí rozhraní ODBC API SQLAllocHandle, SQLSetConnectAttr a SQLDriverConnect byste měli být schopni inicializovat a navázat připojení k databázi.
Podobně jako u ukázky rozhraní ODBC systému Windows je třeba nahradit volání SQLDriverConnect podrobnostmi z parametrů připojovacího řetězce databáze zkopírovaných z Azure Portal dříve.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

Poslední věc, kterou je třeba provést před kompilací, je přidat **rozhraní ODBC** jako závislost knihovny:

![Přidání rozhraní ODBC jako vstupní knihovny](./media/develop-cplusplus-simple/lib.png)

Chcete-li spustit aplikaci, přepněte konzolu Linux z nabídky **ladění** :

![Konzola Linux](./media/develop-cplusplus-simple/linuxconsole.png)

Pokud bylo připojení úspěšné, měl by se teď zobrazit aktuální název databáze vytištěný v konzole pro Linux:

![Výstup okna konzoly pro Linux](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Gratulujeme! Úspěšně jste dokončili kurz a můžete se teď připojit k Azure SQL Database z C++ na platformách Windows a Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Získání kompletního řešení kurzu pro C/C++

Řešení getstarted, které obsahuje všechny ukázky v tomto článku, najdete na GitHubu:

* [Ukázka ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), stažení ukázky Windows C++ ODBC pro připojení k Azure SQL
* [Ukázka ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Stáhněte si ukázku pro Linux c++ ODBC pro připojení k Azure SQL

## <a name="next-steps"></a>Další kroky

* Přečtěte si téma [Přehled vývoje SQL Database](develop-overview.md) .
* Další informace o [referenci rozhraní API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Další zdroje informací

* [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](saas-tenancy-app-design-patterns.md)
* Prozkoumejte všechny [schopnosti služby SQL Database](https://azure.microsoft.com/services/sql-database/).
