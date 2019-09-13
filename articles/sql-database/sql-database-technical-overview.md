---
title: Co je služba Azure SQL Database? | Dokumenty Microsoft
description: Přečtěte si základní informace o službě SQL Database – technické podrobnosti a možností cloudového systému pro správu relačních databází (RDBMS) společnosti Microsoft.
keywords: představení sql,úvod do sql,co je sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: d7cb473c54dc9cf735e43c65bc079fb4f21e4c97
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70913993"
---
# <a name="what-is-azure-sql-database-service"></a>Co je služba Azure SQL Database Service

Azure SQL Database je spravovaná služba relační databáze pro obecné účely, která umožňuje vytvořit vysoce dostupnou a vysoce výkonnou vrstvu úložiště dat pro aplikace a řešení v cloudu Microsoft Azure. SQL Database může být správnou volbou pro celou řadu moderních cloudových aplikací, protože umožňuje používat výkonné funkce pro zpracování relačních dat i nerelačních [struktur](sql-database-multi-model-features.md) , jako jsou grafy, JSON, prostorová data a XML. Vychází z nejnovější stabilní verze [databázového stroje Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) a umožňuje používat bohatou sadu pokročilých funkcí pro zpracování dotazů, jako jsou [vysoce výkonné technologie v paměti](sql-database-in-memory.md) a [Inteligentní zpracování dotazů. ](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
V rámci strategie Microsoftu zaměřené na cloud se nové funkce SQL Serveru uvolňují nejprve do služby SQL Database a až potom do samotného SQL Serveru. Tento přístup vám poskytuje nejnovější funkce SQL Serveru bez režijních nákladů na opravy nebo aktualizace. Kromě toho umožňuje testování těchto funkcí v milionech databází. SQL Database umožňuje snadno definovat a škálovat výkon v rámci dvou různých nákupních modelů: [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md) a [nákupní model založený na DTU](sql-database-service-tiers-dtu.md). SQL Database je plně spravovaná služba, která má integrované vysoce dostupnosti, zálohy a další běžné operace údržby. Microsoft zpracovává veškerou opravu a aktualizaci kódu SQL a operačního systému plynule a předchází všem správám základní infrastruktury.

> [!NOTE]
> Glosář termínů v Azure SQL Database naleznete v tématu [SQL Database terms Glosář](sql-database-glossary-terms.md)

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Database poskytuje následující možnosti nasazení databáze Azure SQL:

![možnosti nasazení](./media/sql-database-technical-overview/deployment-options.png)

- [Samostatná databáze](sql-database-single-database.md) představuje plně spravovanou izolovanou databázi, která je ideální volbou pro moderní cloudové aplikace a mikroslužby, které potřebují jeden spolehlivý zdroj dat. Jedna databáze je podobná databázi s [omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) v [Microsoft SQL Server databázovém stroji](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Managed instance](sql-database-managed-instance.md) je plně spravovaná instance [Microsoft SQL Server databázového stroje](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) obsahujícího sadu databází, které lze použít společně. Je to ideální volba pro snadné migrace místních SQL Server databází do cloudu Azure a pro aplikace, které potřebují využívat výkonné databázové funkce, které poskytuje SQL Server databázový stroj.
- [Elastický fond](sql-database-elastic-pool.md) je kolekce izolovaných [databází](sql-database-single-database.md) se sdílenou sadou prostředků, jako je například procesor nebo paměť. Jednotlivé databáze lze přesunout do elastického fondu nebo z něj.

> [!IMPORTANT]
> Pokud chcete pochopit rozdíly mezi funkcemi SQL Database a SQL Server a rozdíly mezi různými možnostmi nasazení Azure SQL Database, přečtěte si téma [funkce SQL](sql-database-features.md).

SQL Database přináší předvídatelný výkon s více typy prostředků, úrovněmi služeb a výpočetními velikostmi, které poskytují dynamickou škálovatelnost bez výpadků, integrované inteligentní optimalizace, globální škálovatelnost a dostupnost a rozšířené zabezpečení. možnosti – to vše s téměř nulovou správou. Díky těmto možnostem se můžete zaměřit na rychlý vývoj aplikací a zkrácení doby dodání produktu na trh, namísto vynakládání prostředků a drahocenného času na správu virtuálních počítačů a infrastruktury. SQL Database je aktuálně v 38 datových centrech po celém světě a další datová centra se pravidelně přidávají, což vám umožňuje provozovat databázi v datovém centru ve vaší blízkosti.

## <a name="scalable-performance-and-pools"></a>Škálovatelnost výkonu a fondy

Všechny typy SQL Database umožňují definovat množství prostředků, které budou přiřazeny. 
- S izolovanými databázemi jsou jednotlivé databáze izolované od sebe navzájem a přenosné, z nichž každá má vlastní garantované množství výpočetních, paměťových a úložných prostředků. Množství prostředků, které je přiřazeno k databázi, je vyhrazeno pro tuto databázi a nebude se sdílet s ostatními databázemi v cloudu Azure. Poskytuje vám taky možnost dynamicky [škálovat samostatné databázové prostředky](sql-database-single-database-scale.md) . Izolovaná databáze poskytuje různé výpočetní prostředky, paměť a úložné prostředky pro různé potřeby, které se liší od 1 do 80 virtuální jádra, 32 GB až 4 TB atd. [Úroveň služby škálování na úrovni služeb](sql-database-service-tier-hyperscale.md) pro izolovanou databázi vám umožní škálovat na 100 TB s možnostmi rychlého zálohování a obnovení.
- U elastických fondů můžete přiřadit prostředky, které budou sdíleny všemi databázemi ve fondu. Můžete vytvořit novou databázi nebo přesunout existující izolované databáze do fondu zdrojů, aby bylo možné maximalizovat využívání prostředků a ušetřit peníze a možnost dynamicky [škálovat prostředky elastického fondu](sql-database-elastic-pool-scale.md) nahoru a dolů.
- U spravovaných instancí je každá spravovaná instance izolovaná od ostatních instancí se zaručenými prostředky. V rámci spravované instance databáze instancí sdílí sadu prostředků – a možnost dynamicky [škálovat prostředky spravované instance](sql-database-managed-instance-resource-limits.md) směrem nahoru a dolů.

Svou první aplikaci můžete vytvořit na malé a izolované databázi s nízkými náklady za měsíc v úrovni služby pro obecné účely a pak ručně nebo programově změnit její úroveň služby na podnikovou úroveň služeb, aby splňovala požadavky vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

Dynamická škálovatelnost se liší od automatického škálování. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů. Samostatná databáze podporuje ruční dynamickou škálovatelnost, ale ne automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází. Existují však skripty, které mohou přispět k automatizaci škálovatelnosti pro jedinou databázi. Příklad najdete v tématu [použití PowerShellu pro monitorování a škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Nákupní modely

SQL Database nabízí dva nákupní modely:
- [Nákupní model založený na vCoreech](sql-database-service-tiers-vcore.md) umožňuje zvolit počet virtuální jádra, velikost paměti a velikost a rychlost úložiště. Nákupní model založený na vCoreech vám také umožní použít **[Zvýhodněné hybridní využití Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/)** k získání úspory nákladů. Další informace o Zvýhodněné hybridní využití Azure najdete v tématu [Nejčastější dotazy](#sql-database-frequently-asked-questions-faq).
- [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-výstupních prostředků ve třech úrovních služeb, aby se podporovaly odlehčené a těžké databázové úlohy. Výpočetní velikosti v rámci jednotlivých vrstev poskytují různé kombinace těchto prostředků, do kterých můžete přidat další prostředky úložiště.
- [Model bez serveru](sql-database-serverless.md) , který automaticky škáluje výpočetní výkon na základě požadavků na zatížení a faktur za množství výpočetní služby využité za sekundu. Výpočetní vrstva bez serveru taky automaticky pozastaví databáze během neaktivních období, kdy se účtují jenom úložiště, a automaticky obnoví databáze při návratu aktivity.

### <a name="service-tiers"></a>Úrovně služby

Azure SQL Database nabízí tři úrovně služeb, které jsou navrženy pro různé typy aplikací:
- [Pro obecné účely/standardní](sql-database-service-tier-general-purpose.md) úroveň služeb navržená pro běžné úlohy. Nabízí možnosti pro vyvážené výpočty a úložiště s vyrovnanou rozpočtem.
- Úroveň služby [pro důležité obchodní informace/Premium](sql-database-service-tier-business-critical.md) navržená pro aplikace OLTP s vysokou mírou transakcí a nejnižší latencí v/v. Nabízí nejvyšší odolnost proti chybám při použití několika izolovaných replik.
- Úroveň služby s velkým [měřítkem](sql-database-service-tier-hyperscale.md) navržená pro velmi rozsáhlou databázi OLTP a možnost automatického škálování úložiště a škálování výpočetních prostředků. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastické fondy pro maximalizaci využití prostředků

Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. [Elastické fondy](sql-database-elastic-pool.md) jsou navržené k řešení tohoto problému. Princip je jednoduchý. Prostředky výkonu přidělíte místo individuální databáze a platíte za celkový výkon fondu, nikoli pro jednotlivé databáze.

   ![elastické fondy](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

S elastickými fondy se nemusíte starat o zvyšování a snižování výkonu databáze s kolísajícími požadavky na prostředky. Databáze ve fondu spotřebovávají prostředky výkonu elastického fondu podle potřeby. Databáze ve fondu spotřebovávají výkon, ale nepřekračují omezení fondu, takže vaše náklady budou předvídatelné, i když využívání jednotlivých databází odhadnutelné nebude. A navíc můžete [přidávat a odebírat databáze ve fondu](sql-database-elastic-pool-manage-portal.md), škálovat aplikace od několik databází k tisícům a přitom mít rozpočet stále pod kontrolou. Můžete také řídit minimální a maximální prostředky, které mají databáze ve fondu k dispozici, a tím zajistit, že žádná databáze ve fondu nebude využívat všechny prostředky fondu a pro každou databázi ve fondu bude garantováno minimální množství prostředků. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skripty vám můžou pomoct s monitorováním a škálováním elastických fondů. Příklad najdete v tématu [Monitorování a škálování elastického fondu SQL ve službě Azure SQL Database pomocí PowerShellu](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Spravovaná instance nepodporuje elastické fondy. Místo toho je spravovaná instance kolekce databází instancí, které sdílejí prostředky spravované instance.

### <a name="blend-single-databases-with-pooled-databases"></a>Kombinace izolovaných databází s databázemi ve fondu

Jednotlivé databáze můžete kombinovat s elastickými fondy a snadno a rychle měnit úrovně služeb izolovaných databází a elastických fondů, abyste je mohli přizpůsobit vaší situaci. S výkonem a dostupností, které Azure nabízí, můžete se službou SQL Database kombinovat další služby Azure podle konkrétních potřeb vašich aplikací pro zvýšení ekonomičnosti provozu a efektivity prostředků a otevírat tak zcela nové obchodní příležitosti.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Rozsáhlé monitorování a možnosti upozorňování

Azure SQL Database poskytuje sadu pokročilých funkcí monitorování a řešení potíží, které vám pomůžou získat kompletní přehled o funkcích úloh. Funkce a nástroje mohou být zařazeny do kategorií:
 - Integrované možnosti monitorování poskytované nejnovější verzí SQL Server databázového stroje, které umožňují najít přehledy výkonu v reálném čase. 
 - PaaS možnosti monitorování poskytované platformou Azure, které vám umožňují snadno monitorovat velký počet instancí databáze a také nabízí Rady pro řešení potíží, které vám můžou pomoct vyřešit problémy s výkonem.

Nejdůležitější funkcí monitorování databázového stroje, kterou byste měli využít, je součást [úložiště dotazů](sql-database-operate-query-store.md) , která zaznamenává výkon dotazů v reálném čase a umožňuje identifikovat potenciální problémy s výkonem a nejvyšší prostředek. spotřebitelů. Automatické ladění a doporučení poskytují rady týkající se dotazů s navráceným výkonem a chybějícími nebo duplikovanými indexy. Automatické ladění v Azure SQL Database vám umožní ručně použít skripty, které můžou problémy vyřešit, nebo nechat Azure SQL Database použít opravu, test a ověření, a to díky tomu, že je tato výhoda a zachová nebo vrátí změnu v závislosti na výsledku. Kromě možností úložiště dotazů a automatického ladění můžete k monitorování výkonu úloh použít taky standardní [zobrazení dynamické správy a XEvent](sql-database-monitoring-with-dmvs.md) .

Platforma Azure poskytuje integrované nástroje pro [monitorování výkonu](sql-database-performance.md) a [upozorňování](sql-database-insights-alerts-portal.md) v kombinaci s hodnocením výkonu, které vám umožní snadno monitorovat stav tisíců databází. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity na základě aktuálních nebo plánovaných potřeb výkonu. Kromě toho může SQL Database [generovat metriky a diagnostické protokoly](sql-database-metrics-diag-logging.md) pro snazší monitorování. SQL Database můžete nakonfigurovat pro ukládání využití prostředků, pracovních procesů, relací a možností připojení do jednoho z těchto prostředků Azure:

- **Azure Storage**: Pro archivaci obrovského množství telemetrie za nízkou cenu.
- **Centrum událostí Azure**: Pro integraci SQL Database telemetrie s vlastními řešeními monitorování nebo aktivními kanály.
- **Protokoly Azure monitor**: Pro integrované řešení monitorování s využitím sestav, upozorňování a zmírnění možností.

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Možnosti dostupnosti

V tradičním SQL Server prostředí byste měli mít obvykle (nejméně) 2 počítače nastavené místně (synchronně udržované) kopie dat (pomocí funkcí, jako jsou skupiny dostupnosti AlwaysOn nebo instance clusterů s podporou převzetí služeb při selhání) k ochraně před selhání jednoho počítače nebo součásti. To zajišťuje vysokou dostupnost, ale nechrání proti přírodní katastrofě, která zničí vaše datové centrum.

Zotavení po havárii předpokládá, že závažná událost bude geograficky lokalizovaná, aby měla další počítač/sadu počítačů s kopií vašich dat.  V SQL Server můžete k získání této funkce použít skupiny dostupnosti Always On spuštěné v asynchronním režimu.  Rychlost světlého problému obvykle znamená, že uživatelé nechtějí čekat na replikaci, která je daleko před potvrzením transakce, takže při neplánovaných převzetí služeb při selhání může dojít ke ztrátě dat.

Databáze na úrovni Premium a důležitých podnikových služeb už [něco velmi podobné](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) synchronizaci skupiny dostupnosti. Databáze v nižších úrovních služeb poskytují redundanci prostřednictvím úložiště s využitím [jiného, ale ekvivalentního mechanismu](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Existuje logika, která chrání před selháním jednoho počítače.  Funkce aktivní geografické replikace vám dává možnost chránit před haváriemi, kdy je zničena celá oblast.

Zóny dostupnosti Azure je hraní potíží s vysokou dostupností.  Snaží se chránit před výpadkem jediného datového centra v rámci jedné oblasti.  Proto chce chránit před ztrátou energie nebo sítě až po sestavení. V SQL Azure to funguje tak, že se různé repliky v různých zónách dostupnosti (v různých budovách, efektivně) a jinak pracují jako dřív.

Ve skutečnosti má Azure špičkovou smlouvu o úrovni služeb [(SLA)](https://azure.microsoft.com/support/legal/sla/)s 99,99% dostupností, která využívá globální síť datacenter spravovaných Microsoftem, a pomáhá udržet aplikaci spuštěnou 24/7. Platforma Azure plně spravuje každou databázi a garantuje žádnou ztrátu dat a vysoké procento dostupnosti dat. Azure automaticky zpracovává opravy, zálohování, replikaci, detekci selhání, potenciální selhání základního hardwaru, softwaru nebo sítě, nasazování oprav chyb, převzetí služeb při selhání, upgrady databází a další úlohy údržby. Dostupnosti úrovně Standard se dosahuje oddělením výpočetní a úložné vrstvy. Dostupnost Premium se dosahuje integrací výpočetních prostředků a úložiště na jednom uzlu pro zajištění výkonu a následnou implementací technologie podobně jako skupiny dostupnosti Always On v rámci pokrývání. Úplnou diskuzi o funkcích Azure SQL Database vysoké dostupnosti najdete v článku [dostupnost SQL Database](sql-database-high-availability.md). Kromě toho SQL Database nabízí integrované funkce pro [provozní kontinuitu a globální škálovatelnost](sql-database-business-continuity.md), mezi které patří:

- **[Automatické zálohování](sql-database-automated-backups.md)** :

  SQL Database automaticky provádí zálohování Azure SQL Database na základě úplných, rozdílových a transakčních protokolů, které vám umožní obnovení do libovolného bodu v čase. Pro izolované databáze a databáze ve fondu můžete nakonfigurovat SQL Database pro ukládání úplných záloh databáze do Azure Storage pro dlouhodobé uchovávání záloh. U spravovaných instancí můžete pro dlouhodobé uchovávání záloh provádět i zálohy jen pro kopírování.

- **[Obnovení k bodu v čase](sql-database-recovery-using-backups.md)** :

  Všechny možnosti nasazení SQL Database podporují obnovení do libovolného bodu v čase v rámci automatické doby uchovávání záloh pro všechny databáze SQL Azure.
- **[Aktivní geografická replikace](sql-database-active-geo-replication.md)** :

  Izolovaná databáze a databáze ve fondu umožňují nakonfigurovat až čtyři sekundární databáze pro čtení ve stejném nebo globálně distribuovaném datovém centru Azure.  Pokud například máte aplikaci SaaS s databází katalogu s velkým počtem souběžných transakcí jen pro čtení, použijte geografickou replikaci a umožněte globální škálovaní operací čtení a odstraňte problémová místa v hlavní databázi způsobená úlohami čtení. U spravovaných instancí použijte skupiny s automatickým převzetím služeb při selhání.
- **[Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md)** :

  Všechny možnosti nasazení SQL Database umožňují používat skupiny převzetí služeb při selhání pro zajištění vysoké dostupnosti a vyrovnávání zatížení v globálním měřítku, včetně transparentní geografické replikace a převzetí služeb při selhání rozsáhlých sad databází, elastických fondů a spravovaných instancí. Skupiny převzetí služeb při selhání umožňují vytváření globálně distribuovaných aplikací SaaS s minimálními nároky na správu, které zanechávají veškeré komplexní monitorování, směrování a orchestraci převzetí služeb při selhání SQL Database.
- **[Redundantní databáze v zóně](sql-database-high-availability.md)** :

  SQL Database vám umožní zřídit v rámci několika zón dostupnosti důležité nebo podnikové databáze nebo elastické fondy úrovně Premium. Vzhledem k tomu, že tyto databáze a elastické fondy mají z důvodu zajištění vysoké dostupnosti několik redundantních replik, umístění těchto replik do několika zón dostupnosti zajistí vyšší odolnost, včetně možnosti automatického obnovení bez ztráty dat v případě selhání na úrovni datacentra.

## <a name="built-in-intelligence"></a>Integrované inteligentní funkce

Se službou SQL Database získáte integrované inteligentní funkce, které vám pomůžou výrazně snížit náklady na provoz a správu databází a maximalizovat výkon a zabezpečení aplikace. Během nepřetržitého spouštění milionů zákaznických úloh SQL Database shromažďuje a zpracovává obrovské množství telemetrických dat, přičemž zároveň plně respektuje ochranu osobních údajů zákazníků. Různé algoritmy neustále vyhodnocují telemetrická data, aby se služba mohla učit a přizpůsobovat vaší aplikaci. Na základě této analýzy se služba objeví s doporučeními pro zlepšení výkonu přizpůsobeným vašemu konkrétnímu zatížení.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatické monitorování a optimalizace výkonu

SQL Database nabízí podrobné přehledy dotazů, které potřebujete monitorovat. SQL Database zjistí aspekty vaší databáze a umožní vám přizpůsobit schéma databáze vaší úloze. SQL Database poskytuje [doporučení pro optimalizaci výkonu](sql-database-advisor.md), kde můžete zkontrolovat akce optimalizace a použít je.

Nepřetržité monitorování databáze je však pevně a zdlouhavá úloha, zejména při práci s mnoha databázemi. Služba [Intelligent Insights](sql-database-intelligent-insights.md) dělá tuto práci za vás pomocí automatického monitorování výkonu služby SQL Database a informuje vás o problémech se snižováním výkonu, identifikuje původní příčinu problému a poskytuje doporučení pro vylepšení výkonu, pokud je to možné.

Efektivní správa velkého počtu databází může být nemožná i se všemi dostupnými nástroji a sestavami, které SQL Database a Azure Portal nabízí. Místo ručního monitorování a optimalizace databáze můžete zvážit delegování některých akcí monitorování a optimalizace do služby SQL Database pomocí [automatické optimalizace](sql-database-automatic-tuning.md). SQL Database automaticky aplikuje doporučení, testy a ověří každé z jeho akcí optimalizace, aby se zajistilo, že bude výkon neustále zlepšovat. Tímto způsobem se SQL Database automaticky přizpůsobí vašim úlohám řízeným a bezpečným způsobem. Automatická optimalizace znamená, že se výkon vaší databáze pečlivě monitoruje a porovnává před a po každé akci optimalizace, a pokud se výkon nezlepší, akce optimalizace se vrátí zpět.

V současné době se mnoho našich partnerů provozujících [víceklientské aplikace SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) nad službou SQL Database spoléhá na automatickou optimalizaci výkonu pro zajištění, že jejich aplikace budou mít vždy stabilní a předvídatelný výkon. Tato funkce pro ně výrazně snižuje riziko incidentu výkonu uprostřed noci. Vzhledem k tomu, že část jejich zákazníků také používá SQL Server, můžou navíc s využitím stejných doporučení indexování získaných ze služby SQL Database pomáhat svým zákazníkům s SQL Serverem.

Ve službě [SQL Database jsou dostupné](sql-database-automatic-tuning.md) dva aspekty automatické optimalizace:

- **Automatická správa indexů**: Identifikuje indexy, které by měly být přidány do databáze, a indexů, které by měly být odebrány.
- **Automatické opravy plánu**: Identifikuje problematické plány a opravuje problémy s výkonem plánu SQL (již brzy, k dispozici v SQL Server 2017).

### <a name="adaptive-query-processing"></a>Adaptivní zpracování dotazů

Do služby SQL Database přidáváme také řadu funkcí pro [adaptivní zpracování dotazů](/sql/relational-databases/performance/intelligent-query-processing), včetně prokládaného spouštění funkcí s více příkazy vracejících tabulky, odezvy přidělení paměti v režimu dávky a adaptivních příkazů join v režimu dávky. Každá z těchto funkcí pro adaptivní zpracování dotazů používá podobné techniky učení a adaptace a pomáhá tak dále řešit problémy s výkonem související s historicky nezvladatelnými problémy s optimalizací dotazů.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

SQL Database nabízí řadu [integrovaných funkcí zabezpečení a dodržování předpisů](sql-database-security-overview.md), které vaší aplikaci pomůžou se splněním nejrůznějších požadavků na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Azure SQL Database (všechny možnosti nasazení) byly certifikovány na základě řady standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Rozšířené zabezpečení dat je jednotný balíček pro pokročilé funkce zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, správu ohrožení zabezpečení databáze a detekci neobvyklých aktivit, které by pro vaši databázi mohly znamenat hrozbu. Poskytuje centrální místo pro povolování a správu těchto možností.

- [Klasifikace & Discovery Data](sql-database-data-discovery-and-classification.md):

  Tato funkce (aktuálně ve verzi Preview) poskytuje funkce integrované do Azure SQL Database pro zjišťování, klasifikaci a označování & ochrany citlivých dat ve vašich databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md):

  Tato služba může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Detekce hrozeb](sql-database-threat-detection.md):

  Tato funkce detekuje aktivity neobvyklé indikující neobvyklé a potenciálně nebezpečné pokusy o přístup k databázi nebo jejím zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Výstrahy detekce hrozeb poskytují podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit.

### <a name="auditing-for-compliance-and-security"></a>Auditování dodržování předpisů a zabezpečení

[Auditování](sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

### <a name="data-encryption"></a>Šifrování dat

SQL Database zabezpečuje vaše data tím, že poskytuje šifrování pro data v pohybu pomocí [Transport Layer Security](https://support.microsoft.com/kb/3135244), pro neaktivní a šifrovaná [](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)data a pro data, která se používají s [Always šifrovaným](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)šifrováním.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL Database umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoft pomocí [integrace s Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování](sql-database-ssms-mfa-authentication.md) (MFA) pro zvýšení zabezpečení dat a aplikací při současné podpoře jednotného přihlašování.

### <a name="compliance-certification"></a>Certifikace dodržování předpisů

Služba SQL Database se účastní pravidelných auditů a byla certifikována pro řadu standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

## <a name="easy-to-use-tools"></a>Snadno použitelné nástroje

SQL Database zjednodušuje a zefektivňuje vytváření a správu aplikací. SQL Database vám umožňuje soustředit se na to, v čem jste nejlepší – na vytváření skvělých aplikací. Ve službě SQL Database můžete vyvíjet a provádět správu pomocí nástrojů a dovedností, které už máte.

- **[Azure Portal](https://portal.azure.com/)** :

  Webová aplikace pro správu všech služeb Azure
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  Bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Server po SQL Database
- **[Nástroje pro SQL Server dat v aplikaci Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  Bezplatná klientská aplikace ke stažení pro vývoj SQL Server relačních databází, databází Azure SQL, balíčků integračních služeb, Analysis Services datových modelů a sestav služby Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)** :

  Bezplatný, Open Source Editor kódu pro Windows, macOS a Linux, který podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a SQL Data Warehouse.

SQL Database podporuje vytváření aplikací pomocí Pythonu, Javy, Node.js, PHP, Ruby a .NET v systémech MacOS, Linux a Windows. SQL Database podporuje stejné [knihovny připojení](sql-database-libraries.md) jako SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>Nejčastější dotazy SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>Jaká je aktuální verze SQL Database

Aktuální verze SQL Database je V12. V11 verze je vyřazená.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Můžu řídit, kdy dochází k výpadkům při opravování

Ne. Pokud ve své aplikaci použijete [logiku opakování](sql-database-develop-overview.md#resiliency) , nemusíte mít dopad na opravy. Další informace o přípravě na plánované události údržby ve službě Azure SQL Database najdete v tématu [plánování událostí údržby Azure v Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Zvýhodněné hybridní využití Azure dotazy

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Jsou k dispozici dvě práva pro použití s Zvýhodněné hybridní využití Azure pro SQL Server

Máte 180 dnů od duálních práv k používání licence, abyste zajistili bezproblémové spouštění migrací. Po uplynutí 180 dne se dá licence SQL Server použít jenom v cloudu v SQL Database a nemá k dispozici dvě práva na používání místně a v cloudu.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak se Zvýhodněné hybridní využití Azure SQL Server liší od mobility licencí

Dnes nabízíme výhody mobility licencí, které SQL Server zákazníky se Software Assurance, který umožňuje opětovné přiřazení jejich licencí na sdílené servery třetích stran. Tato výhoda se dá využít na Azure IaaS a AWS EC2.
Zvýhodněné hybridní využití Azure pro SQL Server se liší od mobility licencí ve dvou klíčových oblastech:

- Poskytuje ekonomické výhody pro přesun vysoce virtualizovaných úloh do Azure. Zákazníci s SQL EE můžou získat 4 jádra v Azure v rámci Pro obecné účely SKU pro každý jádro, které vlastní místní pro vysoce virtualizované aplikace. Mobilita licencí neumožňuje žádné zvláštní výhody spojené s přesunem virtualizovaných úloh do cloudu.
- Poskytuje pro PaaS cíl v Azure (SQL Database spravované instance), který je vysoce kompatibilní s SQL Serverem v místním prostředí.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jaká jsou konkrétní práva Zvýhodněné hybridní využití Azure pro SQL Server

K Zvýhodněné hybridní využití Azure pro SQL Server budou mít zákazníci SQL Database následující práva:

|Nároky na licenci|K čemu Zvýhodněné hybridní využití Azure SQL Server získat přístup?|
|---|---|
|Zákazníci se základními edicemi SQL Server Enterprise s SA|<li>Může platit základní sazba pro Pro obecné účely nebo Pro důležité obchodní informace SKLADOVOU položku.</li><br><li>1 jádro v místním prostředí = 4 jádra v Pro obecné účely SKU</li><br><li>1 jádro v místním prostředí = 1 jádro v Pro důležité obchodní informace SKU</li>|
|Zákazníci se základními edicemi SQL Server Standard s SA|<li>Může platit základní sazba jenom pro Pro obecné účely SKU.</li><br><li>1 jádro v místním prostředí = 1 jádro v Pro obecné účely SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Spolupráce s technickým týmem SQL Serveru

- [Výměna z stacku DBA](https://dba.stackexchange.com/questions/tagged/sql-server): Klást otázky pro správu databáze
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Zeptat se na vývojové otázky
- [Fóra MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Zeptejte se na technické dotazy
- [Zpětná vazba](https://aka.ms/sqlfeedback): Hlášení chyb a funkcí žádosti
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskuze SQL Server

## <a name="next-steps"></a>Další postup

- Na [stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/) najdete cenové kalkulačky a srovnání cen izolovaných databází a elastických fondů.
- Tyto rychlé starty vám pomůžou začít:

  - [Vytvoření databáze SQL na webu Azure Portal](sql-database-single-database-get-started.md)  
  - [Vytvoření databáze SQL pomocí Azure CLI](sql-database-get-started-cli.md)
  - [Vytvoření databáze SQL pomocí PowerShellu](sql-database-get-started-powershell.md)

- Řadu ukázek v Azure CLI a PowerShellu najdete tady:
  - [Ukázky v Azure CLI pro službu SQL Database](sql-database-cli-samples.md)
  - [Ukázky v Azure PowerShellu pro službu SQL Database](sql-database-powershell-samples.md)

 - Informace o nových funkcích při jejich ohlášení najdete v tématu. 
   - **[Plán Azure pro SQL Database](https://azure.microsoft.com/roadmap/?category=databases)** , kde zjistíte, co je nového a co se chystá dál.
  - **[Azure SQL Database blogu](https://azure.microsoft.com/blog/topics/database)** – místo, kde SQL Server členové produktového týmu SQL Database novinek a funkcí.

