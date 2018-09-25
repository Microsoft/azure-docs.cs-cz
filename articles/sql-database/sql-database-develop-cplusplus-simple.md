---
title: Připojení k SQL Database pomocí jazyka C a C++ | Dokumentace Microsoftu
description: Použití ukázkového kódu v tomto rychlém startu k vytváření moderních aplikací s C++ a vztahuje se na výkonné relační databázi v cloudu s Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: caa61b4cecce3f0f4c37a0b945b0ca5a2fc619c1
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063640"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Připojení k SQL Database pomocí jazyka C a C++
Tento příspěvek je zaměřené na vývojáře v C a C++ pokusu o připojení ke službě Azure SQL DB. To je rozdělena do oddílů, můžete přeskočit k části zachycující nejlepších váš zájem. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Předpoklady pro kurz jazyka C/C++
Ujistěte se, že máte následující položky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/). Je nutné nainstalovat komponentami jazyka C++, sestavte a spusťte tuto ukázku.
* [Vývoj Linuxových aplikací v sadě Visual Studio](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Pokud vyvíjíte v systému Linux, je nutné nainstalovat rozšíření Visual Studio Linuxu. 

## <a id="AzureSQL"></a>Azure SQL Database a SQL Server na virtuálních počítačích
Azure SQL je postavená na systému Microsoft SQL Server a je navrženo pro zajištění vysoké dostupnosti, výkonné a škálovatelné služby. Existuje mnoho výhod použití SQL Azure z vaší proprietární databáze spuštěna v místním prostředí. S SQL Azure není nutné instalovat, nastavit, udržovat nebo spravovat vaši databázi, ale pouze obsah a struktuře vaší databáze. Typické věcí, které jsme si starosti s databází, jako je odolnost proti chybám a redundanci, které jsou všechny vytvořené. 

Azure nyní nabízí dvě možnosti pro hostování úloh SQL serveru: Azure SQL database, databáze jako služba a systému SQL server na virtuálních počítačích (VM). Jsme nebude získat podrobnosti o rozdílech mezi těmito dvěma, s tím rozdílem, že Azure SQL database je nejvhodnější pro nové cloudové aplikace výhod úspory nákladů a optimalizace výkonu, které cloudové služby poskytovat. Pokud zvažujete migrace nebo rozšíří vaši místní aplikace do cloudu, SQL server na virtuálním počítači Azure může fungovat lépe za vás. Pro zjednodušení tohoto článku, Pojďme vytvořit databázi Azure SQL. 

## <a id="ODBC"></a>Technologie datových přístupů: rozhraní ODBC a OLE DB
Připojení ke službě Azure SQL DB se nijak neliší a aktuálně existují dva způsoby, jak připojit se k nim: rozhraní ODBC (Open Database connectivity) a technologie OLE DB (propojování a vkládání objektů databáze). Microsoft má v posledních letech, v souladu s [rozhraní ODBC pro přístup k datům nativní relační](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). ODBC je poměrně jednoduchá a také mnohem rychlejší než OLE DB. Pouze výstrahou je, že ODBC používat staré API C-style. 

## <a id="Create"></a>Krok 1: Vytvoření služby Azure SQL Database
Zobrazit [úvodní stránce](sql-database-get-started-portal.md) informace o vytvoření ukázkové databáze.  Alternativně můžete použít tento [krátký dvouminutové video](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) vytvoření Azure SQL database pomocí webu Azure portal.

## <a id="ConnectionString"></a>Krok 2: Získání připojovacího řetězce
Po zřízení Azure SQL database, budete muset provést následující kroky pro určení informací o připojení a přidat IP adresu vašeho klienta pro přístup přes bránu firewall. 

V [webu Azure portal](https://portal.azure.com/), přejděte k vaší databázi Azure SQL ODBC připojovací řetězec s použitím **zobrazit databázové připojovací řetězce** uvedený jako součást oddílu přehled pro vaši databázi: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Zkopírujte obsah **ODBC (obsahuje Node.js) [ověřování SQL]** řetězec. Tento řetězec informace použijete později připojit z příkazového řádku překladač naše C++ ODBC. Tento řetězec obsahuje podrobnosti, jako jsou ovladače, server a další parametry připojení databáze. 

## <a id="Firewall"></a>Krok 3: Přidání vaše IP adresa brány firewall
Přejděte do části brány firewall pro databázový server a přidejte váš [IP adresu klienta do brány firewall pomocí těchto kroků](sql-database-configure-firewall-settings.md) k Ujistěte se, že jsme navázat připojení k úspěšné: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

V tomto okamžiku jste nakonfigurovali Azure SQL DB a jste připraveni připojit z kódu jazyka C++. 

## <a id="Windows"></a>Krok 4: Připojení z aplikace Windows C/C++
Můžete snadno připojit k vaší [službu Azure SQL DB pomocí rozhraní ODBC ve Windows pomocí této ukázky](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) vytvoří pomocí sady Visual Studio. Ukázka implementuje rozhraní ODBC interpretu příkazového řádku, který slouží k připojení k naší databázi SQL Azure. Tato ukázka přijímá název buď soubor databáze název zdrojového souboru (DSN) jako argument příkazového řádku nebo podrobné připojovací řetězec, který jsme dříve zkopírovali z portálu Azure portal. Otevřete stránku vlastnosti pro tento projekt a vložte připojovací řetězec jako argument příkazu, jak je znázorněno zde: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Ujistěte se, že zadáte podrobnosti správné ověřování pro vaši databázi jako součást tento připojovací řetězec databáze. 

Spusťte aplikaci a sestavte ho. Zobrazí se následující okno ověření v případě úspěšného připojení. Můžete dokonce spouštět některé základní příkazy SQL jako **vytvořit tabulku** k ověření připojení k databázi:

![Příkazy jazyka SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternativně můžete vytvořit soubor DSN pomocí průvodce, který se spustí, když nejsou zadány žádné argumenty příkazu. Doporučujeme tuto možnost a zkuste to. Tento soubor název zdroje dat můžete použít pro automatizaci a nastavení ověřování ochrana: 

![Vytvořte soubor DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Blahopřejeme! Teď úspěšně připojily k Azure SQL pomocí jazyka C++ a rozhraní ODBC na Windows. Čtení dělat to samé pro platformu Linux také můžete pokračovat. 

## <a id="Linux"></a>Krok 5: Připojení z aplikace pro Linux C/C++
V případě, že nebyly slyšeli pro zprávy, ale Visual Studio teď umožňuje vyvíjet aplikace pro C++ Linux stejně. Informace o tomto nový scénář v [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blogu. Pokud chcete vytvořit pro Linux, musíte vzdálený počítač se spuštěným systémem vaší distribuce Linuxu. Pokud nemáte k dispozici, můžete vybrat jednu rychle pomocí [Linux Azure Virtual machines](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Pro účely tohoto kurzu Předpokládejme, že máte Ubuntu 16.04 Linuxová distribuce vytvořená nastavení. Zde uvedené kroky by měl platí také pro Ubuntu 15.10, Red Hat 6 a Red Hat 7. 

Následující kroky instalace knihovny potřebné pro SQL a ODBC pro vaše distribuce:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Spusťte sadu Visual Studio. V nabídce Nástroje -> Možnosti -> pro různé platformy -> Správce připojení, přidání připojení do vašeho systému Linux pole: 

![Možnosti nástrojů](./media/sql-database-develop-cplusplus-simple/tools.png)

Po navázání připojení přes protokol SSH, vytvořte šablonu prázdný projekt (Linux): 

![Nová šablona projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Poté můžete přidat [nové C zdrojového souboru a nahraďte ji metodou tento obsah](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Pomocí funkce ODBC API SQLAllocHandle, SQLSetConnectAttr a SQLDriverConnect, byste měli mít k inicializaci a připojení k databázi. Stejně jako s ukázkou Windows ODBC, budete muset nahraďte toto volání SQLDriverConnect si podrobnosti o vaší dříve zkopírovali z portálu Azure portal parametry připojovacího řetězce databáze. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Poslední věcí, kterou provedete před kompilace je přidání **odbc** jako závislost knihovny: 

![Přidání rozhraní ODBC jako vstupní knihovny](./media/sql-database-develop-cplusplus-simple/lib.png)

Ke spuštění vaší aplikace, otevřete konzolu pro Linux z **ladění** nabídky: 

![Konzola Linuxu](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Pokud vaše připojení bylo úspěšné, měli byste vidět název aktuální databáze vytištěn v konzole pro Linux: 

![Výstup okna konzoly systému Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Blahopřejeme! Byly úspěšně dokončeny v kurzu a možné připojit se ke službě Azure SQL DB z jazyka C++ na platformách Windows a Linux.

## <a id="GetSolution"></a>Získání úplného řešení kurzu C/C++
Můžete najít řešení GetStarted, které obsahuje všechny ukázky v tomto článku na githubu:

* [Ukázky Windows C++ ODBC](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), stáhněte si ukázky Windows C++ rozhraní ODBC pro připojení k Azure SQL
* [ODBC C++ Linux ukázka](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), stáhněte si ukázky ODBC C++ Linux pro připojení k Azure SQL

## <a name="next-steps"></a>Další postup
* Zkontrolujte [přehled vývoje SQL Database](sql-database-develop-overview.md)
* Další informace o [Reference k rozhraní API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Další zdroje informací:
* [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Prozkoumejte všechny [schopnosti služby SQL Database](https://azure.microsoft.com/services/sql-database/).

