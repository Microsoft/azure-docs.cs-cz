---
title: Co je služba Azure SQL Database?
description: 'Získejte úvod do databáze SQL: technické podrobnosti a možnosti systému správy relačních databází (RDBMS) společnosti Microsoft v cloudu.'
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
ms.openlocfilehash: 0d50ddbbeeaed48c14d07c42588efcbb20bb7d79
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411156"
---
# <a name="what-is-the-azure-sql-database-service"></a>Co je služba Azure SQL Database?

Azure SQL Database je plně spravovaný databázový stroj PaaS (Platform as a Service, platforma jako služba), který zpracovává většinu funkcí správy databáze, jako jsou upgrady, opravy, zálohování a monitorování bez zásahu uživatele. Azure SQL Database vždycky běží na nejnovější stabilní verzi databázového stroje SQL Serveru a opraveném operačním systému s 99,99% dostupností. PaaS funkce, které jsou integrované do databáze Azure SQL umožňuje zaměřit se na správu databáze specifické pro doménu a optimalizace činnosti, které jsou důležité pro vaše podnikání.

S Azure SQL Database můžete vytvořit vysoce dostupnou a vysoce výkonnou vrstvu úložiště dat pro aplikace a řešení v Azure. SQL Database může být tou správnou volbou pro celou řadu moderních cloudových aplikací, protože umožňuje zpracovávat relační data i [nerelační struktury](sql-database-multi-model-features.md), jako jsou grafy, JSON, prostorové a XML.

