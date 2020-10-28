---
title: Řešení chyb občasného odchozího připojení v Azure App Service
description: Řešení chyb při občasném připojení a souvisejících potížích s výkonem v Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 07/24/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 76b4408b2f8c631453281ecf6f214d49318252a3
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785047"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Řešení chyb občasného odchozího připojení v Azure App Service

Tento článek vám pomůže vyřešit chyby přerušovaného připojení a související problémy s výkonem v [Azure App Service](./overview.md). V tomto tématu najdete další informace o metodách a odstraňování potíží s postupy pro vyčerpání zdrojových adres portů SNAT (Network Translation). Pokud potřebujete další informace v jakémkoli bodě tohoto článku, obraťte se na odborníky na Azure na [webu MSDN Azure a na fórech Stack Overflow](https://azure.microsoft.com/support/forums/). Případně můžete také zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu** .

## <a name="symptoms"></a>Příznaky

Aplikace a funkce hostované ve službě Azure App Service můžou vykazovat jeden nebo několik následujících příznaků:

* Pomalé odezvy u všech nebo některých instancí v plánu služby.
* Občasné 5xx nebo **chybné chyby brány**
* Chybové zprávy časového limitu
* Nepovedlo se připojit k externím koncovým bodům (například SQLDB, Service Fabric, dalších aplikačních služeb atd.).

## <a name="cause"></a>Příčina

Hlavní příčinou těchto symptomů je, že instance aplikace nemůže otevřít nové připojení k externímu koncovému bodu, protože dosáhlo jednoho z následujících omezení:

* Připojení TCP: existuje omezení počtu odchozích připojení, která lze vytvořit. Tato hodnota je přidružená k velikosti použitého pracovního procesu.
* Porty SNAT: jak je popsáno v části [odchozí připojení v Azure](../load-balancer/load-balancer-outbound-connections.md), Azure používá překlad zdrojového síťového adres (SNAT) a Load Balancer (nevystavený zákazníkům) ke komunikaci s koncovými body mimo Azure ve veřejném adresním prostoru IP adres a koncovými body interních k Azure, které nevyužívají výhody služby nebo privátních koncových bodů. Každé instanci ve službě Azure App Service se zpočátku dostal předem přidělený počet portů **128** SNAT. Toto omezení má vliv na otevírání připojení ke stejné kombinaci hostitelů a portů. Pokud vaše aplikace vytvoří připojení ke kombinaci adres a kombinací portů, nebudete používat porty SNAT. Porty SNAT se používají, když máte opakovaná volání stejné kombinace adres a portů. Po uvolnění portu je možné port v případě potřeby znovu použít. Nástroj pro vyrovnávání zatížení sítě Azure uvolní port SNAT z uzavřených připojení jenom po 4 minutách.

Když aplikace nebo funkce rychle otevřou nové připojení, můžou rychle vyčerpat svou předem přidělenou kvótu 128 portů. Pak jsou zablokované, dokud nebude k dispozici nový port SNAT, a to prostřednictvím dynamického přidělování dalších portů SNAT nebo opakovaného použití uvolněného portu SNAT. Aplikace nebo funkce, které jsou blokované z důvodu neschopnosti vytvářet nová připojení, začnou mít jeden nebo více problémů popsaných v části **příznaky** v tomto článku.

## <a name="avoiding-the-problem"></a>Zamezení problému

Pokud je vaším cílem služba Azure, která podporuje koncové body služby, můžete se vyhnout problémům s vyčerpáním portů SNAT pomocí [místní služby virtuální](./web-sites-integrate-with-vnet.md) sítě a koncových bodů služby nebo privátních koncových bodů. Pokud používáte místní integraci virtuální sítě a umístění koncových bodů služby v podsíti Integration, nebude mít odchozí přenosy na tyto služby omezení portů SNAT. Podobně platí, že pokud použijete místní integraci virtuální sítě a soukromé koncové body, nebudete mít k tomuto cíli žádné odchozí problémy portů SNAT. 

Vyloučení problému s portem SNAT znamená, že se vyhnete vytváření nových připojení opakovaně ke stejnému hostiteli a portu.

Obecné strategie pro zmírnění vyčerpání portů SNAT jsou popsány v [části řešení problémů](../load-balancer/load-balancer-outbound-connections.md) v **odchozích připojeních ke službě Azure** Documentation. Tyto strategie platí pro aplikace a funkce hostované v Azure App Service.

### <a name="modify-the-application-to-use-connection-pooling"></a>Úprava aplikace pro použití sdružování připojení

* Pro sdružování připojení HTTP zkontrolujte [fondy připojení HTTP pomocí HttpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Další informace o SQL Server sdružování připojení najdete v [SQL Server sdružování připojení (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Pro implementaci sdružování s aplikacemi Entity Frameworku si projděte téma [DbContext Pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Tady je kolekce odkazů pro implementaci sdružování připojení pomocí jiného zásobníku řešení.

#### <a name="node"></a>Node

Ve výchozím nastavení nejsou připojení pro NodeJS udržována v neaktivním stavu. Níže jsou uvedené oblíbené databáze a balíčky pro sdružování připojení, které obsahují příklady pro jejich implementaci.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

Keep-Alive HTTP

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [ Dokumentace kNode.js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Níže jsou uvedené oblíbené knihovny používané pro sdružování připojení JDBC, které obsahují příklady jejich implementace: JDBC sdružování připojení.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Sdružování připojení HTTP

* [Správa připojení Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [PoolingHttpClientConnectionManager třídy](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

I když PHP nepodporuje sdružování připojení, můžete zkusit použít trvalá databázová připojení k vašemu back-end serveru.
 
* Server MySQL

   * [Připojení MySQL](https://www.php.net/manual/mysqli.quickstart.connections.php) pro novější verze
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) pro starší verze php

* Další zdroje dat

   * [Správa připojení PHP](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Úprava aplikace pro opětovné použití připojení

*  Další ukazatele a příklady správy připojení ve službě Azure Functions najdete v části [Správa připojení v Azure Functions](../azure-functions/manage-connections.md).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Upravit aplikaci tak, aby používala méně agresivní logiku opakování

* Další doprovodné materiály a příklady najdete v [vzorech opakování](/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Obnovení odchozího nečinného časového limitu pomocí kontroly stavu

* Pro implementaci režimu kontroly a kontroly pro aplikace Node.js můžete zkontrolovat, [že aplikace uzlů provádí nadměrné odchozí hovory](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Další pokyny, které jsou specifické pro App Service:

* [Zátěžový test](/azure/devops/test/load-test/app-service-web-app-performance-test) by měl simulovat reálné celosvětové údaje při ustálené rychlosti krmení. Testování aplikací a funkcí v rámci reálného zatížení dokáže identifikovat a vyřešit problémy s vyčerpáním portů SNAT před časem.
* Ujistěte se, že back-endové služby můžou odpovědi vracet rychle. Řešení potíží s výkonem pomocí Azure SQL Database najdete v tématu [řešení potíží s Azure SQL Database problémy s výkonem Intelligent Insights](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow).
* Horizontální navýšení kapacity App Service naplánování na více instancí. Další informace o škálování najdete v tématu horizontální navýšení [kapacity aplikace v Azure App Service](./manage-scale-up.md). Každá instance pracovního procesu v plánu služby App Service má přidělený počet portů SNAT. Pokud rozšíříte své využití napříč více instancemi, můžete získat využití portu SNAT na instanci níže doporučeným limitem 100 odchozích připojení na jedinečné vzdálené koncové body.
* Zvažte přesunutí na [App Service Environment (POmocného mechanismu)](./environment/using-an-ase.md), kde se přiděluje jedna odchozí IP adresa a omezení pro připojení a porty SNAT jsou mnohem vyšší. V pomocném objektu pro čtení je počet portů SNAT na instanci založený na tabulce předdefinovaných [Vyrovnávání zatížení Azure](../load-balancer/load-balancer-outbound-connections.md#snatporttable) . například služba pomocného procesu s 1-50 instancemi pracovních procesů má 1024 předběžně přidělených portů na 512 51-100 instanci

Vyloučení odchozích omezení TCP je snazší, protože limity jsou nastaveny podle velikosti pracovního procesu. Můžete si prohlédnout omezení pro [Číselná omezení mezi virtuálními počítači izolovaného prostoru (sandbox) – připojení TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Název omezení|Popis|Malé (a1)|Střední (a2)|Velký (a3)|Izolovaná úroveň (pomocného mechanismu)|
|---|---|---|---|---|---|
|Připojení|Počet připojení napříč celým virtuálním počítačem|1920|3968|8064|16 000|

Aby nedocházelo k odchozím limitům TCP, můžete buď zvětšit velikost vašich pracovních procesů, nebo horizontální horizontální navýšení kapacity.

## <a name="troubleshooting"></a>Řešení potíží

Pochopení dvou typů omezení odchozího připojení a toho, co vaše aplikace dělá, by mělo usnadnit řešení potíží. Pokud víte, že vaše aplikace provede mnoho volání stejného účtu úložiště, můžete mít podezření na omezení SNAT. Pokud vaše aplikace vytvoří hodně volání koncových bodů po internetu, měli byste se domnívat, že jste dosáhli limitu počtu virtuálních počítačů.

Pokud neznáte chování aplikace dostatečně rychle, existují některé nástroje a techniky, které jsou k dispozici v App Service k tomu, aby se s tímto určením usnadnily.

### <a name="find-snat-port-allocation-information"></a>Najít informace o přidělení portu SNAT

[Diagnostiku App Service](./overview-diagnostics.md) můžete použít k vyhledání informací o přidělení portů SNAT a ke sledování metriky přidělení portů snat v App Service lokalitě. Pokud chcete najít informace o přidělování portů SNAT, postupujte podle následujících kroků:

1. Pokud chcete získat přístup k diagnostice App Service, přejděte v [Azure Portal](https://portal.azure.com/)do webové aplikace App Service nebo App Service Environment. V levém navigačním panelu vyberte **Diagnostika a řešení problémů** .
2. Vybrat kategorii dostupnosti a výkonu
3. V seznamu dostupných dlaždic v kategorii Vyberte dlaždici vyčerpání portů SNAT. Postup je udržovat pod 128.
Pokud to budete potřebovat, můžete přesto otevřít lístek podpory a pracovník podpory obdrží metriku z back-endu za vás.

Počítejte s tím, že protože použití portu SNAT není k dispozici jako metrika, není možné automatické škálování na základě využití portu SNAT nebo pro konfiguraci automatického škálování na základě metriky přidělování portů SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Připojení TCP a porty SNAT

Připojení TCP a porty SNAT přímo nesouvisejí. Rozpoznávání využití připojení TCP je součástí okna diagnostikovat a řešit problémy libovolné App Service lokality. Vyhledejte frázi "připojení TCP".

* Porty SNAT se používají jenom pro toky externích sítí, ale celkový počet připojení TCP zahrnuje připojení k místním smyčkám.
* Port SNAT může být sdílen různými toky, pokud se toky liší v obou protokolech, IP adrese nebo portu. Metrika připojení TCP počítá každé připojení TCP.
* Limit připojení TCP nastane na úrovni instance pracovního procesu. Služba Vyrovnávání zatížení sítě Azure nepoužívá metriku připojení TCP pro omezení portů SNAT.
* Omezení připojení TCP jsou popsaná v části [Číselná omezení pro různé virtuální počítače izolovaného prostoru (sandbox) – připojení TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Název omezení|Popis|Malé (a1)|Střední (a2)|Velký (a3)|Izolovaná úroveň (pomocného mechanismu)|
|---|---|---|---|---|---|
|Připojení|Počet připojení napříč celým virtuálním počítačem|1920|3968|8064|16 000|

### <a name="webjobs-and-database-connections"></a>Webové úlohy a databázová připojení
 
Pokud jsou vyčerpány porty SNAT, kde se webové úlohy nemohou připojit k SQL Database, neexistuje žádná metrika k zobrazení, kolik připojení je otevřeno jednotlivými procesy webové aplikace. Pokud chcete zjistit problematickou webovou úlohu, přejeďte několik webových úloh na jiný App Service plán, abyste zjistili, jestli se situace zlepšila, nebo pokud problém zůstane v jednom z plánů. Opakujte tento postup, dokud nenajdete problematickou úlohu WebJob.

### <a name="using-snat-ports-sooner"></a>Používání portů SNAT dříve

Nemůžete změnit žádné nastavení Azure tak, aby využívalo dříve použité porty SNAT, protože všechny porty SNAT budou uvolněny podle níže uvedených podmínek a chování je záměrné.
 
* Pokud některý ze serverů nebo klientů odesílá FINACK, [port SNAT se uvolní](../load-balancer/load-balancer-outbound-connections.md) po 240 sekundách.
* Pokud se zobrazí RST, port SNAT se uvolní po 15 sekundách.
* Při dosažení časového limitu nečinnosti se port uvolní.
 
## <a name="additional-information"></a>Další informace

* [SNAT s App Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Řešení potíží s výkonem pomalých aplikací v Azure App Service](./troubleshoot-performance-degradation.md)
