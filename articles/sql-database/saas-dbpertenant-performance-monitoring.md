---
title: 'Aplikace Saas: Sledování výkonu mnoha databází'
description: Sledování a správa výkonu databází a fondů Azure SQL ve víceklientské aplikaci SaaS
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
ms.openlocfilehash: 34c50795567615637e31446ad3dc51a5e1b355f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214465"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Sledování a správa výkonu databází a fondů Azure SQL ve víceklientské aplikaci SaaS

V tomto kurzu jsou prozkoumány několik klíčových scénářů správy výkonu používaných v aplikacích SaaS. Pomocí generátorzatížení simulovat aktivitu ve všech databázích klienta, integrované monitorování a upozorňování funkce SQL Database a elastické fondy jsou demonstrovány.

Wingtip Tickets SaaS databáze na klienta aplikace používá jeden klient datový model, kde každé místo (tenant) má svou vlastní databázi. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Chcete-li využít tento vzor typické využití databáze, databáze klienta jsou nasazeny do elastických fondů. Elastické fondy optimalizují náklady na řešení prostřednictvím sdílení prostředků mezi mnoha databázemi. S tímto typem vzorců je důležité monitorovat využití databáze a prostředků fondu k zajištění, že jsou přiměřeně vyvážená přetížení mezi jednotlivými fondy. Je také potřeba zajistit, že jednotlivé databáze mají adekvátní prostředky a že fondy nedosahují limitů [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model). Tento kurz se věnuje způsobům monitorování a správy databází a fondů a uvádí, jak se provádějí nápravné akce v reakci na variace v úloze.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> 
> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Wingtip Vstupenky SaaS databáze na klienta aplikace se nasadí. Pokud chcete nasadit za méně než pět minut, [přečtěte si informace o nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database SaaS Na klienta.](saas-dbpertenant-get-started-deploy.md)
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Úvod do vzorců správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. Při hostování více klientů jsou elastické fondy nákladově efektivním způsobem, jak poskytovat a spravovat prostředky pro skupinu databází s nepředvídatelnými úlohami. Při určitých vzorcích úloh může být správa ve fondu užitečná pro pouhé dvě databáze S3.

