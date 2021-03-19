---
title: Co je služba Azure SQL Database?
description: 'Seznámení s SQL Database: technické podrobnosti a možnosti systému pro správu relačních databází Microsoft (RDBMS) v cloudu.'
keywords: představení sql,úvod do sql,co je sql database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: overview
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 65710ae40a971d214068f0e2686f78fb994a967e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601376"
---
# <a name="what-is-azure-sql-database"></a>Co je Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL je plně spravovaný databázový stroj typu PaaS (platforma jako služba), který zpracovává většinu funkcí správy databází, jako je upgrade, opravy, zálohování a monitorování, a to bez zásahu uživatele. Azure SQL Database vždy běží na nejnovější stabilní verzi databázového stroje SQL Serveru a opraveného operačního systému s 99,99% dostupností. Funkce PaaS integrované v Azure SQL Database vám umožňují zaměřit se na činnosti správy a optimalizace databáze pro danou doménu, které jsou pro vaši firmu klíčové.

Pomocí Azure SQL Database můžete vytvořit vysoce dostupnou a výkonnou vrstvu datového úložiště pro aplikace a řešení v Azure. SQL Database může být správnou volbou pro celou řadu moderních cloudových aplikací, protože umožňuje zpracovat jak relační, tak i [nerelační struktury](../multi-model-features.md), jako jsou grafy, JSON, prostorová data a XML.

Azure SQL Database vychází z nejnovější stabilní verze [databázového stroje Microsoft SQL Server](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json). Můžete používat funkce pokročilého zpracování dotazů, jako jsou [vysoce výkonné technologie v paměti](../in-memory-oltp-overview.md) a [Inteligentní zpracování dotazů](/sql/relational-databases/performance/intelligent-query-processing?toc=%2fazure%2fsql-database%2ftoc.json). Nejnovější funkce SQL Serveru se dokonce vydávají nejprve v SQL Database a až poté v samotném SQL Serveru. Tyto funkce otestované na milionech databází se k vám tak dostanou bez nutnosti vynaložení dalších nákladů na opravy a upgrade. 

SQL Database umožňuje snadno definovat a škálovat výkon v rámci dvou různých nákupních modelů: [nákupní model založený na Vcore](service-tiers-vcore.md) a [nákupní model založený na DTU](service-tiers-dtu.md). SQL Database je plně spravovaná služba s integrovanou vysokou dostupností, zálohováním a dalšími běžnými operacemi údržby. Společnost Microsoft zpracovává všechny opravy a aktualizace kódu SQL a operačního systému. Správou základní infrastruktury se tedy nemusíte zabývat.

