---
title: Řešení potíží s občasnými chybami odchozího připojení ve službě Azure App Service
description: Řešení problémů s občasnými chybami připojení a souvisejícími problémy s výkonem ve službě Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367548"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Řešení potíží s občasnými chybami odchozího připojení ve službě Azure App Service

Tento článek vám pomůže vyřešit občasné chyby připojení a související problémy s výkonem ve [službě Azure App Service](https://docs.microsoft.com/azure/app-service/overview). Toto téma bude poskytovat další informace o a řešení potíží metodiky pro vyčerpání portů překladu zdrojové adresy (SNAT). Pokud budete potřebovat další pomoc v libovolném bodě v tomto článku, obraťte se na odborníky Azure na [MSDN Azure a zásobníku přetečení fóra](https://azure.microsoft.com/support/forums/). Případně můžete podat incident podpory Azure. Přejděte na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte Získat **podporu**.

## <a name="symptoms"></a>Příznaky

Aplikace a funkce hostované ve službě Azure App mohou vykazovat jeden nebo více z následujících příznaků:

* Pomalé doby odezvy na všechny nebo některé instance v plánu služeb.
* Občasné chyby 5xx nebo **Bad Gateway**
* Chybové zprávy časového opojení
* Nelze se připojit k externím koncovým bodům (například SQLDB, Service Fabric, jiným službám aplikací atd.)

## <a name="cause"></a>Příčina

Hlavní příčinou těchto příznaků je, že instance aplikace není schopna otevřít nové připojení k externímu koncovému bodu, protože dosáhla jednoho z následujících limitů:

* Připojení TCP: Počet odchozích připojení, která lze provést, je omezen. To je spojeno s velikostí použitého pracovníka.
* Porty SNAT: Jak je popsáno v [odchozích připojeních v Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections), Azure používá překlad y adresy zdrojové sítě (SNAT) a balancer zatížení (není vystaven zákazníkům) ke komunikaci s koncovými body mimo Azure ve veřejném ip adresním prostoru. Každá instance ve službě Azure App je zpočátku přidělený počet **128** portů SNAT. Toto omezení ovlivňuje otevření připojení ke stejné kombinaci hostitele a portu. Pokud vaše aplikace vytvoří připojení ke kombinaci kombinací adres a portů, nebudete sníst porty SNAT. Porty SNAT jsou vypáčeny, když máte opakované volání na stejnou adresu a kombinaci portů. Po uvolnění portu je port k dispozici pro opakované použití podle potřeby. Azure Network balancer uvolňuje port SNAT z uzavřených připojení pouze po čekání na 4 minuty.

Když aplikace nebo funkce rychle otevřou nové připojení, mohou rychle vyčerpat předem přidělenou kvótu 128 portů. Poté jsou blokovány, dokud nebude k dispozici nový port SNAT, a to buď dynamickým přidělováním dalších portů SNAT, nebo opětovným použitím regenerovaného portu SNAT. Aplikace nebo funkce, které jsou blokovány z důvodu této neschopnosti vytvořit nová připojení začne docházet k jedné nebo více problémů popsaných v části **příznaky** tohoto článku.

## <a name="avoiding-the-problem"></a>Jak se vyhnout problému

Vyhnout se problému s portem SNAT znamená vyhnout se vytváření nových připojení, která se budou opakující se ke stejnému hostiteli a portu.

Obecné strategie pro zmírnění vyčerpání portů SNAT jsou popsány v [části řešení problémů](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) odchozí připojení dokumentace **Azure.** Z těchto strategií se vztahují následující aplikace a funkce hostované ve službě Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Úprava aplikace tak, aby používala sdružování připojení

* Pro sdružování připojení HTTP zkontrolujte [připojení HTTP fondu s httpfactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Informace o sdružování připojení serveru SQL Server naleznete ve [sdružování připojení serveru SQL Server (ADO.NET).](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling)
* Pro implementaci sdružování s aplikacemi framework entity, zkontrolujte [DbContext sdružování](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Zde je kolekce odkazů pro implementaci sdružování připojení podle různých zásobníků řešení.

#### <a name="node"></a>Node

Ve výchozím nastavení nejsou připojení pro NodeJS udržována naživu. Níže jsou uvedeny oblíbené databáze a balíčky pro sdružování připojení, které obsahují příklady, jak je implementovat.

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Keep-alive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Dokumentace node.js v13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Níže jsou uvedeny oblíbené knihovny používané pro sdružování připojení JDBC, které obsahují příklady jejich implementace: Sdružování připojení JDBC.

* [Kocour 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Sdružování připojení HTTP

* [Správa připojení Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Správce sdružující sdružovací službyHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Přestože PHP nepodporuje sdružování připojení, můžete zkusit použít trvalé připojení k databázi k serveru back-end.
 
* MySQL server

   * [Připojení MySQLi](https://www.php.net/manual/mysqli.quickstart.connections.php) pro novější verze
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) pro starší verze PHP

* Jiné zdroje dat

   * [Správa připojení PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (POZNÁMKA: SQLAlchemy lze použít s jinými databázemi kromě MicrosoftSQL Server)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive je automatické při použití relací [relace-objekty).](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)

Pro jiná prostředí zkontrolujte poskytovatele nebo dokumenty specifické pro ovladač pro implementaci sdružování připojení ve vašich aplikacích.

### <a name="modify-the-application-to-reuse-connections"></a>Úprava aplikace pro opakované použití připojení

*  Další ukazatele a příklady správy připojení ve funkcích Azure najděte v přehledu [Správa připojení ve funkcích Azure](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Upravte aplikaci tak, aby používala méně agresivní logiku opakování

* Další pokyny a příklady naleznete [v příkladu opakování](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Obnovení časového limitu odchozího nečinnosti pomocí udržovatých životů

* Pokud jde o implementaci data údržby aplikací Node.js, přečtěte si recenzi na [aplikaci Můj uzel provádí nadměrné odchozí volání](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Další pokyny specifické pro službu App Service:

* Zátěžový [test](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) by měl simulovat data z reálného světa stabilní rychlostí podávání. Testování aplikací a funkcí v reálném světě může identifikovat a vyřešit problémy s vyčerpáním portů SNAT předem.
* Ujistěte se, že back-endové služby mohou rychle vrátit odpovědi. Řešení potíží s výkonem databáze Azure SQL najdete [v článku Poradce při potížích s výkonem databáze Azure SQL pomocí aplikace Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Horizontální navýšení kapacity plánu služby App Service na více instancí. Další informace o škálování najdete v [tématu Škálování aplikace ve službě Azure App Service](https://docs.microsoft.com/azure/app-service/manage-scale-up). Každé instanci pracovního procesu v plánu služby app service je přidělen počet portů SNAT. Pokud rozlojíte využití mezi více instancí, může získat využití portu SNAT na instanci pod doporučený limit 100 odchozích připojení na jedinečný vzdálený koncový bod.
* Zvažte přechod do [prostředí služby App Service (ASE),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)kde je přidělena jedna odchozí IP adresa a limity pro připojení a porty SNAT jsou mnohem vyšší.

Vyhnout se odchozí tcp limity je snazší vyřešit, protože limity jsou nastaveny podle velikosti vašeho pracovníka. Limity můžete zobrazit v [číselných limitech virtuálních vod sandboxu – připojení TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Omezit název|Popis|Malé (A1)|Střední (A2)|Velký (A3)|Izolovaná vrstva (ASE)|
|---|---|---|---|---|---|
|Připojení|Počet připojení napříč celým virtuálním virtuálním montovnam|1920|3968|8064|16 000|

Chcete-li se vyhnout odchozím limitům protokolu TCP, můžete buď zvětšit velikost svých pracovníků, nebo horizontální mnosajde mez.

## <a name="troubleshooting"></a>Řešení potíží

Znalost dvou typů omezení odchozího připojení a toho, co vaše aplikace dělá, by měla usnadnit řešení potíží. Pokud víte, že vaše aplikace provádí mnoho hovorů na stejný účet úložiště, můžete mít podezření, že limit SNAT. Pokud vaše aplikace vytvoří velké množství volání koncových bodů po celém internetu, měli byste podezření, že dosahujete limitu virtuálních připojení.

Pokud neznáte chování aplikace dost určit příčinu rychle, existují některé nástroje a techniky, které jsou k dispozici v App Service pomoci s tímto určením.

### <a name="find-snat-port-allocation-information"></a>Najít informace o přidělení portu SNAT

Pomocí [diagnostiky služby App Service](https://docs.microsoft.com/azure/app-service/overview-diagnostics) můžete najít informace o přidělení portů SNAT a sledovat metriku přidělení portů SNAT webu služby App Service. Chcete-li najít informace o přidělení portu SNAT, postupujte takto:

1. Chcete-li získat přístup k diagnostice služby App Service, přejděte na portál Azure na [webu App](https://portal.azure.com/)Service do webové aplikace App Service nebo prostředí služby App Service . V levém navigačním panelu vyberte **Diagnostikovat a řešit problémy**.
2. Vybrat kategorii dostupnosti a výkonu
3. V seznamu dostupných dlaždic v kategorii vyberte dlaždici Vyčerpání portu SNAT. Praxe je udržet ji pod 128.
Pokud ji potřebujete, můžete stále otevřít lístek podpory a pracovník podpory získá metriku z back-endu pro vás.

Všimněte si, že vzhledem k tomu, že využití portů SNAT není k dispozici jako metrika, není možné buď automatické škálování na základě využití portu SNAT, nebo nakonfigurovat automatické škálování na základě metriky přidělení portů SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Připojení TCP a porty SNAT

Připojení TCP a porty SNAT spolu přímo nesouvisí. Detektor využití připojení TCP je součástí okna Diagnostika a řešení problémů libovolného webu služby App Service. Vyhledejte frázi "Připojení TCP".

* Porty SNAT se používají pouze pro toky externí sítě, zatímco celkový počet připojení TCP zahrnuje připojení zpětné smyčky místní chod.
* Port SNAT může být sdílen různými toky, pokud se toky liší v protokolu, IP adrese nebo portu. Metrika Připojení TCP počítá každé připojení TCP.
* Limit připojení TCP probíhá na úrovni instance pracovníka. Vyrovnávání odchozího zatížení sítě Azure nepoužívá metriku Připojení TCP pro omezení portu SNAT.
* Omezení připojení TCP jsou popsána v [číselných limitech virtuálních vyt v sandboxových křížových virtuálních bylin – připojení TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Omezit název|Popis|Malé (A1)|Střední (A2)|Velký (A3)|Izolovaná vrstva (ASE)|
|---|---|---|---|---|---|
|Připojení|Počet připojení napříč celým virtuálním virtuálním montovnam|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>WebJobs a připojení k databázi
 
Pokud jsou vyčerpány porty SNAT, kde webjobs se nemohou připojit k databázi Azure SQL, neexistuje žádná metrika, která by ukazovala, kolik připojení otevře každý jednotlivý proces webové aplikace. Chcete-li najít problematickou webovou úlohu, přesuňte několik webjobs do jiného plánu služby App Service, abyste zjistili, zda se situace zlepší nebo pokud problém zůstává v jednom z plánů. Opakujte proces, dokud nenajdete problematickou webovou úlohu.

### <a name="using-snat-ports-sooner"></a>Použití portů SNAT dříve

Nelze změnit žádné nastavení Azure uvolnit použité porty SNAT dříve, protože všechny porty SNAT budou uvolněny podle níže uvedených podmínek a chování je záměrné.
 
* Pokud server nebo klient odešle FINACK, [port SNAT bude uvolněn](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) po 240 sekundách.
* Pokud je vidět RST, port SNAT bude uvolněna po 15 sekundách.
* Pokud byl dosažen časový limit nečinnosti, port je uvolněn.
 
## <a name="additional-information"></a>Další informace

* [SNAT se službou app service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Řešení problémů s pomalým výkonem aplikací ve službě Azure App Service](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
