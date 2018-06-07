---
title: Připojení k databázi SQL pomocí C a C++ | Microsoft Docs
description: Použijte ukázkový kód v této úvodní vytvářet moderní aplikace s C++ a zálohovány výkonné relační databáze v cloudu s Azure SQL Database.
services: sql-database
author: edmacauley
manager: craigg
ms.service: sql-database
ms.custom: develop apps
ms.devlang: cpp
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: edmacauley
ms.openlocfilehash: c37fdaa9f7aa2a0d243fe6cbc175060156967c61
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34644694"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Připojení k databázi SQL pomocí C a C++
Tento příspěvek je zaměřen na vývojáře C a C++ pokusu o připojení k databázi SQL Azure. Ho je rozdělena na oddíly, můžete přejít na oddíl, který nejlépe zaznamená váš zájem. 

## <a name="prerequisites-for-the-cc-tutorial"></a>Předpoklady pro kurz C/C++
Ujistěte se, že máte následující položky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* Sadu [Visual Studio](https://www.visualstudio.com/downloads/). Je nutné nainstalovat součásti jazyk C++ sestavení a spuštění této ukázce.
* [Visual Studio Linux Development](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Pokud vyvíjíte v systému Linux, je nutné nainstalovat rozšíření Visual Studio Linux. 

## <a id="AzureSQL"></a>Azure SQL Database a SQL Server na virtuálních počítačích
Azure SQL je založený na systému Microsoft SQL Server a slouží k zajištění vysoké dostupnosti, původce a škálovatelné služby. Existuje mnoho výhod pomocí SQL Azure přes proprietární databáze spuštěn místně. S SQL Azure nemusíte instalovat, nastavit, provádět údržbu nebo spravovat vaše databáze, ale pouze obsah a struktuře vaší databáze. Typické věcí, které jsme starosti s databázemi jako odolnost proti chybám a redundance jsou všech součástí. 

Azure má aktuálně dvě možnosti pro hostování úloh SQL serveru: Azure SQL database, databáze jako služba a systému SQL server na virtuální počítače (VM). Nám nebude získat podrobnosti o rozdílech mezi těmito dvěma s tím rozdílem, že databáze Azure SQL je nejlepším řešením pro nové cloudové aplikace využívat výhod úsporu nákladů a optimalizace výkonu, které cloudové služby poskytovat. Pokud uvažujete o migraci nebo rozšíření vaše místní aplikace do cloudu, SQL server na virtuální počítač Azure může fungovat lépe za vás. Pro zjednodušení pro tento článek, vytvoříme Azure SQL database. 

## <a id="ODBC"></a>Technologie pro přístup k datům: rozhraní ODBC a OLE DB
Připojení k databázi SQL Azure se neliší a aktuálně existují dva způsoby pro připojení k databázím: rozhraní ODBC (Open Database connectivity) a OLE DB (propojování a vkládání objektů databáze). V posledních letech má Microsoft v souladu s [rozhraní ODBC pro přístup k datům nativní relační](https://blogs.msdn.microsoft.com/sqlnativeclient/2011/08/29/microsoft-is-aligning-with-odbc-for-native-relational-data-access/). Rozhraní ODBC je poměrně jednoduché a také mnohem rychlejší než OLE DB. Pouze výstrahou je, že rozhraní ODBC používat rozhraní API starého stylu jazyka C. 

## <a id="Create"></a>Krok 1: Vytvoření databáze Azure SQL
Najdete v článku [Začínáme stránky](sql-database-get-started-portal.md) se dozvíte, jak k vytvoření ukázkové databáze.  Alternativně můžete to provést [krátké video dvě minuty](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) k vytvoření Azure SQL database pomocí portálu Azure.

## <a id="ConnectionString"></a>Krok 2: Získání připojovacího řetězce
Poté, co byla vytvořena na vaší databázi Azure SQL, budete muset provést následující kroky zjistit informace o připojení a přidat IP adresu vašeho klienta pro přístup přes bránu firewall. 

V [portál Azure](https://portal.azure.com/), přejděte k vaší databázi Azure SQL připojovací řetězec ODBC pomocí **zobrazit databázové připojovací řetězce** uveden jako součást oddílu přehled pro vaši databázi: 

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Zkopírujte obsah **ODBC (zahrnuje Node.js) [ověřování SQL]** řetězec. Tento řetězec používáme později připojit z příkazového řádku překladač naše C++ ODBC. Tento řetězec obsahuje podrobné informace, jako jsou ovladače, serveru a další parametry připojení databáze. 

## <a id="Firewall"></a>Krok 3: Přidání IP do brány firewall
Přejděte do části brány firewall na serveru databáze a přidat váš [IP adresa klienta do brány firewall pomocí těchto kroků](sql-database-configure-firewall-settings.md) zajistit Navážeme úspěšné připojení: 

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

V tomto okamžiku jste nakonfigurovali vaší databázi SQL Azure a jste připraveni připojit z vašeho kódu C++. 

## <a id="Windows"></a>Krok 4: Připojení z aplikace Windows C/C++
Můžete snadno připojit k vaší [databázi SQL Azure pomocí rozhraní ODBC do systému Windows pomocí této ukázce](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) který sestavení pomocí sady Visual Studio. Ukázka implementuje příkazového řádku překladač ODBC, které je možné se připojit k naší databázi SQL Azure. Tato ukázka má buď soubor databáze název zdrojového souboru (DSN) jako argument příkazového řádku nebo podrobné připojovací řetězec, který jsme předtím zkopírovali z portálu Azure. Otevřete stránku vlastnosti pro tento projekt a vložte připojovací řetězec jako argument příkazu, jak je vidět tady: 

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Zajistěte, aby že zadejte podrobnosti správné ověřování pro vaši databázi jako součást tohoto připojovacího řetězce databáze. 

Spusťte aplikaci ji od sestavit. Měli byste vidět následujícím okně ověřování úspěšné připojení. Můžete dokonce spouštět některé základní příkazy SQL jako **vytvořit tabulku** k ověření připojení k databázi:

![SQL příkazy](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternativně můžete vytvořit soubor DSN pomocí průvodce, který se spustí, když jsou k dispozici žádné argumenty příkazu. Doporučujeme tuto možnost a zkuste to. Tento soubor DSN můžete použít pro automatizaci a chrání vaše nastavení ověřování: 

![Vytvoření souboru DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Blahopřejeme! Teď úspěšně připojíte k Azure SQL pomocí C++ a rozhraní ODBC v systému Windows. Čtení provedete stejný pro platformu Linux také můžete pokračovat. 

## <a id="Linux"></a>Krok 5: Připojení z aplikace Linux C/C++
V případě, že ještě nebyly výše uvedené informace, Visual Studio teď umožňuje vyvíjet aplikace C++ Linux také. Další informace o tento nový scénář v [Visual C++ pro Linux Development](https://blogs.msdn.microsoft.com/vcblog/2016/03/30/visual-c-for-linux-development/) blogu. Pokud chcete vytvořit pro Linux, je nutné vzdáleného počítače, kde běží vaše distro Linux. Pokud nemáte k dispozici, můžete nastavit jeden rychle díky [Linux Azure virtuálních počítačů](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

V tomto kurzu dejte nám předpokládají, že byl jako distribučního Ubuntu 16.04 Linux nastavit. Kroky v tomto poli by se měly používat také k Ubuntu 15.10, Red Hat 6 a Red Hat 7. 

Následující kroky instalace knihovny potřebné pro SQL a rozhraní ODBC pro vaše distro:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Spusťte sadu Visual Studio. V nabídce Nástroje -> Možnosti -> Cross platformy -> Správce připojení, přidat připojení do přijímače Linux: 

![Možnosti nástrojů](./media/sql-database-develop-cplusplus-simple/tools.png)

Po navázání připojení přes protokol SSH, vytvořte šablonu prázdný projekt (Linux): 

![Nové šablony projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Poté můžete přidat [nové C zdroje souborů a nahraďte tento obsah](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Pomocí funkce SQLAllocHandle rozhraní API ODBC, SQLSetConnectAttr a SQLDriverConnect, nyní byste měli mít k inicializaci a připojení k vaší databázi. Jako s ukázkou ODBC systému Windows, je třeba nahradit volání SQLDriverConnect s podrobnostmi z vaší dříve zkopírováno z portálu Azure parametry připojovacího řetězce databáze. 

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Věc udělat předtím, než kompilace je přidání **odbc** jako závislost knihovny: 

![Přidání rozhraní ODBC jako vstupní knihovny](./media/sql-database-develop-cplusplus-simple/lib.png)

Ke spuštění aplikace, otevřete konzolu Linux z **ladění** nabídky: 

![Linux konzoly](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Pokud vaše připojení bylo úspěšné, teď byste měli vidět název aktuální databáze vytištěn v konzole pro Linux: 

![Okno výstup konzoly Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Blahopřejeme! Kurz byl úspěšně dokončen a nyní můžete přihlásit k vaší databázi SQL Azure z jazyka C++ na platformách systému Windows a Linux.

## <a id="GetSolution"></a>Získání úplného řešení kurzu C/C++
Můžete najít řešení GetStarted, které obsahuje všechny ukázky v tomto článku na githubu:

* [Ukázka ODBC C++ Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), stažení ukázky Windows C++ rozhraní ODBC pro připojení k Azure SQL
* [Ukázka ODBC C++ Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), stažení ukázky Linux C++ rozhraní ODBC pro připojení k Azure SQL

## <a name="next-steps"></a>Další postup
* Zkontrolujte [přehled vývoje SQL databáze](sql-database-develop-overview.md)
* Další informace o [referenční dokumentace rozhraní API ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Další zdroje informací:
* [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Prozkoumejte všechny [schopnosti služby SQL Database](https://azure.microsoft.com/services/sql-database/).

