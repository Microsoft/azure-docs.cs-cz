---
title: Připojení k SQL Database pomocí jazyka C aC++
description: Vzorový kód v tomto rychlém startu použijte k vytvoření moderní aplikace s C++ a zajištěné pomocí výkonné relační databáze v cloudu s Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: fb6094ec418d2b212759bddd2c4d49c7e6193849
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73690700"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Připojení k SQL Database pomocí jazyka C aC++

Tento příspěvek je zaměřený na C C++ a vývojáře, kteří se pokoušejí připojit ke službě Azure SQL DB. Rozdělí se na oddíly, takže můžete přejít na oddíl, který nejlépe zaznamená váš zájem.

## <a name="prerequisites-for-the-cc-tutorial"></a>Předpoklady pro C/C++ kurz

Ujistěte se, že máte následující položky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/). Je nutné nainstalovat C++ jazykové součásti pro sestavení a spuštění této ukázky.
* [Vývoj pro Linux v systému Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Pokud vyvíjíte v systému Linux, je nutné také nainstalovat rozšíření sady Visual Studio Linux.

## <a id="AzureSQL"></a>Azure SQL Database a SQL Server na virtuálních počítačích
Služba Azure SQL je postavená na Microsoft SQL Server a je navržená tak, aby poskytovala vysokou dostupnost, výkonnou a škálovatelnou službu. Používání SQL Azure ve vaší speciální databázi běžící místně přináší spoustu výhod. Při SQL Azure nemusíte instalovat, nastavovat, udržovat nebo spravovat databázi, ale jenom obsah a strukturu databáze. Mezi běžné věci, které se obáváme s databázemi, jako je odolnost proti chybám a redundance, jsou integrované.

Azure má v současné době dvě možnosti hostování úloh SQL serveru: Azure SQL Database, Database as a Service a SQL Server na Virtual Machines (VM). O rozdílech mezi těmito dvěma informacemi se nedostaneme podrobněji, protože Azure SQL Database je nejlepším řešením pro nové cloudové aplikace, které využívají úspory nákladů a optimalizaci výkonu, které poskytují cloudové služby. Pokud zvažujete migraci nebo rozšiřování místních aplikací do cloudu, může být pro vás vhodnější SQL Server na virtuálním počítači Azure. Abychom v tomto článku mohli něco jednoduché, vytvoříme databázi SQL Azure.

## <a id="ODBC"></a>Technologie pro přístup k datům: ODBC a OLE DB
Připojení k databázi SQL Azure se neliší a v současné době existují dva způsoby připojení k databázím: ODBC (připojení Open Database) a OLE DB (objekt propojování a vkládání databáze). V posledních letech společnost Microsoft zarovnala [rozhraní ODBC pro nativní přístup k relačním datům](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). Rozhraní ODBC je relativně jednoduché a také mnohem rychlejší než OLE DB. Jediným aspektem upozornění je, že rozhraní ODBC používá staré rozhraní API ve stylu jazyka C.

## <a id="Create"></a>Krok 1: vytvoření Azure SQL Database
Informace o tom, jak vytvořit ukázkovou databázi, najdete na [stránce Začínáme](sql-database-single-database-get-started.md) .  Případně můžete pomocí tohoto [krátkého dvouletého videa](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) vytvořit databázi SQL Azure pomocí Azure Portal.

## <a id="ConnectionString"></a>Krok 2: získání připojovacího řetězce
Po zřízení databáze SQL Azure je třeba provést následující kroky, abyste zjistili informace o připojení a přidali IP adresu klienta pro přístup k bráně firewall.

V [Azure Portal](https://portal.azure.com/)do svého připojovacího řetězce rozhraní ODBC služby Azure SQL Database použijte připojovací **řetězce** , které jsou uvedené v části Přehled pro vaši databázi:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Zkopírujte obsah řetězce **ODBC (včetně Node. js) [ověřování SQL]** . Tento řetězec používáme později pro připojení z našeho C++ překladače příkazového řádku ODBC. Tento řetězec poskytuje podrobnosti, jako jsou například ovladače, server a další parametry připojení databáze.

## <a id="Firewall"></a>Krok 3: Přidání IP adresy do brány firewall
V části Brána firewall pro váš databázový server přidejte [IP adresu klienta k bráně firewall pomocí těchto kroků](sql-database-configure-firewall-settings.md) , abyste se ujistili, že je možné navázat úspěšné připojení:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

V tuto chvíli jste nakonfigurovali databázi SQL Azure a jste připraveni se připojit z C++ kódu.

## <a id="Windows"></a>Krok 4: připojení z Windows C/C++ aplikace
Pomocí této ukázky, která se používá v aplikaci Visual Studio, se můžete snadno připojit k [databázi SQL Azure pomocí rozhraní ODBC ve Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) . Ukázka implementuje překladač příkazového řádku ODBC, který se dá použít pro připojení k naší službě Azure SQL DB. Tato ukázka přebírá jako argument příkazového řádku buď soubor názvu zdroje databáze (DSN), nebo podrobný připojovací řetězec, který jste zkopírovali dříve z Azure Portal. Zobrazte stránku vlastností tohoto projektu a vložte připojovací řetězec jako argument příkazu, jak je znázorněno zde:

![Propsfile DSN](./media/sql-database-develop-cplusplus-simple/props.png)

Ujistěte se, že jste zadali správné ověřovací údaje pro vaši databázi jako součást databázového připojovacího řetězce.

Spusťte aplikaci a sestavte ji. Mělo by se zobrazit následující okno ověřování úspěšného připojení. Můžete dokonce spustit některé základní příkazy SQL jako **Create Table** k ověření připojení k databázi:

![Příkazy SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Případně můžete vytvořit soubor DSN pomocí průvodce, který se spustí, když nejsou k dispozici žádné argumenty příkazu. Doporučujeme vyzkoušet také tuto možnost. Tento soubor DSN můžete použít pro automatizaci a ochranu nastavení ověřování:

![Vytvořit soubor DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Blahopřejeme! Úspěšně jste se připojili k Azure SQL pomocí C++ a rozhraní ODBC ve Windows. Můžete pokračovat v čtení, aby to bylo stejné i pro Linux Platform.

## <a id="Linux"></a>Krok 5: připojení ze systému Linux C/C++ aplikace
V případě, že jste zprávy ještě neslyšeli, Visual Studio teď umožňuje vyvíjet C++ i aplikace pro Linux. O tomto novém scénáři si můžete přečíst v blogu věnovaném [vývoji Visual C++ pro Linux](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) . K sestavení pro Linux budete potřebovat vzdálený počítač, ve kterém je spuštěný distribuce se systémem Linux. Pokud nemáte k dispozici žádný obsah, můžete ho rychle nastavit pomocí [virtuálních počítačů Azure s Linux](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pro účely tohoto kurzu můžeme předpokládat, že máte nastavenou distribuci Ubuntu 16,04 Linux. Tento postup by se měl vztahovat taky na Ubuntu 15,10, Red Hat 6 a Red Hat 7.

Následující postup nainstaluje knihovny potřebné pro SQL a ODBC pro distribuce:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Spusťte Visual Studio. V nabídce Nástroje – > Možnosti – > Správce připojení pro různé platformy > přidejte připojení do pole Linux:

![Možnosti nástrojů](./media/sql-database-develop-cplusplus-simple/tools.png)

Po navázání připojení přes SSH vytvořte prázdnou šablonu projektu (Linux):

![Nová šablona projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Pak můžete přidat [nový zdrojový soubor C a nahradit ho tímto obsahem](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Pomocí rozhraní ODBC API SQLAllocHandle, SQLSetConnectAttr a SQLDriverConnect byste měli být schopni inicializovat a navázat připojení k databázi.
Podobně jako u ukázky rozhraní ODBC systému Windows je třeba nahradit volání SQLDriverConnect podrobnostmi z parametrů připojovacího řetězce databáze zkopírovaných z Azure Portal dříve.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Poslední věc, kterou je třeba provést před kompilací, je přidat **rozhraní ODBC** jako závislost knihovny:

![Přidání rozhraní ODBC jako vstupní knihovny](./media/sql-database-develop-cplusplus-simple/lib.png)

Chcete-li spustit aplikaci, přepněte konzolu Linux z nabídky **ladění** :

![Konzola Linux](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Pokud bylo připojení úspěšné, měl by se teď zobrazit aktuální název databáze vytištěný v konzole pro Linux:

![Výstup okna konzoly pro Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Blahopřejeme! Úspěšně jste dokončili kurz a teď se můžete připojit k databázi SQL Azure z C++ na platformách Windows a Linux.

## <a id="GetSolution"></a>Získání kompletního řešení CC++ /tutorial
Řešení getstarted, které obsahuje všechny ukázky v tomto článku, najdete na GitHubu:

* [Ukázka C++ ODBC](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29)pro Windows, stažení ukázky C++ Windows ODBC pro připojení k Azure SQL
* [Ukázka C++ ODBC Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), Stáhněte si ukázku C++ rozhraní Linux ODBC pro připojení k Azure SQL

## <a name="next-steps"></a>Další kroky
* Přečtěte si téma [Přehled vývoje SQL Database](sql-database-develop-overview.md) .
* Další informace o [referenci rozhraní API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Další zdroje
* [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Prozkoumejte všechny [schopnosti služby SQL Database](https://azure.microsoft.com/services/sql-database/).