Pokud s Azure SQL Database začínáte, přečtěte si video s *přehledem Azure SQL Database* z naší podrobných [grafických řad Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

> [!TIP]
> Jak můžeme Azure SQL lépe využít? [Proveďte průzkum](https://microsoft.qualtrics.com/jfe/form/SV_ePOznHhP4gDKfGu?channel=456).

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Database poskytuje pro databázi následující možnosti nasazení:

- [Samostatná databáze](single-database-overview.md) představuje plně spravovanou izolovanou databázi. Tuto možnost můžete použít, pokud máte moderní cloudové aplikace a mikroslužby, které potřebují jeden spolehlivý zdroj dat. Jedna databáze je podobná databázi s [omezením](/sql/relational-databases/databases/contained-databases?toc=%2fazure%2fsql-database%2ftoc.json) v [SQL Server databázovém stroji](/sql/sql-server/sql-server-technical-documentation?toc=%2fazure%2fsql-database%2ftoc.json).
- [Elastický fond](elastic-pool-overview.md) je kolekce izolovaných [databází](single-database-overview.md) se sdílenou sadou prostředků, jako je například procesor nebo paměť. Jednotlivé databáze lze přesunout do elastického fondu nebo z něj.

> [!IMPORTANT]
> Pro pochopení rozdílů mezi funkcemi SQL Database a SQL Server a rozdíly mezi různými možnostmi Azure SQL Database najdete v části [SQL Database funkce](features-comparison.md).

SQL Database poskytuje předvídatelný výkon s více typy prostředků, úrovněmi služeb a výpočetními velikostmi. Poskytuje dynamickou škálovatelnost bez výpadků, integrované inteligentní optimalizace, globální škálovatelnost a dostupnost a rozšířené možnosti zabezpečení. Tyto možnosti vám umožní soustředit se na rychlý vývoj aplikací a zrychlení času na uvedení na trh, nikoli na správu virtuálních počítačů a infrastruktury. SQL Database je v současnosti v 38 datacentrech po celém světě, takže můžete svou databázi běžet v datovém centru blízko vás.

## <a name="scalable-performance-and-pools"></a>Škálovatelnost výkonu a fondy

Můžete definovat množství přiřazených prostředků. 
- U izolovaných databází je každá databáze izolovaná od ostatních a je přenosná. Každá z nich má vlastní garantované množství výpočetních, paměťových a úložných prostředků. Množství prostředků přiřazených k databázi je vyhrazeno pro tuto databázi a není sdíleno s ostatními databázemi v Azure. Je možné dynamicky [škálovat samostatné databázové prostředky](single-database-scale.md) . Možnost jediná databáze nabízí různé výpočetní prostředky, paměť a prostředky úložiště pro různé potřeby. Můžete například získat 1 až 80 virtuální jádra nebo 32 GB až 4 TB. [Úroveň služby škálování](service-tier-hyperscale.md) pro izolované databáze umožňuje škálování na 100 TB a možnosti rychlého zálohování a obnovení.
- U elastických fondů můžete přiřadit prostředky, které jsou sdíleny všemi databázemi ve fondu. Můžete vytvořit novou databázi nebo přesunout existující izolované databáze do fondu zdrojů, aby bylo možné maximalizovat využívání prostředků a ušetřit peníze. Tato možnost vám také umožní dynamicky [škálovat prostředky elastického fondu](elastic-pool-scale.md) směrem nahoru a dolů.

Svou první aplikaci můžete vytvořit na malé a omezené databázi s nízkými náklady měsíčně na úrovni služby pro obecné účely. Svou vrstvu služby pak můžete kdykoli nebo programově změnit na kritickou úroveň služeb, aby splňovala požadavky vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte jenom za prostředky, které potřebujete, když je potřebujete.

*Dynamická škálovatelnost* se liší od *automatického škálování*. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů. Možnost jediná databáze podporuje ruční dynamickou škálovatelnost, ale ne automatické škálování. Pokud chcete automatizovanější prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází. Další možností je použít skripty, které mohou přispět k automatizaci škálovatelnosti pro izolovanou databázi. Příklad najdete v tématu [použití PowerShellu pro monitorování a škálování izolované databáze](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Nákupní modely

SQL Database nabízí následující modely nákupu:
- [Nákupní model založený na vCoreech](service-tiers-vcore.md) umožňuje zvolit počet virtuální jádra, velikost paměti a velikost a rychlost úložiště. Nákupní model založený na vCoreech vám také umožní použít [Zvýhodněné hybridní využití Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) k získání úspory nákladů. Další informace o Zvýhodněné hybridní využití Azure najdete v části Nejčastější dotazy dále v tomto článku.
- [Nákupní model založený na DTU](service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-výstupních prostředků ve třech úrovních služeb, aby bylo možné podporovat světlo náročné databázové úlohy. Výpočetní velikosti v rámci jednotlivých vrstev poskytují různé kombinace těchto prostředků, do kterých můžete přidat další prostředky úložiště.
- [Model bez serveru](serverless-tier-overview.md) automaticky škáluje výpočetní výkon na základě požadavků na zatížení a fakturuje se za množství výpočetní služby za sekundu. Výpočetní vrstva bez serveru taky automaticky pozastaví databáze během neaktivních období, kdy se účtuje jenom úložiště, a automaticky obnoví databáze, když se vrátí aktivita.

### <a name="service-tiers"></a>Úrovně služby

Azure SQL Database nabízí tři úrovně služeb, které jsou navrženy pro různé typy aplikací:
- [Pro obecné účely/standardní](service-tier-general-purpose.md) úroveň služeb navržená pro běžné úlohy. Nabízí možnosti vyrovnaných výpočetních a úložných operací orientovaných na rozpočet.
- Úroveň služby [pro důležité obchodní informace/Premium](service-tier-business-critical.md) navržená pro aplikace OLTP s vysokou přenosovou rychlostí a v/v operace s nejnižší latencí. Nabízí nejvyšší odolnost proti chybám pomocí několika izolovaných replik.
- Úroveň služby s velkým [měřítkem](service-tier-hyperscale.md) navržená pro velmi rozsáhlou databázi OLTP a schopnost automatického škálování úložiště a škálování výpočetních prostředků na tekutiny.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastické fondy pro maximalizaci využití prostředků

Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Nepředvídatelné vzorce používání můžou mít těžkou správu nákladů a obchodního modelu. [Elastické fondy](elastic-pool-overview.md) jsou navržené k řešení tohoto problému. Prostředky výkonu můžete přidělit fondu, nikoli k jednotlivým databázím. Platíte za souhrnné prostředky výkonu fondu, a ne pro výkon izolovaných databází.

   ![Obrázek zobrazující elastické fondy v edicích Basic, Standard a Premium](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

U elastických fondů se nemusíte soustředit na výkon databáze v provozu nahoru a dolů jako poptávka po kolísání prostředků. Databáze ve fondu spotřebovávají prostředky výkonu elastického fondu podle potřeby. Databáze ve fondu spotřebovávají, ale nepřekračují omezení fondu, takže vaše náklady budou předvídatelné i v případě, že využití v jednotlivých databázích nikoliv.

[Do fondu můžete přidávat a odebírat databáze a](elastic-pool-overview.md)škálovat aplikace z několik databází na tisíce, a to vše v rámci rozpočtu, který ovládáte. Můžete také řídit minimální a maximální prostředky, které jsou k dispozici pro databáze ve fondu, aby se zajistilo, že žádná databáze ve fondu nebude využívat všechny prostředky fondu a že každá databáze ve fondu má zaručené minimální množství prostředků. Další informace o návrhových vzorech pro aplikace typu software jako služba (SaaS), které používají elastické fondy, najdete v tématu [vzory návrhu pro víceklientské aplikace SaaS s SQL Database](saas-tenancy-app-design-patterns.md).

Skripty vám můžou pomoct s monitorováním a škálováním elastických fondů. Příklad najdete [v tématu použití PowerShellu pro monitorování a škálování elastického fondu v Azure SQL Database](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Kombinace izolovaných databází s databázemi ve fondu

Můžete blendovat izolované databáze s elastickými fondy a měnit úrovně služeb izolovaných databází a elastických fondů, aby se mohly přizpůsobit vaší situaci. Můžete také kombinovat a porovnat další služby Azure s SQL Database, abyste splnili Vaše jedinečné požadavky na návrh aplikace, náklady a efektivitu zdrojů a odemkli nové obchodní příležitosti.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Rozsáhlé monitorování a možnosti upozorňování

Azure SQL Database poskytuje pokročilé funkce monitorování a řešení potíží, které vám pomůžou získat hlubší přehled o charakteristikách úloh. Mezi tyto funkce a nástroje patří:
 - Integrované možnosti monitorování poskytované nejnovější verzí nástroje SQL Server Database Engine. Umožňují vám najít přehledy výkonu v reálném čase. 
 - PaaS možnosti monitorování poskytované Azure, které umožňují monitorovat a řešit potíže s velkým počtem instancí databáze.

[Úložiště dotazů](/sql/relational-databases/performance/best-practice-with-the-query-store), integrovaná funkce monitorování SQL Server, zaznamenává výkon dotazů v reálném čase a umožňuje identifikovat potenciální problémy s výkonem a nejčastější uživatele prostředků. Automatické ladění a doporučení poskytují rady ohledně dotazů s navráceným výkonem a chybějícími nebo duplikovanými indexy. Automatické ladění v SQL Database umožňuje buď ručně použít skripty, které můžou problémy vyřešit, nebo nechat tuto opravu SQL Database použít. SQL Database může také otestovat a ověřit, zda oprava poskytuje určitou výhodu, a zachovat nebo vrátit změny v závislosti na výsledku. Kromě možností úložiště dotazů a automatického ladění můžete ke sledování výkonu úloh používat standardní [zobrazení dynamické správy a XEvent](monitoring-with-dmvs.md) .

Azure poskytuje [integrované nástroje pro monitorování výkonu](performance-guidance.md) a [upozorňování](alerts-insights-configure-portal.md) v kombinaci s hodnocením výkonu, které vám umožní monitorovat stav tisíců databází. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity na základě aktuálních nebo plánovaných potřeb výkonu. Kromě toho SQL Database možné [vygenerovat metriky a protokoly prostředků](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) pro snazší monitorování. SQL Database můžete nakonfigurovat pro ukládání využití prostředků, pracovních procesů, relací a možností připojení do jednoho z těchto prostředků Azure:

- **Azure Storage:** Pro archivaci obrovských objemů telemetrických dat za nízkou cenu.
- **Azure Event Hubs**: pro integraci SQL Database telemetrie s vlastními řešeními monitorování nebo aktivními kanály.
- **Protokoly Azure monitor**: pro integrované řešení monitorování s využitím sestav, upozorňování a zmírnění možností.

![Diagram architektury monitorování Azure](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Azure SQL Database umožní vašemu podniku pokračovat v práci během přerušení. V tradičním prostředí SQL Server máte obvykle nastavené aspoň dva počítače místně. Tyto počítače mají přesnou a synchronně udržovanou kopii dat, která se mají chránit před selháním jednoho počítače nebo součásti. Toto prostředí poskytuje vysokou dostupnost, ale nechrání proti přirozené havárii, které zničí vaše datové centrum.

Zotavení po havárii předpokládá, že je závažná událost geograficky lokalizovaná, aby mohla mít jiný počítač nebo sadu počítačů s kopií vašich dat daleko pryč. V SQL Server můžete k získání této funkce použít skupiny dostupnosti Always On spuštěné v asynchronním režimu. Lidé často nechtějí čekat na replikaci, která je daleko před potvrzením transakce, takže při neplánovaných převzetí služeb při selhání může dojít ke ztrátě dat.

Databáze na úrovních služeb Premium a Pro důležité obchodní informace již [fungují podobně jako](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability) synchronizace skupiny dostupnosti. Databáze v nižších úrovních služeb poskytují redundanci prostřednictvím úložiště pomocí jiného, [ale ekvivalentního mechanismu](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-locally-redundant-availability). Vestavěná logika pomáhá chránit proti selhání jednoho počítače. Funkce aktivní geografické replikace vám dává možnost chránit před haváriemi, kdy je zničena celá oblast.

Zóny dostupnosti Azure se snaží chránit před výpadkem jednoho datového centra v rámci jedné oblasti. Pomáhá chránit před ztrátou energie nebo sítě až po sestavení. V SQL Database umístíte různé repliky do různých zón dostupnosti (ve skutečnosti i v různých budovách).

Smlouva o úrovni služeb [(SLA)](https://azure.microsoft.com/support/legal/sla/) Azure, která využívá globální síť datacenter spravovaných Microsoftem, pomáhá zajistit, aby vaše aplikace běžela 24/7. Platforma Azure plně spravuje každou databázi a zaručuje žádnou ztrátu dat a vysoké procento dostupnosti dat. Azure automaticky zpracovává opravy, zálohy, replikaci, detekci selhání, základní potenciální hardware, software nebo síť, nasazování oprav chyb, převzetí služeb při selhání, upgrady databáze a další úlohy údržby. Dostupnosti úrovně Standard se dosahuje oddělením výpočetní a úložné vrstvy. Dostupnost Premium se dosahuje integrací výpočetních prostředků a úložiště v jednom uzlu pro výkon a následnou implementací technologie podobně jako u skupin dostupnosti Always On. Úplnou diskuzi o funkcích Azure SQL Database vysoké dostupnosti najdete v článku [dostupnost SQL Database](high-availability-sla.md). 

Kromě toho SQL Database poskytuje integrovanou funkci pro [provozní kontinuitu a globální škálovatelnost](business-continuity-high-availability-disaster-recover-hadr-overview.md) . Tady jsou některé z nich:

- [Automatické zálohování](automated-backups-overview.md):

  SQL Database automaticky provádí zálohování databází na základě úplného, rozdílového a transakčního protokolu, aby vám umožnila obnovení do libovolného bodu v čase. Pro izolované databáze a databáze ve fondu můžete nakonfigurovat SQL Database pro ukládání úplných záloh databáze do Azure Storage pro dlouhodobé uchovávání záloh. U spravovaných instancí můžete pro dlouhodobé uchovávání záloh provádět i zálohy jen pro kopírování.

- [Obnovení k bodu v čase](recovery-using-backups.md):

  Všechny možnosti nasazení SQL Database podporují obnovení do libovolného bodu v čase v rámci automatické doby uchovávání záloh pro všechny databáze.
- [Aktivní geografická replikace](active-geo-replication-overview.md):

  Možnosti databáze a databáze ve fondu umožňují nakonfigurovat až čtyři sekundární databáze pro čtení ve stejném nebo globálně distribuovaném datacentru Azure. Například pokud máte aplikaci SaaS s databází katalogu, která má velký objem souběžných transakcí jen pro čtení, použijte aktivní geografickou replikaci k povolení globálního škálování pro čtení. Tím dojde k odebrání kritických bodů na primárních počítačích, které jsou způsobeny čtením zatížení. U spravovaných instancí použijte skupiny s automatickým převzetím služeb při selhání.
- [Skupiny automatického převzetí služeb při selhání](auto-failover-group-overview.md):

  Všechny možnosti nasazení SQL Database umožňují používat skupiny převzetí služeb při selhání pro zajištění vysoké dostupnosti a vyrovnávání zatížení v globálním měřítku. Zahrnuje transparentní geografickou replikaci a převzetí služeb při selhání velkých sad databází, elastických fondů a spravovaných instancí. Skupiny převzetí služeb při selhání umožňují vytváření globálně distribuovaných aplikací SaaS s minimálními nároky na správu. Tím se zachová veškerá komplexní monitorování, směrování a orchestrace převzetí služeb při selhání SQL Database.
- [Redundantní databáze v zóně](high-availability-sla.md):

  SQL Database umožňuje zřídit databáze úrovně Premium nebo Pro důležité obchodní informace nebo elastické fondy napříč několika zónami dostupnosti. Vzhledem k tomu, že tyto databáze a elastické fondy mají více redundantních replik pro vysokou dostupnost, jejich umístění do více zón dostupnosti poskytuje vyšší odolnost. To zahrnuje možnost obnovování automaticky z chyb škálování Datacenter bez ztráty dat.

## <a name="built-in-intelligence"></a>Integrované inteligentní funkce

Díky SQL Database získáte integrované inteligentní funkce, které vám pomůžou výrazně snížit náklady na spouštění a správu databází a které maximalizují výkon a zabezpečení vaší aplikace. Provoz milionům úloh zákazníků po hodinách SQL Database shromažďuje a zpracovává obrovské množství dat telemetrie a zároveň plně respektuje ochranu osobních údajů zákazníků. Různé algoritmy průběžně vyhodnocují data telemetrie, aby se služba mohla učit a přizpůsobovat k vaší aplikaci.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatické monitorování a optimalizace výkonu

SQL Database nabízí podrobné přehledy dotazů, které potřebujete monitorovat. SQL Database se dozvíte o vzorech databáze a umožňuje vám přizpůsobit schéma databáze pro vaše úlohy. SQL Database poskytuje [doporučení pro optimalizaci výkonu](database-advisor-implement-performance-recommendations.md), kde můžete zkontrolovat akce optimalizace a použít je.

Nepřetržité monitorování databáze je však pevně a zdlouhavá úloha, zejména při práci s mnoha databázemi. Tato úloha [Intelligent Insights](intelligent-insights-overview.md) za vás automaticky monitoruje výkon SQL Database ve velkém měřítku. Informuje vás o problémech se snížením výkonu, identifikuje hlavní příčinu každého problému a poskytuje doporučení pro zlepšení výkonu, pokud je to možné.

Správa obrovského počtu databází může být nemožná, a to i u všech dostupných nástrojů a sestav, které SQL Database a Azure poskytuje. Místo ručního monitorování a optimalizace databáze můžete zvážit delegování některých akcí monitorování a optimalizace SQL Database pomocí [automatického ladění](automatic-tuning-overview.md). SQL Database automaticky aplikuje doporučení, testy a ověří každé z jeho akcí optimalizace, aby se zajistilo, že bude výkon neustále zlepšovat. Tímto způsobem se SQL Database automaticky přizpůsobí vašim úlohám řízeným a bezpečným způsobem. Automatické ladění znamená, že se výkon vaší databáze pečlivě monitoruje a porovnává před a po každé akci optimalizace. Pokud se výkon nezvyšuje, akce optimalizace se vrátí zpět.

Mnohé z našich partnerů, kteří spouštějí [SaaS aplikace SQL Database pro více tenantů](saas-tenancy-app-design-patterns.md) , se spoléhají na automatické ladění výkonu, abyste měli jistotu, že jejich aplikace mají vždycky stabilní a předvídatelný výkon. Tato funkce pro ně výrazně snižuje riziko incidentu výkonu uprostřed noci. Kromě toho, vzhledem k tomu, že součástí jejich základu pro zákazníky je také SQL Server, používají stejná doporučení pro indexování poskytovaná SQL Database, která svým SQL Server zákazníkům pomohou.

[V SQL Database jsou k dispozici](automatic-tuning-overview.md)dvě další aspekty automatického ladění:

- **Automatická správa indexů:** Identifikuje indexy, které by se měly do databáze přidat nebo z ní naopak odebrat.
- **Automatická oprava plánu**: identifikuje problematické plány a opravuje problémy s výkonem plánu SQL.

### <a name="adaptive-query-processing"></a>Adaptivní zpracování dotazů

Můžete použít [adaptivní zpracování dotazů](/sql/relational-databases/performance/intelligent-query-processing), včetně prokládaného spouštění funkcí s více příkazy vracející tabulky, zpětné vazby přidělení paměti v režimu dávky a adaptivních spojení v režimu Batch. Každá z těchto funkcí adaptivního zpracování dotazů používá podobné techniky učení a přizpůsobení, což pomáhá lépe řešit problémy s výkonem související s historickými problémy s optimalizací dotazů.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

SQL Database nabízí řadu [integrovaných funkcí zabezpečení a dodržování předpisů](../../active-directory/identity-protection/concept-identity-protection-security-overview.md), které vaší aplikaci pomůžou se splněním nejrůznějších požadavků na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Microsoft má pro určitý počet standardů dodržování předpisů certifikovaný Azure SQL Database (všechny možnosti nasazení). Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

### <a name="advance-threat-protection"></a>Ochrana před hrozbami

Azure Defender pro SQL je jednotný balíček pro pokročilé funkce zabezpečení SQL. Zahrnuje funkce pro správu ohrožení zabezpečení databáze a zjišťování aktivit neobvyklé, které mohou indikovat hrozbu pro vaši databázi. Poskytuje jedno umístění pro povolení a správu těchto schopností.

- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md):

  Tato služba může zjišťovat, sledovat a pomáhat při nápravě potenciálních ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Detekce hrozeb](threat-detection-configure.md):

  Tato funkce detekuje aktivity neobvyklé, které naznačují neobvyklé a potenciálně škodlivé pokusy o přístup k databázi nebo jejím zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Upozornění detekce hrozeb obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

### <a name="auditing-for-compliance-and-security"></a>Auditování dodržování předpisů a zabezpečení

[Auditování](../../azure-sql/database/auditing-overview.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu služby Azure Storage. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

### <a name="data-encryption"></a>Šifrování dat

SQL Database pomáhá zabezpečit vaše data tím, že poskytuje šifrování. Pro data v pohybu používá [zabezpečení transportní vrstvy](https://support.microsoft.com/kb/3135244). V případě neaktivních dat používá [transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Pro data, která se používají, používá [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Zjišťování a klasifikace dat

[Zjišťování a klasifikace dat](data-discovery-and-classification-overview.md) poskytuje možnosti integrované v Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Poskytuje přehled o stavu klasifikace databáze a sleduje přístup k citlivým datům v rámci databáze a mimo její ohraničení.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL Database umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoft pomocí [integrace s Azure Active Directory](authentication-aad-overview.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje službu [Multi-Factor Authentication](authentication-mfa-ssms-overview.md) pro zvýšení zabezpečení dat a aplikací při podpoře jednotného procesu přihlašování.

## <a name="easy-to-use-tools"></a>Snadno použitelné nástroje

SQL Database zjednodušuje a zefektivňuje vytváření a správu aplikací. SQL Database vám umožňuje soustředit se na to, v čem jste nejlepší – na vytváření skvělých aplikací. Můžete spravovat a vyvíjet v SQL Database pomocí nástrojů a dovedností, které už máte.

|Nástroj|Popis|
|:---|:---|
|[Azure Portal](https://portal.azure.com/)|Webová aplikace pro správu všech služeb Azure.|
|[Azure Data Studio](/sql/azure-data-studio/)|Databázový Nástroj pro různé platformy, který běží na Windows, macOS a Linux.|
|[SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)|Bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Server po SQL Database.|
|[Nástroje pro SQL Server dat v aplikaci Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)|Bezplatná klientská aplikace ke stažení pro vývoj SQL Server relačních databází, databází v Azure SQL Database, balíčků integračních služeb, Analysis Services datových modelů a sestav služby Reporting Services.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Bezplatný a Open Source Editor kódu pro Windows, macOS a Linux. Podporuje rozšíření, včetně [rozšíření MSSQL](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a Azure Azure synapse Analytics.|

SQL Database podporuje vytváření aplikací pomocí jazyků Python, Java, Node.js, PHP, Ruby a .NET v systémech macOS, Linux a Windows. SQL Database podporuje stejné [knihovny připojení](connect-query-content-reference-guide.md#libraries) jako SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database nejčastějších dotazech

### <a name="can-i-control-when-patching-downtime-occurs"></a>Můžu řídit, kdy dochází k výpadkům při opravování?

No. Pokud ve své aplikaci použijete [logiku opakování](develop-overview.md#resiliency) , nemusíte mít dopad na opravy. Další informace najdete v tématu [plánování událostí údržby Azure v Azure SQL Database](planned-maintenance.md).



## <a name="engage-with-the-sql-server-engineering-team"></a>Spolupráce s technickým týmem SQL Serveru

- [Výměna z stacku DBA](https://dba.stackexchange.com/questions/tagged/sql-server): Položte otázky týkající se správy databáze.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Položte otázky pro vývoj.
- [Microsoft Q&Stránka s otázkou](/answers/topics/azure-synapse-analytics.html): Položte technické dotazy.
- [Feedback](https://aka.ms/sqlfeedback): nahlásit chyby a vyžádat funkce.
- [Reddit](https://www.reddit.com/r/SQLServer/): diskuze o SQL Server.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [stránku s cenami](https://azure.microsoft.com/pricing/details/sql-database/) pro porovnání nákladů a kalkulačky týkající se jednotlivých databází a elastických fondů.
- Pokud chcete začít, podívejte se na tyto rychlé starty:

  - [Vytvořit databázi na webu Azure Portal](single-database-create-quickstart.md)  
  - [Vytvoření databáze pomocí Azure CLI](az-cli-script-samples-content-guide.md)
  - [Vytvoření databáze pomocí prostředí PowerShell](powershell-script-content-guide.md)

- Řadu ukázek v Azure CLI a PowerShellu najdete tady:
  - [Ukázky v Azure CLI pro službu SQL Database](az-cli-script-samples-content-guide.md)
  - [Ukázky v Azure PowerShellu pro službu SQL Database](powershell-script-content-guide.md)

- Informace o nových funkcích, které jsou ohlášené, najdete v tématu [plán Azure pro SQL Database](https://azure.microsoft.com/roadmap/?category=databases).
- Podívejte se na [blog Azure SQL Database](https://azure.microsoft.com/blog/topics/database), kde SQL Server členové týmu produktů o SQL Database novinek a funkcích.