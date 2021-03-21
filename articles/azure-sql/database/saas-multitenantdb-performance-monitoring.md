---
title: Sledování výkonu horizontálně dělené databáze s více klienty
description: Monitorování a Správa výkonu horizontálně dělené víceklientské Azure SQL Database v SaaS aplikaci s více klienty
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d37bf2c84b74dba76e5d1921ed67072af7f6c328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790895"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorování a Správa výkonu horizontálně dělené víceklientské Azure SQL Database v SaaS aplikaci s více klienty
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se prozkoumá několik klíčových scénářů správy výkonu používaných v aplikacích SaaS. Pomocí generátoru zatížení pro simulaci aktivity napříč horizontálně dělené databázemi se představí integrované funkce monitorování a upozorňování Azure SQL Database.

Aplikace Wingtip Tickets SaaS s více klienty používá datový model horizontálně dělené pro více tenantů, kde jsou data místa (tenanta) distribuována podle ID tenanta napříč potenciálně více databázemi. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Chcete-li využít výhod tohoto typického vzoru použití databáze, je možné databáze škálovat nahoru a dolů, aby se optimalizoval náklady na řešení. U tohoto typu vzoru je důležité monitorovat využití prostředků databáze, aby bylo zajištěno, že zatížení budou poměrně vyvážená napříč potenciálně více databázemi. Také je potřeba zajistit, aby jednotlivé databáze měly dostatečné prostředky a nemusely zajišťovat omezení [DTU](purchasing-models.md#dtu-based-purchasing-model) . V tomto kurzu se seznámíte s možnostmi pro monitorování a správu databází a o tom, jak provést nápravné akce v reakci na kolísání zatížení.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> 
> * Simulace využití v databázi s více klienty horizontálně dělené spuštěním zadaného generátoru zatížení
> * Monitorování databáze při reakci na navýšení zatížení
> * Horizontální navýšení kapacity databáze na základě zvýšeného zatížení databáze
> * Zřízení tenanta v databázi s jedním klientem

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasadí se aplikace SaaS pro víceklientské klienty. Nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání SaaS aplikace pro více tenantů](./saas-multitenantdb-get-started-deploy.md) .
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Seznámení se vzory správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. 

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Abyste se vyhnuli nutnosti ručního monitorování výkonu, je nejúčinnější **nastavit výstrahy, které se aktivují, když se databáze nevyskytují v normálním rozsahu**.
* Aby bylo možné reagovat na krátkodobé kolísání výpočetní velikosti databáze, **úroveň DTU se dá škálovat nahoru nebo dolů**. Pokud dochází k této výkyvy na pravidelném nebo předvídatelném základu, **může se naplánování velikosti databáze naplánovat na automatické výskyty**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Aby bylo možné reagovat na dlouhodobé výkyvy nebo změny v klientech, **mohou být jednotliví klienti přesunuti do jiné databáze**.
* Aby bylo možné reagovat na krátkodobé zvýšení zatížení *jednotlivých* klientů, **můžete jednotlivé klienty vyřadit z databáze a přiřadit individuální výpočetní velikost**. Po snížení zatížení se může klient vrátit do databáze s více klienty. V případě, že je tato skutečnost známa předem, mohou být klienti bez potíží přesunuti, aby měli jistotu, že bude mít databáze vždy potřebné prostředky a aby se zabránilo dopadu na ostatní klienty v víceklientské databázi. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Pro SQL Database jsou v databázích k dispozici monitorování a upozorňování. Toto integrované monitorování a upozorňování je specifické pro konkrétní prostředky, takže je vhodné použít pro malý počet prostředků, ale není vhodné při práci s mnoha prostředky.

