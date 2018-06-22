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
ms.date: 06/20/2018
ms.author: carlrab
ms.openlocfilehash: a212a595c02a048721f1a8753b437f74f2fc4973
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308950"
---
# <a name="choose-a-cloud-sql-server-option-azure-sql-paas-database-or-sql-server-on-azure-vms-iaas"></a>Volba cloudového řešení systému SQL Server: Azure SQL (PaaS) Database nebo SQL Server na virtuálních počítačích Azure (IaaS)

V Azure, může mít vaše úlohy SQL serveru běží v hostované infrastruktury (IaaS) nebo jako hostované služby ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)):

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): databázový stroj A SQL, podle Enterprise edice SQL serveru, která je optimalizovaná pro vývoj moderních aplikací. Azure SQL Database nabízí dvě verze SQL jako hostované služby: logické servery a [Azure databáze spravované instance SQL (preview)](sql-database-managed-instance.md). Databáze SQL Azure s obě verze přidá další funkce, které nejsou k dispozici v systému SQL Server, jako například vestavěné inteligentní a správa. První verzí může mít logický server obsahující [jedna databáze](sql-database-servers-databases.md) a můžete seskupit servery do [elastický fond](sql-database-elastic-pool.md) sdílet prostředky a snížit náklady. Logického serveru Azure SQL Database obsahující databáze jeden a ve fondu nabízí většina funkcí obor databáze systému SQL Server. S Azure SQL spravované Instance databáze Azure SQL Database nabízí sdílené prostředky pro databáze a další funkce obor instance. Azure SQL Database spravované Instance podporuje migraci databáze s minimálním ke změně databáze.
* [SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server nainstalovaný a hostovaný v cloudu na Windows Server nebo Linux virtuální počítače (VM) spuštěné v Azure, také označované jako infrastruktura jako služba (IaaS). SQL Server na virtuálních počítačích Azure je vhodný pro migraci místní databáze SQL serveru a aplikace bez jakékoli změny databáze. Všechny poslední verze a edice systému SQL Server jsou k dispozici pro instalaci na virtuálním počítači IaaS. Nejdůležitější rozdíl z databáze SQL je, že virtuálním počítačům systému SQL Server umožňují plnou kontrolu nad databázového stroje. Můžete zvolit při údržby nebo opravy spustí, chcete-li změnit model obnovení simple nebo hromadně protokolované povolit rychlejší zatížení menší protokolu pozastavení nebo spustit modul v případě potřeby a můžete plně přizpůsobit databázový stroj SQL Server. Pro tento další ovládací prvek se dodává s přidané zodpovědnost za správu virtuálních počítačů.

Zjistěte, jak jednotlivé možnosti zapadají do datové platformy společnosti Microsoft, a získejte pomoc se spárováním té správné volby s požadavky vaší firmy. Ať už je pro vás určující úspora nákladů nebo minimální režie z hlediska správy (které pro vás mají vyšší prioritu než cokoli jiného), tento článek vám může pomoci rozhodnout, jaký přístup přinese vaší firmě nejlepší výsledky z hlediska požadavků, na jejichž splnění vám nejvíc záleží.

## <a name="microsofts-sql-data-platform"></a>Datová platforma společnosti Microsoft SQL

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

**Azure SQL Database** je relační databáze jako služba (DBaaS) hostovaná v cloudu Azure, která spadá do oborové kategorie z *platformy jako služba (PaaS)*. [SQL Database](sql-database-technical-overview.md) je postavená na standardizovaném hardwaru a softwaru, který vlastní, hostuje a spravuje Microsoft. V databázi SQL můžete použít integrované funkce, které vyžadují rozsáhlé konfiguraci v systému SQL Server. Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení. Azure SQL Database, s podporou pro oba [jedna databáze](sql-database-servers-databases.md) a [elastické fondy](sql-database-elastic-pool.md) pro sdílení prostředků, je ideální prostředí pro vývoj nových aplikací v cloudu. A s [Azure SQL Database spravované Instance](sql-database-managed-instance.md), můžete zahrnout vlastní licenci. Kromě toho tato možnost poskytuje všechny výhody PaaS databáze SQL Azure, ale přidá funkce, které byly dřív dostupné jenom ve virtuálních počítačích SQL. To zahrnuje nativní virtuální síť (VNet) a téměř 100 % kompatibilitu s místním SQL serveru. [Spravované Instance](sql-database-managed-instance.md) je ideální pro místní databázi s minimálními změnami požadované migrací do Azure. 

**SQL Server na virtuálních počítačích Azure** spadá do oborové kategorie *IaaS* a umožňuje vám spustit SQL Server uvnitř virtuálního počítače v cloudu. [Virtuální počítače systému SQL Server](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) spustit také na standardizovaném hardwaru, který je ve vlastnictví, hostován a spravován společností Microsoft. Při použití SQL serveru na virtuálním počítači, můžete buď platím – stejně jako jste cestu pro licenci systému SQL Server, který je již součástí bitové kopie systému SQL Server nebo snadno použít stávající licence. Můžete také zastavit nebo obnovit virtuální počítač podle potřeby.

Obecně jsou tyto dvě možnosti SQL optimalizovány pro různé účely:

* **Azure SQL Database** je optimalizovaná tak, aby snížit celkové náklady na správu na minimum pro zřizování a správu velkého počtu databází. Snižuje náklady na průběžnou správu, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](sql-database-automated-backups.md). Obecně platí, že Azure SQL Database může výrazně zvýšit počet databází, které spravuje jeden IT nebo vývojářský zdroj. [Elastické fondy](sql-database-elastic-pool.md) také podporovat architektury víceklientské aplikace SaaS s funkcemi, včetně izolaci mezi klienty a schopnost škálování ke snížení nákladů díky sdílení prostředků mezi databázemi. [Azure SQL Database spravované Instance](sql-database-managed-instance.md) poskytuje podporu pro obor instance funkce povolení snadno migrace existujících aplikací, jakož i sdílení prostředků mezi databází.
* **SQL Server běžící na virtuálních počítačích Azure** je optimalizovaný pro migraci stávajících aplikací do Azure nebo rozšíření stávajících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. V případě používání SQL Serveru na virtuálních počítačích Azure máte úplná práva pro správu pro vyhrazenou instanci SQL Serveru a cloudové virtuální počítače. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.

