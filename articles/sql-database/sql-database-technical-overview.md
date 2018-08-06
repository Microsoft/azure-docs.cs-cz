---
title: Co je služba Azure SQL Database? | Microsoft Docs
description: Přečtěte si základní informace o službě SQL Database – technické podrobnosti a možností cloudového systému pro správu relačních databází (RDBMS) společnosti Microsoft.
keywords: představení sql,úvod do sql,co je sql database
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.topic: overview
ms.date: 08/01/2018
ms.author: carlrab
ms.openlocfilehash: f7a314b3de112ad1fa7a5a356c2325846e9371b1
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413378"
---
# <a name="what-is-the-azure-sql-database-service"></a>Co je služba Azure SQL Database? 

SQL Database je spravovaná služba relačních databází pro obecné účely v Microsoft Azure, která podporuje struktury, jako jsou relační data, JSON, prostorová data a XML. SQL Database zajišťuje dynamicky škálovatelný výkon v rámci dvou nákupních modelů, [nákupního modelu založeného na virtuálních jádrech](sql-database-service-tiers-vcore.md) a [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md). SQL Database také poskytuje možnosti jako [indexy columnstore](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) pro extrémní analytické analýzy a generování sestav nebo [OLTP v paměti](sql-database-in-memory.md) pro extrémní zpracování transakcí. Microsoft zajišťuje bezproblémové opravy a aktualizace základního kódu SQL a odděluje veškerou správu základní infrastruktury. 

Azure SQL Database poskytuje následující možnosti nasazení databáze SQL Azure:
- Jako jednoúčelová databáze s vlastní sadou prostředků spravovaných přes logický server. 
- Jako databáze v [elastickém fondu](sql-database-elastic-pool.md) se sdílenou sadou prostředků spravovaných přes logický server.
- Jako součást kolekce databází označované jako [spravovaná instance](sql-database-managed-instance.md) (ve verzi Public Preview), která obsahuje systémové a uživatelské databáze a sdílí sadu prostředků.

Následující obrázek ukazuje tyto možnosti nasazení:

![možnosti nasazení](./media/sql-database-technical-overview/deployment-options.png) 

SQL Database sdílí základ kódu s [databázovým strojem Microsoft SQL Serveru](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation). V rámci strategie Microsoftu zaměřené na cloud se nové funkce SQL Serveru uvolňují nejprve do služby SQL Database a až potom do samotného SQL Serveru. Tento přístup vám poskytuje nejnovější funkce SQL Serveru bez režijních nákladů na opravy nebo aktualizace. Kromě toho umožňuje testování těchto funkcí v milionech databází. Pokud se chcete o nových funkcích dozvědět hned po jejich oznámení, podívejte se na:

- **[Plány Azure do budoucna pro službu SQL Database:](https://azure.microsoft.com/roadmap/?category=databases)** Místo, kde můžete zjistit, co je nového a co se právě chystá. 
- **[Blog o službě Azure SQL Database:](https://azure.microsoft.com/blog/topics/database)** Místo, kde členové produktového týmu SQL Serveru píší o novinkách a funkcích služby SQL Database. 

> [!IMPORTANT]
> Rozdíly mezi funkcemi služeb SQL Database a SQL Server jsou popsané v článku o [funkcích SQL](sql-database-features.md). 

SQL Database nabízí předvídatelný výkon na několika úrovních služeb, které poskytují dynamickou škálovatelnost bez přerušení provozu, integrovanou inteligentní optimalizaci, globální škálovatelnost a dostupnost a pokročilé možnosti zabezpečení – to vše téměř bez nutnosti jakékoli správy. Díky těmto možnostem se můžete zaměřit na rychlý vývoj aplikací a zkrácení doby dodání produktu na trh, namísto vynakládání prostředků a drahocenného času na správu virtuálních počítačů a infrastruktury. SQL Database je aktuálně v 38 datových centrech po celém světě a další datová centra se pravidelně přidávají, což vám umožňuje provozovat databázi v datovém centru ve vaší blízkosti.

> [!NOTE]
> SQL Database Managed Instance je aktuálně ve verzi Preview a je k dispozici pouze na jedné úrovni služby. Další informace najdete v tématu [SQL Database Managed Instance](sql-database-managed-instance.md).
>

## <a name="scalable-performance-and-pools"></a>Škálovatelnost výkonu a fondy

Ve službě SQL Database je každá databáze izolovaná od všech ostatních, je přenosná a má vlastní úroveň služeb v rámci [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md) nebo [nákupního modelu založeného na virtuálních jádrech](sql-database-service-tiers-vcore.md) s garantovanou úrovní výkonu. SQL Database nabízí různé úrovně výkonu pro různé potřeby a umožňuje spojení databází do fondu pro maximalizaci využití prostředků a snížení nákladů.

V případě SQL Database Managed Instance jsou jednotlivé instance navzájem izolované a mají garantované prostředky. Další informace najdete v tématu [SQL Database Managed Instance](sql-database-managed-instance.md). 

### <a name="adjust-performance-and-scale-without-downtime"></a>Úprava výkonu a škálování bez výpadků

SQL Database nabízí [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). 
- Nákupní model založený na DTU nabízí pro zajištění podpory databázových úloh od zcela nenáročných až po velmi náročné kombinaci výpočetních, paměťových a vstupně-výstupních prostředků na třech úrovních služby: Basic, Standard a Premium. Úrovně výkonu na jednotlivých úrovních poskytují různou kombinaci těchto prostředků, ke kterým můžete přidat další prostředky úložiště.
- Nákupní model založený na virtuálních jádrech umožňuje výběr počtu virtuálních jader, velikosti paměti a velikosti a rychlosti úložiště.

S nízkými měsíčními náklady můžete sestavit svou první aplikaci s malou izolovanou databází a později ručně nebo programově změnit úroveň služby, aby splňovala požadavky vašeho řešení. Můžete upravit úroveň výkonu bez přerušení provozu aplikace a bez dopadu na vaše zákazníky. Dynamická škálovatelnost umožňuje databázím transparentně reagovat na rychle se měnící požadavky na prostředky a vy díky tomu platíte pouze za prostředky, které potřebujete, když je potřebujete.

Dynamická škálovatelnost se liší od automatického škálování. K automatickému škálování dochází, když se služba škáluje automaticky na základě kritérií, zatímco dynamická škálovatelnost umožňuje ruční škálování bez prostojů. Izolovaná databáze Azure SQL Database podporuje ruční dynamickou škálovatelnost, nikoli automatické škálování. Pokud chcete *automatizovanější* prostředí, zvažte použití elastických fondů, které databázím umožňují sdílet prostředky ve fondu na základě potřeb jednotlivých databází. Existují ale skripty, které mohou automatickou škálovatelnost pro izolovanou databázi Azure SQL Database usnadnit. Příklad najdete v tématu [Monitorování a škálování izolované databáze SQL pomocí PowerShellu](scripts/sql-database-monitor-and-scale-database-powershell.md). 

SQL Database Managed Instance je ve verzi Preview a nabízí jedinou úroveň služby. Další informace najdete v tématu [SQL Database Managed Instance](sql-database-managed-instance.md).

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastické fondy pro maximalizaci využití prostředků

Řadě firem a aplikací stačí, že může vytvářet izolované databáze a nastavovat větší nebo menší výkon na vyžádání, zejména při relativně předvídatelném způsobu používání. Ale pokud vaše vzorce používání předvídatelné nejsou, může být správa nákladů a údržba obchodního modelu velmi těžká. [Elastické fondy](sql-database-elastic-pool.md) jsou navržené k řešení tohoto problému. Princip je jednoduchý. Prostředky výkonu přidělujete fondu, nikoli jednotlivým databázím, a platíte za souhrnné prostředky výkonu fondu místo placení za výkon izolovaných databází. 

   ![elastické fondy](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

S elastickými fondy se nemusíte starat o zvyšování a snižování výkonu databáze s kolísajícími požadavky na prostředky. Databáze ve fondu spotřebovávají prostředky výkonu elastického fondu podle potřeby. Databáze ve fondu spotřebovávají výkon, ale nepřekračují omezení fondu, takže vaše náklady budou předvídatelné, i když využívání jednotlivých databází odhadnutelné nebude. A navíc můžete [přidávat a odebírat databáze ve fondu](sql-database-elastic-pool-manage-portal.md), škálovat aplikace od několik databází k tisícům a přitom mít rozpočet stále pod kontrolou. Můžete také řídit minimální a maximální prostředky, které mají databáze ve fondu k dispozici, a tím zajistit, že žádná databáze ve fondu nebude využívat všechny prostředky fondu a pro každou databázi ve fondu bude garantováno minimální množství prostředků. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Skripty vám můžou pomoct s monitorováním a škálováním elastických fondů. Příklad najdete v tématu [Monitorování a škálování elastického fondu SQL ve službě Azure SQL Database pomocí PowerShellu](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> SQL Database Managed Instance nepodporuje elastické fondy.

### <a name="blend-single-databases-with-pooled-databases"></a>Kombinace izolovaných databází s databázemi ve fondu

Ať už si vyberete kteroukoli cestu – izolované databáze nebo elastické fondy – nejste k ní odsouzeni na věčné časy. Izolované databáze můžete kombinovat s elastickými fondy a snadno a rychle měnit úrovně služeb izolovaných databází a elastických fondů, abyste je přizpůsobili své situaci. S výkonem a dostupností, které Azure nabízí, můžete se službou SQL Database kombinovat další služby Azure podle konkrétních potřeb vašich aplikací pro zvýšení ekonomičnosti provozu a efektivity prostředků a otevírat tak zcela nové obchodní příležitosti.

### <a name="extensive-monitoring-and-alerting-capabilities"></a>Rozsáhlé monitorování a možnosti upozorňování

Jak ale můžeme srovnávat relativní výkon izolovaných databází a elastických fondů? Jak poznáme správnou hodnotu nastavení při přidávání nebo ubírání výkonu? Můžete využít integrované nástroje pro [monitorování výkonu](sql-database-performance.md) a [upozorňování](sql-database-insights-alerts-portal.md) v kombinaci s hodnocením výkonu. Pomocí těchto nástrojů můžete rychle posoudit dopad vertikálního navýšení nebo snížení kapacity v závislosti na stávajících nebo předpokládaných požadavcích. Další informace najdete v článku o [nákupním modelu založeném na DTU](sql-database-service-tiers-dtu.md) nebo o [nákupním modelu založeném na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Kromě toho může SQL Database [generovat metriky a diagnostické protokoly](sql-database-metrics-diag-logging.md) pro snazší monitorování. SQL Database můžete nakonfigurovat pro ukládání využití prostředků, pracovních procesů, relací a možností připojení do jednoho z těchto prostředků Azure:

- **Azure Storage:** Pro archivaci obrovských objemů telemetrických dat za nízkou cenu.
- **Centrum událostí Azure:** Pro integraci telemetrických dat služby SQL Database s vlastními řešeními monitorování nebo aktivními kanály.
- **Azure Log Analytics:** Pro integrované řešení monitorování s možnostmi generování sestav, upozorňování a omezování rizik. Tato funkce je součástí [Operations Management Suite (OMS)](../operations-management-suite/operations-management-suite-overview.md).

    ![Architektura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Možnosti dostupnosti

Dostupnost služby Azure se smlouvou o úrovní služeb [(SLA)](http://azure.microsoft.com/support/legal/sla/) dosahuje špičkové hodnoty 99,99 %, protože staví na globální síti Microsoftem spravovaných datových center. Může tedy udržet vaše aplikace v nepřetržitém provozu každý den po celý rok. Platforma Azure plně spravuje všechny služby Azure SQL Database a zajišťuje nulovou ztrátu dat a vysokou procentní dostupnost dat. Azure automaticky zpracovává opravy, zálohování, replikaci, detekci selhání, potenciální selhání základního hardwaru, softwaru nebo sítě, nasazování oprav chyb, převzetí služeb při selhání, upgrady databází a další úlohy údržby. Dostupnosti úrovně Standard se dosahuje oddělením výpočetní a úložné vrstvy. Dostupnosti úrovně Premium se dosahuje integrací výpočetního prostředí a úložiště na jednom uzlu za účelem zajištění výkonu a implementací skupin dostupnosti AlwaysOn na pozadí. Úplný popis možností zajištění vysoké dostupnosti v Azure SQL Database najdete v tématu [Dostupnost služby SQL Database](sql-database-high-availability.md). Kromě toho SQL Database nabízí integrované funkce pro [provozní kontinuitu a globální škálovatelnost](sql-database-business-continuity.md), mezi které patří:

- **[Automatické zálohování:](sql-database-automated-backups.md)** SQL Database automaticky provádí úplné a rozdílové zálohování a zálohování protokolů transakcí.
- **[Obnovení k určitému bodu v čase:](sql-database-recovery-using-backups.md)** SQL Database podporuje obnovení k libovolnému bodu v čase v rámci doby uchování automatických záloh.
- **[Aktivní geografická replikace:](sql-database-geo-replication-overview.md)** SQL Database umožňuje konfigurovat až čtyři sekundární databáze s možností čtení ve stejném datovém centru nebo v globálně distribuovaných datových centrech.  Pokud například máte aplikaci SaaS s databází katalogu s velkým počtem souběžných transakcí jen pro čtení, použijte geografickou replikaci a umožněte globální škálovaní operací čtení a odstraňte problémová místa v hlavní databázi způsobená úlohami čtení. 
- **[Skupiny převzetí služeb při selhání:](sql-database-geo-replication-overview.md)** SQL Database umožňuje povolit vysokou dostupnost a vyrovnávání zatížení v globálním měřítku, včetně transparentní geografické replikace a převzetí služeb při selhání pro velké sady databází a elastické fondy. Skupiny převzetí služeb při selhání a aktivní geografická replikace umožňují vytváření globálně distribuovaných aplikací SaaS s minimálními nároky na správu. Veškeré komplexní monitorování, směrování a orchestraci převzetí služeb při selhání zajišťuje služba SQL Database.
- **[Zónově redundantní databáze:](sql-database-high-availability.md)** SQL Database umožňuje zřizování databází nebo elastických fondů na úrovni Premium nebo Pro důležité obchodní informace napříč několika zónami dostupnosti. Vzhledem k tomu, že tyto databáze a elastické fondy mají z důvodu zajištění vysoké dostupnosti několik redundantních replik, umístění těchto replik do několika zón dostupnosti zajistí vyšší odolnost, včetně možnosti automatického obnovení bez ztráty dat v případě selhání na úrovni datacentra.  

## <a name="built-in-intelligence"></a>Integrované inteligentní funkce

Se službou SQL Database získáte integrované inteligentní funkce, které vám pomůžou výrazně snížit náklady na provoz a správu databází a maximalizovat výkon a zabezpečení aplikace. Během nepřetržitého spouštění milionů zákaznických úloh SQL Database shromažďuje a zpracovává obrovské množství telemetrických dat, přičemž zároveň plně respektuje ochranu osobních údajů zákazníků. Různé algoritmy neustále vyhodnocují telemetrická data, aby se služba mohla učit a přizpůsobovat vaší aplikaci. Na základě této analýzy služba nabízí doporučení pro vylepšení výkonu přizpůsobená pro konkrétní úlohu. 

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatické monitorování a optimalizace výkonu

SQL Database nabízí podrobné přehledy dotazů, které potřebujete monitorovat. SQL Database zjistí aspekty vaší databáze a umožní vám přizpůsobit schéma databáze vaší úloze. SQL Database poskytuje [doporučení pro optimalizaci výkonu](sql-database-advisor.md), kde můžete zkontrolovat akce optimalizace a použít je. 

Neustálé monitorování databáze je však náročný a zdlouhavý úkol, zejména při práci s mnoha databázemi. Služba [Intelligent Insights](sql-database-intelligent-insights.md) dělá tuto práci za vás pomocí automatického monitorování výkonu služby SQL Database a informuje vás o problémech se snižováním výkonu, identifikuje původní příčinu problému a poskytuje doporučení pro vylepšení výkonu, pokud je to možné.

Efektivní správa velkého počtu databází může být nemožná i se všemi dostupnými nástroji a sestavami, které SQL Database a Azure Portal nabízí. Místo ručního monitorování a optimalizace databáze můžete zvážit delegování některých akcí monitorování a optimalizace do služby SQL Database pomocí [automatické optimalizace](sql-database-automatic-tuning.md). SQL Database automaticky aplikuje doporučení a testuje a ověřuje každou z akcí optimalizace pro zajištění stálého vylepšování výkonu. Díky tomu se služba SQL Database může automaticky přizpůsobit vaší úloze řízeným a bezpečným způsobem. Automatická optimalizace znamená, že se výkon vaší databáze pečlivě monitoruje a porovnává před a po každé akci optimalizace, a pokud se výkon nezlepší, akce optimalizace se vrátí zpět.

V současné době se mnoho našich partnerů provozujících [víceklientské aplikace SaaS](sql-database-design-patterns-multi-tenancy-saas-applications.md) nad službou SQL Database spoléhá na automatickou optimalizaci výkonu pro zajištění, že jejich aplikace budou mít vždy stabilní a předvídatelný výkon. Tato funkce pro ně výrazně snižuje riziko incidentu výkonu uprostřed noci. Vzhledem k tomu, že část jejich zákazníků také používá SQL Server, můžou navíc s využitím stejných doporučení indexování získaných ze služby SQL Database pomáhat svým zákazníkům s SQL Serverem.

Ve službě [SQL Database jsou dostupné](sql-database-automatic-tuning.md) dva aspekty automatické optimalizace:

- **Automatická správa indexů:** Identifikuje indexy, které by se měly do databáze přidat nebo z ní naopak odebrat.
- **Automatická oprava plánů:** Identifikuje problematické plány a řeší problémy s výkonem plánu SQL (již brzy, aktuálně k dispozici v SQL Serveru 2017).

### <a name="adaptive-query-processing"></a>Adaptivní zpracování dotazů

Do služby SQL Database přidáváme také řadu funkcí pro [adaptivní zpracování dotazů](/sql/relational-databases/performance/adaptive-query-processing), včetně prokládaného spouštění funkcí s více příkazy vracejících tabulky, odezvy přidělení paměti v režimu dávky a adaptivních příkazů join v režimu dávky. Každá z těchto funkcí pro adaptivní zpracování dotazů používá podobné techniky učení a adaptace a pomáhá tak dále řešit problémy s výkonem související s historicky nezvladatelnými problémy s optimalizací dotazů.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

SQL Database nabízí řadu [integrovaných funkcí zabezpečení a dodržování předpisů](sql-database-security-overview.md), které vaší aplikaci pomůžou se splněním nejrůznějších požadavků na zabezpečení a dodržování předpisů. 

### <a name="advance-threat-protection"></a>Advanced Threat Protection

SQL Advanced Threat Protection je jednotný balíček pokročilých možností zabezpečení SQL. Zahrnuje funkce pro zjišťování a klasifikaci citlivých dat, správu ohrožení zabezpečení databáze a detekci neobvyklých aktivit, které by pro vaši databázi mohly znamenat hrozbu. Poskytuje centrální místo pro povolování a správu těchto možností.

- [Zjišťování a klasifikace dat](sql-database-data-discovery-and-classification.md) (aktuálně ve verzi Preview) poskytuje funkce integrované do Azure SQL Database pro zjišťování, klasifikaci, označování a ochranu citlivých dat v databázích. Může sloužit k poskytování přehledu o stavu klasifikace databáze a ke sledování přístupu k citlivým datům v databázi i mimo ni.
- [Posouzení ohrožení zabezpečení](sql-vulnerability-assessment.md) je snadno konfigurovatelná služba, která může zjišťovat, sledovat a pomáhat opravovat potenciální ohrožení zabezpečení databáze. Poskytuje přehled o stavu zabezpečení a zahrnuje praktické kroky k vyřešení problémů se zabezpečením a zlepšení ochrany databáze.
- [Detekce hrozeb](sql-database-threat-detection.md) zjišťuje neobvyklé aktivity, které můžou ukazovat na neobvyklé a potenciálně škodlivé pokusy o přístup k databázi nebo její zneužití. Nepřetržitě monitoruje podezřelé aktivity v databázi a okamžitě poskytuje výstrahy zabezpečení týkající se potenciálních ohrožení zabezpečení, útoků prostřednictvím injektáže SQL a neobvyklých vzorů přístupu k databázi. Upozornění detekce hrozeb obsahují podrobnosti o podezřelé aktivitě a doporučení akce k prošetření a zmírnění hrozby.

### <a name="auditing-for-compliance-and-security"></a>Auditování dodržování předpisů a zabezpečení

[Auditování služby SQL Database](sql-database-auditing.md) sleduje události databáze a zapisuje je do protokolu auditu ve vašem účtu Azure Storage. Auditování pomáhá zajistit dodržování předpisů, porozumět databázové aktivitě a získat přehled o nesrovnalostech a anomáliích, které můžou značit problémy obchodního charakteru nebo vzbuzovat podezření na narušení zabezpečení.

### <a name="data-encryption"></a>Šifrování dat

SQL Database chrání data tím, že zajišťuje šifrování přenášených dat pomocí [protokolu TLS (Transport Layer Security)](https://support.microsoft.com/kb/3135244), neaktivních uložených dat pomocí [transparentního šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) a používaných dat pomocí funkce [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

SQL Database umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoft pomocí [integrace s Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování](sql-database-ssms-mfa-authentication.md) (MFA) pro zvýšení zabezpečení dat a aplikací při současné podpoře jednotného přihlašování.

### <a name="compliance-certification"></a>Certifikace dodržování předpisů

Služba SQL Database se účastní pravidelných auditů a byla certifikována pro řadu standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://azure.microsoft.com/support/trust-center/), kde také najdete nejnovější seznam [certifikátů služby SQL Database v oblasti dodržování předpisů](https://azure.microsoft.com/support/trust-center/services/).

## <a name="easy-to-use-tools"></a>Snadno použitelné nástroje

SQL Database zjednodušuje a zefektivňuje vytváření a správu aplikací. SQL Database vám umožňuje soustředit se na to, v čem jste nejlepší – na vytváření skvělých aplikací. Ve službě SQL Database můžete vyvíjet a provádět správu pomocí nástrojů a dovedností, které už máte.

- **[Azure Portal:](https://portal.azure.com/)** Webová aplikace pro správu všech služeb Azure. 
- **[SQL Server Management Studio:](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** Bezplatná klientská aplikace ke stažení pro správu jakékoli infrastruktury SQL, od SQL Serveru až po službu SQL Database.
- **[SQL Server Data Tools v sadě Visual Studio:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** Bezplatná klientská aplikace ke stažení pro vývoj relačních databází SQL Serveru, databází Azure SQL, balíčků služby SSIS, datových modelů služby Analysis Services a sestav služby Reporting Services.
- **[Visual Studio Code:](https://code.visualstudio.com/docs)** Bezplatný opensourcový editor kódu ke stažení pro Windows, macOS a Linux, který podporuje rozšíření, včetně [rozšíření mssql](https://aka.ms/mssql-marketplace) pro dotazování Microsoft SQL Serveru, služby Azure SQL Database a služby SQL Data Warehouse.

SQL Database podporuje vytváření aplikací pomocí Pythonu, Javy, Node.js, PHP, Ruby a .NET v systémech MacOS, Linux a Windows. SQL Database podporuje stejné [knihovny připojení](sql-database-libraries.md) jako SQL Server.

## <a name="engage-with-the-sql-server-engineering-team"></a>Spolupráce s technickým týmem SQL Serveru

- [DBA na webu Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Pokládání dotazů týkajících se správy databází
- [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-server): Pokládání dotazů týkajících se vývoje
- [Fóra na webu MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Pokládání technických dotazů
- [Feedback](http://aka.ms/sqlfeedback): Hlášení chyb a žádosti o funkce
- [Reddit](https://www.reddit.com/r/SQLServer/): Diskuze o SQL Serveru

## <a name="next-steps"></a>Další kroky

- Na [stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/) najdete cenové kalkulačky a srovnání cen izolovaných databází a elastických fondů.

- Tyto rychlé starty vám pomůžou začít:

  - [Vytvoření databáze SQL na webu Azure Portal](sql-database-get-started-portal.md)  
  - [Vytvoření databáze SQL pomocí Azure CLI](sql-database-get-started-cli.md)
  - [Vytvoření databáze SQL pomocí PowerShellu](sql-database-get-started-powershell.md)

- Řadu ukázek v Azure CLI a PowerShellu najdete tady:
  - [Ukázky v Azure CLI pro službu SQL Database](sql-database-cli-samples.md)
  - [Ukázky v Azure PowerShellu pro službu SQL Database](sql-database-powershell-samples.md)
