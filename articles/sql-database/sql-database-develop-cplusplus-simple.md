---
title: Připojení k databázi SQL pomocí c a c++
description: Pomocí ukázkového kódu v tomto rychlém startu můžete vytvořit moderní aplikaci s C++ a podporovanou výkonnou relační databází v cloudu pomocí Azure SQL Database.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73690700"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Připojení k databázi SQL pomocí c a c++

Tento příspěvek je zaměřen na vývojáře C a C++, kteří se pokoušejí připojit k Azure SQL DB. Je rozdělena do sekcí, takže můžete přejít do sekce, která nejlépe zachycuje váš zájem.

## <a name="prerequisites-for-the-cc-tutorial"></a>Požadavky na kurz C/C++

Ujistěte se, že máte následující položky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/). Chcete-li vytvořit a spustit tuto ukázku, je nutné nainstalovat součásti jazyka C++.
* [Visual Studio Linux Vývoj](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Pokud vyvíjíte na Linuxu, musíte také nainstalovat rozšíření Visual Studio Linux.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL Database a SQL Server na virtuálních počítačích
Azure SQL je integrovaný na Microsoft SQL Serveru a je navržený tak, aby poskytoval vysoce dostupnost, výkonnou a škálovatelnou službu. Existuje mnoho výhod pro použití SQL Azure přes proprietární databáze spuštěné v místním prostředí. S SQL Azure nemusíte instalovat, nastavovat, udržovat nebo spravovat databázi, ale pouze obsah a strukturu databáze. Typické věci, které se obáváme s databázemi, jako je odolnost proti chybám a redundance, jsou zabudovány.

Azure má aktuálně dvě možnosti pro hostování úloh SQL serveru: Azure SQL databáze, databáze jako služba a SQL server na virtuálních počítačích (VM). Podrobnosti o rozdílech mezi těmito dvěma informacemi nebudeme zakládat s výjimkou toho, že databáze Azure SQL je vaší nejlepší volbou pro nové cloudové aplikace, které využijí úspory nákladů a optimalizace výkonu, které cloudové služby poskytují. Pokud uvažujete o migraci nebo rozšíření místních aplikací do cloudu, sql server ve virtuálním počítači Azure může fungovat lépe pro vás. Chcete-li, aby věci jednoduché pro tento článek, pojďme vytvořit databázi Azure SQL.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Technologie přístupu k datům: ODBC a OLE DB
Připojení k Azure SQL DB se nijak neliší a v současné době existují dva způsoby připojení k databázím: ODBC (připojení otevřené databáze) a OLE DB (databáze propojování a vkládání objektů). V posledních letech společnost Microsoft zarovnala rozhraní [ODBC pro nativní relační přístup k datům](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/). Rozhraní ODBC je poměrně jednoduché a také mnohem rychlejší než TECHNOLOGIE OLE DB. Jedinou námitkou je, že rozhraní ODBC používá staré rozhraní API ve stylu C.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Krok 1: Vytvoření azure sql databáze
Podívejte se na [stránku Začínáme,](sql-database-single-database-get-started.md) kde se dozvíte, jak vytvořit ukázkovou databázi.  Případně můžete sledovat toto [krátké dvouminutové video](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) a vytvořit databázi Azure SQL pomocí portálu Azure.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Krok 2: Získání připojovacího řetězce
Po zřízení databáze Azure SQL je třeba provést následující kroky k určení informací o připojení a přidat IP adresu klienta pro přístup k bráně firewall.

Na [webu Azure Portal](https://portal.azure.com/)přejděte do připojovacího řetězce ODBC databáze Azure SQL pomocí **připojovacích řetězců Zobrazit databázi, které** jsou uvedeny jako součást části přehledu pro vaši databázi:

![Řetězec připojení ODBC](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![Rozhraní ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Zkopírujte obsah řetězce **ODBC (Includes Node.js) [SQL authentication].** Tento řetězec použijeme později pro připojení z našeho překladače příkazového řádku C++ ODBC. Tento řetězec obsahuje podrobnosti, jako je například ovladač, server a další parametry připojení databáze.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Krok 3: Přidání IP adresy do brány firewall
Přejděte do části brány firewall pro databázový server a přidejte [ip adresu klienta do brány firewall pomocí těchto kroků,](sql-database-configure-firewall-settings.md) abyste se ujistili, že můžeme navázat úspěšné připojení:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

V tomto okamžiku jste nakonfigurovali Azure SQL DB a jsou připraveni k připojení z kódu C++.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Krok 4: Připojení z aplikace pro Windows C/C++
Pomocí této ukázky, která se staví v sadě Visual Studio, se můžete snadno připojit k [Azure SQL DB pomocí rozhraní ODBC v systému Windows.](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29) Ukázka implementuje překladač příkazového řádku ODBC, který lze použít k připojení k našemu Azure SQL DB. Tato ukázka přebírá soubor souboru názvu zdrojové databáze (DSN) jako argument příkazového řádku nebo podrobný připojovací řetězec, který jsme zkopírovali dříve z portálu Azure. Převezte stránku vlastností pro tento projekt a vložte připojovací řetězec jako příkazový argument, jak je znázorněno zde:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Ujistěte se, že jste jako součást připojovacího řetězce databáze zadali správné ověřovací údaje pro databázi.

Spusťte aplikaci a vytvořte ji. Měli byste vidět následující okno ověřování úspěšné připojení. Můžete dokonce spustit některé základní příkazy SQL, jako **je vytvoření tabulky** pro ověření připojení k databázi:

![Příkazy SQL](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Případně můžete vytvořit soubor DSN pomocí průvodce, který je spuštěn, když nejsou k dispozici žádné argumenty příkazu. Doporučujeme vyzkoušet i tuto možnost. Tento soubor DSN můžete použít pro automatizaci a ochranu nastavení ověřování:

![Vytvořit soubor DSN](./media/sql-database-develop-cplusplus-simple/datasource.png)

Blahopřejeme! Nyní jste se úspěšně připojili k Azure SQL pomocí C++ a ODBC v systému Windows. Můžete pokračovat ve čtení, abyste udělali totéž i pro platformu Linux.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Krok 5: Připojení z aplikace Linux C/C++
V případě, že jste ještě neslyšeli novinky, Visual Studio nyní umožňuje vyvíjet aplikaci C++ Linux také. O tomto novém scénáři si můžete přečíst v blogu [Visual C++ for Linux Development.](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) Chcete-li vytvořit pro Linux, potřebujete vzdálený počítač, kde je spuštěna vaše distribuce Linuxu. Pokud nemáte k dispozici, můžete nastavit rychle pomocí [Virtuálních počítačů Linux Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pro tento výukový program předpokládejme, že máte nastavenou distribuci Linuxu Ubuntu 16.04. Kroky by se měly vztahovat také na Ubuntu 15.10, Red Hat 6 a Red Hat 7.

Následující kroky nainstalují knihovny potřebné pro SQL a ODBC pro vaše distro:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Spusťte Visual Studio. V části Tools -> Options -> Cross Platform -> Connection Manager přidejte připojení k poli Linuxu:

![Možnosti nástrojů](./media/sql-database-develop-cplusplus-simple/tools.png)

Po navázání připojení přes SSH vytvořte šablonu prázdného projektu (Linux):

![Nová šablona projektu](./media/sql-database-develop-cplusplus-simple/template.png)

Poté můžete přidat [nový zdrojový soubor C a nahradit jej tímto obsahem](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Pomocí rozhraní API ROZHRANÍ ODBC SQLAllocHandle, SQLSetConnectAttr a SQLDriverConnect byste měli být schopni inicializovat a navázat připojení k databázi.
Stejně jako u ukázky rozhraní ODBC systému Windows je třeba nahradit volání SQLDriverConnect podrobnostmi z parametrů připojovacího řetězce databáze zkopírovaných z portálu Azure dříve.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Poslední věc, kterou udělat před kompilací je přidat **odbc** jako závislost knihovny:

![Přidání rozhraní ODBC jako vstupní knihovny](./media/sql-database-develop-cplusplus-simple/lib.png)

Chcete-li aplikaci spustit, vydejte linuxovou konzoli z nabídky **Ladění:**

![Linuxkonzole](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Pokud bylo připojení úspěšné, měl by nyní zobrazit aktuální název databáze vytištěný v konzole Linux:

![Výstup okna konzoly Linux](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Blahopřejeme! Úspěšně jste dokončili kurz a teď se můžete připojit k Azure SQL DB z C++ na platformách Windows a Linux.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Získejte kompletní výukové řešení c/c++
Řešení GetStarted, které obsahuje všechny ukázky v tomto článku na GitHubu:

* [Ukázka systému ODBC C++ pro Windows](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), Stažení ukázky rozhraní ODBC pro Windows C++ pro připojení k Azure SQL
* [Ukázka od BC C++ linuxu](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29), stažení ukázky odhlašování ODBC v Linuxu pro připojení k Azure SQL

## <a name="next-steps"></a>Další kroky
* Projděte si [přehled vývoje databáze SQL](sql-database-develop-overview.md)
* Další informace o [příručce ROZHRANÍ API ROZHRANÍ ODBC](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Další zdroje
* [Vzory návrhu pro aplikace SaaS s více tenanty využívající Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Prozkoumejte všechny [schopnosti služby SQL Database](https://azure.microsoft.com/services/sql-database/).