U scénářů s vysokým objemem, kde pracujete s mnoha prostředky, je možné použít [protokoly Azure monitor](https://azure.microsoft.com/services/log-analytics/) . Jedná se o samostatnou službu Azure, která poskytuje analýzy prostřednictvím vygenerovaných protokolů shromážděných v pracovním prostoru Log Analytics. Protokoly Azure Monitor můžou shromažďovat telemetrii z mnoha služeb a používat je k dotazování a nastavování výstrah.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získat lístky Wingtip Tickets SaaS Database Code a Script aplikace pro více tenantů

V úložišti GitHubu [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) jsou k dispozici skripty SaaS s více klienty a zdrojový kód aplikace. Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

Aby bylo dobré pochopit, jak funguje sledování a Správa výkonu, je nutné, abyste měli více tenantů ve horizontálně dělené databázi s více klienty.

Pokud jste v předchozím kurzu již zřídili dávku tenantů, přejděte k části [simulace využití ve všech databázích tenantů](#simulate-usage-on-all-tenant-databases) .

1. V **prostředí POWERSHELL ISE** otevřete... \\ \\Demo-PerformanceMonitoringAndManagement.ps1pro monitorování a správu výkonu výukových modulů \\ ** Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavení **$DemoScenario**  =  **1** _zřídí dávku tenantů_ .
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 klientů do víceklientské databáze za několik minut. 

Skript *New-TenantBatch* vytvoří nové klienty s jedinečnými klíči tenanta v rámci databáze horizontálně dělené s více klienty a inicializuje je pomocí názvu tenanta a typu místa. To je konzistentní s tím, jak aplikace zřídí nového tenanta. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Demo-PerformanceMonitoringAndManagement.ps1* skript je k dispozici, která simuluje zatížení běžící proti víceklientské databázi. Zatížení je generováno pomocí jednoho z dostupných scénářů načítání:

| Ukázka | Scenario |
|:--|:--|
| 2 | Generovat normální zatížení svítivosti (přibližně 30 jednotek DTU) |
| 3 | Generování zátěže s delšími shluky na tenanta|
| 4 | Generování zátěže s vyšším nárůstem DTU na tenanta (přibližně 70 DTU)|
| 5 | Vygenerujte vysokou intenzitu (přibližně 90 DTU) na jednom tenantovi a normální zatížení intenzity na všech ostatních klientech. |

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v DTU), doba trvání a intervaly jsou v rámci všech databází různé a simulují nepředvídatelné aktivity tenanta.

1. V **prostředí POWERSHELL ISE** otevřete... \\ \\Demo-PerformanceMonitoringAndManagement.ps1pro monitorování a správu výkonu výukových modulů \\ ** Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario**  =  **2**, _Generovat normální zatížení intenzity_
1. Stisknutím klávesy **F5** použijte zatížení pro všechny klienty.

SaaS App Database je víceklientské SaaS aplikace a reálné zatížení aplikace SaaS je obvykle občas a nepředvídatelné. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Pro vzorek zatížení je potřeba několik minut, proto spusťte generátor zatížení po dobu 3-5 minut, než se pokusíte monitorovat zatížení v následujících oddílech.

> [!IMPORTANT]
> Generátor zatížení je spuštěn jako řada úloh v novém okně prostředí PowerShell. Při zavření relace se zastaví generátor zátěže. Generátor zatížení zůstane ve stavu *vyvolání úlohy* , kde generuje zatížení pro všechny nové klienty zřízené po spuštění generátoru. Pomocí *kombinace kláves CTRL-C* můžete zastavit vyvolání nových úloh a ukončit skript. Generátor zatížení bude nadále běžet, ale pouze ve stávajících klientech.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorování využití prostředků pomocí Azure Portal

Pokud chcete monitorovat využití prostředků, které jsou výsledkem aplikovaného zatížení, otevřete portál pro víceklientské databáze **tenants1**, která obsahuje klienty:

1. Otevřete [Azure Portal](https://portal.azure.com) a přejděte na server *tenants1-MT- &lt; &gt; User*.
1. Přejděte dolů a vyhledejte databáze a klikněte na **tenants1**. Tato databáze horizontálně dělené s více klienty obsahuje všechny dosud vytvořené klienty.

![databázový graf](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Sledujte graf **DTU** .

## <a name="set-performance-alerts-on-the-database"></a>Nastavení upozornění na výkon databáze

Nastavte upozornění na databázi, která aktivuje \> 75% využití následujícím způsobem:

1. V [Azure Portal](https://portal.azure.com)otevřete databázi *tenants1* (na *tenants1-MT- &lt; User &gt;* serveru).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento DTU**
   * **Podmínka = větší než**
   * **Prahová hodnota = 75**.
   * **Perioda = za posledních 30 minut**
1. Přidejte e-mailovou adresu do pole *Další e-maily správce* a klikněte na **OK**.

   ![nastavení upozornění](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Navýšení kapacity zaneprázdněné databáze

Pokud se úroveň zatížení zvětšuje v databázi na bod, který navyšuje databázi a dosáhne 100% využití DTU, dojde k ovlivnění výkonu databáze a potenciálně zpomaluje dobu odezvy na dotazy.

**Krátkodobé**, zvažte horizontální navýšení kapacity databáze a poskytování dalších prostředků nebo odebírání klientů z víceklientské databáze (jejich přesun z databáze s více klienty do samostatné databáze).

**Už** je vhodné zvážit optimalizaci dotazů nebo využití indexu a zvýšit tak výkon databáze. V závislosti na citlivosti aplikace k problémům s výkonem doporučujeme škálovat databázi až předtím, než dosáhne 100% využití DTU. Použijte výstrahu, abyste byli předem upozornění.

Můžete simulovat zaneprázdněnou databázi zvýšením zatížení vytvořeného generátorem. Způsob, jakým se klienti rozrůstá častěji a za delší dobu, zvyšují zatížení víceklientské databáze, aniž by bylo potřeba měnit požadavky jednotlivých tenantů. Vertikální navýšení kapacity databáze se snadno provádí na portálu nebo v PowerShellu. Při tomto cvičení se používá portál.

1. Nastavte *$DemoScenario*  =  **3**, _vygenerujte zatížení s delším a častými nárůsty na databázi_ , abyste zvýšili intenzitu agregovaného zatížení databáze beze změny maximálního zatížení, které vyžaduje každý tenant.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.
1. V Azure Portal přejdete do databáze **tenants1** .

Sledujte vyšší využití DTU databáze v horním grafu. Nové načtení nového zatížení trvá několik minut, ale měli byste rychle vidět, že se databáze začne přihlédnout k maximálnímu využití, a jako zátěžový stabilizuje do nového vzoru se rychle přetěžuje databáze.

1. Pro horizontální navýšení kapacity databáze klikněte na **cenová úroveň (DTU škálování)** v okně nastavení.
1. Upravte nastavení **DTU** na **100**. 
1. Kliknutím na **použít** odešlete žádost o škálování databáze.

Vraťte se zpět na **tenants1**  >  **Přehled** a zobrazte grafy monitorování. Sledujte účinek poskytnutí databáze více prostředky (i když s malým počtem klientů a náhodným zatížením se to už nesnadno lehce nepamatuje, dokud to nebudete mít nějakou dobu běžet). Při prohlížení grafů Pamatujte na to, že 100% v horním grafu nyní představuje 100 DTU, zatímco v dolním 100 grafu je% stále 50 DTU.

Databáze je v průběhu procesu online a plně dostupná. Kód aplikace by měl být vždy zapsán, aby bylo možné znovu vyřadit připojení, a pak se znovu připojí k databázi.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového tenanta ve vlastní databázi 

Model horizontálně dělené multi-tenant vám umožňuje zvolit, jestli se má zřídit nový tenant v databázi s více klienty současně s ostatními klienty, nebo jestli se má tenant zřídit v databázi sami. Když tenanta zřídíte ve své vlastní databázi, těží z izolace v samostatné databázi, což vám umožní spravovat výkon tohoto tenanta nezávisle na ostatních, obnovovat tohoto tenanta nezávisle na ostatních atd. Můžete se třeba rozhodnout, že budete chtít umístit bezplatnou zkušební verzi nebo běžné zákazníky do víceklientské databáze a zákazníky úrovně Premium v jednotlivých databázích.  Pokud se vytvoří izolované databáze s jedním tenantům, můžou se v elastickém fondu spravovat společně, aby se optimalizoval náklady na prostředky.

Pokud jste již zřídili nového tenanta ve vlastní databázi, přeskočte několik dalších kroků.

1. V **prostředí POWERSHELL ISE** otevřete... \\ Výukové moduly \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1*. 
1. Modify **$TenantName = "Salix Salsa"** a **$VenueType = "roztancoval"**
1. Nastavení **$Scenario**  =  **2** _zřízení tenanta v nové databázi s jedním tenantovi_
1. Stisknutím klávesy **F5** spusťte skript.

Skript zřídí tohoto tenanta v samostatné databázi, zaregistruje databázi a tenanta do katalogu a pak otevře stránku události tenanta v prohlížeči. Aktualizujte stránku centra událostí a zobrazí se jako místo "Salix Salsa".

## <a name="manage-performance-of-an-individual-database"></a>Správa výkonu jednotlivé databáze

Pokud má jeden tenant v rámci víceklientské databáze trvalé zatížení, může to být tím, že bude mít za následek značnou úroveň databázových prostředků a vliv na ostatní klienty ve stejné databázi. Pokud je aktivita pravděpodobně v určitou dobu pokračovat, může být klient dočasně přesunut z databáze a do vlastní databáze s jedním klientem. To umožňuje, aby tenant měl další prostředky, které potřebuje, a plně ho izoluje od ostatních tenantů.

Toto cvičení simuluje účinek Salix Salsa s vysokým zatížením, když lístky jdou na prodej pro oblíbenou událost.

1. Otevřete... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Nastavte **$DemoScenario = 5**, _vygenerujte normální zatížení plus vysoké zatížení v jednom tenantovi (přibližně 90 DTU)._
1. Set **$SingleTenantName = Salix Salsa**
1. Skript proveďte pomocí **F5**.

Přejděte na portál a přejděte k   >  **přehledu** salixsalsa a zobrazte grafy monitorování. 

## <a name="other-performance-management-patterns"></a>Další vzory správy výkonu

**Škálování samoobslužných služeb klienta**

Vzhledem k tomu, že škálování je úkol, který se snadno vyvolal prostřednictvím rozhraní API pro správu, můžete snadno vytvořit schopnost škálovat databáze tenantů do vaší aplikace zaměřené na tenanta a nabídnout ji jako funkci vaší služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Škálování databáze nahoru a dolů podle plánu tak, aby odpovídaly vzorům používání**

Kde agregované využití tenanta se řídí předvídatelnými vzorci použití, můžete použít Azure Automation ke škálování databáze směrem nahoru a dolů podle plánu. Můžete například škálovat databázi dolů po 18:00 a znovu před 6:00 v pracovních dnech, když víte, že se nachází v požadavcích na prostředek pro zrušení.

## <a name="next-steps"></a>Další kroky

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Simulace využití v databázi s více klienty horizontálně dělené spuštěním zadaného generátoru zatížení
> * Monitorování databáze při reakci na navýšení zatížení
> * Horizontální navýšení kapacity databáze na základě zvýšeného zatížení databáze
> * Zřízení tenanta v databázi s jedním klientem

## <a name="additional-resources"></a>Další zdroje informací

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Automatizace Azure](../../automation/automation-intro.md)