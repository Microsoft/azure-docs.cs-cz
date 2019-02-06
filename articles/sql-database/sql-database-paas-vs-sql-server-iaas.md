---
title: Volba mezi možnostmi SQL v Azure | Dokumentace Microsoftu
description: Zjistěte, jak zvolit možnosti nasazení v rámci Azure SQL Database a mezi Azure SQL Database a SQL Server na virtuálních počítačích Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Cloud SQL Server, SQL Server v cloudu, databáze PaaS, cloudový SQL Server, DBaaS
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: ff0589b87ae031db5bda96dc238359bf9cfb3b11
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751595"
---
# <a name="choose-the-right-sql-server-option-in-azure"></a>Zvolte vpravo systému SQL Server v Azure

V Azure, můžete mít svoje úlohy systému SQL Server běží v prostředí infrastruktury (IaaS) nebo jako hostované služby ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). V rámci PaaS máte několik možností nasazení a úrovně služeb v rámci každé možnosti nasazení. Klíčovou otázkou, které budete muset požádat při rozhodování mezi PaaS nebo IaaS se chcete ke správě databáze, provedení oprav, zálohování, nebo chcete delegovat tyto operace do Azure?
V závislosti na odpovědi máte následující možnosti:

- [Azure SQL Database](sql-database-technical-overview.md): Plně spravovanými databázový stroj SQL, podle nejnovější stabilní Enterprise edici systému SQL Server. Toto je relační databáze jako služba (DBaaS hostovaná v cloudu Azure, která spadá do oborové kategorie) *Platform-as-a-Service (PaaS)*. SQL database má více možností nasazení, z nichž každý je postavená na standardizovaném hardwaru a softwaru, který je ve vlastnictví, hostován a spravován společností Microsoft. SQL Database, můžete použít integrované funkce, které vyžadují rozsáhlé konfiguraci při použití v systému SQL Server (místně nebo v Azure virtuální počítač). Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení. SQL Database má další funkce, které nejsou k dispozici v systému SQL Server, jako je například integrovanou vysokou dostupnost, inteligentní funkce a správu. Azure SQL Database nabízí následující možnosti nasazení:
  
  - Jako [izolované databáze](sql-database-single-database.md) s vlastní sadou prostředků spravovat přes server SQL Database. Izolovaná databáze je podobný [databáze s omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) v systému SQL Server. Tato možnost je optimalizovaná pro vývoj moderních aplikací nových cloudových aplikací.
  - [Elastického fondu](sql-database-elastic-pool.md), což je kolekce databází se sdílenou sadou prostředků spravovat přes server SQL Database. Izolované databáze můžete přesunout do a z elastického fondu. Tato možnost je optimalizovaná pro vývoj moderních aplikací z nových cloudových aplikací s použitím vícetenantovou aplikaci SaaS.
  - [Spravovaná instance](sql-database-managed-instance.md), což je kolekce systémových a uživatelských databází se sdílenou sadou prostředků. Managed instance je podobný instance nabídky [databázového stroje Microsoft SQL Server] sdílené prostředky pro databáze a další funkce s oborem instance. Spravovaná instance podporuje migrace databáze z místního s minimálními změn databáze. Tato možnost poskytuje všechny výhody PaaS služby Azure SQL Database, ale přidá funkce, které byly dříve k dispozici pouze ve virtuálních počítačích SQL. To zahrnuje nativní virtuální síť (VNet) a téměř 100 % kompatibilitu s místním SQL serverem.
