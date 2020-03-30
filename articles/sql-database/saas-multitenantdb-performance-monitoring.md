---
title: Sledování výkonu databáze s oddíly více klientů
description: Sledování a správa výkonu víceklientské databáze Azure SQL s více klienty ve víceklientské aplikaci SaaS
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 0af476b69f2effd836fe76d62059259076c16f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214165"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Sledování a správa výkonu víceklientské databáze Azure SQL s více klienty ve víceklientské aplikaci SaaS

V tomto kurzu jsou prozkoumány několik klíčových scénářů správy výkonu používaných v aplikacích SaaS. Pomocí generátorzatížení simulovat aktivitu napříč vrstvené víceklientské databáze, integrované monitorování a upozorňování funkce SQL Database jsou demonstrovány.

Wingtip Tickets SaaS Víceklientská databázová aplikace používá oborový datový model s více tenanty, kde jsou data místa (tenanta) distribuována podle ID klienta napříč potenciálně více databázemi. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Chcete-li využít tento vzor typické využití databáze, databáze lze škálovat nahoru a dolů optimalizovat náklady na řešení. S tímto typem vzoru je důležité sledovat využití prostředků databáze, aby bylo zajištěno, že zatížení jsou přiměřeně vyvážené napříč potenciálně více databází. Je také třeba zajistit, že jednotlivé databáze mají odpovídající prostředky a nejsou dosažení jejich limity [DTU.](sql-database-purchase-models.md#dtu-based-purchasing-model) Tento kurz zkoumá způsoby monitorování a správu databází a jak provést nápravná opatření v reakci na odchylky v zatížení.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> 
> * Simulace využití v databázi s více klienty s dělené pomocí spuštění generátoru zatížení za předpokladu,
> * Sledujte databázi tak, jak reaguje na zvýšení zatížení
> * Škálování databáze v reakci na zvýšené zatížení databáze
> * Zřízení klienta do databáze s jedním tenantem

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Wingtip Vstupenky SaaS Víceklientdatabáze aplikace je nasazena. Informace o nasazení za méně než pět minut najdete v [tématu Nasazení a prozkoumání aplikace Wingtip Tickets SaaS Multi-tenant Database](saas-multitenantdb-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Úvod do vzorců správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. 

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Chcete-li se vyhnout ručnímu sledování výkonu, je nejúčinnější **nastavit výstrahy, které se aktivují, když databáze zabloudí z normálního rozsahu**.
* Chcete-li reagovat na krátkodobé výkyvy ve výpočetní velikosti databáze, **úroveň DTU lze škálovat nahoru nebo dolů**. Pokud k tomuto kolísání dochází v pravidelných nebo předvídatelných základě, **škálování databáze může být naplánováno dojít automaticky**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Chcete-li reagovat na dlouhodobější výkyvy nebo změny v tenantech, **mohou být jednotliví klienti přesunuti do jiné databáze**.
* Chcete-li reagovat na krátkodobé zvýšení *zatížení jednotlivých* klientů, **mohou být jednotliví klienti vyjmuti z databáze a přiřazena individuální velikost výpočetních prostředků**. Jakmile je zatížení snížena, klienta pak lze vrátit do databáze více klientů. Pokud je to známo předem, klienti mohou být přesunuty preventivně zajistit, že databáze má vždy prostředky, které potřebuje, a aby se zabránilo dopadu na ostatní klienty v databázi více klientů. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Pro SQL Database je monitorování a upozorňování k dispozici v databázích. Toto integrované monitorování a výstrahy jsou specifické pro prostředky, takže je vhodné použít pro malý počet prostředků, ale není vhodné při práci s mnoha prostředky.

Pro scénáře s velkým objemem, kde pracujete s mnoha prostředky, lze použít [protokoly Azure Monitoru.](https://azure.microsoft.com/services/log-analytics/) Toto je samostatná služba Azure, která poskytuje analýzy přes emitované protokoly shromážděné v pracovním prostoru Log Analytics. Protokoly Azure Monitor můžete shromažďovat telemetrie z mnoha služeb a slouží k dotazování a nastavení výstrah.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získejte Wingtip Tickets SaaS Víceklientský databázový kód aplikace a skripty

Wingtip Tickets SaaS Multi-tenant databázové skripty a zdrojový kód aplikace jsou k dispozici v [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Úložiště GitHub. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

Pro dobré pochopení toho, jak funguje monitorování a správa výkonu ve velkém měřítku, tento kurz vyžaduje, abyste měli více klientů v horizontálně dělené databázi více klientů.

Pokud jste již zřízené dávky klientů v předchozím kurzu, přejděte na [simulovat využití na všech databázíklientů](#simulate-usage-on-all-tenant-databases) části.

1. V **prostředí PowerShell ISE**otevřete ... \\Výukové moduly\\Sledování\\výkonu a správa*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario** = **1**, _Zřídit dávku klientů_
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 klientů do databáze s více klienty během několika minut. 

*New-TenantBatch* skript vytvoří nové klienty s jedinečnými klíči tenanta v rámci databáze s více klienty a inicializuje je s názvem klienta a typu místa konání. To je v souladu se způsobem, jakým aplikace zřizována nového klienta. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Demo-PerformanceMonitoringAndManagement.ps1* skript je k dispozici, který simuluje zatížení spuštěné proti databázi více klientů. Zatížení je generováno pomocí jednoho ze scénářů načítání k dispozici:

| Ukázka | Scénář |
|:--|:--|
| 2 | Generovat normální intenzitu zatížení (přibližně 30 DTU) |
| 3 | Generovat zatížení s delšími shluky na klienta|
| 4 | Generovat zatížení s vyššími shluky DTU na klienta (přibližně 70 DTU)|
| 5 | Generovat vysokou intenzitu (přibližně 90 DTU) na jednoho klienta plus normální intenzita zatížení na všechny ostatní klienty |

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v DTU), doba trvání a intervaly se liší ve všech databázích, simulující nepředvídatelné aktivity klienta.

1. V **prostředí PowerShell ISE**otevřete ... \\Výukové moduly\\Sledování\\výkonu a správa*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario** = **2**, Generovat _normální intenzitu zatížení_
1. Stisknutím **klávesy F5** aplikujte zatížení na všechny své klienty.

Wingtip Tickets SaaS Multi-tenant Database je aplikace SaaS a reálné zatížení aplikace SaaS je obvykle sporadické a nepředvídatelné. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Několik minut je zapotřebí, aby se objevil vzor zatížení, takže spusťte generátor zatížení po dobu 3-5 minut, než se pokusíte sledovat zatížení v následujících částech.

> [!IMPORTANT]
> Generátor zatížení běží jako řada úloh v novém okně prostředí PowerShell. Pokud ukončíte relaci, generátor zatížení se zastaví. Generátor zatížení zůstává ve stavu *vyvolání úlohy,* kde generuje zatížení všech nových klientů, které jsou zřízeny po spuštění generátoru. Pomocí *kombinace kláves Ctrl-C* můžete zastavit vyvolání nových úloh a ukončit skript. Generátor zatížení bude nadále spuštěn, ale pouze u stávajících klientů.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorování využití prostředků pomocí portálu Azure

Chcete-li sledovat využití prostředků, které je výsledkem použitého zatížení, otevřete portál do databáze s více **klienty, tenanty1**, obsahující klienty:

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte na *serverových&lt;&gt;tenantů1-mt- USER*.
1. Posuňte se dolů a vyhledejte databáze a klepněte na **položku Tenants1**. Tato databáze s více klienty s oddíly jsou tvořena všemi dosud vytvořenými klienty.

![databázový graf](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Sledujte tabulku **DTU.**

## <a name="set-performance-alerts-on-the-database"></a>Nastavení upozornění na výkon databáze

Nastavte výstrahu v databázi, \>která se aktivuje při 75 % využití následujícím způsobem:

1. Otevřete *databázi tenantů1* (na *serveru tenants1-mt-&lt;USER)&gt; * na webu Azure [Portal](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento DTU**
   * **Podmínka = větší než**
   * **Prahová hodnota = 75**.
   * **Tečka = Za posledních 30 minut**
1. Přidejte e-mailovou adresu do pole *Další e-maily správce* a klepněte na tlačítko **OK**.

   ![nastavení upozornění](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Škálování naverteneji zaneprázdněnou databázi

Pokud se úroveň zatížení zvyšuje v databázi do té míry, že maxes z databáze a dosáhne 100 % využití DTU, pak je ovlivněn výkon databáze, potenciálně zpomaluje časy odezvy dotazu.

**Krátkodobé**, zvažte škálování databáze poskytnout další prostředky nebo odebrání klientů z databáze více klientů (jejich přesunutí z databáze více klientů do samostatné databáze).

**Dlouhodobější**, zvažte optimalizaci dotazů nebo využití indexu ke zlepšení výkonu databáze. V závislosti na citlivosti aplikace na výkon vydává své osvědčené postupy pro škálování databáze před dosažením 100 % využití DTU. Použijte výstrahu, abyste byli předem upozornění.

Můžete simulovat zaneprázdněné databáze zvýšením zatížení produkované generátoru. Způsobuje klienti shlukovat častěji a déle, zvýšení zatížení databáze více klientů beze změny požadavků jednotlivých klientů. Škálování databáze se snadno provádí na portálu nebo z Prostředí PowerShell. Při tomto cvičení se používá portál.

1. Nastavte *$DemoScenario* = **3**, Generovat zatížení s delší a _častější shluky na databázi_ zvýšit intenzitu agregační zatížení v databázi beze změny zatížení ve špičce vyžadované každého klienta.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.
1. Přejděte do databáze **tenantů1** na webu Azure Portal.

Sledujte zvýšené využití databáze DTU v horním grafu. Trvá několik minut pro nové vyšší zatížení nakopnout, ale měli byste rychle vidět, že databáze začít přístupit maximální využití a jako zatížení ustálí do nového vzoru, rychle přetížení databáze.

1. Chcete-li databázi vertikálně navýšit kapacitu, klikněte v okně nastavení na **Cenovou úroveň (škálování dtu).**
1. Nastavte **DTU** na **100**. 
1. Klepnutím na tlačítko **Použít** odešlete požadavek na škálování databáze.

Vraťte se do **tenantů1** > **Přehled** zobrazit grafy monitorování. Sledujte účinek poskytování databáze s více prostředků (i když s několika klienty a randomizované zatížení není vždy snadné vidět přesvědčivě, dokud spustit na nějakou dobu). Zatímco se díváte na grafy, mějte na paměti, že 100% v horním grafu nyní představuje 100 DTU, zatímco na dolním grafu 100% je stále 50 DTU.

Databáze je v průběhu procesu online a plně dostupná. Kód aplikace by měl být vždy zapsán, aby se pokusili o přerušená připojení, a proto se znovu připojí k databázi.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového klienta ve vlastní databázi 

Oborový model s více tenanty umožňuje zvolit, zda chcete zřídit nového klienta v databázi s více klienty vedle jiných klientů, nebo zřídit klienta v vlastní databázi. Zřízením tenanta ve své vlastní databázi těží z izolace vlastní samostatné databázi, což umožňuje spravovat výkon tohoto klienta nezávisle na ostatních, obnovit tohoto klienta nezávisle na ostatních atd. Můžete se například rozhodnout umístit bezplatné zkušební nebo běžné zákazníky do databáze s více klienty a prémiové zákazníky v jednotlivých databázích.  Pokud jsou vytvořeny izolované databáze s jedním tenantem, lze je stále spravovat společně v elastickém fondu pro optimalizaci nákladů na prostředky.

Pokud jste již zřídit nového klienta ve své vlastní databázi, přeskočte několik dalších kroků.

1. V **prostředí PowerShell ISE**otevřete ... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Změnit **$TenantName = "Salix Salsa"** a **$VenueType = "tanec"**
1. Nastavit **$Scenario** = **2**, _Zřízení klienta v nové databázi s jedním klientem_
1. Stisknutím klávesy **F5** spusťte skript.

Skript zřídí tohoto klienta v samostatné databázi, zaregistruje databázi a klienta s katalogem a otevře stránku Události klienta v prohlížeči. Aktualizujte stránku Centra událostí a jako místo konání se zobrazí "Salix Salsa".

## <a name="manage-performance-of-an-individual-database"></a>Správa výkonu jednotlivých databází

Pokud jeden klient v rámci databáze s více klienty dochází k trvalé vysoké zatížení, může mít tendenci dominují databázové prostředky a vliv na ostatní klienty ve stejné databázi. Pokud aktivita je pravděpodobné, že bude pokračovat po určitou dobu, klient může být dočasně přesunutz databáze do vlastní databáze s jedním tenantem. To umožňuje tenantovi mít další prostředky, které potřebuje, a plně je izoluje od ostatních klientů.

Toto cvičení simuluje účinek Salix Salsa zažívá vysoké zatížení, když vstupenky jít na prodej pro populární událost.

1. Otevřete ... \\ *Ukázkový skript PerformanceMonitoringAndManagement.ps1.*
1. Nastavit **$DemoScenario = 5**, Generovat normální zatížení plus vysoké zatížení na jednoho _klienta (přibližně 90 DTU)._
1. Sada **$SingleTenantName = Salix Salsa**
1. Skript proveďte pomocí **F5**.

Přejděte na portál a přejděte na **salixsalsa** > **Přehled** pro zobrazení monitorovacích grafů. 

## <a name="other-performance-management-patterns"></a>Další vzorce správy výkonu

**Samoobslužné škálování klienta**

Vzhledem k tomu, že škálování je úloha snadno volat prostřednictvím rozhraní API pro správu, můžete snadno vytvořit možnost škálování databází klientů do klienta aplikace a nabídnout ji jako funkci služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Škálování databáze nahoru a dolů podle plánu tak, aby odpovídala vzorcům využití**

Kde agregované využití tenanta sleduje předvídatelné vzorce využití, můžete pomocí Azure Automation škálovat databázi nahoru a dolů podle plánu. Například škálování databáze dolů po osmnáct hodin a znovu před 6 hodin ráno ve všední dny, když víte, že je pokles požadavků na prostředky.

## <a name="next-steps"></a>Další kroky

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Simulace využití v databázi s více klienty s dělené pomocí spuštění generátoru zatížení za předpokladu,
> * Sledujte databázi tak, jak reaguje na zvýšení zatížení
> * Škálování databáze v reakci na zvýšené zatížení databáze
> * Zřízení klienta do databáze s jedním tenantem

## <a name="additional-resources"></a>Další zdroje

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)