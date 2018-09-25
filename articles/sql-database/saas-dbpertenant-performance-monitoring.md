---
title: Sledování výkonu v aplikaci SaaS s více tenanty mnoho databází Azure SQL | Dokumentace Microsoftu
description: Monitorování a správa výkonu databáze Azure SQL a fondů v aplikaci SaaS s více tenanty
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
ms.openlocfilehash: e774394eeb95fbc8d80e181a614a7e30258a100e
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47056766"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Monitorování a správa výkonu databáze Azure SQL a fondů v aplikaci SaaS s více tenanty

V tomto kurzu jsou prozkoumali několika důležitým scénářům řízení výkonu použít v aplikacích SaaS. Pomocí generátoru zatížení simulovat aktivity ve všech tenantských databázích, integrované monitorování a upozorňování funkcí služby SQL Database a elastické fondy jsou jsme vám ukázali.

Aplikace Wingtip Tickets SaaS databáze na Tenanta používá model dat s jedním tenantem, kde každé místo (tenant) má svou vlastní databázi. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Abyste mohli využít výhod tohoto typického vzoru používání databáze, databáze tenantů se nasazují do elastických databázových fondů. Elastické fondy optimalizují náklady na řešení prostřednictvím sdílení prostředků mezi mnoha databázemi. S tímto typem vzorců je důležité monitorovat využití databáze a prostředků fondu k zajištění, že jsou přiměřeně vyvážená přetížení mezi jednotlivými fondy. Je také potřeba zajistit, že jednotlivé databáze mají adekvátní prostředky a že fondy nedosahují limitů [eDTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus). Tento kurz se věnuje způsobům monitorování a správy databází a fondů a uvádí, jak se provádějí nápravné akce v reakci na variace v úloze.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta. Nasazení za méně než pět minut najdete v tématu [nasazení a zkoumání aplikace Wingtip Tickets SaaS databáze na Tenanta](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Úvod do principu správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. Při hostování více tenantů představují fondy elastické databáze nenákladný způsob zajištění a správy prostředků pro skupinu databází s nepředvídatelnými úlohami. Při určitých vzorcích úloh může být správa ve fondu užitečná pro pouhé dvě databáze S3.

![Diagram aplikace](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Fondů a databází ve fondech, by měl být monitorovat tak zajistilo že jejich setrvání v přijatelných rozsazích výkonu. Vylaďte konfiguraci fondu podle potřeb agregované úlohy všechny databáze, zajištění, že jsou jednotky Edtu fondu vhodné pro celkovou úlohu. Upravte maximální a minimální hodnoty eDTU jednotlivých databází na vhodné hodnoty pro vaše konkrétní aplikační požadavky.

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Abyste nemuseli výkon monitorovat ručně, je nejvhodnějším **nastavit upozornění, která se aktivuje, když databáze nebo fondy odchýlí od normálního rozsahu**.
* K reakci na krátkodobé kolísání v agregované výpočetní velikost fondu **úrovně eDTU fondu je možné škálovat směrem nahoru nebo dolů**. Pokud k tomuto kolísání dochází pravidelně nebo je předvídatelné, **je možné naplánovat automatické škálování fondu**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Pokud chcete reagovat na dlouhodobější kolísání nebo změny počtu databází, **je možné přesunout jednotlivé databáze do jiných fondů**.
* Reagovat na krátkodobé zvýšení *jednotlivé* zatížení databáze **jednotlivé databáze můžete dostala mimo fond a přiřazené velikosti jednotlivých výpočetních**. Po snížení zatížení je možné databázi vrátit do fondu. Pokud je to víte předem, je možné databáze přesouvat preventivně Ujistěte se, že databáze vždy obsahuje potřebné prostředky, které potřebuje a aby se zabránilo vlivu na ostatní databáze ve fondu. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Ve službě SQL Database je monitorování a upozorňování k dispozici v databázích a fondech. Toto integrované monitorování a upozorňování je specifické podle prostředků, takže je pohodlné používat ho u malého počtu prostředků, ale není příliš pohodlné při práci s mnoha prostředky.

U velkoobjemových scénářů, ve kterém pracujete s mnoha prostředky, [Log Analytics](saas-dbpertenant-log-analytics.md) lze použít. Toto je samostatná služba Azure, která poskytuje analýzy přes vydávané diagnostické protokoly a telemetrii získanou v pracovním prostoru log analytics. Log Analytics může shromažďovat telemetrii z mnoha služeb a použije k dotazování a nastavování výstrah.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS databáze na Tenanta

Jsou k dispozici v databázi s více tenanty SaaS aplikace Wingtip Tickets skripty a zdrojový kód aplikace [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) pokyny ke stažení a odblokování SaaS aplikace Wingtip Tickets skripty.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

I když fondy můžou být nákladově efektivní jenom se dvěma databázemi S3, platí, že čím více databází fond obsahuje, tím efektivnější z hlediska nákladů začne být efekt zprůměrování. Pro zajištění správného porozumění fungování monitorování a správy výkonu na škále vyžaduje tento kurz, abyste měli nasazených nejméně 20 databází.

Pokud už jste v předchozím kurzu zřídili dávku tenantů, pokračujte [simulace použití ve všech databázích tenantů](#simulate-usage-on-all-tenant-databases) oddílu.

1. V **prostředí PowerShell ISE**, otevřete... \\Learning Modules\\monitorování a správa výkonu\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario** = **1**, **Zřízení dávky tenantů**
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 tenantů za méně než pět minut.

*New-TenantBatch* skript používá vnořenou nebo propojenou sadu [Resource Manageru](../azure-resource-manager/index.md) šablony, které vytvářejí dávku tenantů, která ve výchozím nastavení zkopíruje databázi **basetenantdb**na katalogový server k vytvoření nového tenanta databází, následně je zaregistruje v katalogu a nakonec s typem název a místní příslušností tenanta. To je konzistentní se způsobem, jakým aplikace zřizuje nového tenanta. Všechny změny provedené *basetenantdb* se použijí u všech nově zřízených tenantů. Najdete v článku [kurzu Správa schématu](saas-tenancy-schema-management.md) zobrazíte pokyny pro provedení změn schématu *existující* databázích tenantů (včetně *basetenantdb* databáze).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Demo-PerformanceMonitoringAndManagement.ps1* skriptu je k dispozici, který simuluje úlohu spuštěnou na všech databázích tenantů. Zatížení je generována pomocí jednoho z dostupných zatížení scénářů:

| Ukázka | Scénář |
|:--|:--|
| 2 | Generování normální intenzity zatížení (asi 40 DTU) |
| 3 | Generování zatížení s delšími a častějšími nárůsty zatížení na databázi|
| 4 | Generování zatížení s vyššími nárůsty DTU na databázi (přibližně 80 DTU)|
| 5 | Generování normálního a vysokého zatížení v jednom tenantovi (přibližně 95 DTU)|
| 6 | Generování nevyváženého zatížení mezi více fondy|

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v eDTU), doba trvání a intervaly jsou napříč všemi databázemi různé, což simuluje nepředvídanou činnost tenanta.

1. V **prostředí PowerShell ISE**, otevřete... \\Learning Modules\\monitorování a správa výkonu\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario** = **2**, *generování normální intenzity zatížení*.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

Je SaaS aplikace Wingtip Tickets SaaS databáze na Tenanta a reálné zatížení aplikace SaaS je obvykle sporadické a nepředvídatelné. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Několik minut, než je třeba vzor zatížení objeví, proto generátor zatížení běžel 3 až 5 minut před pokusem o sledování zatížení v následujících částech.

> [!IMPORTANT]
> Generátor zatížení běží jako řada úloh v místní relaci prostředí PowerShell. Nechte otevřenou kartu *Demo-PerformanceMonitoringAndManagement.ps1*. Pokud tuto kartu zavřete nebo pozastavíte počítač, generátor zatížení se zastaví. Generátor zatížení zůstává ve *vyvolání úlohy* stavu, ve kterém generuje zatížení na všech nových tenantů, které jsou zřízené po spuštění generátor kódu. Použití *Ctrl-C* zastavit vyvolání nové úlohy a zavřete tento skript. Generátor zatížení budou dál běžet, ale pouze tenantům.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorování využití prostředků pomocí webu Azure portal

Pokud chcete monitorovat využití prostředků, která je výsledkem použití zatížení, otevřete portál na fondu, který obsahuje databáze tenantů:

1. Otevřít [webu Azure portal](https://portal.azure.com) a přejděte do *tenants1-dpt -&lt;uživatele&gt;*  serveru.
1. Přejděte dolů na elastické fondy a klikněte na **Pool1**. Tento fond obsahuje všechny dosud vytvořené databáze tenantů.

Podívejte se **monitorování Elastických fondů** a **monitorování elastické databáze** grafy.

Využití prostředků fondu je agregační databáze využití pro všechny databáze ve fondu. Graf databáze zobrazuje 5 nejvytíženějších databází:

![databáze grafu](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Vzhledem k tomu, že existují další databáze ve fondu nad rámec pěti hlavní, jeho využití ukazuje aktivitu, která se neprojeví v grafu pěti hlavních databází. Další podrobnosti získáte kliknutím **využití databázových prostředků**:

![využití databázových prostředků](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Nastavení výstrah výkonu ve fondu

Nastavení upozornění na fond, která se spustí při \>75 % využití následujícím způsobem:

1. Otevřít *Pool1* (na *tenants1-dpt -\<uživatele\>*  serveru) v [webu Azure portal](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento eDTU**
   * **Podmínka = je větší než**
   * **Prahová hodnota = 75**
   * **Období = více než posledních 30 minut**
1. Přidat e-mailovou adresu k *další email(s) správce* pole a klikněte na tlačítko **OK**.

   ![nastavení upozornění](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Vertikální navýšení kapacity zaneprázdněného fondu

Pokud se agregovaná úroveň zatížení ve fondu zvýší na bod, která fond navyšuje na maximální hodnotu a dosáhne 100% využití eDTU, má to vliv na individuální výkon databáze a potenciálně vykazuje pomalejší doby odezvy pro všechny databáze ve fondu.

**Krátkodobé**, zvažte navýšení kapacity fondu tak, aby poskytoval další prostředky, nebo odeberte databáze z fondu (přemístěním do jiných fondů nebo mimo fond do samostatné servisní vrstvy).

**Dlouhodobější**, uvažujte o optimalizaci dotazů nebo indexu využití kvůli zlepšení výkonu databáze. Podle toho, jak je aplikace citlivá na problémy s výkonem, se osvědčilo navýšit kapacitu fondu předtím, než dosáhne 100% využití eDTU. Použijte výstrahu, abyste byli předem upozornění.

Zaneprázdněný fond můžete simulovat tak, že zvýšíte zatížení vytvořené generátorem. Způsobí častější zvýšení provozu častěji a pro déle, databáze, zvýšení agregovaného zatížení ve fondu beze změny požadavků jednotlivých databází. Škálování fondu se snadno provádí na portálu nebo z prostředí PowerShell. Při tomto cvičení se používá portál.

1. Nastavte možnost *$DemoScenario* = **3**, _Generování zatížení s delšími a častějšími nárůsty zatížení na databázi_ na zvýšení intenzity agregovaného zatížení ve fondu beze změny vrcholového zatížení požadovaného jednotlivými databázemi.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

1. Přejděte na **Pool1** na webu Azure Portal.

Monitorování využití eDTU zvýšenou fondu v grafu nahoře. Trvá několik minut, než se nové vyšší zatížení se rozjíždí, ale měli byste rychle zaznamenat fond začíná dosahovat maximální využití a jak se zatížení stabilizuje na novém vzorci, rychle přetížení fondu.

1. Vertikální navýšení kapacity fondu, klikněte na tlačítko **konfigurace fondu** v horní části **Pool1** stránky.
1. Upravit **eDTU fondu** nastavení **100**. Změna eDTU fondu se nemění podle nastavení databáze (což je stále maximálně 50 eDTU na databázi). Nastavení pro jednotlivé databáze můžete zobrazit na pravé straně **konfigurace fondu** stránky.
1. Klikněte na tlačítko **Uložit** a odeslat žádost o škálování fondu.

Přejděte zpět na **Pool1** > **přehled** k zobrazení monitorovacích grafů. Monitorujte efekt zřizování fondu s více prostředky (i když s méně databázemi a náhodným zatížením není vždy nezvratně dokud nespustíte nechystáte nějakou dobu). Při interpretaci grafů mějte na paměti, že 100 % v horním grafu teď reprezentuje 100 eDTU, zatímco v dolním grafu je 100 % vždy 50 eDTU, protože maximum na databázi je 50 eDTU.

Databáze je v průběhu procesu online a plně dostupná. A nakonec – protože je každá databáze připravená k aktivaci s eDTU nového fondu, bude každé aktivní propojení přerušeno. Kód aplikace by měl vždy být zapsán tak, aby se znovu pokoušel obnovit porušené propojení a tím pádem se znovu připojí k databázi ve fondu s navýšenou kapacitou.

## <a name="load-balance-between-pools"></a>Nástroj pro vyrovnávání zatížení mezi fondy

Jako alternativu navýšení kapacity fondu vytvořte druhý fond a přemístěte do něho databáze, aby se vyrovnalo zatížení mezi dvěma fondy. K tomu je potřeba vytvořit tento nový fond na stejném serveru jako první.

1. V [webu Azure portal](https://portal.azure.com), otevřete **tenants1-dpt -&lt;uživatele&gt;**  serveru.
1. Klikněte na tlačítko **+ nový fond** vytvořit fond na aktuálním serveru.
1. Na **elastického databázového fondu** šablony:

    1. Nastavte **název** k *Pool2*.
    1. Cenovou úroveň nechte jako **Fond Standard**.
    1. Klikněte na **Konfigurovat fond**
    1. Nastavte **eDTU fondu** k *50 eDTU*.
    1. Klikněte na tlačítko **přidat databáze** zobrazíte seznam databází na serveru, který lze přidat do *Pool2*.
    1. Vyberte všechny 10 databáze je přesunout do nového fondu, a potom klikněte na **vyberte**. Pokud jste byl spuštěn generátor zatížení, služba již ví, že váš profil výkonu vyžaduje větší než výchozí velikost 50 eDTU fondu a doporučuje, začínající 100 eDTU nastavení.

    ![doporučení](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. Pro účely tohoto kurzu ponechte výchozí nastavení na 50 Edtu a klikněte na tlačítko **vyberte** znovu.
    1. Vyberte **OK** vytvořit nový fond a přemístěte do něho vybrané databáze.

Vytvoření fondu a přesunutí databází trvá několik minut. Jak přesunu databází zůstat online a plně přístupná až do okamžiku, poslední, v tomto okamžiku všechna otevřená připojení zavřou. Za předpokladu, budete mít nějaké logiky opakování, klienti se pak připojení k databázi v novém fondu.

Přejděte do **Pool2** (na *tenants1-dpt -\<uživatele\>*  serveru) k otevření fondu a monitorujte jeho výkon. Pokud ho nevidíte, počkejte zřizování nového fondu k dokončení.

Nyní uvidíte že využití prostředků na *Pool1* klesla a že *Pool2* je nyní podobně načten.

## <a name="manage-performance-of-a-single-database"></a>Správa výkonu izolované databáze

Pokud má izolovaná databáze ve fondu stabilně vysoké zatížení, může mít v závislosti na konfiguraci fondu tendenci dominovat prostředkům ve fondu a ovlivňovat ostatní databáze. Pokud je aktivita může nějakou dobu pokračovat, můžete databázi dočasně přemístit mimo fond. To umožňuje databázi mít další prostředky, které potřebuje a izolovat ji od ostatních databází.

Toto cvičení simuluje vliv vysokého zatížení při prodeji lístků na populární koncert v Koncertním sále Contoso.

1. V **prostředí PowerShell ISE**, otevřete... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skriptu.
1. Nastavte **$DemoScenario = 5, Generování normálního a vysokého zatížení v jednom tenantovi (přibližně 95 DTU).**
1. Nastavte **$SingleTenantDatabaseName = contosoconcerthall**
1. Skript proveďte pomocí **F5**.


1. V [webu Azure portal](https://portal.azure.com), přejděte na seznam databází na *tenants1-dpt -\<uživatele\>*  serveru. 
1. Klikněte na **contosoconcerthall** databáze.
1. Klikněte na fond, který **contosoconcerthall** probíhá. Vyhledejte ve fondu **elastického databázového fondu** oddílu.

1. Zkontrolujte **monitorování Elastických fondů** grafu a hledejte zvýšené fondu využití eDTU. Po jedné až dvou minutách by mělo začít zvýšené zatížení a měli byste rychle zaznamenat, že fond dosáhl 100% využití.
2. Zkontrolujte **monitorování elastické databáze** zobrazení, které zobrazují nejvytíženější databáze za uplynulou hodinu. *Contosoconcerthall* databáze by měla brzy zobrazit jako jedna z 5 nejvytíženějších databází.
3. **Klikněte na monitorování elastické databáze** **grafu** a otevře se **využití databázových prostředků** stránku, kde můžete sledovat žádnou databázi. Díky tomu můžete izolovat zobrazení *contosoconcerthall* databáze.
4. V seznamu databází, klikněte na **contosoconcerthall**.
5. Klikněte na tlačítko **cenová úroveň (Dtu škálování)** otevřít **konfigurace výkonu** stránku, kde můžete nastavit velikost samostatné výpočetní prostředky pro databázi.
6. Kliknutím na kartu **Standardní** otevřete možnosti škálování ve standardní vrstvě.
7. Nastavte posuvník **posuvník DTU** doprava a vyberte **100** Dtu. Poznámka: to odpovídá cíli služby **S3**.
8. Klikněte na tlačítko **použít** k přesunutí databáze mimo fond a nastavte ji *Standard S3* databáze.
9. Po dokončení škálování sledujte vliv na databázi contosoconcerthall a Pool1 na okna elastického fondu a databáze.

Jakmile vysokého zatížení databáze contosoconcerthall poklesne vám byste ji ihned vrátit do fondu, aby se snížilo její. Pokud není jasné, kdy se to stane, můžete nastavit výstrahy na databázi, která se aktivuje při využití DTU klesne pod na databázi ve fondu maximální. Přesunutí databáze do fondu je popsáno v cvičení 5.

## <a name="other-performance-management-patterns"></a>Další vzorce správy výkonu

**Preemptivní škálování** v cvičení výše uvedené ve kterém jste vyzkoušeli škálování izolované databáze, jste zjistili, kterou databázi Hledat. Pokud management Contoso koncertní Hall koncertního sálu vás Wingtips o nadcházejícím prodeji lístků, databázi přesunuta mimo fond preventivně. Jinak by to vyžadovalo výstrahu ve fondu nebo v databázi s cílem zjistit, co se stalo. Pravděpodobně byste to nechtěli zjistit proto, že by si ostatní tenanti ve fondu stěžovali na snížený výkon. A kdyby mohli tenanti předvídat, na jak dlouho by potřebovali další prostředky, můžete nastavit runbook Azure Automation na přesunutí databáze mimo fond a potom zpět podle předem definovaného plánu.

**Samoobslužné škálování tenanta**  Protože škálování je úkol, který se snadno volá prostřednictvím rozhraní API pro škálu, můžete snadno vytvořit možnost škálovat databáze tenantů do aplikace směřující k tenantovi a nabízet ji jako funkci služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Vertikální navýšení nebo snížení kapacity podle plánu se vzorci využití**

Tam, kde agregované využití tenanta probíhá podle předvídatelných vzorců použití, můžete pomocí Azure Automation podle plánu vertikálně zvyšovat nebo snižovat kapacitu fondu. Můžete například snížit kapacitu fondu po 6. hodině večer a navýšit před 6. hodinou ráno ve dnech, kdy víte, že existuje pokles požadavků na prostředky.



## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze

[Kurz Obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Další zdroje informací:

* Další [kurzy, které vycházejí z nasazení aplikace Wingtip Tickets SaaS databáze na Tenanta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastické fondy SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) – kurz Nastavení a používání Log Analytics
