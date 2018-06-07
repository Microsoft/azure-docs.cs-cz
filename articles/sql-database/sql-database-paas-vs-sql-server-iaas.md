---
title: SQL (PaaS) Database vs. SQL Server v cloudu na virtuálních počítačích (IaaS) | Dokumentace Microsoftu
description: 'Další informace o tom, která cloudová možnost SQL Serveru nejlépe odpovídá vaší aplikaci: Azure SQL (PaaS) Database nebo SQL Server v cloudu na virtuálních počítačích Azure.'
services: sql-database, virtual-machines
keywords: Cloud SQL Server, SQL Server v cloudu, databáze PaaS, cloudový SQL Server, DBaaS
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: carlrab
ms.openlocfilehash: 236a9489f65f3347076699aa5357476e74f7f986
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649563"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Volba cloudového řešení systému SQL Server: Azure SQL (PaaS) Database nebo SQL Server na virtuálních počítačích Azure (IaaS)
Azure nabízí pro hostování úloh SQL Serveru v Microsoft Azure dvě možnosti:

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): Databáze SQL nativní pro cloud označovaná také jako databáze PaaS nebo databáze DBaaS, která je optimalizovaná pro vývoj aplikací SaaS. Nabízí kompatibilitu s většinou funkcí systému SQL Server. Další informace o modelu PaaS najdete v tématu [Co je PaaS](https://azure.microsoft.com/overview/what-is-paas/).
* [SQL Server na virtuálních počítačích Azure:](https://azure.microsoft.com/services/virtual-machines/sql-server/) SQL Server nainstalovaný a hostovaný v cloudu na virtuálních počítačích s Windows Serverech běžící v Azure označovaný také jako IaaS.
  SQL Server na virtuálních počítačích Azure je optimalizovaný pro migraci stávajících aplikací systému SQL Server. Dostupné jsou všechny verze a edice systému SQL Server. Nabízí 100% kompatibilitu se systémem SQL Server a umožňuje tak hostovat kolik databází je potřeba a provádět mezidatabázové transakce. Nabízí úplné řízení systému SQL Server a Windows.

Zjistěte, jak jednotlivé možnosti zapadají do datové platformy společnosti Microsoft, a získejte pomoc se spárováním té správné volby s požadavky vaší firmy. Ať už je pro vás určující úspora nákladů nebo minimální režie z hlediska správy (které pro vás mají vyšší prioritu než cokoli jiného), tento článek vám může pomoci rozhodnout, jaký přístup přinese vaší firmě nejlepší výsledky z hlediska požadavků, na jejichž splnění vám nejvíc záleží.

## <a name="microsofts-data-platform"></a>Datová platforma společnosti Microsoft
Jednou z nejdůležitějších věcí, které je potřeba chápat, pokud se porovnávají výhody databází na Azure oproti využívání místních databází SQL Serveru, je to, že použitelné jsou všechny z nich. Datová platforma společnosti Microsoft využívá technologii SQL Serveru a zpřístupňuje ji napříč fyzickými místními počítači, prostředími privátního cloudu, prostředími privátního cloudu hostovanými třetí stranou a veřejným cloudem. SQL Server na virtuálních počítačích Azure vám umožňuje vyhovět jedinečným a rozmanitým potřebám firmy díky kombinaci místního nasazení a nasazení hostovaného v cloudu pomocí stejné sady serverových produktů, vývojových nástrojů a odborných znalosti v těchto prostředích.

   ![Možnosti SQL Serveru v cloudu: SQL server na IaaS nebo databáze SQL SaaS v cloudu.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak je vidět v diagramu, každou nabídku je možné charakterizovat úrovní správy, kterou nad infrastrukturou máte (na ose X), a stupněm nákladové efektivity dosahovaným konsolidací úrovně databáze a automatizace (na ose Y).

Při navrhování aplikace jsou pro hostování součásti aplikace s SQL Serverem k dispozici čtyři základní možnosti:

* SQL Server na nevirtualizovaných fyzických počítačích
* SQL Server v místních virtualizovaných počítačích (privátní cloud)
* SQL Server na virtuálním počítači Azure (veřejný cloud Microsoftu)
* Azure SQL Database (veřejný cloud Microsoftu)

V následujících oddílech se dozvíte o systému SQL Server ve veřejném cloudu Microsoftu: Azure SQL Database a SQL Server na virtuálních počítačích Azure. Kromě toho prozkoumáte obvyklé motivační faktory firem pro určení toho, která možnost je pro konkrétní aplikaci nejvhodnější.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Bližší pohled na Azure SQL Database a SQL Server na virtuálních počítačích Azure
**Azure SQL Database** je relační databáze jako služba (DBaaS) hostovaná v cloudu Azure, která spadá do oborových kategorií *SaaS* a *PaaS*. [SQL Database](sql-database-technical-overview.md) je postavená na standardizovaném hardwaru a softwaru, který vlastní, hostuje a spravuje Microsoft. Pomocí SQL Database můžete provádět vývoj přímo na úrovni služby pomocí integrovaných funkcí. Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení.

**SQL Server na virtuálních počítačích Azure** spadá do oborové kategorie *IaaS* a umožňuje vám spustit SQL Server uvnitř virtuálního počítače v cloudu. Podobně jako SQL Database je i tato databáze postavena na standardizovaném hardwaru, který je vlastněn, hostován a spravován společností Microsoft. Když na virtuálním počítači používáte SQL Server, můžete průběžně platit za licenci systému SQL Server obsaženou v imagi systému SQL Server nebo jednoduše použít stávající licenci. Můžete také vertikálně navýšit/snížit kapacitu virtuálního počítače nebo jej pozastavit/obnovit podle potřeby.

Obecně jsou tyto dvě možnosti SQL optimalizovány pro různé účely:

* **Azure SQL Database** je optimalizována pro snížení celkových nákladů na minimum pro zřizování a správu velkého počtu databází. Snižuje náklady na průběžnou správu, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](sql-database-automated-backups.md). Obecně platí, že Azure SQL Database může výrazně zvýšit počet databází, které spravuje jeden IT nebo vývojářský zdroj.
* **SQL Server běžící na virtuálních počítačích Azure** je optimalizovaný pro migraci stávajících aplikací do Azure nebo rozšíření stávajících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. V případě používání SQL Serveru na virtuálních počítačích Azure máte úplná práva pro správu pro vyhrazenou instanci SQL Serveru a cloudové virtuální počítače. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.

Následující tabulka shrnuje hlavní vlastnosti SQL Database a SQL Serveru na virtuálních počítačích Azure:

| **Nejvhodnější pro:** | **Azure SQL Database** | **SQL Server na virtuálním počítači Azure** |
| --- | --- | --- |
|  |Nové aplikace navržené pro cloud, které mají časová omezení z hlediska vývoje a marketingu. |Existující aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami. Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. |
|  | Týmy, které potřebují integrovanou vysokou dostupnost, možnost zotavení po havárii a možnost upgradu databáze. |Týmy, které mohou konfigurovat a spravovat vysokou dostupnost, zotavení po havárii a použití dílčích oprav systému SQL Server. Některé poskytované automatizované funkce to značně zjednodušují. | |
|  | Týmy, které nechtějí spravovat příslušný operační systém a nastavení konfigurace. |Budete potřebovat přizpůsobené prostředí s plná práva pro správu. | |
|  | Databáze až 4 TB nebo větší databáze, které může být [vodorovně nebo svisle na oddíly](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) pomocí vzoru Škálováním na více systémů. |Instance systému SQL Server s úložištěm o velikosti až 64 TB. Instance může podporovat tolik databází, kolik je potřeba. | |
|  | | |
| **Zdroje a prostředky:** |Nechcete využívat prostředky IT ke konfiguraci a správě základní infrastruktury, ale chcete se soustředit na aplikační vrstvu. |Máte některé prostředky IT ke konfiguraci a správě. Některé poskytované automatizované funkce to značně zjednodušují. |
| **Celkové náklady na vlastnictví:** |Eliminuje náklady na hardware a snižuje náklady na správu. |Eliminuje náklady na hardware. |
| **Kontinuita podnikových procesů:** |Kromě funkcí infrastruktury s integrovanou odolnost proti chybám Azure SQL Database nabízí funkce, jako například [automatizované zálohování](sql-database-automated-backups.md), [v daném okamžiku obnovení](sql-database-recovery-using-backups.md#point-in-time-restore), [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore), a [aktivní geografickou replikací](sql-database-geo-replication-overview.md) pro zvýšení kontinuity podnikových procesů. Další informace najdete v tématu [Databáze SQL – kontinuita podnikových procesů (přehled)](sql-database-business-continuity.md). |SQL Server na virtuálních počítačích Azure umožňuje nastavit vysoce dostupné řešení s možností zotavení po havárii pro konkrétní potřeby vaší databáze. Můžete tak mít systém, který je vysoce optimalizovaný pro vaši aplikaci. Sami podle potřeby můžete otestovat a spustit převzetí služeb při selhání. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridní cloud:** |Vaše místní aplikace mohou přistupovat k datům v Azure SQL Database. |V případě SQL Serveru na virtuálních počítačích Azure můžete mít aplikace, které běží částečně v cloudu a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Kromě toho můžete uložit místní datové soubory v úložišti Azure pomocí [datových souborů SQL Serveru v Azure](http://msdn.microsoft.com/library/dn385720.aspx). Další informace najdete v tématu [Úvod do hybridního cloudu SQL Serveru 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Podporuje [transakční replikaci systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako předplatitel k replikaci dat. |Plně podporuje [transakční replikaci systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [skupiny dostupnosti AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), integrační služby a přesouvání protokolu k replikaci dat. Navíc jsou plně podporované tradiční zálohy systému SQL Server. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivace firem pro zvolení Azure SQL Database nebo SQL Serveru na virtuálních počítačích Azure
### <a name="cost"></a>Náklady
Ať už jste začínající společnost, která nemá mnoho peněz, nebo tým v zavedené společnosti, který pracuje s limitovaným rozpočtem, je omezená výše prostředků často primárním rozhodujícím faktorem při volbě řešení pro hostování firemních databází. V tomto oddílu zjistíte základní informace o fakturaci a licencování v Azure s ohledem na tyto dvě možnosti relačních databází: SQL Database a SQL Server na virtuálních počítačích Azure. Zjistíte také, jak vypočítat celkové náklady na aplikaci.

#### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování
**SQL Database** je zákazníkům prodávána jako služba, nikoli s licencí.  [SQL Server na virtuálních počítačích Azure](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) je prodáván se zahrnutou licencí, za kterou platíte po minutách. Máte-li stávající licenci, můžete ji také použít.  

V současné době je **SQL Database** k dispozici v několika cenových úrovních služeb, přičemž všechny z nich se účtují hodinově s pevnou sazbou na základě cenové úrovně služby a výkonu, které jste zvolili. Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Úrovně služeb Basic, Standard, Premium, obecné účely a zvláště důležité slouží k tomu, aby umožňovaly dosahovat předvídatelného výkonu s více úrovněmi výkonu podle požadavků vaší aplikace ve špičce. Podle potřeby můžete mezi cenovými a výkonými úrovněmi služeb přecházet, abyste zohlednili měnící se potřeby vaší aplikace z hlediska propustnosti. Nejnovější informace o aktuálně podporovaných cenových úrovních služeb najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md). Můžete také vytvořit [elastické fondy](sql-database-elastic-pool.md), které umožňují sdílení prostředků výkonu mezi různými instancemi databáze.

> [!IMPORTANT]
> Pokud má vaše databáze velký objem transakcí a musí zajišťovat podporu pro více souběžných uživatelů, doporučujeme, abyste úrovně služeb Premium nebo zvláště důležité. Pro zajištění minimální latence mezi vaší aplikace a databáze SQL, najít aplikaci ve stejné oblasti jako databáze a pak testování výkonu - zvýšení úrovně a výkonu vaší služby podle potřeby.

V případě **SQL Database** je databázový software konfigurován, opravován a upgradován automaticky společností Microsoft, což snižuje náklady na správu. Kromě toho vám [integrované funkce zálohování](sql-database-automated-backups.md) pomůžou dosáhnout výrazných úspor nákladů, hlavně v případě, že máte velký počet databází.

V případě systému **SQL Server na virtuálních počítačích Azure** můžete použít jakoukoli image systému SQL Server poskytovanou platformou (která zahrnuje licenci) nebo použít svou vlastní licenci systému SQL Server. K dispozici jsou všechny podporované verze systému SQL Server (2008R2, 2012, 2014, 2016) a jeho edice (Developer, Express, Web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL Serveru platíte cenu za licenci SQL Serveru a Windows Serveru po minutách, a k tomu navíc náklady na úložiště Azure za disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud pro Azure použijete svoji vlastní licenci na SQL Server, bude se vám účtovat jenom cena za Windows Server a úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci
Když začnete používat cloudovou platformu, budou náklady na provozování vaší aplikace zahrnovat náklady na vývoj a správu spolu s náklady na služby platformy veřejného cloudu.

Tady je podrobný je výpočet nákladů na vaši aplikaci běžící v SQL Database a na SQL Serveru na virtuálních počítačích Azure:

**Při používání Azure SQL Database:**

*Celkové náklady na aplikaci = vysoce minimalizované náklady na správu + náklady na vývoj softwaru + náklady na službu SQL Database*

**Při používání SQL Serveru na virtuálních počítačích Azure:**

*Celkové náklady na aplikaci = minimalizované náklady na vývoj softwaru + náklady na správu + náklady na licence systému SQL Server a Windows Server + náklady na službu Azure Storage*

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

* [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
* [Virtual Machines – ceny](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

> [!NOTE]
> SQL Server má malou podmnožinu funkcí, které nejsou platné nebo dostupné při používání SQL Database. Další informace najdete v tématech [Funkce služby SQL Database](sql-database-features.md) a [Informace o jazyce Transact-SQL služby SQL Database](sql-database-transact-sql-information.md). Pokud do cloudu přesouváte existující řešení SQL Serveru, najdete další informace v tématu [Migrace databáze SQL Serveru do Azure SQL Database](sql-database-cloud-migrate.md). Při migraci stávající místní aplikace SQL Serveru do SQL Database zvažte možnost aktualizace aplikace, abyste mohli využívat funkce, které nabízejí cloudové služby. Můžete například zvážit hostování vaší aplikační vrstvy pomocí [Azure Web App Service](https://azure.microsoft.com/services/app-service/web/) nebo [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/), a zvýšit si tak efektivitu nákladů.
> 
> 

### <a name="administration"></a>Správa
Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. V případě služby **SQL Database** se o správu základního hardwaru stará Microsoft. Microsoft automaticky replikuje všechna data pro zajištění vysoké dostupnosti, konfiguruje a upgraduje databázový software, spravuje vyrovnávání zatížení a provádí transparentní převzetí služeb při selhání, pokud dojde k selhání serveru. Nadále můžete spravovat svoji databázi, ale nebudete už muset spravovat databázový stroj,operační systém serveru ani hardware.  Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení.

V případě systému **SQL Server na virtuálních počítačích Azure** máte plnou kontrolu nad konfigurací operačního systému a instance systému SQL Server. V případě virtuálních počítačů je na vás, kdy se rozhodnete aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako například antivirový program. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)
Pro řadu IT oddělení je nejvyšší prioritou plnit povinnosti z hlediska garantované doby provozuschopnosti vyplývající ze Smlouvy o úrovni služeb (SLA). V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **SQL Database** úrovně služeb Basic, Standard, Premium, obecné účely a zvláště důležité poskytuje Microsoft smlouvu SLA 99,99 % s dostupností. Nejnovější informace najdete v tématu věnovaném [Smlouvám o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/sql-database/). Nejnovější informace o úrovních služeb SQL Database a podporovaných plánech kontinuity podnikových najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md).

Pro **SQL Server běžící na virtuálních počítačích Azure** poskytuje Microsoft smlouvu SLA s dostupností 99,95 %, která se vztahuje pouze na virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro vysokou dostupnost (HA – High Availability) databází v rámci virtuálních počítačů musíte nakonfigurovat jednu z podporovaných možností vysoké dostupnosti SQL Serveru, jako jsou například [skupiny dostupnosti AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

### <a name="market"></a>Rychlost uvedení na trh
**Databáze SQL** je tím správným řešením pro aplikace navržené pro cloud, pokud je důležitým faktorem produktivita vývojářů a rychlé uvedení na trh. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze. Můžete například pomocí [REST API](http://msdn.microsoft.com/library/azure/dn505719.aspx) a [rutin prostředí PowerShell](http://msdn.microsoft.com/library/mt740629.aspx) automatizovat a spravovat operace správy pro tisíce databází. Funkce, jako jsou [elastické fondy](sql-database-elastic-pool.md), vám umožní zaměřit se na aplikační vrstvu a zajistit rychlejší dodání vašeho řešení na trh.

**SQL Server běžící na virtuálních počítačích Azure** je ideálním řešením, pokud vaše stávající nebo nové aplikace vyžadují rozsáhlé databáze, vzájemně propojené databáze nebo přístup ke všem funkcím systému SQL Server nebo Windows. Je také vhodné, pokud chcete migrovat existující místní aplikace a databáze do Azure tak, jak jsou. Vzhledem k tomu, že nemusíte měnit prezentační, aplikační ani datové vrstvy, ušetříte čas a finanční prostředky z rozpočtu, které byste jinak museli vynaložit na vytvoření nové architektury existujícího řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Souhrn
Tento článek se věnoval SQL Database a SQL Serveru na virtuálních počítačích Azure a běžným motivačním faktorům firem, které by mohly mít vliv na vaše rozhodnutí. Následuje souhrn návrhů, které doporučujeme zvážit:

**Databázi SQL Azure** zvolte, pokud:

* Sestavujete nové cloudové aplikace a chcete využít výhod cloudových služeb v podobě úspory nákladů a optimalizace výkonu. Tento přístup přináší výhody v podobě plně spravované cloudové služby, pomáhá zkrátit čas nutný k uvedení produktu na trh a může zajistit dlouhodobou optimalizaci nákladů.
* Budete chtít, aby Microsoft prováděl běžné operace správy vašich databází a vyžadovat pro databáze smlouvy SLA s vyšší dostupností.

**SQL Server na virtuálních počítačích Azure** zvolte, pokud:

* Máte stávající místní aplikace, které chcete migrovat nebo rozšířit do cloudu, nebo pokud chcete vytvořit podnikové aplikace, které jsou větší než 4 TB. Tento přístup přináší výhody v podobě 100% kompatibility jazyka SQL, velké kapacity databáze, plné kontroly nad systémem SQL Server a Windows a zabezpečené tunelové propojení s místními aplikacemi. Tento přístup minimalizuje náklady na vývoj a úpravy stávajících aplikací.
* Máte stávající prostředky IT a zvládnete zajistit použití dílčích oprav, zálohování a vysokou dostupnost databáze. Všimněte si, že některé automatizované funkce tyto operace značně zjednodušují. 

## <a name="next-steps"></a>Další postup
* Pokud začínáte se službou SQL Database, přečtěte si téma [První Azure SQL Database](sql-database-get-started-portal.md).
* Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
* Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

