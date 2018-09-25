---
title: Sledování výkonu horizontálně dělené databáze Azure SQL více tenantů v SaaS aplikaci s více tenanty | Dokumentace Microsoftu
description: Monitorování a správa výkonu horizontálně dělené databázi Azure SQL s více tenanty v aplikaci SaaS s více tenanty
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: 873660f362d2ad0002f512f911d4149519092787
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055941"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorování a správa výkonu horizontálně dělené databázi Azure SQL s více tenanty v aplikaci SaaS s více tenanty

V tomto kurzu jsou prozkoumali několika důležitým scénářům řízení výkonu použít v aplikacích SaaS. Pomocí generátoru zatížení simulovat aktivity v horizontálně dělené databáze s více tenanty, integrované monitorování a upozorňování funkcí služby SQL Database je ukázán.

Aplikace s víceklientskou databázi s SaaS aplikace Wingtip Tickets používá model horizontálně dělených dat s více tenanty, kde místo (tenant) data jsou distribuovaná podle ID tenanta v potenciálně více databázích. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Chcete-li využít výhod tohoto typického vzoru používání databáze, databáze je možné škálovat nahoru a dolů optimalizovat náklady na řešení. S tímto typem vzorců je důležité monitorovat využití prostředků databáze k zajištění, že zatížení jsou přiměřeně vyvážená přetížení mezi potenciálně více databází. Je také potřeba zajistit, že jednotlivé databáze mají adekvátní prostředky a nedosahují jejich [DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus) omezení. Tento kurz se věnuje způsobům monitorování a Správa databází a jak k provedení nápravné akce v reakci na variace v úloze.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Simulace použití v horizontálně dělené databázi s více tenanty pomocí dodaného generátoru zatížení
> * Monitorujte databáze, jako jsou reaguje na zvýšení zatížení
> * Vertikálně navýšit kapacitu databáze v reakci na zvýšení zatížení databáze
> * Zřizování tenanta do databáze s jedním tenantem

Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Databázi s více tenanty SaaS aplikace Wingtip Tickets aplikace je nasazená. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání aplikace Wingtip Tickets SaaS víceklientskou databází](saas-multitenantdb-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Úvod do principu správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. 

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Abyste nemuseli výkon monitorovat ručně, je nejvhodnějším **nastavit upozornění, která se aktivuje, když databáze odchýlí od normálního rozsahu**.
* Reagovat na krátkodobé kolísání v výpočetního prostředí databáze **jednotek DTU úrovně je možné škálovat směrem nahoru nebo dolů**. Pokud tomuto kolísání dochází pravidelně nebo je předvídatelné základ, **škálování databáze může být naplánováno do automaticky**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Reagovat na dlouhodobější kolísání nebo změny v tenantech, **jednotlivých tenantů, lze přesunout do jiné databáze**.
* Reagovat na krátkodobé zvýšení *jednotlivé* tenanta zatížení **jednotlivé tenanty můžete vyjímají z databáze a přiřazené velikosti jednotlivých výpočetních**. Po snížení zatížení je tenanta může být vrátili, aby databázi s více tenanty. Pokud je to víte předem, tenanti můžou přesouvat preventivně, ujistěte se, že databáze vždy obsahuje potřebné prostředky, které potřebuje a vyhnout se dopad na jiných tenantů v databázi s více tenanty. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Pro službu SQL Database monitorování a upozorňování je k dispozici v databázích. Toto integrované monitorování a upozorňování je specifické podle prostředků, takže je pohodlné používat ho u malého počtu prostředků, ale není vhodné při práci s mnoha prostředky.

U velkoobjemových scénářů, ve kterém pracujete s mnoha prostředky, [Log Analytics](https://azure.microsoft.com/services/log-analytics/) lze použít. Toto je samostatná služba Azure, která poskytuje analýzy přes vydávané diagnostické protokoly a telemetrii získanou v pracovním prostoru log analytics. Log Analytics může shromažďovat telemetrii z mnoha služeb a použije k dotazování a nastavování výstrah.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Získat zdrojový kód aplikace Wingtip Tickets SaaS databázi s více tenanty a skripty

Jsou k dispozici v databázi s více tenanty SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

Pro dostatečné povědomí o monitorování a správa výkonu fungování ve velkém měřítku, tento kurz vyžaduje, abyste měli více tenantů v horizontálně dělené databázi s více tenanty.

Pokud jste už zřídili dávku tenantů v předchozím kurzu, pokračujte [simulace použití ve všech databázích tenantů](#simulate-usage-on-all-tenant-databases) oddílu.

1. V **prostředí PowerShell ISE**, otevřete... \\Learning Modules\\monitorování a správa výkonu\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario** = **1**, _Zřízení dávky tenantů_
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 tenantů do víceklientskou databázi za pár minut. 

*New-TenantBatch* skript vytvoří nové tenanty s tenantem jedinečné klíče v rámci horizontálně dělené databázi s více tenanty a inicializuje s typem název a místní příslušností tenanta. To je konzistentní se způsobem, jakým aplikace zřizuje nového tenanta. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Demo-PerformanceMonitoringAndManagement.ps1* skriptu je k dispozici, který simuluje úlohu spuštěnou na databázi s více tenanty. Zatížení je generována pomocí jednoho z dostupných zatížení scénářů:

| Ukázka | Scénář |
|:--|:--|
| 2 | Generování normální intenzity zatížení (přibližně 30 jednotek DTU) |
| 3 | Generování zatížení s delší nárůsty zatížení na tenanta|
| 4 | Generování zatížení s vyššími nárůsty DTU na tenantovi (přibližně 70 DTU)|
| 5 | Vysoká míra generovat (přibližně 90 jednotek DTU) na jednoho tenanta plus normální intenzity zatížení na všech ostatních tenantů |

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v jednotkách Dtu), doba trvání a intervaly jsou napříč všemi databázemi, které simulují nepředvídanou činnost tenanta různé.

1. V **prostředí PowerShell ISE**, otevřete... \\Learning Modules\\monitorování a správa výkonu\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario** = **2**, _generování normální intenzity zatížení_
1. Stisknutím klávesy **F5** použijte zatížení pro všechny tenanty.

Databázi s více tenanty SaaS aplikace Wingtip Tickets je SaaS aplikace a reálné zatížení aplikace SaaS je obvykle sporadické a nepředvídatelné. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Několik minut, než je třeba vzor zatížení objeví, proto generátor zatížení běžel 3 až 5 minut před pokusem o sledování zatížení v následujících částech.

> [!IMPORTANT]
> Generátor zatížení běží jako řada úloh v novém okně prostředí PowerShell. Pokud zavřete relaci, generátor zatížení se zastaví. Generátor zatížení zůstává ve *vyvolání úlohy* stavu, ve kterém generuje zatížení na všech nových tenantů, které jsou zřízené po spuštění generátor kódu. Použití *Ctrl-C* zastavit vyvolání nové úlohy a zavřete tento skript. Generátor zatížení budou dál běžet, ale pouze tenantům.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorování využití prostředků pomocí webu Azure portal

Pokud chcete monitorovat využití prostředků, která je výsledkem použití zatížení, otevřete portál na databázi s více tenanty **tenants1**, obsahující klienty:

1. Otevřít [webu Azure portal](https://portal.azure.com) a přejděte na server *tenants1-mt –&lt;uživatele&gt;*.
1. Posuňte se dolů a najděte databáze a klikněte na tlačítko **tenants1**. Tato horizontálně dělené databázi s více tenanty obsahuje všechny dosud vytvořené klienty.

![databáze grafu](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Podívejte se **DTU** grafu.

## <a name="set-performance-alerts-on-the-database"></a>Nastavení výstrah výkonu na databázi

Nastavení upozornění na databázi, která se spustí při \>75 % využití následujícím způsobem:

1. Otevřít *tenants1* databáze (na *tenants1-mt –&lt;uživatele&gt;*  serveru) v [webu Azure portal](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento DTU**
   * **Podmínka = je větší než**
   * **Prahová hodnota = 75**.
   * **Období = více než posledních 30 minut**
1. Přidat e-mailovou adresu k *další email(s) správce* pole a klikněte na tlačítko **OK**.

   ![nastavení upozornění](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Vertikálně navýšit kapacitu zaneprázdněné databáze

Hodnota se zvyšuje úroveň zatížení na databázi do bodu, navyšuje na databázi a dosáhne 100 % využití DTU, pak výkonu databáze je a potenciálně vykazuje pomalejší doby odezvy.

**Z krátkodobého**, zvažte navýšení kapacity databáze, aby poskytoval další prostředky, nebo odebrání tenantů z databáze více tenantů (přemístěním mimo databázi s více tenanty do samostatné databáze).

**Dlouhodobější**, uvažujte o optimalizaci dotazů nebo indexu využití kvůli zlepšení výkonu databáze. V závislosti na tom aplikace citlivá na výkon vydá osvědčilo navýšit databázi předtím, než dosáhne 100 % využití DTU. Použijte výstrahu, abyste byli předem upozornění.

Zaneprázdněná databáze můžete simulovat tak, že zvýšíte zatížení vytvořené generátorem. Příčinou klientům pro rozšíření už, častěji a pro zvýšení zatížení na databázi s více tenanty beze změny požadavků jednotlivých tenantů. Škálování databáze se snadno provádí na portálu nebo z Powershellu. Při tomto cvičení se používá portál.

1. Nastavte *$DemoScenario* = **3**, _generování zatížení s delšími a častějšími nárůsty zatížení na databázi_ na zvýšení intenzity agregovaného zatížení na databáze beze změny vrcholového zatížení požadovaného každého tenanta.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.
1. Přejděte **tenants1** databáze na webu Azure Portal.

Monitorování databáze zvýšené využití DTU v grafu nahoře. Trvá několik minut, než se nové vyšší zatížení se rozjíždí, ale měli byste rychle zaznamenat databáze začíná dosahovat maximální využití a jak se zatížení stabilizuje na novém vzorci, rychle přetížení databáze.

1. Pokud chcete vertikálně navýšit její kapacitu, klikněte na tlačítko **cenová úroveň (Dtu škálování)** v okně nastavení.
1. Upravit **DTU** nastavení **100**. 
1. Klikněte na tlačítko **použít** a odeslat žádost o škálování databáze.

Přejděte zpět na **tenants1** > **přehled** k zobrazení monitorovacích grafů. Monitorujte efekt zřizování databáze s více prostředky (i když se několik tenantů a náhodným zatížením není vždy nezvratně dokud nespustíte nechystáte nějakou dobu). Když se díváte grafů mějte, že 100 % horním grafu teď reprezentuje 100 Dtu, zatímco v dolním grafu je 100 % 50 Dtu.

Databáze je v průběhu procesu online a plně dostupná. Kód aplikace by měl vždy být zapsán do porušené propojení a proto bude připojení k databázi.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Zřízení nového tenanta v její vlastní databázi 

Horizontálně dělené víceklientského modelu můžete zvolit, jestli se ke zřízení nového tenanta v databázi více tenantů vedle jiných tenantů nebo pro zřízení tenanta na databázi. Tím, že zajistíte tenanta v její vlastní databázi, jeho výhody plynou z izolace vyplývajících z samostatné databáze, abyste mohli spravovat výkon tohoto tenanta nezávisle na ostatních, obnovení tohoto tenanta nezávisle na ostatních, atd. Můžete například umístit bezplatné zkušební verze nebo běžné zákazníky v databázi s více tenanty a zákazníci premium v jednotlivých databázích.  Pokud se vytvoří oddělené databáze s jedním tenantem, se stále dala spravovat společně v elastickém fondu optimalizovat náklady na prostředky.

Pokud jste už zřídili nového tenanta v její vlastní databázi, přeskočte několik dalších kroků.

1. V **prostředí PowerShell ISE**, otevřete... \\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Upravit **$TenantName = "Salix Salsa"** a **$VenueType = "taneček"**
1. Nastavte **$Scenario** = **2**, _zřizování tenanta v nové databáze s jedním tenantem_
1. Stisknutím klávesy **F5** spusťte skript.

Skript zřízení tohoto tenanta v samostatné databáze, databáze a tenanta zaregistrovat v katalogu a v prohlížeči otevřete stránku události vašeho tenanta. Aktualizujte stránku centra událostí a uvidíte, že "Salix Salsa" se přidala jako jako místo.

## <a name="manage-performance-of-a-single-database"></a>Správa výkonu izolované databáze

Pokud jednoho tenanta v databázi s více tenanty stabilně vysoké zatížení, může mají tendenci dominovat prostředkům databáze a mít vliv na ostatní tenanti ve stejné databázi. Pokud je aktivita může nějakou dobu pokračovat, tenanta může být dočasně přesunul z databáze a samostatné databáze s jedním tenantem. To umožňuje, aby měl další prostředky a zase se plně izolovat ji od ostatních tenantů tenant.

Toto cvičení simuluje vliv Salix Salsa dochází k vysoké zatížení, pokud lístky prodávat pro oblíbené událost.

1. Otevřít... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skriptu.
1. Nastavte **$DemoScenario = 5**, _generování normálního a vysokého zatížení v jednom tenantovi (přibližně 90 jednotek DTU)._
1. Nastavte **$SingleTenantName = Salix Salsa**
1. Skript proveďte pomocí **F5**.

Přejděte na portál a přejděte do **salixsalsa** > **přehled** k zobrazení monitorovacích grafů. 

## <a name="other-performance-management-patterns"></a>Další vzorce správy výkonu

**Samoobslužné škálování tenanta**

Protože škálování je úkol, snadno volá prostřednictvím rozhraní API, můžete snadno vytvořit možnost škálovat databáze tenantů do aplikace směřující k tenantovi a nabízet ji jako funkci služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Škálování databází navýšení nebo snížení kapacity podle plánu se vzorci využití**

Pokud využití agregace tenanta probíhá podle předvídatelných vzorců použití, můžete pomocí Azure Automation databázi škálování nahoru i dolů podle plánu. Například databázi škálovat po 18: 00 a nahoru znovu před 6: 00 ve všední dny, ve kterých víte existuje pokles požadavků na prostředky.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Simulace použití v horizontálně dělené databázi s více tenanty pomocí dodaného generátoru zatížení
> * Monitorujte databáze, jako jsou reaguje na zvýšení zatížení
> * Vertikálně navýšit kapacitu databáze v reakci na zvýšení zatížení databáze
> * Zřizování tenanta do databáze s jedním tenantem

## <a name="additional-resources"></a>Další zdroje informací:

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)