- [SQL Server na virtuálních počítačích Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) spadá do oborové kategorie *Infrastructure-as--Service (IaaS)* a umožní vám spustit SQL Server uvnitř virtuálního počítače plně spravovaná v cloudu Azure. [Virtuální počítače s SQL serverem](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) spustit také na standardizovaném hardwaru, který je ve vlastnictví, hostován a spravován společností Microsoft. Při použití SQL serveru na virtuálním počítači, můžete platit – stejně jako průběžně k licenci systému SQL Server obsaženou v imagi systému SQL Server nebo jednoduše použít stávající licenci. Můžete také zastavit nebo obnovit virtuální počítač podle potřeby. SQL Server nainstalovaný a hostovaný v cloudu na Windows Server nebo virtuální počítače s Linuxem (VM) běžících v Azure, označované také jako infrastruktura jako služba (IaaS). SQL Server na virtuálních počítačích Azure je vhodný pro migraci místních databází SQL serveru a aplikace bez jakékoli změny databáze. Všechny nejnovější verze a edice systému SQL Server jsou dostupná k instalaci na virtuálním počítači IaaS. Nejdůležitější rozdíl mezi SQL Database je, že virtuální počítače SQL serveru umožňují plnou kontrolu nad databázovým strojem. Můžete zvolit při údržby nebo oprav se spustí, chcete-li změnit model obnovení simple nebo hromadně protokolované povolit rychlejší načítání méně protokolu, pozastavit nebo spustit modul, pokud je nepotřebujete, a můžete plně přizpůsobit databázovém stroji SQL serveru. Pro tento další ovládací prvek obsahuje přidané odpovědnost ke správě virtuálních počítačů.

V následující tabulce jsou uvedené hlavní rozdíly mezi těmito možnostmi:

| SQL Server na virtuálním počítači | Spravovaná instance SQL Database | Izolované databáze nebo elastického fondu ve službě SQL Database |
| --- | --- | --- |
|Máte plnou kontrolu nad databázovém stroji SQL Server.<br/>Až 99,95 % dostupnost.<br/>Úplnou paritu s odpovídající verzí na místní SQL Server.<br/>Oprava, dobře známé databázový stroj verze.<br/>Snadná migrace z místního SQL serveru.<br/>Privátní IP adresu v rámci virtuální sítě Azure.<br/>Máte možnost nasadit aplikace nebo služby na hostiteli, kde je umístěn SQL Server.| Vysokou kompatibilitu díky místním SQL serverem.<br/>99,99 % dostupnost zaručit.<br/>Integrované zálohování, použití dílčích oprav, obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Rychlou migraci z SQL serveru.<br/>Privátní IP adresu v rámci virtuální sítě Azure.<br/>Integrované pokročilé inteligentní funkce a zabezpečení.<br/>Online změny prostředků (procesoru a úložiště).|Nejčastěji používané funkce SQL serveru jsou k dispozici.<br/>99,99 % dostupnost zaručit.<br/>Integrované zálohování, použití dílčích oprav, obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Schopnost přidělit potřebných prostředků (procesoru/storage) pro jednotlivé databáze.<br/>Integrované pokročilé inteligentní funkce a zabezpečení.<br/>Online změny prostředků (procesoru a úložiště).|
|Potřebujete spravovat opravy a zálohování.<br>Budete muset implementovat řešení vysoké dostupnosti.<br/>Při změně resources(CPU/storage) je výpadek|Je stále některé minimální počet funkcí systému SQL Server, které nejsou k dispozici.<br/>Ne, zaručeno, že Údržba přesný čas (ale téměř transparentní).<br/>Kompatibilita s verzí SQL serveru můžete dosáhnout jenom pomocí úrovně kompatibility databáze.|Migrace z SQL serveru může být obtížné.<br/>Nejsou k dispozici některé funkce serveru SQL Server.<br/>Ne, zaručeno, že Údržba přesný čas (ale téměř transparentní).<br/>Kompatibilita s verzí SQL serveru můžete dosáhnout jenom pomocí úrovně kompatibility databáze.<br/>Nelze přiřadit privátní IP adresa (můžete omezit přístup pomocí pravidel brány firewall).|

Zjistěte, jak jednotlivé možnosti nasazení zapadají do datové platformy Microsoftu a získejte pomoc se spárováním té správné volby s požadavky vaší společnosti. Ať už je pro vás určující úspora nákladů nebo minimální režie z hlediska správy (které pro vás mají vyšší prioritu než cokoli jiného), tento článek vám může pomoci rozhodnout, jaký přístup přinese vaší firmě nejlepší výsledky z hlediska požadavků, na jejichž splnění vám nejvíc záleží.

