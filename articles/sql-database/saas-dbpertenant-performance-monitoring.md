---
title: Sledování výkonu mnoho databází Azure SQL v aplikaci SaaS víceklientské | Microsoft Docs
description: Sledování a správa výkonu databáze Azure SQL a fondy v aplikaci SaaS více klientů
keywords: kurz k sql database
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: f76a9f0aa5e68e729c67dad52f6dae4e01fdb56a
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Sledování a správa výkonu databáze Azure SQL a fondy v aplikaci SaaS více klientů

V tomto kurzu jsou prozkoumali několik klíčových scénářů správy používány aplikací SaaS. Pomocí generátoru zatížení simulovat aktivity mezi všechny databáze klienta, integrované monitorování a výstrah funkce SQL Database a elastické fondy je ukázán.

Adresář Wingtip lístky SaaS databáze za klienta aplikace používá klienta jeden datový model, kde každý místo (klient) má své vlastní databázi. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Abyste mohli využít výhod tohoto typického vzoru používání databáze, databáze tenantů se nasazují do elastických databázových fondů. Elastické fondy optimalizují náklady na řešení prostřednictvím sdílení prostředků mezi mnoha databázemi. S tímto typem vzorců je důležité monitorovat využití databáze a prostředků fondu k zajištění, že jsou přiměřeně vyvážená přetížení mezi jednotlivými fondy. Je také potřeba zajistit, že jednotlivé databáze mají adekvátní prostředky a že fondy nedosahují limitů [eDTU](sql-database-what-is-a-dtu.md). Tento kurz se věnuje způsobům monitorování a správy databází a fondů a uvádí, jak se provádějí nápravné akce v reakci na variace v úloze.

V tomto kurzu se naučíte:

> [!div class="checklist"]

> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Nasazení aplikace Wingtip lístky SaaS databáze za klienta. Nasazení za méně než pět minut najdete v tématu [nasazení a seznamte se s aplikací Wingtip lístky SaaS databáze za klienta](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Úvod k modelům správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. Při hostování více tenantů představují fondy elastické databáze nenákladný způsob zajištění a správy prostředků pro skupinu databází s nepředvídatelnými úlohami. Při určitých vzorcích úloh může být správa ve fondu užitečná pro pouhé dvě databáze S3.

![Diagram aplikace](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Fondy a databází ve fondech, je potřeba sledovat zajistit, že zůstanou v rámci přijatelné rozsahů výkonu. Vyladění konfigurace fondu podle potřeb pracovního vytížení agregační všech databází, zajistíte, že Edtu fondu jsou vhodné pro celkové zatížení. Upravte maximální a minimální hodnoty eDTU jednotlivých databází na vhodné hodnoty pro vaše konkrétní aplikační požadavky.

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Abyste se vyhnuli nutnosti ručně sledovat výkon, je nejúčinnější k **nastavit výstrahy, které aktivují, když databáze nebo fondy Stray Zbloudilá mimo normální rozsahy**.
* V reakci na krátkodobé kolísání v agregované úrovni výkonu fondu je možné **vertikálně navýšit nebo snížit kapacitu úrovně eDTU fondu**. Pokud k tomuto kolísání dochází pravidelně nebo je předvídatelné, **je možné naplánovat automatické škálování fondu**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Pokud chcete reagovat na dlouhodobější kolísání nebo změny počtu databází, **je možné přesunout jednotlivé databáze do jiných fondů**.
* Reakce na krátkodobé zvyšování *jednotlivých* zatížení databáze **jednotlivé databáze může být převzaté z fondu a přiřazení úroveň výkonu jednotlivých**. Po snížení zatížení je možné databázi vrátit do fondu. Když je znám předem, databází můžete přesunout pre-emptively, zajistěte, aby byl vždy prostředky, které potřebuje databázi a vyhnout se dopad na jiné databáze ve fondu. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Ve službě SQL Database je monitorování a upozorňování k dispozici v databázích a fondech. Toto integrované monitorování a výstrah je konkrétní prostředky, takže je možné použít pro malý počet prostředků, ale není možnost je užitečná při práci s množství prostředků.

Vysoký počet scénářů, při kterém pracujete s mnoha prostředky, [analýzy protokolů](saas-dbpertenant-log-analytics.md) lze použít. Toto je samostatný služba Azure, která nabízí v porovnání s emitovaného diagnostické protokoly a telemetrie získané v pracovním prostoru analýzy protokolů analýzy. Analýzy protokolů můžete shromažďovat telemetrická data z mnoha služeb a použije k dotazování a nastavit výstrahy.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat aplikační skripty Wingtip lístky SaaS databáze za klienta

Adresář Wingtip lístky SaaS víceklientské databázové skripty a zdrojový kód aplikace, které jsou k dispozici v [WingtipTicketsSaaS DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) úložiště GitHub. Podívejte se [obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) kroky, jak stáhnout a odblokování skripty Wingtip lístky SaaS.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

I když fondy můžou být nákladově efektivní jenom se dvěma databázemi S3, platí, že čím více databází fond obsahuje, tím efektivnější z hlediska nákladů začne být efekt zprůměrování. Pro zajištění správného porozumění fungování monitorování a správy výkonu na škále vyžaduje tento kurz, abyste měli nasazených nejméně 20 databází.

Pokud dávky klientů, které se už zřízené v předchozí kurzu, pokračujte [simulovat využití na všechny databáze klienta](#simulate-usage-on-all-tenant-databases) části.

1. V **prostředí PowerShell ISE**, otevřete... \\Učení moduly\\sledování výkonu a správy\\*ukázku PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario** = **1**, **Zřízení dávky tenantů**
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 tenantů za méně než pět minut.

*New-TenantBatch* skript používá sadu vnořené nebo propojené [Resource Manager](../azure-resource-manager/index.md) šablony, které vytvoření dávky klientů, který ve výchozím nastavení zkopíruje databázi **basetenantdb**na serveru katalog pro vytvoření nového klienta databáze, pak zaregistruje v katalogu a nakonec je inicializuje s typem klientovi název a místo. To je konzistentní s způsob, jakým aplikace zřídí nového klienta. Některé změny provedené *basetenantdb* se použijí pro žádné nové klienty zřízený po tomto datu. Najdete v článku [Správa schématu kurzu](saas-tenancy-schema-management.md) chcete zjistit, jak provést změny schématu *existující* klienta databáze (včetně *basetenantdb* databáze).

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Ukázku PerformanceMonitoringAndManagement.ps1* je skript zadaný, který simuluje zatížení spuštěným pro všechny databáze klienta. Zatížení je generována pomocí jednoho z dostupných zatížení scénářů:

| Ukázka | Scénář |
|:--|:--|
| 2 | Generování normální intenzity zatížení (asi 40 DTU) |
| 3 | Generování zatížení s delšími a častějšími nárůsty zatížení na databázi|
| 4 | Generování zatížení s vyššími nárůsty DTU na databázi (přibližně 80 DTU)|
| 5 | Generování normálního a vysokého zatížení v jednom tenantovi (přibližně 95 DTU)|
| 6 | Generování nevyváženého zatížení mezi více fondy|

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v eDTU), doba trvání a intervaly jsou napříč všemi databázemi různé, což simuluje nepředvídanou činnost tenanta.

1. V **prostředí PowerShell ISE**, otevřete... \\Učení moduly\\sledování výkonu a správy\\*ukázku PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario** = **2**, *generování normální intenzitou zatížení*.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

Adresář Wingtip lístky SaaS databáze za klienta je aplikace SaaS a reálného zatížení aplikace SaaS je obvykle ojediněle a nepředvídatelným. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Několik minut, je potřeba pro vzor zatížení objeví, takže spuštění generátor zatížení po dobu 3 až 5 minut před pokusem o monitorování zatížení v následujících částech.

> [!IMPORTANT]
> Generátor zatížení běží jako řada úloh v místní relaci prostředí PowerShell. Nechte otevřenou kartu *Demo-PerformanceMonitoringAndManagement.ps1*. Pokud tuto kartu zavřete nebo pozastavíte počítač, generátor zatížení se zastaví. Generátor zatížení zůstává ve *vyvolání úlohy* stavu, kde vygeneruje zatížení žádné nové klienty, které jsou zřízené po spuštění generátor. Použití *Ctrl-C* zastavení vyvolání nové úlohy a ukončení skript. Generátor zatížení bude nadále spouštět, ale jenom na stávající klienty.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Sledování využití prostředků pomocí portálu Azure

Pokud chcete monitorovat využití prostředků, která je výsledkem zatížení, které jsou aplikovány, otevřete portál na fond, který obsahuje databáze klienta:

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte do *tenants1-dpt -&lt;uživatele&gt;*  serveru.
1. Přejděte dolů na elastické fondy a klikněte na **Pool1**. Tento fond obsahuje všechny dosud vytvořené databáze tenantů.

Sledovat **elastického fondu monitorování** a **elastické databáze monitorování** grafy.

Využití prostředků fondu je agregační databáze využití pro všechny databáze ve fondu. Databáze graf zobrazuje pět nejprodávanějších databází:

![Graf databáze](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Vzhledem k tomu, že existují další databáze ve fondu nad horní pět, využití fondu ukazuje aktivity, která se neodrazí v horní grafu pět databáze. Další podrobnosti získáte kliknutím na **využití prostředků databáze**:

![databáze využití prostředků](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Nastavení výstrah výkonu ve fondu

Nastavit výstrahy na fond, který aktivuje na \>75 % využití následujícím způsobem:

1. Otevřete *Pool1* (na *tenants1-dpt -\<uživatele\>*  serveru) v [portál Azure](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento eDTU**
   * **Podmínka = větší než**
   * **Prahová hodnota = 75**
   * **Období = za posledních 30 minut**
1. Přidání e-mailovou adresu k *další správce email(s)* pole a klikněte na tlačítko **OK**.

   ![nastavení upozornění](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Vertikální navýšení kapacity zaneprázdněného fondu

Pokud se agregovaná úroveň zatížení ve fondu zvýší na bod, která fond navyšuje na maximální hodnotu a dosáhne 100% využití eDTU, má to vliv na individuální výkon databáze a potenciálně vykazuje pomalejší doby odezvy pro všechny databáze ve fondu.

**Krátkodobé**, zvažte vertikálním navýšení kapacity fondu se mají poskytnout další prostředky, nebo odebráním databází z fondu (jejich přesunutí do jiných fondů nebo mimo fondu vrstvu samostatné služby).

**Dlouhodobější**, zvažte optimalizaci dotazů nebo indexu využití ke zlepšení výkonu databáze. Podle toho, jak je aplikace citlivá na problémy s výkonem, se osvědčilo navýšit kapacitu fondu předtím, než dosáhne 100% využití eDTU. Použijte výstrahu, abyste byli předem upozornění.

Zaneprázdněný fond můžete simulovat tak, že zvýšíte zatížení vytvořené generátorem. Způsobuje databáze, burst častěji a pro zvýšení agregační zatížení ve fondu, aniž byste museli měnit požadavky na jednotlivé databáze. Škálování fondu se snadno provádí na portálu nebo z prostředí PowerShell. Při tomto cvičení se používá portál.

1. Nastavte možnost *$DemoScenario* = **3**, _Generování zatížení s delšími a častějšími nárůsty zatížení na databázi_ na zvýšení intenzity agregovaného zatížení ve fondu beze změny vrcholového zatížení požadovaného jednotlivými databázemi.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

1. Přejděte na **Pool1** na portálu Azure.

Sledování využití eDTU vyšší fondu v horní grafu. Trvá několik minut, než nové vyšší zátěž nové, ale měli byste vidět rychle začít dosáhl maximální využití fondu a jako zatížení steadies do nové vzor, rychle přetížení fondu.

1. Škálování fondu, klikněte na tlačítko **konfigurace fondu** v horní části **Pool1** stránky.
1. Upravit **eDTU fondu** nastavení **100**. Změna eDTU fondu se nemění podle nastavení databáze (což je stále maximálně 50 eDTU na databázi). Jednotlivé databáze nastavení můžete zobrazit na pravé straně **konfigurace fondu** stránky.
1. Klikněte na tlačítko **Uložit** odeslat žádost o škálování fondu.

Přejděte zpět na **Pool1** > **přehled** zobrazení monitorování grafy. Sledování účinku fondu poskytování více prostředků (i když několik databází a náhodnou zatížení není vždy snadno zjistit jednoznačně, dokud nespustíte nějakou dobu). Při interpretaci grafů mějte na paměti, že 100 % v horním grafu teď reprezentuje 100 eDTU, zatímco v dolním grafu je 100 % vždy 50 eDTU, protože maximum na databázi je 50 eDTU.

Databáze je v průběhu procesu online a plně dostupná. A nakonec – protože je každá databáze připravená k aktivaci s eDTU nového fondu, bude každé aktivní propojení přerušeno. Kód aplikace by měl vždy být zapsán tak, aby se znovu pokoušel obnovit porušené propojení a tím pádem se znovu připojí k databázi ve fondu s navýšenou kapacitou.

## <a name="load-balance-between-pools"></a>Vyrovnávání zatížení mezi fondy

Jako alternativu navýšení kapacity fondu vytvořte druhý fond a přemístěte do něho databáze, aby se vyrovnalo zatížení mezi dvěma fondy. K tomu je potřeba vytvořit tento nový fond na stejném serveru jako první.

1. V [portál Azure](https://portal.azure.com), otevřete **tenants1-dpt -&lt;uživatele&gt;**  serveru.
1. Klikněte na tlačítko **+ nový fond** vytvoření fondu na aktuálním serveru.
1. Na **fond elastické databáze** šablony:

    1. Nastavit **název** k *Pool2*.
    1. Cenovou úroveň nechte jako **Fond Standard**.
    1. Klikněte na **Konfigurovat fond**
    1. Nastavit **eDTU fondu** k *50 eDTU*.
    1. Klikněte na tlačítko **přidat databáze** zobrazíte seznam databází na serveru, který lze přidat do *Pool2*.
    1. Vyberte všechny 10 databáze přesunout do nového fondu a potom klikněte na **vyberte**. Pokud jste byla spuštěna generátor zatížení, služba již ví, že váš profil výkonu vyžaduje fond větší než velikost 50 eDTU výchozí a doporučuje od 100 nastavení eDTU.

    ![doporučení](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

    1. V tomto kurzu ponechte výchozí hodnota 50 Edtu a klikněte na tlačítko **vyberte** znovu.
    1. Vyberte **OK** k vytvoření nového fondu a přesunout vybrané databáze do ní.

Vytvoření fondu a přesunutí databáze trvá několik minut. Jak je, že zůstat online a plně dostupné až do okamžiku, velmi poslední přesunu databází, v tomto okamžiku jsou uzavřeny žádné otevřené připojení. Tak dlouho, dokud máte některé logika opakovaných pokusů, klienti se potom připojují k databázi v nového fondu.

Přejděte do **Pool2** (na *tenants1-dpt -\<uživatele\>*  serveru) otevřete fondu a sledovat jeho výkon. Pokud ho nevidíte, počkejte zřizování nového fondu k dokončení.

Nyní uvidíte že využití prostředků na *Pool1* klesla a že *Pool2* je nyní podobně načtena.

## <a name="manage-performance-of-a-single-database"></a>Správa výkonu služby jedné databáze

Pokud má izolovaná databáze ve fondu stabilně vysoké zatížení, může mít v závislosti na konfiguraci fondu tendenci dominovat prostředkům ve fondu a ovlivňovat ostatní databáze. Pokud je aktivita může pokračovat po určitou dobu, můžete databáze dočasně přesunul z fondu. To umožňuje databázi do mají další prostředky potřebuje a izoluje z jiných databází.

Toto cvičení simuluje vliv vysokého zatížení při prodeji lístků na populární koncert v Koncertním sále Contoso.

1. V **prostředí PowerShell ISE**, otevřete... \\ *Ukázku PerformanceMonitoringAndManagement.ps1* skriptu.
1. Nastavte **$DemoScenario = 5, Generování normálního a vysokého zatížení v jednom tenantovi (přibližně 95 DTU).**
1. Nastavte **$SingleTenantDatabaseName = contosoconcerthall**
1. Skript proveďte pomocí **F5**.


1. V [portál Azure](https://portal.azure.com), přejděte na seznam databází na *tenants1-dpt -\<uživatele\>*  serveru. 
1. Klikněte na **contosoconcerthall** databáze.
1. Klikněte na fond, **contosoconcerthall** v. Vyhledejte ve fondu **fond elastické databáze** části.

1. Zkontrolujte **elastického fondu monitorování** grafu a vyhledejte využití eDTU vyšší fondu. Po jedné až dvou minutách by mělo začít zvýšené zatížení a měli byste rychle zaznamenat, že fond dosáhl 100% využití.
2. Zkontrolujte **elastické databáze monitorování** zobrazení, která zobrazuje nejprodávanějších databáze za poslední hodinu. *Contosoconcerthall* databáze by měla brzy zobrazí jako jedna z pěti nejprodávanějších databází.
3. **Klikněte na monitorování elastické databáze** **grafu** a otevře se **využití prostředků databáze** stránky, kde můžete sledovat některé z databází. Díky tomu si zobrazení pro izolovat *contosoconcerthall* databáze.
4. Ze seznamu databází, klikněte na tlačítko **contosoconcerthall**.
5. Klikněte na tlačítko **cenová úroveň (škálování Dtu)** otevřete **konfigurace výkonu** stránky, kde můžete nastavit úroveň samostatné výkonu databáze.
6. Kliknutím na kartu **Standardní** otevřete možnosti škálování ve standardní vrstvě.
7. Posuňte **DTU posuvníku** vpravo vyberte **100** Dtu. Poznámka: odpovídá cíl služby **S3**.
8. Klikněte na tlačítko **použít** přesunout databázi z fondu a změňte ji *úrovně Standard S3* databáze.
9. Po dokončení škálování sledujte účinek na databázi contosoconcerthall a Pool1 v elastickém fondu a databáze oknech.

Jakmile vysokého zatížení v databázi contosoconcerthall subvence by měl neprodleně vrátí do fondu na snížení nákladů na jeho. Pokud je při tom nejasné po který se stane, můžete nastavit upozornění na databázi, se aktivuje při jeho využití v jednotkách DTU klesne pod jednotlivé databáze maximální ve fondu. Přesunutí databáze do fondu je popsáno v cvičení 5.

## <a name="other-performance-management-patterns"></a>Dalšími vzory správy výkonu

**Preemptivní škálování** v cvičení výše kde jste prozkoumali postup škálování izolované databáze, budete vědět, databázi, kterou chcete vyhledat. Pokud správu Contoso vzájemné součinnosti Hall měl informován Wingtips brzké prodeje lístků, databázi přesunuta mimo fondu pre-emptively. Jinak by to vyžadovalo výstrahu ve fondu nebo v databázi s cílem zjistit, co se stalo. Pravděpodobně byste to nechtěli zjistit proto, že by si ostatní tenanti ve fondu stěžovali na snížený výkon. A kdyby mohli tenanti předvídat, na jak dlouho by potřebovali další prostředky, můžete nastavit runbook Azure Automation na přesunutí databáze mimo fond a potom zpět podle předem definovaného plánu.

**Samoobslužné škálování tenanta**  Protože škálování je úkol, který se snadno volá prostřednictvím rozhraní API pro škálu, můžete snadno vytvořit možnost škálovat databáze tenantů do aplikace směřující k tenantovi a nabízet ji jako funkci služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Škálování fondu nahoru a dolů na plán tak, aby odpovídaly vzorce používání**

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

* Další [návodů, které stavět na adresář Wingtip lístky SaaS databáze za klienta nasazení aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastické fondy SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Log Analytics](saas-dbpertenant-log-analytics.md) – kurz Nastavení a používání Log Analytics