![diagram aplikace](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Fondy a databáze ve fondech by měly být sledovány, aby bylo zajištěno, že zůstanou v přijatelných rozsahech výkonu. Vylaďte konfiguraci fondu tak, aby vyhovovala potřebám agregovanéúlohy všech databází a zajistila, že eDTU fondu jsou vhodné pro celkové zatížení. Upravte maximální a minimální hodnoty eDTU jednotlivých databází na vhodné hodnoty pro vaše konkrétní aplikační požadavky.

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Chcete-li se vyhnout ručnímu sledování výkonu, je nejúčinnější **nastavit výstrahy, které se aktivují, když databáze nebo fondy zabloudí z normálního rozsahu**.
* Chcete-li reagovat na krátkodobé výkyvy v agregované výpočetní velikosti fondu, **úroveň eDTU fondu lze škálovat nahoru nebo dolů**. Pokud k tomuto kolísání dochází pravidelně nebo je předvídatelné, **je možné naplánovat automatické škálování fondu**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Pokud chcete reagovat na dlouhodobější kolísání nebo změny počtu databází, **je možné přesunout jednotlivé databáze do jiných fondů**.
* Chcete-li reagovat na krátkodobé zvýšení *zatížení jednotlivých* databází **jednotlivých databází lze vyjmout z fondu a přiřadit individuální výpočetní velikost**. Po snížení zatížení je možné databázi vrátit do fondu. Pokud je to známo předem, databáze lze přesunout preventivně zajistit, že databáze má vždy prostředky, které potřebuje, a aby se zabránilo dopadu na jiné databáze ve fondu. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Ve službě SQL Database je monitorování a upozorňování k dispozici v databázích a fondech. Toto integrované monitorování a upozorňování je specifické pro prostředky, takže je vhodné použít pro malý počet prostředků, ale není příliš výhodné při práci s mnoha prostředky.

Pro scénáře s velkým objemem, kde pracujete s mnoha prostředky, lze použít [protokoly Azure Monitoru.](saas-dbpertenant-log-analytics.md) Toto je samostatná služba Azure, která poskytuje analýzy přes emitované protokoly shromážděné v pracovním prostoru Log Analytics. Protokoly Azure Monitor můžete shromažďovat telemetrie z mnoha služeb a slouží k dotazování a nastavení výstrah.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získat wingtip vstupenky SaaS databáze na klienta aplikační skripty

Wingtip Tickets SaaS Víceklientské databázové skripty a zdrojový kód aplikace jsou k dispozici v úložišti [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub. Podívejte se na [obecné pokyny pro](saas-tenancy-wingtip-app-guidance-tips.md) kroky ke stažení a odblokování wingtip vstupenky SaaS skripty.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

I když fondy můžou být nákladově efektivní jenom se dvěma databázemi S3, platí, že čím více databází fond obsahuje, tím efektivnější z hlediska nákladů začne být efekt zprůměrování. Pro zajištění správného porozumění fungování monitorování a správy výkonu na škále vyžaduje tento kurz, abyste měli nasazených nejméně 20 databází.

Pokud jste již zřízené dávky klientů v předchozím kurzu, přejděte na [simulovat využití na všech databázíklientů](#simulate-usage-on-all-tenant-databases) části.

1. V **prostředí PowerShell ISE**otevřete ... \\Výukové moduly\\Sledování\\výkonu a správa*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario** = **1**, **Zřídit dávku klientů**
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 tenantů za méně než pět minut.

*New-TenantBatch* skript používá vnořené nebo propojené sady šablon [Správce prostředků,](../azure-resource-manager/index.yml) které vytvářejí dávku klientů, který ve výchozím nastavení zkopíruje **databázi basetenantdb** na serveru katalogu k vytvoření nové databáze klienta, pak zaregistruje tyto v katalogu a nakonec inicializuje je s názvem klienta a typ místa konání. To je v souladu se způsobem, jakým aplikace zřizována nového klienta. Všechny změny provedené *v basetenantdb* se použijí na všechny nové klienty zřízené poté. Podívejte se na [kurz Správa schématu,](saas-tenancy-schema-management.md) kde najdete, jak provést změny schématu *v existujících* databázích klientů (včetně *databáze basetenantdb).*

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Demo-PerformanceMonitoringAndManagement.ps1* skript je k dispozici, který simuluje úlohy spuštěné proti všechny databáze klienta. Zatížení je generováno pomocí jednoho ze scénářů načítání k dispozici:

| Ukázka | Scénář |
|:--|:--|
| 2 | Generovat normální intenzitu zatížení (přibližně 40 DTU) |
| 3 | Generování zatížení s delšími a častějšími nárůsty zatížení na databázi|
| 4 | Generovat zatížení s vyššími shluky DTU na databázi (přibližně 80 DTU)|
| 5 | Generovat normální zatížení plus vysoké zatížení na jednoho klienta (přibližně 95 DTU)|
| 6 | Generování nevyváženého zatížení mezi více fondy|

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v eDTU), doba trvání a intervaly jsou napříč všemi databázemi různé, což simuluje nepředvídanou činnost tenanta.

1. V **prostředí PowerShell ISE**otevřete ... \\Výukové moduly\\Sledování\\výkonu a správa*Demo-PerformanceMonitoringAndManagement.ps1*. Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavit **$DemoScenario** = **2**, Generovat *normální intenzitu zatížení*.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

Wingtip Tickets SaaS Databáze na tenanta je aplikace SaaS a reálné zatížení aplikace SaaS je obvykle sporadické a nepředvídatelné. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Několik minut je zapotřebí, aby se objevil vzor zatížení, takže spusťte generátor zatížení po dobu 3-5 minut, než se pokusíte sledovat zatížení v následujících částech.

> [!IMPORTANT]
> Generátor zatížení běží jako řada úloh v místní relaci prostředí PowerShell. Nechte otevřenou kartu *Demo-PerformanceMonitoringAndManagement.ps1*. Pokud tuto kartu zavřete nebo pozastavíte počítač, generátor zatížení se zastaví. Generátor zatížení zůstává ve stavu *vyvolání úlohy,* kde generuje zatížení všech nových klientů, které jsou zřízeny po spuštění generátoru. Pomocí *kombinace kláves Ctrl-C* můžete zastavit vyvolání nových úloh a ukončit skript. Generátor zatížení bude nadále spuštěn, ale pouze u stávajících klientů.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorování využití prostředků pomocí portálu Azure

Chcete-li sledovat využití prostředků, které je výsledkem použitého zatížení, otevřete portál do fondu obsahujícího databáze klientů:

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte na *tenantyserver1-dpt-&lt;USER.&gt; *
1. Přejděte dolů na elastické fondy a klikněte na **Pool1**. Tento fond obsahuje všechny dosud vytvořené databáze tenantů.

Sledujte **monitorování elastického fondu** a **grafy monitorování elastické databáze.**

Využití prostředků fondu je agregované využití databáze pro všechny databáze ve fondu. Databázový graf zobrazuje pět nejteplejších databází:

![databázový graf](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Vzhledem k tomu, že existují další databáze ve fondu za prvních pět, využití fondu zobrazuje aktivitu, která se neprojeví v grafu prvních pět databází. Další podrobnosti zobrazíte klepnutím na **položku Využití prostředků databáze**:

![využití prostředků databáze](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Nastavení výstrah výkonu ve fondu

Nastavte výstrahu ve fondu, \>která se aktivuje při 75 % využití následujícím způsobem:

1. Otevřete *Pool1* (na *\<\> tenants1-dpt- uživatelský* server) na webu Azure [Portal](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento eDTU**
   * **Podmínka = větší než**
   * **Prahová hodnota = 75**
   * **Tečka = Za posledních 30 minut**
1. Přidejte e-mailovou adresu do pole *Další e-maily správce* a klepněte na tlačítko **OK**.

   ![nastavení upozornění](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Vertikální navýšení kapacity zaneprázdněného fondu

Pokud se agregovaná úroveň zatížení ve fondu zvýší na bod, která fond navyšuje na maximální hodnotu a dosáhne 100% využití eDTU, má to vliv na individuální výkon databáze a potenciálně vykazuje pomalejší doby odezvy pro všechny databáze ve fondu.

**Krátkodobé**, zvažte škálování fondu poskytnout další prostředky nebo odebrání databází z fondu (jejich přesunutí do jiných fondů nebo z fondu na samostatnou úroveň služby).

**Dlouhodobější**, zvažte optimalizaci dotazů nebo využití indexu ke zlepšení výkonu databáze. Podle toho, jak je aplikace citlivá na problémy s výkonem, se osvědčilo navýšit kapacitu fondu předtím, než dosáhne 100% využití eDTU. Použijte výstrahu, abyste byli předem upozornění.

Zaneprázdněný fond můžete simulovat tak, že zvýšíte zatížení vytvořené generátorem. Způsobuje databáze shlukovat častěji a déle, zvýšení agregační zatížení fondu beze změny požadavků jednotlivých databází. Škálování fondu se snadno provádí na portálu nebo z prostředí PowerShell. Při tomto cvičení se používá portál.

1. Nastavte *$DemoScenario* = **3**, Generovat zatížení s delší a _častější shluky na databázi_ zvýšit intenzitu agregační zatížení fondu beze změny zatížení ve špičce vyžadované každou databázi.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

1. Přejděte do **fondu 1** na webu Azure Portal.

Sledujte zvýšené využití fondu eDTU v horním grafu. Trvá několik minut, než se nové vyšší zatížení nastartuje, ale měli byste rychle vidět, že fond začne zasáhnout maximální využití, a jak se zatížení ustálí do nového vzoru, rychle přetíží fond.

1. Pokud chcete fond škálovat, klikněte v horní části stránky **Pool1** na **Konfigurovat fond.**
1. Nastavte **nastavení poolu eDTU** na **100**. Změna eDTU fondu se nemění podle nastavení databáze (což je stále maximálně 50 eDTU na databázi). Nastavení pro databázi najdete na pravé straně stránky **Konfigurovat fond.**
1. Kliknutím na **Uložit** odešlete požadavek na škálování fondu.

Přejděte zpět do **fondu1** > **Přehled** pro zobrazení grafy monitorování. Sledujte účinek poskytování fondu s více prostředků (i když s několika databázemi a randomizované zatížení není vždy snadné vidět přesvědčivě, dokud spustit na nějakou dobu). Při interpretaci grafů mějte na paměti, že 100 % v horním grafu teď reprezentuje 100 eDTU, zatímco v dolním grafu je 100 % vždy 50 eDTU, protože maximum na databázi je 50 eDTU.

Databáze je v průběhu procesu online a plně dostupná. A nakonec – protože je každá databáze připravená k aktivaci s eDTU nového fondu, bude každé aktivní propojení přerušeno. Kód aplikace by měl vždy být zapsán tak, aby se znovu pokoušel obnovit porušené propojení a tím pádem se znovu připojí k databázi ve fondu s navýšenou kapacitou.

## <a name="load-balance-between-pools"></a>Vyrovnávání zatížení mezi fondy

Jako alternativu navýšení kapacity fondu vytvořte druhý fond a přemístěte do něho databáze, aby se vyrovnalo zatížení mezi dvěma fondy. K tomu je potřeba vytvořit tento nový fond na stejném serveru jako první.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete **server tenants1-dpt-&lt;USER.&gt; **
1. Kliknutím na **+ Nový fond** vytvořte fond na aktuálním serveru.
1. V šabloně **elastický fond:**

   1. Nastavte **název** na *pool2*.
   1. Cenovou úroveň nechte jako **Fond Standard**.
   1. Klepněte na tlačítko **Konfigurovat fond**.
   1. Nastavte **fond eDTU** na *50 eDTU*.
   1. Klepnutím na tlačítko **Přidat databáze** zobrazíte seznam databází na serveru, který lze přidat do *fondu Pool2*.
   1. Vyberte libovolných 10 databází, chcete-li je přesunout do nového fondu, a klepněte na tlačítko **Vybrat**. Pokud jste spustili generátor zatížení, služba už ví, že váš profil výkonu vyžaduje větší fond než výchozí velikost 50 eDTU a doporučuje začít s nastavením 100 eDTU.

      ![Doporučení](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. V tomto kurzu ponechte výchozí hodnotu 50 eDTU a znovu klikněte na **Vybrat.**
   1. Výběrem **možnosti OK** vytvořte nový fond a přesuňte do něj vybrané databáze.

Vytvoření fondu a přesunutí databází trvá několik minut. Jak jsou databáze přesouvány, zůstávají online a plně přístupné až do poslední chvíle, kdy jsou všechna otevřená připojení uzavřena. Tak dlouho, dokud máte nějaké logiky opakování, klienti se pak připojí k databázi v novém fondu.

Přejděte na **Pool2** (na *\<tenants1-dpt- uživatelský\> * server) otevřít fond a sledovat jeho výkon. Pokud ho nevidíte, počkejte na dokončení zřizování nového fondu.

Nyní vidíte, že využití prostředků na *Pool1* klesla a že *Pool2* je nyní podobně načten.

## <a name="manage-performance-of-an-individual-database"></a>Správa výkonu jednotlivých databází

Pokud jednotlivé databáze ve fondu dochází k trvalé vysoké zatížení, v závislosti na konfiguraci fondu, může mít tendenci dominují prostředky ve fondu a vliv na jiné databáze. Pokud aktivita pravděpodobně bude nějakou dobu pokračovat, může být databáze dočasně přesunuta z fondu. To umožňuje databázi mít další prostředky, které potřebuje, a izoluje ji z jiných databází.

Toto cvičení simuluje vliv vysokého zatížení při prodeji lístků na populární koncert v Koncertním sále Contoso.

1. V **prostředí PowerShell ISE**otevřete ... \\ *Ukázkový skript PerformanceMonitoringAndManagement.ps1.*
1. Nastavte **$DemoScenario = 5, Generovat normální zatížení plus vysoké zatížení na jednoho klienta (přibližně 95 DTU).**
1. Nastavte **$SingleTenantDatabaseName = contosoconcerthall**
1. Skript proveďte pomocí **F5**.


1. Na [webu Azure Portal](https://portal.azure.com)přejděte seznam databází na uživatelském *\> serveru\<tenantů1-dpt-* uživatele. 
1. Klikněte na databázi **contosoconcerthall.**
1. Klikněte na bazén, ve které je **contosoconcerthall.** Vyhledejte fond v části **Elastic pool.**

1. Zkontrolujte graf **monitorování elastického fondu** a vyhledejte zvýšené využití eDTU fondu. Po jedné až dvou minutách by mělo začít zvýšené zatížení a měli byste rychle zaznamenat, že fond dosáhl 100% využití.
2. Zkontrolujte zobrazení **monitorování elastické databáze,** které zobrazuje nejžhavější databáze za poslední hodinu. Databáze *contosoconcerthall* by se měla brzy objevit jako jedna z pěti nejžhavějších databází.
3. **Klikněte na graf monitorování elastické databáze** **chart** a otevře stránku Využití **prostředků databáze,** kde můžete sledovat některou z databází. To vám umožní izolovat zobrazení pro databázi *contosoconcerthall.*
4. V seznamu databází klepněte na **contosoconcerthall**.
5. Kliknutím na **Cenovou úroveň (měřítko DTU)** otevřete stránku **Konfigurovat výkon,** kde můžete nastavit samostatnou velikost výpočetních prostředků pro databázi.
6. Kliknutím na kartu **Standardní** otevřete možnosti škálování ve standardní vrstvě.
7. Posunutím **jezdce DTU** doprava vyberte **100** DTU. Všimněte si, že to odpovídá cíli služby **S3**.
8. Klepnutím na tlačítko **Použít** přesuňte databázi z fondu a vytvořte z ní standardní databázi *S3.*
9. Po dokončení škálování, sledujte vliv na databázi contosoconcerthall a Pool1 na elastické fondu a databáze okna.

Jakmile vysoké zatížení databáze contosoconcerthall odezní, měli byste jej okamžitě vrátit do fondu, abyste snížili jeho náklady. Pokud není jasné, kdy k tomu dojde, můžete nastavit výstrahu v databázi, která se aktivuje, když jeho využití DTU klesne pod maximální hodnotu databáze ve fondu. Přesunutí databáze do fondu je popsáno v cvičení 5.

## <a name="other-performance-management-patterns"></a>Další vzorce správy výkonu

**Preventivní změna měřítka** Ve výše uvedeném cvičení, kde jste prozkoumali, jak škálovat izolované databáze, jste věděli, kterou databázi hledat. Pokud by vedení koncertní síně Contoso informovalo Wingtips o blížícím se prodeji vstupenek, databáze mohla být preventivně přesunuta z bazénu. Jinak by to vyžadovalo výstrahu ve fondu nebo v databázi s cílem zjistit, co se stalo. Pravděpodobně byste to nechtěli zjistit proto, že by si ostatní tenanti ve fondu stěžovali na snížený výkon. A kdyby mohli tenanti předvídat, na jak dlouho by potřebovali další prostředky, můžete nastavit runbook Azure Automation na přesunutí databáze mimo fond a potom zpět podle předem definovaného plánu.

**Samoobslužné škálování tenanta ** Protože škálování je úkol, který se snadno volá prostřednictvím rozhraní API pro škálu, můžete snadno vytvořit možnost škálovat databáze tenantů do aplikace směřující k tenantovi a nabízet ji jako funkci služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Vertikální navýšení a snížení kapacity podle plánu v souladu se vzorci využití**

Tam, kde agregované využití tenanta probíhá podle předvídatelných vzorců použití, můžete pomocí Azure Automation podle plánu vertikálně zvyšovat nebo snižovat kapacitu fondu. Můžete například snížit kapacitu fondu po 6. hodině večer a navýšit před 6. hodinou ráno ve dnech, kdy víte, že existuje pokles požadavků na prostředky.



## <a name="next-steps"></a>Další kroky

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze

[Kurz Obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Další zdroje

* Další [kurzy, které vycházejí z nasazení aplikace Wingtip Tickets SaaS Database SaaS na klienta](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastické fondy SQL](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Protokoly Azure Monitoru](saas-dbpertenant-log-analytics.md) – výuka nastavení a používání protokolů Azure Monitoru