## <a name="microsofts-sql-data-platform"></a>Datová platforma společnosti Microsoft SQL

Jednou z nejdůležitějších věcí, které je potřeba chápat, pokud se porovnávají výhody databází na Azure oproti využívání místních databází SQL Serveru, je to, že použitelné jsou všechny z nich. Datová platforma společnosti Microsoft využívá technologii SQL Serveru a zpřístupňuje ji napříč fyzickými místními počítači, prostředími privátního cloudu, prostředími privátního cloudu hostovanými třetí stranou a veřejným cloudem. SQL Server na virtuálních počítačích Azure vám umožňuje vyhovět jedinečným a rozmanitým potřebám firmy díky kombinaci místního nasazení a nasazení hostovaného v cloudu pomocí stejné sady serverových produktů, vývojových nástrojů a odborných znalosti v těchto prostředích.

   ![Možnosti SQL serveru v cloudu: Databáze systému SQL server na IaaS nebo SaaS SQL v cloudu.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak je vidět v diagramu, každou nabídku je možné charakterizovat úrovní správy, kterou nad infrastrukturou máte (na ose X), a stupněm nákladové efektivity dosahovaným konsolidací úrovně databáze a automatizace (na ose Y).

Při navrhování aplikace jsou pro hostování součásti aplikace s SQL Serverem k dispozici čtyři základní možnosti:

- SQL Server na nevirtualizovaných fyzických počítačích
- SQL Server v místních virtualizovaných počítačích (privátní cloud)
- SQL Server na virtuálním počítači Azure (veřejný cloud Microsoftu)
- Azure SQL Database (veřejný cloud Microsoftu)

V následujících částech se dozvíte o SQL serveru ve veřejném cloudu Microsoftu: Azure SQL Database a SQL Server na virtuálních počítačích Azure. Kromě toho prozkoumáte obvyklé motivační faktory firem pro určení toho, která možnost je pro konkrétní aplikaci nejvhodnější.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Bližší pohled na Azure SQL Database a SQL Server na virtuálních počítačích Azure

Obecně jsou tyto dvě možnosti SQL optimalizovány pro různé účely:

- **Azure SQL Database**

Optimalizována pro snížení celkové náklady na správu na minimum pro zřizování a správu velkého počtu databází. Snižuje náklady na průběžnou správu, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](sql-database-automated-backups.md). Obecně platí, že Azure SQL Database může výrazně zvýšit počet databází, které spravuje jeden IT nebo vývojářský zdroj. [Elastické fondy](sql-database-elastic-pool.md) také podporu architektury víceklientské aplikace SaaS s funkcí, včetně izolaci klientů a schopnost škálování snížit náklady na sdílení prostředků mezi databázemi. [Spravovaná instance](sql-database-managed-instance.md) poskytuje podporu pro funkce s oborem instance umožňuje snadnou migraci stávajících aplikací, jakož i sdílení prostředků mezi databázemi.

- **SQL Server běžící na virtuálních počítačích Azure**

Optimalizovaný pro migraci stávajících aplikací do Azure nebo rozšíření stávajících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. V případě používání SQL Serveru na virtuálních počítačích Azure máte úplná práva pro správu pro vyhrazenou instanci SQL Serveru a cloudové virtuální počítače. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.

Následující tabulka shrnuje hlavní vlastnosti SQL Database a SQL Serveru na virtuálních počítačích Azure:

