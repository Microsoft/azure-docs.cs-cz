---
title: Co je služba Azure SQL Database? | Dokumenty Microsoft
description: 'Seznámení s SQL Database: technické podrobnosti a možnosti systému pro správu relačních databází Microsoft (RDBMS) v cloudu.'
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
ms.openlocfilehash: e5782ba016cf58335de17cdacabbcca95914f59a
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066281"
---
# <a name="what-is-the-azure-sql-database-service"></a>Co je služba Azure SQL Database?

Azure SQL Database je relační databáze pro obecné účely, která je poskytována jako spravovaná služba. Díky tomu můžete vytvořit vysoce dostupnou a vysoce výkonnou vrstvu úložiště dat pro aplikace a řešení v Azure. SQL Database může být správnou volbou pro celou řadu moderních cloudových aplikací, protože umožňuje zpracovat jak relační, tak i [nerelační struktury](sql-database-multi-model-features.md), jako jsou grafy, JSON, prostorová data a XML.

Vychází z nejnovější stabilní verze [databázového stroje Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Můžete používat funkce pokročilého zpracování dotazů, jako jsou [vysoce výkonné technologie v paměti](sql-database-in-memory.md) a [Inteligentní zpracování dotazů](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Nejnovější funkce SQL Server jsou nejprve vydávány SQL Database a následně SQL Server sám sebe. Získáte nejnovější SQL Server možnosti bez režie pro opravy nebo upgrade, testováno v milionech databází. 

SQL Database umožňuje snadno definovat a škálovat výkon v rámci dvou různých nákupních modelů: [nákupní model založený na Vcore](sql-database-service-tiers-vcore.md) a [nákupní model založený na DTU](sql-database-service-tiers-dtu.md). SQL Database je plně spravovaná služba, která má vestavěnou vysokou dostupnost, zálohování a další běžné operace údržby. Společnost Microsoft zpracovává všechny opravy a aktualizace kódu SQL a operačního systému. Nemusíte spravovat základní infrastrukturu.

> [!NOTE]
> Relevantní pojmy a jejich definice najdete v [glosáři SQL Databasech podmínek](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Database poskytuje následující možnosti nasazení databáze Azure SQL:

![Diagram možností nasazení](./media/sql-database-technical-overview/deployment-options.png)

- [Samostatná databáze](sql-database-single-database.md) představuje plně spravovanou izolovanou databázi. Tuto možnost můžete použít, pokud máte moderní cloudové aplikace a mikroslužby, které potřebují jeden spolehlivý zdroj dat. Jedna databáze je podobná databázi s [omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) v [Microsoft SQL Server databázovém stroji](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Managed instance](sql-database-managed-instance.md) je plně spravovaná instance [Microsoft SQL Server databázového stroje](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Obsahuje sadu databází, které lze použít společně. Tato možnost slouží k snadné migraci místních SQL Server databází do cloudu Azure a pro aplikace, které potřebují používat databázové funkce, které poskytuje SQL Server databázový stroj.
- [Elastický fond](sql-database-elastic-pool.md) je kolekce izolovaných [databází](sql-database-single-database.md) se sdílenou sadou prostředků, jako je například procesor nebo paměť. Jednotlivé databáze lze přesunout do elastického fondu nebo z něj.

> [!IMPORTANT]
> Pro pochopení rozdílů mezi funkcemi SQL Database a SQL Server a rozdíly mezi různými možnostmi nasazení Azure SQL Database najdete v tématu [SQL Database funkce](sql-database-features.md).

SQL Database poskytuje předvídatelný výkon s více typy prostředků, úrovněmi služeb a výpočetními velikostmi. Poskytuje dynamickou škálovatelnost bez výpadků, integrované inteligentní optimalizace, globální škálovatelnost a dostupnost a rozšířené možnosti zabezpečení. Tyto možnosti vám umožní soustředit se na rychlý vývoj aplikací a zrychlení času na uvedení na trh, nikoli na správu virtuálních počítačů a infrastruktury. Služba SQL Database je aktuálně ve 38 datacentrech po celém světě, takže můžete svou databázi běžet v datovém centru blízko vás.

## <a name="scalable-performance-and-pools"></a>Škálovatelnost výkonu a fondy

Můžete definovat množství přiřazených prostředků. 
- U izolovaných databází je každá databáze izolovaná od ostatních a je přenosná. Každá z nich má vlastní garantované množství výpočetních, paměťových a úložných prostředků. Množství prostředků přiřazených k databázi je vyhrazeno pro tuto databázi a není sdíleno s ostatními databázemi v Azure. Je možné dynamicky [škálovat samostatné databázové prostředky](sql-database-single-database-scale.md) . Možnost jediná databáze nabízí různé výpočetní prostředky, paměť a prostředky úložiště pro různé potřeby. Můžete například získat 1 až 80 virtuální jádra nebo 32 GB až 4 TB. [Úroveň služby škálování na úrovni služeb](sql-database-service-tier-hyperscale.md) pro izolovanou databázi vám umožní škálovat na 100 TB s možnostmi rychlého zálohování a obnovení.
- U elastických fondů můžete přiřadit prostředky, které jsou sdíleny všemi databázemi ve fondu. Můžete vytvořit novou databázi nebo přesunout existující izolované databáze do fondu zdrojů, aby bylo možné maximalizovat využívání prostředků a ušetřit peníze. Tato možnost vám také umožní dynamicky [škálovat prostředky elastického fondu](sql-database-elastic-pool-scale.md) směrem nahoru a dolů.
- U spravovaných instancí je každá spravovaná instance izolovaná od ostatních instancí se zaručenými prostředky. V rámci spravované instance databáze instancí sdílí sadu prostředků. [Prostředky spravované instance](sql-database-managed-instance-resource-limits.md) můžete dynamicky škálovat směrem nahoru a dolů.

Svou první aplikaci můžete vytvořit na malé a omezené databázi s nízkými náklady měsíčně na úrovni služby pro obecné účely. Svou vrstvu služby pak můžete kdykoli nebo programově změnit na kritickou úroveň služeb, aby splňovala požadavky vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, když je potřebujete.

*Dynamická škálovatelnost* se liší od *automatického škálování*. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů. Možnost jediná databáze podporuje ruční dynamickou škálovatelnost, ale ne automatické škálování. Pro další automatické prostředí zvažte použití elastických fondů, které umožňují databázím sdílet prostředky ve fondu na základě potřeb jednotlivých databází. Další možností je použít skripty, které mohou přispět k automatizaci škálovatelnosti pro izolovanou databázi. Příklad najdete v tématu [použití PowerShellu pro monitorování a škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Nákupní modely

SQL Database nabízí následující modely nákupu:
- [Nákupní model založený na vCoreech](sql-database-service-tiers-vcore.md) umožňuje zvolit počet virtuální jádra, velikost paměti a velikost a rychlost úložiště. Nákupní model založený na vCoreech vám také umožní použít [Zvýhodněné hybridní využití Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) k získání úspory nákladů. Další informace o Zvýhodněné hybridní využití Azure najdete v části Nejčastější dotazy dále v tomto článku.
- [Nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-výstupních prostředků ve třech úrovních služeb, aby bylo možné podporovat světlo náročné databázové úlohy. Výpočetní velikosti v rámci jednotlivých vrstev poskytují různé kombinace těchto prostředků, do kterých můžete přidat další prostředky úložiště.
- [Model bez serveru](sql-database-serverless.md) automaticky škáluje výpočetní výkon na základě požadavků na zatížení a fakturuje se za množství výpočetní služby za sekundu. Výpočetní vrstva bez serveru taky automaticky pozastaví databáze během neaktivních období, kdy se účtuje jenom úložiště, a automaticky obnoví databáze, když se vrátí aktivita.

### <a name="service-tiers"></a>Úrovně služby

Azure SQL Database nabízí tři úrovně služeb, které jsou navrženy pro různé typy aplikací:
- [Pro obecné účely/standardní](sql-database-service-tier-general-purpose.md) úroveň služeb navržená pro běžné úlohy. Nabízí možnosti vyrovnaných výpočetních a úložných operací orientovaných na rozpočet.
- Úroveň služby [pro důležité obchodní informace/Premium](sql-database-service-tier-business-critical.md) navržená pro aplikace OLTP s vysokou přenosovou rychlostí a v/v operace s nejnižší latencí. Nabízí nejvyšší odolnost proti chybám pomocí několika izolovaných replik.
- Úroveň služby s velkým [měřítkem](sql-database-service-tier-hyperscale.md) navržená pro velmi rozsáhlou databázi OLTP a možnost automatického škálování úložiště a škálování výpočetních prostředků. 

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastické fondy pro maximalizaci využití prostředků

Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Nepředvídatelné vzorce používání můžou mít těžkou správu nákladů a obchodního modelu. [Elastické fondy](sql-database-elastic-pool.md) jsou navržené k řešení tohoto problému. Prostředky výkonu můžete přidělit fondu, nikoli k jednotlivým databázím. Platíte za souhrnné prostředky výkonu fondu, a ne pro výkon izolovaných databází.

   ![Obrázek zobrazující elastické fondy v edicích Basic, Standard a Premium](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

S elastickými fondy se nemusíte starat o zvyšování a snižování výkonu databáze s kolísajícími požadavky na prostředky. Databáze ve fondu spotřebovávají prostředky výkonu elastického fondu podle potřeby. Databáze ve fondu spotřebovávají výkon, ale nepřekračují omezení fondu, takže vaše náklady budou předvídatelné, i když využívání jednotlivých databází odhadnutelné nebude.

[Do fondu můžete přidávat a odebírat databáze a](sql-database-elastic-pool-manage-portal.md)škálovat aplikace z několik databází na tisíce, a to vše v rámci rozpočtu, který ovládáte. Můžete také řídit minimální a maximální prostředky, které jsou k dispozici pro databáze ve fondu, aby se zajistilo, že žádná databáze ve fondu nebude využívat všechny prostředky fondu a že každá databáze ve fondu má zaručené minimální množství prostředků. Další informace o návrhových vzorech pro aplikace typu software jako služba (SaaS), které používají elastické fondy, najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS s SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Skripty vám můžou pomoct s monitorováním a škálováním elastických fondů. Příklad najdete [v tématu použití PowerShellu pro monitorování a škálování elastického fondu SQL v Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Spravovaná instance nepodporuje elastické fondy. Místo toho je spravovaná instance kolekce databází instancí, které sdílejí prostředky spravované instance.

### <a name="blend-single-databases-with-pooled-databases"></a>Kombinace izolovaných databází s databázemi ve fondu

Můžete blendovat izolované databáze s elastickými fondy a měnit úrovně služeb izolovaných databází a elastických fondů, aby se mohly přizpůsobit vaší situaci. Můžete také kombinovat a porovnat další služby Azure s SQL Database, abyste splnili Vaše jedinečné požadavky na návrh aplikace, náklady a efektivitu zdrojů a odemkli nové obchodní příležitosti.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Rozsáhlé monitorování a možnosti upozorňování

Azure SQL Database poskytuje pokročilé funkce monitorování a řešení potíží, které vám pomůžou získat hlubší přehled o charakteristikách úloh. Mezi tyto funkce a nástroje patří:
 - Integrované možnosti monitorování poskytované nejnovější verzí nástroje SQL Server Database Engine. Umožňují vám najít přehledy výkonu v reálném čase. 
 - PaaS možnosti monitorování poskytované Azure, které umožňují monitorovat a řešit potíže s velkým počtem instancí databáze.

[Úložiště dotazů](sql-database-operate-query-store.md), integrovaná funkce monitorování SQL Server, zaznamenává výkon dotazů v reálném čase a umožňuje identifikovat potenciální problémy s výkonem a nejčastější uživatele prostředků. Automatické ladění a doporučení poskytují rady ohledně dotazů s navráceným výkonem a chybějícími nebo duplikovanými indexy. Automatické ladění v SQL Database umožňuje buď ručně použít skripty, které můžou problémy vyřešit, nebo nechat tuto opravu SQL Database použít. SQL Database může také otestovat a ověřit, zda oprava poskytuje určitou výhodu, a zachovat nebo vrátit změny v závislosti na výsledku. Kromě možností úložiště dotazů a automatického ladění můžete ke sledování výkonu úloh používat standardní [zobrazení dynamické správy a XEvent](sql-database-monitoring-with-dmvs.md) .

Azure poskytuje [integrované nástroje pro monitorování výkonu](sql-database-performance.md) a [upozorňování](sql-database-insights-alerts-portal.md) v kombinaci s hodnocením výkonu, které vám umožní monitorovat stav tisíců databází. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity na základě aktuálních nebo plánovaných potřeb výkonu. Kromě toho může SQL Database [generovat metriky a diagnostické protokoly](sql-database-metrics-diag-logging.md) pro snazší monitorování. SQL Database můžete nakonfigurovat pro ukládání využití prostředků, pracovních procesů, relací a možností připojení do jednoho z těchto prostředků Azure:

- **Azure Storage**: Pro archivaci obrovského množství telemetrie za nízkou cenu.
- **Event Hubs Azure**: Pro integraci SQL Database telemetrie s vlastními řešeními monitorování nebo aktivními kanály.
- **Protokoly Azure monitor**: Integrované řešení monitorování s využitím sestav, upozorňování a zmírnění možností.

![Diagram architektury monitorování Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Možnosti dostupnosti

V tradičním prostředí SQL Server máte obvykle nastavené aspoň dva počítače místně. Tyto počítače mají přesnou a synchronně udržovanou kopii dat, která se mají chránit před selháním jednoho počítače nebo součásti. Toto prostředí poskytuje vysokou dostupnost, ale nechrání proti přirozené havárii, které zničí vaše datové centrum.

Zotavení po havárii předpokládá, že je závažná událost geograficky lokalizovaná, aby mohla mít jiný počítač nebo sadu počítačů s kopií vašich dat daleko pryč. V SQL Server můžete k získání této funkce použít skupiny dostupnosti Always On spuštěné v asynchronním režimu. Lidé často nechtějí čekat na replikaci, která je daleko před potvrzením transakce, takže při neplánovaných převzetí služeb při selhání může dojít ke ztrátě dat.

Databáze na úrovni Premium a důležitých podnikových služeb už [něco velmi podobné](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) synchronizaci skupiny dostupnosti. Databáze v nižších úrovních služeb poskytují redundanci prostřednictvím úložiště pomocí jiného, [ale ekvivalentního mechanismu](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Vestavěná logika pomáhá chránit proti selhání jednoho počítače. Funkce aktivní geografické replikace vám dává možnost chránit před haváriemi, kdy je zničena celá oblast.

Zóny dostupnosti Azure se snaží chránit před výpadkem jednoho datového centra v rámci jedné oblasti. Pomáhá chránit před ztrátou energie nebo sítě až po sestavení. V SQL Database umístíte různé repliky do různých zón dostupnosti (ve skutečnosti i v různých budovách).

Smlouva o úrovni služeb [(SLA)](https://azure.microsoft.com/support/legal/sla/) Azure, která využívá globální síť datacenter spravovaných Microsoftem, pomáhá zajistit, aby vaše aplikace běžela 24/7. Platforma Azure plně spravuje každou databázi a zaručuje žádnou ztrátu dat a vysoké procento dostupnosti dat. Azure automaticky zpracovává opravy, zálohy, replikaci, detekci selhání, základní potenciální hardware, software nebo síť, nasazování oprav chyb, převzetí služeb při selhání, upgrady databáze a další úlohy údržby. Dostupnosti úrovně Standard se dosahuje oddělením výpočetní a úložné vrstvy. Dostupnost Premium se dosahuje integrací výpočetních prostředků a úložiště v jednom uzlu pro výkon a následnou implementací technologie podobně jako u skupin dostupnosti Always On. Úplnou diskuzi o funkcích Azure SQL Database vysoké dostupnosti najdete v článku [dostupnost SQL Database](sql-database-high-availability.md). 

Kromě toho SQL Database poskytuje integrovanou funkci pro [provozní kontinuitu a globální škálovatelnost](sql-database-business-continuity.md) . Mezi ně patří:

- [Automatické zálohování](sql-database-automated-backups.md):

  SQL Database automaticky provádí zálohování databází SQL prostřednictvím úplného, rozdílového a transakčního protokolu, aby bylo možné provést obnovení do libovolného bodu v čase. Pro izolované databáze a databáze ve fondu můžete nakonfigurovat SQL Database pro ukládání úplných záloh databáze do Azure Storage pro dlouhodobé uchovávání záloh. U spravovaných instancí můžete pro dlouhodobé uchovávání záloh provádět i zálohy jen pro kopírování.

- [Obnovení k bodu v čase](sql-database-recovery-using-backups.md):

  Všechny možnosti nasazení SQL Database podporují obnovení do libovolného bodu v čase v rámci automatické doby uchovávání záloh pro všechny databáze SQL.
- [Aktivní geografická replikace](sql-database-active-geo-replication.md):

  Možnosti databáze a databáze ve fondu umožňují nakonfigurovat až čtyři sekundární databáze pro čtení ve stejném nebo globálně distribuovaném datacentru Azure. Například pokud máte aplikaci SaaS s databází katalogu, která má velký objem souběžných transakcí jen pro čtení, použijte aktivní geografickou replikaci k povolení globálního škálování pro čtení. Tím dojde k odebrání kritických bodů na primárních počítačích, které jsou způsobeny čtením zatížení. U spravovaných instancí použijte skupiny s automatickým převzetím služeb při selhání.
- [Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md):

  Všechny možnosti nasazení SQL Database umožňují používat skupiny převzetí služeb při selhání pro zajištění vysoké dostupnosti a vyrovnávání zatížení v globálním měřítku. Zahrnuje transparentní geografickou replikaci a převzetí služeb při selhání velkých sad databází, elastických fondů a spravovaných instancí. Skupiny převzetí služeb při selhání umožňují vytváření globálně distribuovaných aplikací SaaS s minimálními nároky na správu. Tím se zachová veškerá komplexní monitorování, směrování a orchestrace převzetí služeb při selhání SQL Database.
- [Redundantní databáze v zóně](sql-database-high-availability.md):

  SQL Database vám umožní zřídit v rámci několika zón dostupnosti důležité nebo podnikové databáze nebo elastické fondy úrovně Premium. Vzhledem k tomu, že tyto databáze a elastické fondy mají více redundantních replik pro vysokou dostupnost, jejich umístění do více zón dostupnosti poskytuje vyšší odolnost. To zahrnuje možnost obnovování automaticky z chyb škálování Datacenter bez ztráty dat.

## <a name="built-in-intelligence"></a>Integrované inteligentní funkce

Díky SQL Database získáte integrované inteligentní funkce, které vám pomůžou výrazně snížit náklady na spouštění a správu databází a které maximalizují výkon a zabezpečení vaší aplikace. Provoz milionům úloh zákazníků po hodinách SQL Database shromažďuje a zpracovává obrovské množství dat telemetrie a zároveň plně respektuje ochranu osobních údajů zákazníků. Různé algoritmy průběžně vyhodnocují data telemetrie, aby se služba mohla učit a přizpůsobovat k vaší aplikaci.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatické monitorování a optimalizace výkonu

SQL Database nabízí podrobné přehledy dotazů, které potřebujete monitorovat. SQL Database se dozvíte o vzorech databáze a umožňuje vám přizpůsobit schéma databáze pro vaše úlohy. SQL Database poskytuje [doporučení pro optimalizaci výkonu](sql-database-advisor.md), kde můžete zkontrolovat akce optimalizace a použít je.

Nepřetržité monitorování databáze je však pevně a zdlouhavá úloha, zejména při práci s mnoha databázemi. Tato úloha [Intelligent Insights](sql-database-intelligent-insights.md) za vás automaticky monitoruje výkon SQL Database ve velkém měřítku. Informuje vás o problémech se snížením výkonu, identifikuje hlavní příčinu každého problému a poskytuje doporučení pro zlepšení výkonu, pokud je to možné.

Správa obrovského počtu databází může být nemožná, a to i u všech dostupných nástrojů a sestav, které SQL Database a Azure poskytuje. Místo ručního monitorování a optimalizace databáze můžete zvážit delegování některých akcí monitorování a optimalizace SQL Database pomocí [automatického ladění](sql-database-automatic-tuning.md). SQL Database automaticky aplikuje doporučení, testy a ověří každé z jeho akcí optimalizace, aby se zajistilo, že bude výkon neustále zlepšovat. Tímto způsobem se SQL Database automaticky přizpůsobí vašim úlohám řízeným a bezpečným způsobem. Automatické ladění znamená, že se výkon vaší databáze pečlivě monitoruje a porovnává před a po každé akci optimalizace. Pokud se výkon nezvyšuje, akce optimalizace se vrátí zpět.

Mnohé z našich partnerů, kteří spouštějí [SaaS aplikace SQL Database pro více tenantů](sql-database-design-patterns-multi-tenancy-saas-applications.md) , se spoléhají na automatické ladění výkonu, abyste měli jistotu, že jejich aplikace mají vždycky stabilní a předvídatelný výkon. Tato funkce pro ně výrazně snižuje riziko incidentu výkonu uprostřed noci. Kromě toho, vzhledem k tomu, že součástí jejich základu pro zákazníky je také SQL Server, používají stejná doporučení pro indexování poskytovaná SQL Database, která svým SQL Server zákazníkům pomohou.

[V SQL Database jsou k dispozici](sql-database-automatic-tuning.md)dvě další aspekty automatického ladění:

- **Automatická správa indexů**: Identifikuje indexy, které by měly být přidány do databáze, a indexů, které by měly být odebrány.
- **Automatické opravy plánu**: Identifikuje problematické plány a opravuje problémy s výkonem plánu SQL.

### <a name="adaptive-query-processing"></a>Adaptivní zpracování dotazů

Můžete použít [adaptivní zpracování dotazů](/sql/relational-databases/performance/intelligent-query-processing), včetně prokládaného spouštění funkcí s více příkazy vracející tabulky, zpětné vazby přidělení paměti v režimu dávky a adaptivních spojení v režimu Batch. Každá z těchto funkcí pro adaptivní zpracování dotazů používá podobné techniky učení a adaptace a pomáhá tak dále řešit problémy s výkonem související s historicky nezvladatelnými problémy s optimalizací dotazů.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

SQL Database nabízí řadu [integrovaných funkcí zabezpečení a dodržování předpisů](sql-database-security-overview.md), které vaší aplikaci pomůžou se splněním nejrůznějších požadavků na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Microsoft má pro určitý počet standardů dodržování předpisů certifikovaný Azure SQL Database (všechny možnosti nasazení). Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

### <a name="advance-threat-protection"></a>Ochrana před hrozbami

Rozšířené zabezpečení dat je jednotný balíček pro pokročilé funkce zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, správu ohrožení zabezpečení databáze a zjišťování neobvykléch aktivit, které mohou naznačovat hrozbu pro vaši databázi. Poskytuje jedno umístění pro povolení a správu těchto schopností.

- [Zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md):

  Tato funkce poskytuje integrované funkce Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Poskytuje přehled o stavu klasifikace databáze a sleduje přístup k citlivým datům v rámci databáze a mimo její ohraničení.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md):

  Tato služba může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Detekce hrozeb](sql-database-threat-detection.md):

  Tato funkce detekuje aktivity neobvyklé, které naznačují neobvyklé a potenciálně škodlivé pokusy o přístup k databázi nebo jejím zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Výstrahy detekce hrozeb poskytují podrobnosti o podezřelé aktivitě a doporučuje akci, jak tuto hrozbu prozkoumat a zmírnit.

### <a name="auditing-for-compliance-and-security"></a>Auditování dodržování předpisů a zabezpečení

[Auditování](sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage. Auditování vám může pomoci zajistit dodržování legislativních předpisů, pochopit databázovou činnost a získat přehled o nesrovnalostech a anomáliích, které by mohly poukazovat na obavy z podnikání nebo na podezřelé porušení zabezpečení.

### <a name="data-encryption"></a>Šifrování dat

SQL Database pomáhá zabezpečit vaše data tím, že zajišťuje šifrování. Pro data v pohybu používá [zabezpečení transportní vrstvy](https://support.microsoft.com/kb/3135244). V případě neaktivních dat používá [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Pro data, která se používají, používá [vždycky šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL Database umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoft pomocí [integrace s Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje službu [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md) pro zvýšení zabezpečení dat a aplikací při podpoře jednotného procesu přihlašování.

## <a name="easy-to-use-tools"></a>Snadno použitelné nástroje

SQL Database zjednodušuje a zefektivňuje vytváření a správu aplikací. SQL Database vám umožňuje soustředit se na to, v čem jste nejlepší – na vytváření skvělých aplikací. Můžete spravovat a vyvíjet v SQL Database pomocí nástrojů a dovedností, které už máte.

- [Azure Portal](https://portal.azure.com/):

  Webová aplikace pro správu všech služeb Azure.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Server po SQL Database.
- [Nástroje pro SQL Server dat v aplikaci Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Bezplatná klientská aplikace ke stažení pro vývoj SQL Server relačních databází, databází SQL, balíčků integračních služeb, Analysis Services datových modelů a sestav služby Reporting Services.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Bezplatný a Open Source Editor kódu pro Windows, macOS a Linux. Podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a Azure SQL Data Warehouse.

SQL Database podporuje vytváření aplikací pomocí jazyků Python, Java, Node. js, PHP, Ruby a .NET v systémech macOS, Linux a Windows. SQL Database podporuje stejné [knihovny připojení](sql-database-libraries.md) jako SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database nejčastějších dotazech

### <a name="what-is-the-current-version-of-sql-database"></a>Jaká je aktuální verze SQL Database?

Aktuální verze SQL Database je V12. V11 verze je vyřazená.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Můžu řídit, kdy dochází k výpadkům při opravování?

Ne. Pokud ve své aplikaci použijete [logiku opakování](sql-database-develop-overview.md#resiliency) , nemusíte mít dopad na opravy. Další informace najdete v tématu [plánování událostí údržby Azure v Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Zvýhodněné hybridní využití Azure dotazy

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Jsou k dispozici dvě práva pro použití s Zvýhodněné hybridní využití Azure pro SQL Server?

Máte 180 dnů od duálních práv k používání licence, abyste zajistili bezproblémové spouštění migrací. Po uplynutí 180 dne můžete použít jenom licenci SQL Server v cloudu v SQL Database. Už nemusíte mít k dispozici dvojí používání místních i cloudových práv.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak se Zvýhodněné hybridní využití Azure SQL Server liší od mobility licencí?

SQL Server zákazníkům se Software Assurance nabízíme výhody mobility licencí. To umožňuje přeřazení svých licencí na sdílené servery partnera. Tuto výhodu můžete využít v Azure IaaS a AWS EC2.

Zvýhodněné hybridní využití Azure pro SQL Server se liší od mobility licencí ve dvou klíčových oblastech:

- Poskytuje ekonomické výhody pro přesun vysoce virtualizovaných úloh do Azure. Zákazníci edice SQL Server Enterprise můžou získat čtyři jádra v Azure v rámci Pro obecné účely SKU pro každé jádro, které vlastní místní pro vysoce virtualizované aplikace. Mobilita licencí neumožňuje žádné speciální náklady na přesun virtualizovaných úloh do cloudu.
- Poskytuje pro PaaS cíl v Azure (SQL Database spravované instance), který je vysoce kompatibilní s SQL Serverem v místním prostředí.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jaká jsou specifická práva Zvýhodněné hybridní využití Azure pro SQL Server?

Zákazníci SQL Database mají pro SQL Server k Zvýhodněné hybridní využití Azure přidružená tato práva:

|Využití licencí|K čemu Zvýhodněné hybridní využití Azure SQL Server získat přístup?|
|---|---|
|Zákazníci se základními edicemi SQL Server Enterprise s SA|<li>Může platit základní sazba pro Pro obecné účely nebo Pro důležité obchodní informace SKLADOVOU položku.</li><br><li>1 jádro v místním prostředí = 4 jádra v Pro obecné účely SKU</li><br><li>1 jádro v místním prostředí = 1 jádro v Pro důležité obchodní informace SKU</li>|
|Zákazníci se základními edicemi SQL Server Standard s SA|<li>Může platit základní sazba jenom pro Pro obecné účely SKU.</li><br><li>1 jádro v místním prostředí = 1 jádro v Pro obecné účely SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Spolupráce s technickým týmem SQL Serveru

- [Výměna z stacku DBA](https://dba.stackexchange.com/questions/tagged/sql-server): Zeptejte se otázek správy databáze.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Zeptejte se na vývojové otázky.
- [Fóra MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Zeptejte se na technické dotazy.
- [Zpětná vazba](https://aka.ms/sqlfeedback): Nahlásit chyby a funkci žádosti.
- [Reddit](https://www.reddit.com/r/SQLServer/): Prodiskutujte SQL Server.

## <a name="next-steps"></a>Další postup

- Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/sql-database/) pro porovnání nákladů a kalkulačky týkající se jednotlivých databází a elastických fondů.
- Pokud chcete začít, podívejte se na tyto rychlé starty:

  - [Vytvoření databáze SQL na webu Azure Portal](sql-database-single-database-get-started.md)  
  - [Vytvoření databáze SQL pomocí Azure CLI](sql-database-get-started-cli.md)
  - [Vytvoření databáze SQL pomocí PowerShellu](sql-database-get-started-powershell.md)

- Řadu ukázek v Azure CLI a PowerShellu najdete tady:
  - [Ukázky v Azure CLI pro službu SQL Database](sql-database-cli-samples.md)
  - [Ukázky v Azure PowerShellu pro službu SQL Database](sql-database-powershell-samples.md)

- Informace o nových funkcích, které jsou ohlášené, najdete v tématu [plán Azure pro SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Podívejte se na [blog Azure SQL Database](https://azure.microsoft.com/blog/topics/database), kde SQL Server členové týmu produktů o SQL Database novinek a funkcích.