Je založen na nejnovější stabilní verzi [databázového stroje Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Můžete použít pokročilé funkce zpracování dotazů, jako jsou [vysoce výkonné technologie v paměti](sql-database-in-memory.md) a inteligentní zpracování [dotazů](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). Ve skutečnosti nejnovější možnosti SQL Server jsou vydány nejprve sql database a pak sql server sám. Získáte nejnovější funkce SQL Serveru bez režie pro opravy nebo upgrade, testovány napříč miliony databází. 

SQL Database umožňuje snadno definovat a škálovat výkon v rámci dvou různých nákupních modelů: [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) a nákupní model založený na [DTU](sql-database-service-tiers-dtu.md). SQL Database je plně spravovaná služba, která má integrovanou vysokou dostupnost, zálohy a další běžné operace údržby. Společnost Microsoft zpracovává všechny opravy a aktualizace kódu SQL a operačního systému. Není třeba spravovat základní infrastrukturu.

> [!NOTE]
> Příslušné termíny a jejich definice naleznete v [glosáři termínů databáze SQL](sql-database-glossary-terms.md).

## <a name="deployment-models"></a>Modely nasazení

Azure SQL Database poskytuje následující možnosti nasazení databáze Azure SQL:

![Diagram možností nasazení](./media/sql-database-technical-overview/deployment-options.png)

- [Jedna databáze](sql-database-single-database.md) představuje plně spravovanou izolovanou databázi. Tuto možnost můžete použít, pokud máte moderní cloudové aplikace a mikroslužby, které potřebují jeden spolehlivý zdroj dat. Jedna databáze je podobná obsažené databázi v [databázovém](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) [stroji serveru Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Spravovaná instance](sql-database-managed-instance.md) je plně spravovanou instancí [databázového stroje Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Obsahuje sadu databází, které lze použít společně. Tuto možnost použijte pro snadnou migraci místních databází SQL Serveru do cloudu Azure a pro aplikace, které potřebují používat databázové funkce, které poskytuje SQL Server Database Engine.
- [Elastický fond](sql-database-elastic-pool.md) je kolekce [jednotlivých databází](sql-database-single-database.md) se sdílenou sadou prostředků, jako je například procesor nebo paměť. Jednotlivé databáze lze přesunout do a z elastického fondu.

> [!IMPORTANT]
> Informace o rozdílech mezi funkcemi mezi sql database a sql serverem a také o rozdílech mezi různými možnostmi nasazení databáze Azure SQL database najdete v [tématu funkce databáze SQL](sql-database-features.md).

SQL Database poskytuje předvídatelný výkon s více typy prostředků, úrovněmi služeb a výpočetními velikostmi. Poskytuje dynamickou škálovatelnost bez prostojů, integrovanou inteligentní optimalizaci, globální škálovatelnost a dostupnost a pokročilé možnosti zabezpečení. Tyto funkce vám umožní zaměřit se na rychlý vývoj aplikací a urychlení doby uvedení na trh, nikoli na správu virtuálních počítačů a infrastruktury. Služba SQL Database je aktuálně v 38 datových centrech po celém světě, takže můžete spouštět databázi v datovém centru ve vašem okolí.

## <a name="scalable-performance-and-pools"></a>Škálovatelnost výkonu a fondy

Můžete definovat množství přiřazených zdrojů. 
- S jednotlivými databázemi je každá databáze izolována od ostatních a je přenosná. Každý z nich má své vlastní garantované množství výpočetních prostředků, paměti a prostředků úložiště. Množství prostředků přiřazených k databázi je vyhrazeno pro tuto databázi a není sdíleno s jinými databázemi v Azure. Můžete dynamicky [škálovat prostředky jedné databáze](sql-database-single-database-scale.md) nahoru a dolů. Možnost jedné databáze poskytuje různé výpočetní prostředky, paměť a prostředky úložiště pro různé potřeby. Můžete například získat 1 až 80 virtuálních jader nebo 32 GB až 4 TB. [Úroveň služby hyperškálování](sql-database-service-tier-hyperscale.md) pro jednu databázi umožňuje škálovat na 100 TB s možností rychlého zálohování a obnovení.
- Pomocí elastických fondů můžete přiřadit prostředky, které jsou sdíleny všemi databázemi ve fondu. Můžete vytvořit novou databázi nebo přesunout existující jednotlivé databáze do fondu zdrojů, abyste maximalizovali využití prostředků a ušetřili peníze. Tato možnost také umožňuje dynamicky [škálovat prostředky elastického fondu](sql-database-elastic-pool-scale.md) nahoru a dolů.
- U spravovaných instancí je každá spravovaná instance izolována od jiných instancí se zaručenými prostředky. V rámci spravované instance databáze instancí sdílejí sadu prostředků. Prostředky spravované instance můžete dynamicky [škálovat](sql-database-managed-instance-resource-limits.md) nahoru a dolů.

Svou první aplikaci můžete vytvořit na malé, jediné databázi s nízkými náklady za měsíc ve vrstvě služeb pro obecné účely. Potom můžete změnit jeho úroveň služeb ručně nebo programově kdykoli na úroveň služeb kritické pro podnikání, aby vyhovovaly potřebám vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky. Platíte pouze za zdroje, které potřebujete, když je potřebujete.

*Dynamická škálovatelnost* se liší od *automatického škálování*. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů. Možnost jedné databáze podporuje ruční dynamickou škálovatelnost, ale ne automatické škálování. Pokud chcete automatizovanější prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází. Další možností je použití skriptů, které mohou pomoci automatizovat škálovatelnost pro jednu databázi. Příklad najdete v [tématu Použití Prostředí PowerShell ke sledování a škálování jedné databáze](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Nákupní modely

SQL Database nabízí následující nákupní modely:
- [Nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) umožňuje zvolit počet virtuálních jader, velikost paměti a velikost a rychlost úložiště. Nákupní model založený na virtuálních jádrech také umožňuje používat [hybridní výhody Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) k dosažení úspor nákladů. Další informace o hybridní výhodě Azure najdete v části Nejčastější dotazy dále v tomto článku.
- Nákupní [model založený na DTU](sql-database-service-tiers-dtu.md) nabízí kombinaci výpočetních, paměťových a vstupně-v.I prostředků ve třech úrovních služeb, které podporují náročné až náročné databázové úlohy. Výpočetní velikosti v rámci každé vrstvy poskytují jinou kombinaci těchto prostředků, do kterých můžete přidat další prostředky úložiště.
- [Model bez serveru](sql-database-serverless.md) automaticky škáluje výpočetní prostředky na základě poptávky po pracovním vytížení a účty za množství výpočetních prostředků použitých za sekundu. Výpočetní úroveň bez serveru také automaticky pozastaví databáze během neaktivních období, kdy se účtuje pouze úložiště, a automaticky obnoví databáze, když se vrátí aktivita.

### <a name="service-tiers"></a>Úrovně služby

Azure SQL Database nabízí tři úrovně služeb, které jsou navržené pro různé typy aplikací:
- Úroveň služby [pro obecné účely a standardy](sql-database-service-tier-general-purpose.md) určená pro běžné úlohy. Nabízí nízkopřehledně orientované vyvážené výpočetní a úložné možnosti.
- Úroveň služeb [Business Critical/Premium](sql-database-service-tier-business-critical.md) určená pro aplikace OLTP s vysokou transakční sazbou a nejnižší latencí vstupně-výstupních operací. Nabízí nejvyšší odolnost proti selhání pomocí několika izolovaných replik.
- [Vrstva](sql-database-service-tier-hyperscale.md) služby Hyperscale určená pro velmi rozsáhlou databázi OLTP a možnost automatického škálování úložiště a škálování výpočetních prostředků plynule.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastické fondy pro maximalizaci využití prostředků

Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Nepředvídatelné vzorce využití mohou ztížit správu nákladů a obchodního modelu. [Elastické fondy](sql-database-elastic-pool.md) jsou navržené k řešení tohoto problému. Prostředky výkonu přidělujete do fondu, nikoli do jednotlivé databáze. Platíte za kolektivní prostředky výkonu fondu, nikoli za výkon jedné databáze.

   ![Grafika znázorněná elastické fondy v základních, standardních a prémiových edicích](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

U elastických fondů se nemusíte soustředit na vytáčení výkonu databáze nahoru a dolů, protože poptávka po prostředcích kolísá. Databáze ve fondu spotřebovávají prostředky výkonu elastického fondu podle potřeby. Sdružené databáze spotřebovávají, ale nepřekračují limity fondu, takže vaše náklady zůstanou předvídatelné i v případě, že použití jednotlivých databází není.

Můžete [přidat a odebrat databáze do fondu](sql-database-elastic-pool-manage-portal.md), škálování aplikace z několika databází na tisíce, to vše v rámci rozpočtu, který řídíte. Můžete také řídit minimální a maximální prostředky, které jsou k dispozici pro databáze ve fondu, abyste zajistili, že žádná databáze ve fondu nepoužívá všechny prostředky fondu a že každá sdružená databáze má zaručené minimální množství prostředků. Další informace o návrhových vzorcích pro aplikace softwaru jako služby (SaaS), které používají elastické fondy, naleznete v [tématu Návrhové vzory pro víceklientské aplikace SaaS s databází SQL](sql-database-design-patterns-multi-tenancy-saas-applications.md)Database .

Skripty vám můžou pomoct s monitorováním a škálováním elastických fondů. Příklad najdete v tématu [Použití PowerShellu ke sledování a škálování elastického fondu SQL v Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Spravovaná instance nepodporuje elastické fondy. Spravovaná instance je spíše kolekce databází instancí, které sdílejí prostředky spravované instance.

### <a name="blend-single-databases-with-pooled-databases"></a>Kombinace izolovaných databází s databázemi ve fondu

Můžete prolnout jednotlivé databáze s elastické fondy a změnit úrovně služeb jednotlivých databází a elastických fondů přizpůsobit vaší situaci. Můžete také kombinovat a sladit další služby Azure s databází SQL Database, abyste vyhověli vašim jedinečným potřebám návrhu aplikací, zvýšit efektivitu nákladů a prostředků a odemknout nové obchodní příležitosti.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Rozsáhlé monitorování a možnosti upozorňování

Azure SQL Database poskytuje pokročilé funkce monitorování a řešení potíží, které vám pomůžou získat hlubší přehled o charakteristikách pracovního vytížení. Mezi tyto funkce a nástroje patří:
 - Integrované funkce monitorování poskytované nejnovější verzí databázového stroje SQL Server. Umožňují vám najít přehledy o výkonu v reálném čase. 
 - Možnosti monitorování PaaS poskytované Azure, které vám umožní sledovat a řešit velký počet instancí databáze.

[Úložiště dotazů](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), integrovaná funkce monitorování serveru SQL Server, zaznamenává výkon dotazů v reálném čase a umožňuje identifikovat potenciální problémy s výkonem a hlavní spotřebitele prostředků. Automatické ladění a doporučení poskytují rady týkající se dotazů s regresovaný výkon a chybějící nebo duplicitní indexy. Automatické ladění v databázi SQL umožňuje buď ručně použít skripty, které mohou opravit problémy, nebo nechat SQL Database použít opravu. SQL Database můžete také otestovat a ověřit, že oprava poskytuje některé výhody a zachovat nebo vrátit změnu v závislosti na výsledku. Kromě úložiště dotazů a možností automatického ladění můžete ke sledování výkonu pracovního vytížení použít standardní [zařízení DMV a XEvent.](sql-database-monitoring-with-dmvs.md)

Azure poskytuje integrované nástroje [pro monitorování výkonu](sql-database-performance-guidance.md) a [upozorňování](sql-database-insights-alerts-portal.md) v kombinaci s hodnocením výkonu, které umožňují sledovat stav tisíců databází. Pomocí těchto nástrojů můžete rychle posoudit dopad škálování nahoru nebo dolů na základě aktuálních nebo předpokládaných potřeb výkonu. Kromě toho může SQL Database [generovat metriky a diagnostické protokoly](sql-database-metrics-diag-logging.md) pro snazší monitorování. SQL Database můžete nakonfigurovat pro ukládání využití prostředků, pracovních procesů, relací a možností připojení do jednoho z těchto prostředků Azure:

- **Azure Storage:** Pro archivaci obrovských objemů telemetrických dat za nízkou cenu.
- **Azure Event Hubs:** Pro integraci telemetrie databáze SQL s vlastním řešením monitorování nebo horkými kanály.
- **Protokoly Azure Monitor:** Pro integrované řešení monitorování s vytvářením sestav, upozorňování a zmírnění funkcí.

![Diagram architektury monitorování Azure](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Azure SQL Database umožňuje vaší firmě pokračovat v provozu během přerušení. V tradičním prostředí SQL Server obecně máte alespoň dva počítače místně nastavit. Tyto stroje mají přesné, synchronně udržované kopie dat, které chrání před selháním jednoho stroje nebo součásti. Toto prostředí poskytuje vysokou dostupnost, ale nechrání před přírodní katastrofou ničí vaše datové centrum.

Zotavení po havárii předpokládá, že katastrofická událost je geograficky lokalizována natolik, aby měla jiný počítač nebo sadu počítačů s kopií dat daleko. Na serveru SQL Server můžete tuto funkci získat pomocí skupin y dostupnosti always on spuštěné v asynchronním režimu. Lidé často nechtějí čekat na replikaci tak daleko před potvrzením transakce, takže existuje potenciál pro ztrátu dat, když provedete neplánované převzetí služeb při selhání.

Databáze v úrovních premium a business kritické služby již [provést něco podobného](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) synchronizace skupiny dostupnosti. Databáze v nižších úrovních služeb poskytují redundanci prostřednictvím úložiště pomocí [jiného, ale ekvivalentnímechanismu .](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) Vestavěná logika pomáhá chránit před selháním jednoho počítače. Aktivní funkce geografické replikace umožňuje chránit před havárií, kdy je zničena celá oblast.

Zóny dostupnosti Azure se pokusí chránit před výpadkem jednoho sestavení datového centra v rámci jedné oblasti. Pomáhá chránit před ztrátou energie nebo sítě do budovy. V databázi SQL umístíte různé repliky v různých zónách dostupnosti (různé budovy, efektivně).

Smlouva o úrovni služeb [(SLA)](https://azure.microsoft.com/support/legal/sla/) Azure, založená na globální síti datových center spravovaných Microsoftem, ve skutečnosti pomáhá udržovat vaši aplikaci spuštěnou 24 hodin denně, 7 dní v ruce. Platforma Azure plně spravuje každou databázi a zaručuje žádnou ztrátu dat a vysoké procento dostupnosti dat. Azure automaticky zpracovává opravy, zálohování, replikaci, zjišťování selhání, základní potenciální selhání hardwaru, softwaru nebo sítě, nasazování oprav chyb, převzetí služeb při selhání, upgrady databází a další úlohy údržby. Dostupnosti úrovně Standard se dosahuje oddělením výpočetní a úložné vrstvy. Dostupnosti úrovně Premium je dosaženo integrací výpočetních prostředků a úložiště na jednom uzlu pro výkon a implementací technologie podobné skupinám dostupnosti always on. Úplnou diskusi o možnostech vysoké dostupnosti azure sql database najdete v [tématu dostupnost databáze SQL](sql-database-high-availability.md). 

Kromě toho sql database poskytuje integrované [funkce kontinuity podnikání a globální škálovatelnosti.](sql-database-business-continuity.md) Mezi ně patří:

- [Automatické zálohování](sql-database-automated-backups.md):

  SQL Database automaticky provádí úplné, rozdílové a transakční protokol zálohy databází SQL, které vám umožní obnovit do libovolného bodu v čase. Pro jednotlivé databáze a sdružené databáze můžete nakonfigurovat sql database pro ukládání úplných záloh databází do Úložiště Azure pro dlouhodobé uchovávání záloh. U spravovaných instancí můžete také provádět zálohování pouze pro kopírování pro dlouhodobé uchovávání záloh.

- [Obnovení bodu v čase](sql-database-recovery-using-backups.md):

  Všechny možnosti nasazení databáze SQL podporují obnovení do libovolného bodu v čase v rámci období automatického uchovávání záloh pro libovolnou databázi SQL.
- [Aktivní geografická replikace](sql-database-active-geo-replication.md):

  Možnosti jedné databáze a sdružených databází umožňují konfigurovat až čtyři čitelné sekundární databáze ve stejných nebo globálně distribuovaných datových centrech Azure. Například pokud máte aplikaci SaaS s databází katalogu, který má velký objem souběžných transakcí jen pro čtení, použijte aktivní geografickou replikaci k povolení globálního škálování čtení. Tím se odstraní kritické body na primární, které jsou z důvodu úlohy čtení. Pro spravované instance použijte skupiny s automatickým převzetím služeb při selhání.
- [Skupiny s automatickým převzetím služeb při selhání](sql-database-auto-failover-group.md):

  Všechny možnosti nasazení databáze SQL umožňují použít skupiny převzetí služeb při selhání k povolení vysoké dostupnosti a vyrovnávání zatížení v globálním měřítku. To zahrnuje transparentní geografickou replikaci a převzetí služeb při selhání velkých sad databází, elastických fondů a spravovaných instancí. Skupiny s podporou převzetí služeb při selhání umožňují vytváření globálně distribuovaných aplikací SaaS s minimální režií správy. To ponechává všechny složité monitorování, směrování a převzetí služeb při selhání orchestrace do databáze SQL.
- [Zónově redundantní databáze](sql-database-high-availability.md):

  SQL Database umožňuje zřídit prémiové nebo podnikové kritické databáze nebo elastické fondy napříč několika zónami dostupnosti. Vzhledem k tomu, že tyto databáze a elastické fondy mají více redundantní repliky pro vysokou dostupnost, umístění těchto replik do více zón dostupnosti poskytuje vyšší odolnost. To zahrnuje možnost automaticky obnovit z selhání škálování datového centra, bez ztráty dat.

## <a name="built-in-intelligence"></a>Integrované inteligentní funkce

S SQL Database získáte integrovanou inteligenci, která vám pomůže výrazně snížit náklady na provoz a správu databází a maximalizuje výkon i zabezpečení vaší aplikace. Sql Database nepřetržitě sbíhá miliony zákaznických úloh a shromažďuje a zpracovává obrovské množství telemetrických dat a zároveň plně respektuje soukromí zákazníků. Různé algoritmy průběžně vyhodnocují telemetrická data tak, aby se služba mohla učit a přizpůsobovat se s vaší aplikací.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatické monitorování a optimalizace výkonu

SQL Database nabízí podrobné přehledy dotazů, které potřebujete monitorovat. SQL Database se dozví o vzorcích databáze a umožňuje přizpůsobit schéma databáze úlohy. SQL Database poskytuje [doporučení pro optimalizaci výkonu](sql-database-advisor.md), kde můžete zkontrolovat akce optimalizace a použít je.

Neustálé sledování databáze je však pevný a zdlouhavý úkol, zejména při práci s mnoha databázemi. [Inteligentní přehledy](sql-database-intelligent-insights.md) tuto práci provádí za vás tím, že automaticky sledují výkon databáze SQL ve velkém měřítku. Informuje vás o problémech se snížením výkonu, identifikuje hlavní příčinu každého problému a pokud je to možné, poskytuje doporučení ke zlepšení výkonu.

Správa obrovského počtu databází může být nemožné provést efektivně i se všemi dostupnými nástroji a sestavami, které poskytují SQL Database a Azure. Namísto ručního monitorování a ladění databáze můžete zvážit delegování některých akcí monitorování a ladění do databáze SQL pomocí [automatického ladění](sql-database-automatic-tuning.md). SQL Database automaticky použije doporučení, testy a ověří každou ze svých akcí ladění, aby bylo zajištěno, že se výkon neustále zlepšuje. Tímto způsobem SQL Database automaticky přizpůsobí vaše úlohy řízeným a bezpečným způsobem. Automatické ladění znamená, že výkon databáze je pečlivě sledován a porovnán před a po každé akci ladění. Pokud se výkon nezlepší, akce ladění se vrátí.

Mnoho našich partnerů, kteří spouštějí [víceklientské aplikace SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) nad sql database, se spoléhá na automatické ladění výkonu, aby bylo zajištěno, že jejich aplikace budou mít vždy stabilní a předvídatelný výkon. Tato funkce pro ně výrazně snižuje riziko incidentu výkonu uprostřed noci. Navíc vzhledem k tomu, že část jejich zákaznické základny také používá SQL Server, používají stejná doporučení pro indexování, která poskytuje DATABÁZE SQL, aby pomohli svým zákazníkům serveru SQL Server.

V [databázi SQL](sql-database-automatic-tuning.md)jsou k dispozici dva aspekty automatického ladění :

- **Automatická správa indexů:** Identifikuje indexy, které by se měly do databáze přidat nebo z ní naopak odebrat.
- **Automatická oprava plánu**: Identifikuje problematické plány a opravuje problémy s výkonem plánu SQL.

### <a name="adaptive-query-processing"></a>Adaptivní zpracování dotazů

Můžete použít [adaptivní zpracování dotazů](/sql/relational-databases/performance/intelligent-query-processing), včetně prokládání spuštění pro funkce s více příkazy tabulka s hodnotou, dávkový režim paměti udělit zpětnou vazbu a dávkový režim adaptivní spojení. Každá z těchto funkcí adaptivního zpracování dotazů používá podobné techniky "učit se a přizpůsobovat", což pomáhá dále řešit problémy s výkonem související s historicky neřešitelnými problémy optimalizace dotazů.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

SQL Database nabízí řadu [integrovaných funkcí zabezpečení a dodržování předpisů](sql-database-security-overview.md), které vaší aplikaci pomůžou se splněním nejrůznějších požadavků na zabezpečení a dodržování předpisů.

> [!IMPORTANT]
> Společnost Microsoft má certifikované Azure SQL Database (všechny možnosti nasazení) podle řady standardů dodržování předpisů. Další informace naleznete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

### <a name="advance-threat-protection"></a>Předběžná ochrana před hrozbami

Pokročilé zabezpečení dat je jednotný balíček pro pokročilé možnosti zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, správu chyb zabezpečení databáze a zjišťování neobvyklých aktivit, které mohou znamenat ohrožení databáze. Poskytuje jediné umístění pro povolení a správu těchto funkcí.

- [Zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md):

  Tato funkce poskytuje funkce integrované do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat ve vašich databázích. Poskytuje přehled o stavu klasifikace databáze a sleduje přístup k citlivým datům v rámci databáze a za jejími hranicemi.
- [Posouzení zranitelnosti](sql-vulnerability-assessment.md):

  Tato služba může zjišťovat, sledovat a pomoci vám napravit potenciální chyby zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Detekce hrozeb](sql-database-threat-detection.md):

  Tato funkce detekuje neobvyklé aktivity, které označují neobvyklé a potenciálně škodlivé pokusy o přístup k databázi nebo o její zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Výstrahy detekce hrozeb poskytují podrobnosti o podezřelé aktivitě a doporučují akci, jak prozkoumat a zmírnit hrozbu.

### <a name="auditing-for-compliance-and-security"></a>Auditování dodržování předpisů a zabezpečení

[Auditování](sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu úložiště Azure. Auditování vám může pomoci udržovat dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které mohou naznačovat obchodní problémy nebo podezření na porušení zabezpečení.

### <a name="data-encryption"></a>Šifrování dat

SQL Database pomáhá zabezpečit vaše data tím, že poskytuje šifrování. Pro data v pohybu používá [zabezpečení transportní vrstvy](https://support.microsoft.com/kb/3135244). Pro data v klidovém stavu používá [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Pro data v provozu používá [vždy šifrované](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL Database umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoft pomocí [integrace s Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování](sql-database-ssms-mfa-authentication.md) pro zvýšení zabezpečení dat a aplikací a zároveň podporuje proces jednotného přihlášení.

## <a name="easy-to-use-tools"></a>Snadno použitelné nástroje

SQL Database zjednodušuje a zefektivňuje vytváření a správu aplikací. SQL Database vám umožňuje soustředit se na to, v čem jste nejlepší – na vytváření skvělých aplikací. Můžete spravovat a rozvíjet v databázi SQL pomocí nástrojů a dovedností, které již máte.

- [Portál Azure](https://portal.azure.com/):

  Webová aplikace pro správu všech služeb Azure.
- [Sql Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Serveru po DATABÁZI SQL.
- [Datové nástroje serveru SQL Server v sadě Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Bezplatná klientská aplikace ke stažení pro vývoj relačních databází serveru SQL Server, databází SQL, balíčků integrationových služeb, datových modelů Analysis Services a sestav Reporting Services.
- [Kód sady Visual Studio](https://code.visualstudio.com/docs):

  Bezplatný, ke stažení, open-source editor kódu pro Windows, macOS a Linux. Podporuje rozšíření, včetně [rozšíření mssql](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Server, Azure SQL Database a Azure SQL Data Warehouse.

SQL Database podporuje vytváření aplikací s Pythonem, Javou, Node.js, PHP, Ruby a .NET v systémech macOS, Linux a Windows. SQL Database podporuje stejné [knihovny připojení](sql-database-libraries.md) jako SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Nejčastější dotazy k databázi SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>Jaká je aktuální verze databáze SQL?

Aktuální verze databáze SQL je V12. Verze V11 byla vyřazena.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Mohu určit, kdy dojde k prostojům oprav?

Ne. Dopad opravy obecně není patrné, pokud [používáte logiku opakování](sql-database-develop-overview.md#resiliency) ve vaší aplikaci. Další informace najdete [v tématu Plánování událostí údržby Azure v Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Otázky týkající se hybridních výhod Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Existují práva dvojího užití s hybridní výhodou Azure pro SQL Server?

Máte 180 dní práva na dvojí použití licence, abyste zajistili bezproblémový provoz migrace. Po tomto 180denním období můžete licenci SQL Serveru používat pouze v cloudu v databázi SQL Database. Už nemáte práva k dvojímu použití místně a v cloudu.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Jak se hybridní přínos Azure pro SQL Server liší od mobility licencí?

Nabízíme výhody mobility licencí zákazníkům SQL Serveru s programem Software Assurance. To umožňuje přeřazení licencí na sdílené servery partnera. Tuto výhodu můžete využít na Azure IaaS a AWS EC2.

Hybridní výhoda Azure pro SQL Server se liší od mobility licencí ve dvou klíčových oblastech:

- Poskytuje ekonomické výhody pro přesun vysoce virtualizovaných úloh do Azure. Zákazníci SQL Serveru Enterprise Edition můžou získat čtyři jádra v Azure ve skladové jednotce pro obecné účely pro každé jádro, které vlastní místně pro vysoce virtualizované aplikace. Mobilita licencí neumožňuje žádné speciální výhody pro přesun virtualizovaných úloh do cloudu.
- Poskytuje cíl PaaS v Azure (instance spravované databází SQL), který je vysoce kompatibilní s místním SQL Serverem.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Jaká jsou konkrétní práva hybridní výhody Azure pro SQL Server?

Zákazníci sql database mají následující práva přidružená k hybridnívýhodě Azure pro SQL Server:

|Stopa licence|Co vám azure hybridní výhody pro SQL Server získáte?|
|---|---|
|Hlavní zákazníci SQL Server Enterprise Edition se sa|<li>Může platit základní sazbu pro obecné účely nebo kritickou obchodní skladovou položku.</li><br><li>1 jádro v místním prostředí = 4 jádra ve skladové jednotce pro obecné účely</li><br><li>1 jádro v místním prostředí = 1 jádro ve skladové jednotce kritické pro podnikání</li>|
|Hlavní zákazníci SQL Serveru Standard Edition s s sa|<li>Může platit základní sazbu pouze pro obecné účely SKU</li><br><li>1 jádro v místním prostředí = 1 jádro ve skladové jednotce pro obecné účely</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Spolupráce s technickým týmem SQL Serveru

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Zeptejte se na otázky správy databáze.
- [Přetečení zásobníku](https://stackoverflow.com/questions/tagged/sql-server): Ptejte se na otázky vývoje.
- [MSDN Fórum](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Pokládejte technické otázky.
- [Zpětná vazba](https://aka.ms/sqlfeedback): Nahlašte chyby a funkci požadavku.
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskutovat sql server.

## <a name="next-steps"></a>Další kroky

- Na [stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/) najdete porovnání nákladů a kalkulačky týkající se jednotlivých databází a elastických fondů.
- Chcete-li začít, podívejte se na tyto rychlé starty:

  - [Vytvoření databáze SQL na webu Azure Portal](sql-database-single-database-get-started.md)  
  - [Vytvoření databáze SQL pomocí Azure CLI](sql-database-get-started-cli.md)
  - [Vytvoření databáze SQL pomocí PowerShellu](sql-database-get-started-powershell.md)

- Řadu ukázek v Azure CLI a PowerShellu najdete tady:
  - [Ukázky v Azure CLI pro službu SQL Database](sql-database-cli-samples.md)
  - [Ukázky v Azure PowerShellu pro službu SQL Database](sql-database-powershell-samples.md)

- Informace o nových funkcích při jejich oznámení najdete v [tématu Plán Azure pro sql database](https://azure.microsoft.com/roadmap/?category=databases).
- Podívejte se na [blog Azure SQL Database](https://azure.microsoft.com/blog/topics/database), kde členové produktového týmu SQL Serveru blogují o novinkách a funkcích databáze SQL.