| | Izolované databáze SQL Database a elastické fondy | SQL Database managed instance |Virtuální počítače Azure s SQL serverem |
| --- | --- | --- |---|
| **Nejvhodnější pro:** |Nové aplikace navržené pro cloud, které chcete použít nejnovější stabilní funkcí systému SQL Server a mají časová omezení z hlediska vývoje a marketingu. | Nové aplikace nebo stávající místní aplikace, které chcete použít nejnovější stabilní funkcí systému SQL Server a že se migrují do cloudu s minimálními změnami.  | Existující aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami nebo žádné změny. Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. |
|  | Týmy, které potřebují integrovanou vysokou dostupnost, možnost zotavení po havárii a možnost upgradu databáze. | Stejné jako jednoduchá a ve fondu databází SQL Database. | Týmy, které můžete nakonfigurovat, správně vyladit, přizpůsobení a správa vysoké dostupnosti, zotavení po havárii a opravy pro SQL Server. Některé poskytované automatizované funkce to značně zjednodušují. | |
|  | Týmy, které nechtějí spravovat příslušný operační systém a nastavení konfigurace. | Stejné jako jednoduchá a ve fondu databází SQL Database. | Budete potřebovat přizpůsobené prostředí s úplnými právy. | |
|  | Databáze o velikosti až 100 TB. | Až do 8 TB. | Instance systému SQL Server s úložištěm o velikosti až 64 TB. Instance může podporovat tolik databází, kolik je potřeba. |
| **Kompatibilita** | Podporuje většinu místních funkcí úrovni databáze. | Podporuje téměř všechny místní funkce na úrovni instance a databáze. | Podporuje všechny místních funkcí. |
| **Zdroje a prostředky:** | Nechcete využívat prostředky IT ke konfiguraci a správě základní infrastruktury, ale chcete se soustředit na aplikační vrstvu. | Stejné jako jednoduchá a ve fondu databází SQL Database. | Máte některé prostředky IT ke konfiguraci a správě. Některé poskytované automatizované funkce to značně zjednodušují. |
| **Celkové náklady na vlastnictví:** | Eliminuje náklady na hardware a snižuje náklady na správu. | Stejné jako jednoduchá a ve fondu databází SQL Database. | Eliminuje náklady na hardware. |
| **Kontinuita podnikových procesů:** |Kromě [funkcí infrastruktury s předdefinovanou odolnost proti chybám](sql-database-high-availability.md), Azure SQL Database nabízí funkce, jako například [automatické zálohování](sql-database-automated-backups.md), [v daném okamžiku obnovení](sql-database-recovery-using-backups.md#point-in-time-restore), [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore), [aktivní geografickou replikaci](sql-database-active-geo-replication.md), a [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md) pro zvýšení kontinuity. Další informace najdete v tématu [Databáze SQL – kontinuita podnikových procesů (přehled)](sql-database-business-continuity.md). | Stejně jako jednoduchá a ve fondu databází SQL Database, jen rozšířené o uživatelem iniciované, pouze kopie zálohy jsou k dispozici. | SQL Server na virtuálních počítačích Azure umožňuje nastavit vysoce dostupné řešení s možností zotavení po havárii pro konkrétní potřeby vaší databáze. Můžete tak mít systém, který je vysoce optimalizovaný pro vaši aplikaci. Sami podle potřeby můžete otestovat a spustit převzetí služeb při selhání. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridní cloud:** |Vaše místní aplikace mohou přistupovat k datům v Azure SQL Database. | [Implementace nativní virtuální sítě](sql-database-managed-instance-vnet-configuration.md) a připojení k vaší místní prostředí pomocí Azure Express Route nebo VPN Gateway. | V případě SQL Serveru na virtuálních počítačích Azure můžete mít aplikace, které běží částečně v cloudu a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Další informace o hybridní Cloudová řešení, najdete v části [rozšíření místních řešení pro data do cloudu](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Podporuje [transakční replikaci systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako předplatitel k replikaci dat. | Replikace je podporována pro spravovanou instanci jako funkci ve verzi preview. | Plně podporuje [transakční replikaci systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), [skupin dostupnosti Always On](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), integrační služby a přesouvání protokolu k replikaci dat. Navíc jsou plně podporované tradiční zálohy systému SQL Server. | |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivace firem pro zvolení Azure SQL Database nebo SQL Serveru na virtuálních počítačích Azure

Existuje několik faktorů, které můžou ovlivnit vaše rozhodnutí zvolit PaaS nebo IaaS k hostování vašich databází SQL:

- [Náklady na](#cost) -i PaaS a IaaS možnost zahrnovat základní ceny přidané titulní licencování a základní infrastruktury. Nicméně s možností IaaS musíte investovat další čas i prostředky ke správě databáze, zatímco v modelu PaaS se zobrazuje zahrnutá v ceně tyto funkce pro správu. IaaS umožňuje vypnutí vašich prostředků při jejich snížit náklady, zatímco verze PaaS bude vždy spuštěn, pokud nepoužíváte-li vyřadit a znovu vytvořit prostředky, když jsou potřeba.
- [Správa](#administration) – možnosti PaaS omezit množství času, které je potřeba investovat ke správě databáze. Ale to také nebudete vykonávat některé vlastní správy, které může zlepšit výkon vašich úloh.
- [Smlouvu o úrovni služeb](#service-level-agreement-sla) -i IaaS a PaaS poskytují nejvyšší, oborový standard smlouvu SLA. Možnost PaaS zaručuje 99,99 % smlouva SLA, zatímco IaaS zaručuje GARANTOVANÁ dostupnost 99,95 % pro infrastrukturu, což znamená, že je potřeba implementovat další mechanismy pro zajištění dostupnosti vaší databáze. V extrémním případě Pokud chcete implementovat řešení vysoké dostupnosti, které je odpovídající PaaS, potřebujete vytvořit další systému SQL Server na virtuálním počítači a konfigurace skupin dostupnosti AlwaysOn, které může být dvojí náklady na vaši databázi.
- [Čas do Azure přesunout](#market) – SQL Server na virtuálním počítači Azure je přesnou shodu prostředí, proto migrace z místního na virtuální počítač SQL Azure není liší od přesunutí databází z jednoho místního serveru na jiný. Spravovaná instance také umožňuje velmi snadno migrace; může však být některé změny, které je potřeba ještě před migrací do spravované instance.

Tyto faktory probereme podrobněji v následujících částech.

### <a name="cost"></a>Náklady

Ať už jste začínající společnost, která nemá mnoho peněz, nebo tým v zavedené společnosti, který pracuje s limitovaným rozpočtem, je omezená výše prostředků často primárním rozhodujícím faktorem při volbě řešení pro hostování firemních databází. V této části se dozvíte o fakturaci a licencování základy v Azure s ohledem na tyto dvě volby relačních databází: SQL Database a SQL Server na virtuálních počítačích Azure. Zjistíte také, jak vypočítat celkové náklady na aplikaci.

#### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování

V současné době **SQL Database** se prodává jako služba a je k dispozici několik možností nasazení a několik úrovní služeb s jinými cenami pro prostředky, které se účtují po hodinách s pevnou sazbou na základě na úrovni služby a Vypočítat velikost, kterou zvolíte. Nejnovější informace o aktuálně podporovaných cenových úrovních služeb najdete compute velikosti a množství úložiště, najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

- Díky izolovanou databázi SQL Database můžete rozhodnout o úrovni služeb, který nejlépe vyhovuje vašim potřebám z široké škály ceny od 5$ měsíčně pro úroveň basic.
- Můžete vytvořit [elastické fondy](sql-database-elastic-pool.md) sdílení prostředků mezi různými instancemi databáze ke snížení nákladů a využití zohlednit špičky.
- Spravované instance SQL Database můžete také přinést vlastní licenci. Další informace o používání vlastní licence, najdete v části [mobilitu licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/) nebo použijte [kalkulačky pro program Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) zobrazíte jak **ušetříte až 40 %**.

Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Můžete dynamicky upravit úrovně služeb a vypočítat velikost tak, aby odpovídaly potřebám různých propustnosti vaší aplikace.

V případě **SQL Database** je databázový software konfigurován, opravován a upgradován automaticky společností Microsoft, což snižuje náklady na správu. Kromě toho vám [integrované funkce zálohování](sql-database-automated-backups.md) pomůžou dosáhnout výrazných úspor nákladů, hlavně v případě, že máte velký počet databází.

V případě systému **SQL Server na virtuálních počítačích Azure** můžete použít jakoukoli image systému SQL Server poskytovanou platformou (která zahrnuje licenci) nebo použít svou vlastní licenci systému SQL Server. K dispozici jsou všechny podporované verze systému SQL Server (2008R2, 2012, 2014, 2016) a jeho edice (Developer, Express, Web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL serveru platíte za minutu licenční náklady na SQL Server a Windows nebo Linux Server spolu s náklady na úložiště Azure pro disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud jste do Azure přinést vlastní licenci systému SQL Server, bude vám účtována serveru a jenom náklady na úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci

Když začnete používat cloudovou platformu, zahrnuje náklady na provozování vaší aplikace náklady pro vývoj nových projektů a náklady na průběžnou správu a náklady na služby platformy veřejného cloudu.

**Při používání Azure SQL Database:**

- Vysoce minimalizované náklady na správu
- Náklady na vývoj omezené pro migrované aplikace (spravované instance)
- Náklady na službu SQL Database
- Žádné nákupní náklady na hardware

**Při používání SQL Serveru na virtuálních počítačích Azure:**

- Vyšší náklady na správu
- Omezeno na žádné náklady na vývoj pro migrované aplikace
- Náklady na službu virtuálního počítače
- Žádné nákupní náklady na hardware

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

- [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
- [Ceny virtuálních počítačů](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. IaaS a PaaS, Microsoft spravuje základní infrastrukturu a automaticky replikuje všechna data pro zajištění zotavení po havárii, konfiguruje a upgraduje databázový software, spravuje Vyrovnávání zatížení a provádí transparentní převzetí služeb při selhání, pokud dojde Chyba serveru v rámci datového centra.

- S **Azure SQL Database**, můžete nadále spravovat svoji databázi, ale už muset spravovat databázový stroj, operační systém ani hardware. Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Kromě toho Konfigurace vysoké dostupnosti do jiného datového centra vyžaduje minimální konfiguraci a správu.
- V případě systému **SQL Server na virtuálních počítačích Azure** máte plnou kontrolu nad konfigurací operačního systému a instance systému SQL Server. V případě virtuálních počítačů je na vás, kdy se rozhodnete aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako například antivirový program. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)

Pro řadu IT oddělení je nejvyšší prioritou plnit povinnosti z hlediska garantované doby provozuschopnosti vyplývající ze Smlouvy o úrovni služeb (SLA). V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **SQL Database**, poskytuje Microsoft smlouvu SLA zajišťující 99,99 % dostupností. Nejnovější informace najdete v tématu věnovaném [Smlouvám o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/sql-database/).

Pro **SQL Server běžící na virtuálních počítačích Azure** poskytuje Microsoft smlouvu SLA s dostupností 99,95 %, která se vztahuje pouze na virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Databáze vysoké dostupnosti (HA) v rámci virtuálních počítačů, musíte nakonfigurovat jednu z možností podporovaných vysokou dostupnost v systému SQL Server, jako [skupin dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

### <a name="market"></a>Čas přesunout do Azure

**Izolované databáze SQL Database nebo elastické fondy** jsou má to pravé řešení pro cloudové aplikace, když je nejdůležitější produktivitu vývojářů a rychlou dobu uvedení na trh nová řešení. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze.

**SQL Database managed instance** výrazně zjednodušuje migraci stávajících aplikací do Azure SQL Database, můžete k tomu migrovaná databáze dostaly rychle uvedla na trh v Azure.

**SQL Server běžící na virtuálních počítačích Azure** je ideálním řešením, pokud vaše existující nebo nové aplikace vyžadují velké databáze nebo přístup ke všem funkcím systému SQL Server nebo Windows/Linux a chcete se vyhnout čas a náklady na získávání nových v místním hardwaru. Je také vhodné, pokud chcete provést migraci stávajících místních aplikací a databází do Azure jako-je – v případech, kde spravované instance Azure SQL Database není vhodné. Protože nemusíte měnit prezentační, aplikace ani datové vrstvy, ušetříte čas a rozpočtu na předělávání architektury vaší existující řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Další postup

- Pokud začínáte se službou SQL Database, přečtěte si téma [První Azure SQL Database](sql-database-single-database-get-started.md).
- Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
- Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
