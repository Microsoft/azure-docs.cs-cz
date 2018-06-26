---
title: Sledování výkonu horizontálně dělené databáze Azure SQL víceklientské v aplikaci SaaS víceklientské | Microsoft Docs
description: Sledování a správa výkonu horizontálně dělené víceklientské Azure SQL database v aplikaci SaaS více klientů
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 75431715b5948525e92c99b778842d26a684da82
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753438"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Sledování a správa výkonu horizontálně dělené víceklientské Azure SQL database v aplikaci SaaS více klientů

V tomto kurzu jsou prozkoumali několik klíčových scénářů správy používány aplikací SaaS. Pomocí generátoru zatížení simulovat aktivity mezi databázemi horizontálně dělené víceklientské, integrované monitorování a výstrah funkcí služby SQL Database je ukázán.

Adresář Wingtip lístky SaaS víceklientské databáze aplikace používá horizontálně dělená data víceklientského modelu, kde je distribuován místo (klient) dat podle ID klienta do potenciálně více databází. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Abyste mohli využívat tohoto vzoru využití typické databáze, je možné databáze rozšířit nahoru a dolů optimalizovat náklady na řešení. S tímto typem vzor je důležité monitorovat využití prostředků databáze k zajištění, že zatížení jsou to bude přiměřeně vyvážená potenciálně více databází. Musíte také zajistěte, aby jednotlivé databáze mít dostatečné zdroje a nejsou stiskne jejich [DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus) omezení. V tomto kurzu prozkoumá způsoby, jak sledovat a spravovat databáze a postupy k provedení nápravné akce v reakci na rozdíly v zatížení.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Simulovat využití na databázi horizontálně dělené víceklientské spuštěním generátor zadaný zatížení
> * Monitorování databáze jako odpoví na zvýšení zatížení
> * Škálování databáze v reakci na zatížení vyšší databáze
> * Přidělení klienta do jednoho klienta databáze

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip lístky SaaS víceklientské databáze. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS víceklientské databáze](saas-multitenantdb-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Úvod k modelům správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. 

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Abyste se vyhnuli nutnosti ručně sledovat výkon, je nejúčinnější k **nastavit výstrahy, které aktivují, když databáze Stray Zbloudilá mimo normální rozsahy**.
* Reakce na krátkodobé kolísání úroveň výkonu databáze, **DTU úrovně můžete škálovat nahoru nebo dolů**. Pokud dojde k této kolísání na základě pravidelných nebo předvídatelný **škálování databáze může být naplánováno automaticky**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Reagovat dlouhodobější kolísání nebo změny v klientech, **jednotlivých klientů můžete přesunout do jiné databáze**.
* Reakce na krátkodobé zvyšování *jednotlivých* zatížení klienta **jednotlivých klientů můžete převzaté z databáze a přiřadit na úroveň výkonu jednotlivých**. Jakmile se snižuje zatížení, klient může vracet pak do databáze více klientů. Když je znám předem, klienty můžete přesunout pre-emptively, zajistěte, aby byl vždy prostředky, které potřebuje databázi a vyhnout se ovlivnění jiných klientů v databázi více klientů. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Pro databázi SQL je k dispozici v databázích monitorování a výstrahy. Toto integrované monitorování a výstrah je konkrétní prostředky, takže je možné použít pro malý počet prostředků, ale není vhodné při práci s mnoha prostředky.

Vysoký počet scénářů, při kterém pracujete s mnoha prostředky, [analýzy protokolů](https://azure.microsoft.com/services/log-analytics/) lze použít. Toto je samostatný služba Azure, která nabízí v porovnání s emitovaného diagnostické protokoly a telemetrie získané v pracovním prostoru analýzy protokolů analýzy. Analýzy protokolů můžete shromažďovat telemetrická data z mnoha služeb a použije k dotazování a nastavit výstrahy.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Zdrojový kód Wingtip lístky SaaS víceklientské databáze aplikace a skripty

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

Pro dobrou znalost jazyka jak funguje monitorování výkonu a správy ve velkém měřítku, tento kurz vyžaduje, abyste databáze horizontálně dělené víceklientské obsahuje několik klientů.

Pokud dávky klientů, které mají už zřízené v předchozí kurzu, pokračujte [simulovat využití na všechny databáze klienta](#simulate-usage-on-all-tenant-databases) části.

1. V **prostředí PowerShell ISE**, otevřete... \\Učení moduly\\sledování výkonu a správy\\*ukázku PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario** = **1**, _Zřízení dávky tenantů_
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 klienty do databáze více klientů za pár minut. 

*New-TenantBatch* skript vytvoří nové klienty s jedinečný klienta klíčů v horizontálně dělené databáze více klientů a inicializuje je typ názvu a místo klienta. To je konzistentní s způsob, jakým aplikace zřídí nového klienta. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Ukázku PerformanceMonitoringAndManagement.ps1* je skript zadaný, který simuluje zátěži v databázi více klientů. Zatížení je generována pomocí jednoho z dostupných zatížení scénářů:

| Ukázka | Scénář |
|:--|:--|
| 2 | Generovat normální intenzitou zatížení (přibližně 30 DTU) |
| 3 | Generovat zatížení s delší shluky každého klienta|
| 4 | Generovat zatížení s vyšší DTU shluky každého klienta (přibl 70 DTU)|
| 5 | Generovat vysokou intenzitou (přibližně 90 DTU) na jednoho klienta a normální intenzitou zatížení ve všech ostatních klientů |

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Mezi všechny databáze, simulaci aktivity nepředvídatelným klienta jsou nastaveny úrovních zátěže (v Dtu), doba trvání a intervaly.

1. V **prostředí PowerShell ISE**, otevřete... \\Učení moduly\\sledování výkonu a správy\\*ukázku PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario** = **2**, _generování normální intenzitou zatížení_
1. Stiskněte klávesu **F5** použít zatížení svým klientům.

Adresář Wingtip lístky SaaS víceklientské databáze je aplikace SaaS a reálného zatížení aplikace SaaS je obvykle ojediněle a nepředvídatelným. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Několik minut, je potřeba pro vzor zatížení objeví, takže spuštění generátor zatížení po dobu 3 až 5 minut před pokusem o monitorování zatížení v následujících částech.

> [!IMPORTANT]
> Generátor zatížení běží jako řadu úloh v novém okně prostředí PowerShell. Pokud zavřete relaci, generátor zatížení se zastaví. Generátor zatížení zůstává ve *vyvolání úlohy* stavu, kde vygeneruje zatížení žádné nové klienty, které jsou zřízené po spuštění generátor. Použití *Ctrl-C* zastavení vyvolání nové úlohy a ukončení skript. Generátor zatížení bude nadále spouštět, ale jenom na stávající klienty.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Sledování využití prostředků pomocí portálu Azure

Pokud chcete monitorovat využití prostředků, která je výsledkem zatížení, které jsou aplikovány, otevřete portál k databázi víceklientské **tenants1**, která obsahuje klienty:

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte na server *tenants1-mt -&lt;uživatele&gt;*.
1. Posuňte se dolů a najděte databáze a klikněte na tlačítko **tenants1**. Tato horizontálně dělené víceklientské databáze obsahuje všechny klienty, které dosud vytvořili.

![Graf databáze](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Sledovat **DTU** grafu.

## <a name="set-performance-alerts-on-the-database"></a>Nastavit výstrahy výkonu na databázi

Nastavit výstrahy na databázi, která aktivuje na \>75 % využití následujícím způsobem:

1. Otevřete *tenants1* databáze (na *tenants1-mt -&lt;uživatele&gt;*  serveru) v [portál Azure](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento DTU**
   * **Podmínka = větší než**
   * **Prahová hodnota = 75**.
   * **Období = za posledních 30 minut**
1. Přidání e-mailovou adresu k *další správce email(s)* pole a klikněte na tlačítko **OK**.

   ![nastavení upozornění](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Škálování zaneprázdněn databáze

Úroveň zatížení zvyšuje v databázi do bodu, aby maxes na databázi a dosáhne využití v jednotkách DTU 100 %, pak databázi ovlivňuje výkon, potenciálně zpomalení odezvy dotazů.

**Krátkodobý**, zvažte škálování zálohu databáze poskytnout další prostředky, nebo odebrání klientů z databáze víceklientské (jejich přesunutí z databáze nástroje víceklientské samostatné databáze).

**Dlouhodobější**, zvažte optimalizaci dotazů nebo indexu využití ke zlepšení výkonu databáze. V závislosti na citlivosti aplikačním výkon vydává jeho osvědčený postup škálování databáze před dosažením využití v jednotkách DTU 100 %. Použijte výstrahu, abyste byli předem upozornění.

Zvýšení zatížení vytváří generátorem můžete simulovat zaneprázdněn databáze. Způsobuje klientům, burst častěji a pro zvýšení zátěže víceklientské databáze beze změny požadavky na jednotlivé klienty. Škálování zálohu databáze je snadno udělat na portálu nebo z prostředí PowerShell. Při tomto cvičení se používá portál.

1. Nastavit *$DemoScenario* = **3**, _generování zatížení s delší a častější shluky na databázi_ zvýšit intenzitou agregační zatížení na databáze, aniž byste museli měnit zátěž ve špičce vyžadovanou každého klienta.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.
1. Přejděte na **tenants1** databáze na portálu Azure.

Monitoruje databázi vyšší využití v jednotkách DTU v horním grafu. Trvá několik minut, než nové vyšší zátěž nové, ale měli byste vidět rychle začít dosáhl maximální využití databáze a jako zatížení steadies do nové vzor, rychle přetížení databáze.

1. Škálování databáze, klikněte na tlačítko **cenová úroveň (škálování Dtu)** v okně nastavení.
1. Upravit **DTU** nastavení **100**. 
1. Klikněte na tlačítko **použít** odeslat žádost o škálování databáze.

Přejděte zpět na **tenants1** > **přehled** zobrazení monitorování grafy. Sledování účinku databázi poskytování více prostředků (i když několik klientů a náhodnou zatížení není vždy snadno zjistit jednoznačně, dokud nespustíte nějakou dobu). Při prohlížení opatřeny grafy Pamatujte, že 100 % horním grafu nyní představuje 100 Dtu, zatímco na nižší graf 100 % je stále 50 Dtu.

Databáze je v průběhu procesu online a plně dostupná. Kód aplikace budou zasílány vždy opakovat vynechaných připojení a proto bude připojení k databázi.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového klienta ve vlastní databázi 

Horizontálně dělené víceklientského modelu můžete zvolit, jestli ke zřízení nového klienta v databázi víceklientské souběžně s jinými klienty nebo pro zřízení klienta na databázi. Služba klienta v svou vlastní databázi, výhody z izolace vyplývajících z samostatné databáze, abyste mohli spravovat výkon tohoto klienta nezávisle na ostatních, obnovení tohoto klienta nezávisle na ostatních, atd. Můžete například zvolit chápat jednotlivé databáze bez zkušební verze nebo běžné zákazníků v databázi více klientů a zákazníky premium.  Pokud jsou vytvořeny izolované databáze jednoho klienta, se můžete dál spravovat souhrnně v elastickém fondu za účelem optimalizace prostředků náklady.

Pokud už zřízené v svou vlastní databázi nového klienta, přeskočte několika dalších krocích.

1. V **prostředí PowerShell ISE**, otevřete... \\Učení moduly\\ProvisionTenants\\*ukázku ProvisionTenants.ps1*. 
1. Upravit **$TenantName = "Salix Salsa"** a **$VenueType = "tance"**
1. Nastavit **$Scenario** = **2**, _zřídit klienta v novou databázi jednoho klienta_
1. Stisknutím klávesy **F5** spusťte skript.

Skript bude zřízení tohoto klienta v samostatné databáze, zaregistrujte databáze a klienta s katalogem a pak otevřete klienta události stránku v prohlížeči. Aktualizujte stránce centra událostí a zobrazí se, že "Salix Salsa" byla přidána jako místo.

## <a name="manage-performance-of-a-single-database"></a>Správa výkonu služby jedné databáze

Pokud jednoho klienta v databázi víceklientské vyskytne dlouhodobě vysokého zatížení, je většinou dominujte prostředky databáze a mít vliv na ostatní klienti ve stejné databázi. Pokud je aktivita může pokračovat po určitý čas, klient může dočasně přesunout z databáze a do databáze svůj vlastní jednoho klienta. To umožňuje klientovi další prostředky potřebuje a plně ji izoluje od ostatních klientů.

Tento postup vytvoří efekt Salix Salsa zaznamenat vysokého zatížení při lístků, přejděte na prodej oblíbených události.

1. Otevřete... \\ *Ukázku PerformanceMonitoringAndManagement.ps1* skriptu.
1. Nastavit **$DemoScenario = 5**, _generovat Normální zatížení a vysoké zatížení na jednoho klienta (přibližně 90 DTU)._
1. Nastavit **$SingleTenantName = Salix Salsa**
1. Skript proveďte pomocí **F5**.

Přejděte na portál a přejděte do **salixsalsa** > **přehled** zobrazení monitorování grafy. 

## <a name="other-performance-management-patterns"></a>Dalšími vzory správy výkonu

**Samoobslužná služba klienta škálování**

Protože škálování je úloha snadno názvem přes správu rozhraní API, můžete snadno vytvářet schopnost škálování databáze klienta do své aplikace na straně klienta a nabízejí jako součást služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Škálování databáze nahoru a dolů na plán tak, aby odpovídaly vzorce používání**

Kde využití agregační klienta odpovídá vzorce používání předvídatelné, můžete v Azure Automation nahoru a dolů škálování databáze podle plánu. Například škálování databáze po 18: 00 a až znovu před 6: 00 ve všední dny víte, když je pokles požadavků na prostředky.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Simulovat využití na databázi horizontálně dělené víceklientské spuštěním generátor zadaný zatížení
> * Monitorování databáze jako odpoví na zvýšení zatížení
> * Škálování databáze v reakci na zatížení vyšší databáze
> * Přidělení klienta do jednoho klienta databáze

## <a name="additional-resources"></a>Další zdroje informací:

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)