Následující tabulka shrnuje hlavní vlastnosti SQL Database a SQL Serveru na virtuálních počítačích Azure:

| | Azure SQL Database<br>Logické servery, elastické fondy a izolované databáze | Azure SQL Database<br>MI |Virtuální počítače Azure<br>SQL Server |
| --- | --- | --- |---|
| **Nejvhodnější pro:** |Nové aplikace navržené pro cloud, které chcete použít nejnovější stabilní systému SQL Server funkce andhave časová omezení z hlediska vývoje a marketingu. | Nové aplikace nebo existující místní aplikace, které chcete použít nejnovější stabilní funkcí systému SQL Server a že se migrují do cloudu s minimálními změnami.  | Existující aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami nebo žádné změny. Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. |
|  | Týmy, které potřebují integrovanou vysokou dostupnost, možnost zotavení po havárii a možnost upgradu databáze. | Stejné jako databáze SQL. | Týmy, které můžete nakonfigurovat, bez problémů ladit, přizpůsobení a správa vysokou dostupnost, zotavení po havárii a opravy pro SQL Server. Některé poskytované automatizované funkce to značně zjednodušují. | |
|  | Týmy, které nechtějí spravovat příslušný operační systém a nastavení konfigurace. | Stejné jako databáze SQL. | Budete potřebovat přizpůsobené prostředí s plná práva pro správu. | |
|  | Databáze až 4 TB nebo větší databáze, které může být [vodorovně nebo svisle na oddíly](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) pomocí vzoru Škálováním na více systémů. | Stejné jako databáze SQL. | Instance systému SQL Server s úložištěm o velikosti až 64 TB. Instance může podporovat tolik databází, kolik je potřeba. |
| **Kompatibilita** | Podporuje většinu místních funkcí úroveň databáze. | Podporuje téměř všechny místní instanci databáze úrovni a možnosti. | Podporuje všechny místní možnosti. |
| **Zdroje a prostředky:** | Nechcete využívat prostředky IT ke konfiguraci a správě základní infrastruktury, ale chcete se soustředit na aplikační vrstvu. | Stejné jako databáze SQL. | Máte některé prostředky IT ke konfiguraci a správě. Některé poskytované automatizované funkce to značně zjednodušují. |
| **Celkové náklady na vlastnictví:** | Eliminuje náklady na hardware a snižuje náklady na správu. | Stejné jako databáze SQL. | Eliminuje náklady na hardware. |
| **Kontinuita podnikových procesů:** |Kromě [možnosti infrastruktury předdefinované odolnost proti chybám](sql-database-high-availability.md), Azure SQL Database poskytuje funkce, jako například [automatizované zálohování](sql-database-automated-backups.md), [v daném okamžiku obnovení](sql-database-recovery-using-backups.md#point-in-time-restore), [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore), a [převzetí služeb při selhání skupiny a aktivní geografickou replikací](sql-database-geo-replication-overview.md) pro zvýšení kontinuity podnikových procesů. Další informace najdete v tématu [Databáze SQL – kontinuita podnikových procesů (přehled)](sql-database-business-continuity.md). | Stejné jako databázi SQL a uživatel spustil, pouze kopie zálohy jsou k dispozici. | SQL Server na virtuálních počítačích Azure umožňuje nastavit vysoce dostupné řešení s možností zotavení po havárii pro konkrétní potřeby vaší databáze. Můžete tak mít systém, který je vysoce optimalizovaný pro vaši aplikaci. Sami podle potřeby můžete otestovat a spustit převzetí služeb při selhání. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridní cloud:** |Vaše místní aplikace mohou přistupovat k datům v Azure SQL Database. | [Implementace nativní virtuální sítě](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-managed-instance-vnet-configuration) a připojení k prostředí místní pomocí Azure Express trasy nebo brány sítě VPN. | V případě SQL Serveru na virtuálních počítačích Azure můžete mít aplikace, které běží částečně v cloudu a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Kromě toho můžete uložit místní datové soubory v úložišti Azure pomocí [datových souborů SQL Serveru v Azure](http://msdn.microsoft.com/library/dn385720.aspx). Další informace najdete v tématu [Úvod do hybridního cloudu SQL Serveru 2014](http://msdn.microsoft.com/library/dn606154.aspx). |
|  | Podporuje [transakční replikaci systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako předplatitel k replikaci dat. | Replikace není podporována pro spravované instanci serveru Azure SQL Database. | Plně podporuje [transakční replikace systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [skupin dostupnosti Always On](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), Integration Services a přesouvání protokolu chcete replikovat data. Navíc jsou plně podporované tradiční zálohy systému SQL Server. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivace firem pro zvolení Azure SQL Database nebo SQL Serveru na virtuálních počítačích Azure
### <a name="cost"></a>Náklady
Ať už jste začínající společnost, která nemá mnoho peněz, nebo tým v zavedené společnosti, který pracuje s limitovaným rozpočtem, je omezená výše prostředků často primárním rozhodujícím faktorem při volbě řešení pro hostování firemních databází. V tomto oddílu zjistíte základní informace o fakturaci a licencování v Azure s ohledem na tyto dvě možnosti relačních databází: SQL Database a SQL Server na virtuálních počítačích Azure. Zjistíte také, jak vypočítat celkové náklady na aplikaci.

#### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování

V současné době **SQL Database** se prodává jako služba a je k dispozici v několika úrovně služeb s různými cenami pro prostředky, všechny z nich se účtují HODINOVĚ s pevnou sazbou na základě služby a výkonu úrovně zvolíte. S Instance databáze serveru SQL spravovaná můžete taky zahrnout vlastní licenci. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Můžete dynamicky upravit úrovně služeb a úrovně výkonu tak, aby odpovídaly potřebám vaší aplikace rozmanitých propustnost. Nejnovější informace o aktuálně podporovaných cenových úrovních služeb najdete v tématu [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) a [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md). Můžete také vytvořit [elastické fondy](sql-database-elastic-pool.md) ke sdílení prostředků mezi instancí databáze snížit náklady a přizpůsobení využití špičky.

V případě **SQL Database** je databázový software konfigurován, opravován a upgradován automaticky společností Microsoft, což snižuje náklady na správu. Kromě toho vám [integrované funkce zálohování](sql-database-automated-backups.md) pomůžou dosáhnout výrazných úspor nákladů, hlavně v případě, že máte velký počet databází. 

V případě systému **SQL Server na virtuálních počítačích Azure** můžete použít jakoukoli image systému SQL Server poskytovanou platformou (která zahrnuje licenci) nebo použít svou vlastní licenci systému SQL Server. K dispozici jsou všechny podporované verze systému SQL Server (2008R2, 2012, 2014, 2016) a jeho edice (Developer, Express, Web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL serveru platíte za minutu licenční náklady na serveru SQL Server a Windows nebo Linux Server spolu s náklady na úložiště Azure pro disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud jste přineste si vlastní licenci na SQL Server do Azure, budou se vám účtovat pro server a pouze náklady na úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci
Když začnete používat cloudovou platformu, zahrnuje náklady na provozování vaší aplikace náklady pro nový vývoj a náklady na průběžnou správu a náklady na služby platforma veřejného cloudu.

**Při používání Azure SQL Database:**

- Vysoce minimalizované náklady na správu
- Náklady na vývoj omezené pro migrované aplikace
- Náklady na služby SQL Database
- Žádné nákupu náklady na hardware

**Při používání SQL Serveru na virtuálních počítačích Azure:**

- Vyšší náklady na správu
- Omezeno na žádné náklady na vývoj pro migrované aplikace
- Náklady na službu virtuálního počítače
- Náklady na licence systému SQL Server
- Žádné nákupu náklady na hardware

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

* [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
* [Virtual Machines – ceny](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
* [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa
Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. S IaaS a PaaS, Microsoft spravuje odpovídající infrastruktury a automaticky replikuje všechna data pro zajištění zotavení po havárii, konfiguruje a upgraduje databázový software, spravuje Vyrovnávání zatížení a nemá transparentní převzetí služeb při selhání, pokud je selhání serveru v rámci datového centra. 

- S **Azure SQL Database**, můžete dál spravovat vaše databáze, ale už muset spravovat databázový stroj, serverový operační systém nebo hardwaru.  Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Kromě toho Konfigurace vysoké dostupnosti do jiného datového centra vyžaduje minimální konfigurace a správy.
- V případě systému **SQL Server na virtuálních počítačích Azure** máte plnou kontrolu nad konfigurací operačního systému a instance systému SQL Server. V případě virtuálních počítačů je na vás, kdy se rozhodnete aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako například antivirový program. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/windows/sizes.md). 

### <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)
Pro řadu IT oddělení je nejvyšší prioritou plnit povinnosti z hlediska garantované doby provozuschopnosti vyplývající ze Smlouvy o úrovni služeb (SLA). V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **SQL Database**, společnost Microsoft poskytuje dostupnost SLA 99,99 %. Nejnovější informace najdete v tématu věnovaném [Smlouvám o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/sql-database/). 

Pro **SQL Server běžící na virtuálních počítačích Azure** poskytuje Microsoft smlouvu SLA s dostupností 99,95 %, která se vztahuje pouze na virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pro databáze vysokou dostupnost (HA) v rámci virtuálních počítačů, měli byste nakonfigurovat jednu z možností podporované vysoké dostupnosti v systému SQL Server, jako [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

### <a name="market"></a>Když chcete přesunout do Azure
**Logické servery SQL Database, elastické fondy a izolované databáze** je správným řešením pro aplikace navržené pro cloud, když je důležité produktivita vývojářů a rychlé čas na trh noew řešení. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze. 

**Spravované Instance databáze SQL** výrazně zjednodušuje migraci existujících aplikací do Azure SQL Database, umožňuje přepněte migrované databáze aplikace na trh rychle v Azure.

**SQL Server běžící na virtuálních počítačích Azure** je ideální, pokud existující nebo nové aplikace vyžadují velké databáze nebo přístup ke všem funkcím v systému SQL Server nebo Windows nebo Linuxem a budete chtít vyhnout na čas a náklady získání nového hardwaru místní. Je také vhodné, pokud chcete migrovat existující místní aplikace a databáze do Azure jako-je – v případech, kdy Azure SQL Database spravované Instance není vhodné. Vzhledem k tomu, že nemusíte měnit prezentační, aplikační ani datové vrstvy, ušetříte čas a finanční prostředky z rozpočtu, které byste jinak museli vynaložit na vytvoření nové architektury existujícího řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="summary"></a>Souhrn
Tento článek se věnoval SQL Database a SQL Serveru na virtuálních počítačích Azure a běžným motivačním faktorům firem, které by mohly mít vliv na vaše rozhodnutí. Následuje souhrn návrhů, které doporučujeme zvážit:

**Databázi SQL Azure** zvolte, pokud:

* Sestavujete nové cloudové aplikace a chcete využít výhod cloudových služeb v podobě úspory nákladů a optimalizace výkonu. Tento přístup přináší výhody v podobě plně spravované cloudové služby, pomáhá zkrátit čas nutný k uvedení produktu na trh a může zajistit dlouhodobou optimalizaci nákladů.
* Budete chtít, aby Microsoft prováděl běžné operace správy vašich databází a vyžadovat pro databáze smlouvy SLA s vyšší dostupností.
* Chcete migrovat existující aplikace jako-je k provádění příkazu spravované Instance databáze SQL Azure a využít další parita s SQL Server nebo pokročilým zabezpečením a sítě. Spravované Instance je dobrou volbou pro nové i stávající aplikace.

**SQL Server na virtuálních počítačích Azure** zvolte, pokud:

* Máte stávající místní aplikace, které chcete migrovat nebo rozšířit do cloudu, nebo pokud chcete vytvořit podnikové aplikace, které jsou větší než 4 TB. Tento přístup poskytuje výhodu pomocí systému SQL Server verze a edice volba, kapacity velké databáze, plnou kontrolu nad systému SQL Server a Windows nebo Linuxem a zabezpečené tunelové propojení místně. Tento přístup minimalizuje náklady na vývoj a úpravy stávajících aplikací.
* Máte stávající prostředky IT a zvládnete zajistit použití dílčích oprav, zálohování a vysokou dostupnost databáze. Všimněte si, že některé automatizované funkce tyto operace značně zjednodušují. 

## <a name="next-steps"></a>Další postup

* Pokud začínáte se službou SQL Database, přečtěte si téma [První Azure SQL Database](sql-database-get-started-portal.md).
* Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
* Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).