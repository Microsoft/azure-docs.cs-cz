---
title: Výběr mezi volbami SQL v Azure | Microsoft Docs
description: Naučte se vybírat mezi možnostmi nasazení v rámci Azure SQL Database a mezi Azure SQL Database a SQL Server na virtuálních počítačích Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: Cloud SQL Server, SQL Server v cloudu, databáze PaaS, cloudový SQL Server, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/11/2019
ms.openlocfilehash: fc0204e774e66e9304887f47377a102df1b828cb
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884334"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Volba správného nasazení v Azure SQL

V Azure můžete mít vaše SQL Server úlohy spuštěné v hostované infrastruktuře (IaaS) nebo běží jako hostovaná služba ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)). V rámci PaaS máte v rámci každé možnosti nasazení více možností nasazení a úrovní služeb. Klíčovou otázkou, kterou potřebujete klást při rozhodování mezi PaaS nebo IaaS, chcete spravovat svou databázi, použít opravy, provést zálohování nebo chcete tyto operace delegovat na Azure?
V závislosti na odpovědi máte následující možnosti:

- [Azure SQL Database](sql-database-technical-overview.md): Plně spravovaný stroj SQL Database založený na nejnovější stabilní verzi Enterprise SQL Server. Toto je relační databáze jako služba (DBaaS) hostovaná v cloudu Azure, která spadá do oborové kategorie *platformy jako služby (PaaS)* . SQL Database má několik možností nasazení, z nichž každý je postaven na standardizovaném hardwaru a softwaru, který je vlastněn, hostován a spravován společností Microsoft. Pomocí SQL Database můžete používat integrované funkce a funkce, které vyžadují rozsáhlou konfiguraci při použití v SQL Server (místně nebo na virtuálním počítači Azure). Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení. SQL Database má další funkce, které nejsou k dispozici v SQL Server, jako je například integrovaná vysoká dostupnost, inteligentní funkce a správa. Azure SQL Database nabízí následující možnosti nasazení:
  
  - Jako [jediná databáze](sql-database-single-database.md) s vlastní sadou prostředků spravovaných prostřednictvím serveru SQL Database. Jedna databáze je podobná databázi s [omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) v SQL Server. Tato možnost je optimalizovaná pro vývoj moderních aplikací pro nové aplikace, které se zanesou do cloudu.
  - [Elastický fond](sql-database-elastic-pool.md), což je kolekce databází se sdílenou sadou prostředků spravovaných prostřednictvím serveru SQL Database. Jednotlivé databáze lze přesunout do elastického fondu nebo z něj. Tato možnost je optimalizovaná pro vývoj moderních aplikací pro nové aplikace v cloudu, a to pomocí aplikace SaaS pro více tenantů.
  - [Spravovaná instance](sql-database-managed-instance.md), která je kolekcí systémových a uživatelských databází se sdílenou sadou prostředků. Spravovaná instance je podobná instanci nástroje [Microsoft SQL Server Database Engine], která nabízí sdílené prostředky pro databáze a další funkce v rozsahu instance. Spravovaná instance podporuje migraci databáze z místního prostředí s minimálními nároky na změnu databáze. Tato možnost poskytuje všechny PaaS výhody Azure SQL Database, ale přidává funkce, které byly dříve dostupné jenom ve virtuálních počítačích SQL. To zahrnuje nativní virtuální síť (VNet) a téměř 100% kompatibilitu s místními SQL Server.
- [SQL Server v Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) spadají do kategorie oboru *IaaS (infrastruktura jako služba)* a umožňuje spouštět SQL Server v plně spravovaném virtuálním počítači v cloudu Azure. [SQL Server virtuální počítače](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md) také běží na standardizovaném hardwaru, který je vlastněn, hostován a spravován společností Microsoft. Při použití SQL Server na virtuálním počítači můžete platit průběžně s platbou SQL Server licence, která už je zahrnutá v SQL Server imagi, nebo snadno použít stávající licenci. Virtuální počítač můžete také zastavit nebo obnovit podle potřeby. SQL Server nainstalovaná a hostovaná v cloudu na virtuálních počítačích s Windows serverem nebo Linux spuštěných v Azure, označovaných také jako infrastruktura jako služba (IaaS). SQL Server na virtuálních počítačích Azure je vhodná možnost migrace místních databází SQL Server a aplikací bez změny v databázi. Všechny poslední verze a edice SQL Server jsou k dispozici pro instalaci na virtuálním počítači s IaaS. Nejvýznamnějším rozdílem z SQL Database je to, že SQL Server virtuální počítače umožňují plnou kontrolu nad databázovým strojem. Můžete zvolit, kdy se má spustit Údržba/opravy, změnit model obnovení na jednoduché nebo Hromadně protokolované, pozastavit nebo spustit službu v případě potřeby a můžete plně přizpůsobit SQL Server databázový stroj. Tento další ovládací prvek přinese zodpovědnost za správu virtuálního počítače.

Hlavní rozdíly mezi těmito možnostmi jsou uvedené v následující tabulce:

| SQL Server na virtuálním počítači | Spravovaná instance v SQL Database | Jedna databáze/elastický fond v SQL Database |
| --- | --- | --- |
|Máte plnou kontrolu nad SQL Server modulem.<br/>Až 99,99% dostupnost.<br/>Úplná parita se stejnou verzí místních SQL Server.<br/>Opravená, dobře známá verze databázového stroje.<br/>Snadná migrace z SQL Server v místním prostředí.<br/>Privátní IP adresa v rámci virtuální sítě Azure.<br/>Máte možnost nasadit aplikace nebo služby na hostitele, kde je umístěn SQL Server.| Vysoká kompatibilita s SQL Serverem v místním prostředí.<br/>je zaručeno 99,99% dostupnosti.<br/>Předdefinované zálohy, opravy a obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Snadná migrace z SQL Server.<br/>Privátní IP adresa v rámci virtuální sítě Azure.<br/>Integrovaná Pokročilá logika a zabezpečení<br/>Online Změna prostředků (CPU/Storage).|K dispozici jsou nejčastěji používané SQL Server funkce.<br/>je zaručeno 99,99% dostupnosti.<br/>Předdefinované zálohy, opravy a obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Možnost přiřazení nezbytných prostředků (CPU/úložiště) k jednotlivým databázím.<br/>Integrovaná Pokročilá logika a zabezpečení<br/>Online Změna prostředků (CPU/Storage).|
|Musíte spravovat zálohy a opravy.<br>Musíte implementovat vlastní řešení s vysokou dostupností.<br/>Při změně prostředků (CPU/úložiště) dochází k výpadku.|Stále je k dispozici nějaký minimální počet funkcí SQL Server, které nejsou k dispozici.<br/>Žádná záruka přesné doby údržby (ale skoro transparentní).<br/>Kompatibilitu s SQL Server verzí lze dosáhnout pouze pomocí úrovní kompatibility databáze.|Migrace z SQL Server může být těžká.<br/>Některé funkce SQL Server nejsou k dispozici.<br/>Žádná záruka přesné doby údržby (ale skoro transparentní).<br/>Kompatibilitu s SQL Server verzí lze dosáhnout pouze pomocí úrovní kompatibility databáze.<br/>Privátní IP adresu nelze přiřadit (přístup můžete omezit pomocí pravidel brány firewall).|

Přečtěte si, jak jednotlivé možnosti nasazení zapadají do datové platformy Microsoftu, a Získejte nápovědu, která odpovídá správné možnosti pro vaše obchodní požadavky. Ať už je pro vás určující úspora nákladů nebo minimální režie z hlediska správy (které pro vás mají vyšší prioritu než cokoli jiného), tento článek vám může pomoci rozhodnout, jaký přístup přinese vaší firmě nejlepší výsledky z hlediska požadavků, na jejichž splnění vám nejvíc záleží.

## <a name="microsofts-sql-data-platform"></a>Datová platforma SQL společnosti Microsoft

Jednou z nejdůležitějších věcí, které je potřeba chápat, pokud se porovnávají výhody databází na Azure oproti využívání místních databází SQL Serveru, je to, že použitelné jsou všechny z nich. Datová platforma společnosti Microsoft využívá technologii SQL Serveru a zpřístupňuje ji napříč fyzickými místními počítači, prostředími privátního cloudu, prostředími privátního cloudu hostovanými třetí stranou a veřejným cloudem. SQL Server na virtuálních počítačích Azure vám umožňuje vyhovět jedinečným a rozmanitým potřebám firmy díky kombinaci místního nasazení a nasazení hostovaného v cloudu pomocí stejné sady serverových produktů, vývojových nástrojů a odborných znalosti v těchto prostředích.

   ![Možnosti cloudového SQL Server: SQL Server v IaaS nebo SaaS SQL Database v cloudu.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak je vidět v diagramu, každou nabídku je možné charakterizovat úrovní správy, kterou nad infrastrukturou máte (na ose X), a stupněm nákladové efektivity dosahovaným konsolidací úrovně databáze a automatizace (na ose Y).

Při navrhování aplikace jsou pro hostování součásti aplikace s SQL Serverem k dispozici čtyři základní možnosti:

- SQL Server na nevirtualizovaných fyzických počítačích
- SQL Server v místních virtualizovaných počítačích (privátní cloud)
- SQL Server na virtuálním počítači Azure (veřejný cloud Microsoftu)
- Azure SQL Database (veřejný cloud Microsoftu)

V následujících částech se dozvíte o SQL Server ve veřejném cloudu Microsoftu: Azure SQL Database a SQL Server na virtuálních počítačích Azure. Kromě toho prozkoumáte obvyklé motivační faktory firem pro určení toho, která možnost je pro konkrétní aplikaci nejvhodnější.

## <a name="a-closer-look-at-azure-sql-database-and-sql-server-on-azure-vms"></a>Bližší pohled na Azure SQL Database a SQL Server na virtuálních počítačích Azure

Obecně jsou tyto dvě možnosti SQL optimalizovány pro různé účely:

- **Azure SQL Database**

Optimalizováno pro snížení celkových nákladů na správu na minimum pro zřizování a správu velkého počtu databází. Snižuje náklady na průběžnou správu, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](sql-database-automated-backups.md). Obecně platí, že Azure SQL Database může výrazně zvýšit počet databází, které spravuje jeden IT nebo vývojářský zdroj. [Elastické fondy](sql-database-elastic-pool.md) také podporují SaaS architektury víceklientské aplikace s funkcemi, jako je izolace klientů, a schopnost škálování, aby se snížily náklady sdílením prostředků napříč databázemi. [Spravovaná instance](sql-database-managed-instance.md) poskytuje podporu pro funkce s rozsahem instancí, které umožňují snadnou migraci stávajících aplikací a také sdílení prostředků mezi databázemi.

- **SQL Server spuštěné na virtuálních počítačích Azure**

Optimalizováno pro migraci stávajících aplikací do Azure nebo rozšíření stávajících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. V případě používání SQL Serveru na virtuálních počítačích Azure máte úplná práva pro správu pro vyhrazenou instanci SQL Serveru a cloudové virtuální počítače. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.

Následující tabulka shrnuje hlavní vlastnosti SQL Database a SQL Serveru na virtuálních počítačích Azure:

| | SQL Database samostatné databáze a elastické fondy | SQL Database spravované instance |Virtuální počítače Azure s SQL Server |
| --- | --- | --- |---|
| **Nejvhodnější pro:** |Nové aplikace navržené pro Cloud, které chtějí využívat nejnovější stabilní SQL Server funkce a mají časová omezení pro vývoj a marketing. | Nové aplikace nebo stávající místní aplikace, které chtějí využívat nejnovější stabilní funkce SQL Server a které jsou migrovány do cloudu s minimálními změnami.  | Existující aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami nebo bez změn. Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. |
|  | Týmy, které potřebují integrovanou vysokou dostupnost, možnost zotavení po havárii a možnost upgradu databáze. | Stejné jako SQL Database jednotlivé databáze a databáze ve fondu. | Týmy, které mohou konfigurovat, doladit, přizpůsobit a spravovat vysokou dostupnost, zotavení po havárii a opravy pro SQL Server. Některé poskytované automatizované funkce to značně zjednodušují. |
|  | Týmy, které nechtějí spravovat příslušný operační systém a nastavení konfigurace. | Stejné jako SQL Database jednotlivé databáze a databáze ve fondu. | Budete potřebovat přizpůsobené prostředí s úplnými právy správce. |
|  | Databáze o až 100 TB. | Až 8 TB. | Instance SQL Server s až 256 TB úložiště. Instance může podporovat tolik databází, kolik je potřeba. |
| **Režim** | Podporuje většinu místních funkcí na úrovni databáze. | Podporuje téměř všechny místní funkce na úrovni instance a databáze. | Podporuje všechny místní funkce. |
| **Zdroje a prostředky:** | Nechcete využívat prostředky IT ke konfiguraci a správě základní infrastruktury, ale chcete se zaměřit na aplikační vrstvu. | Stejné jako SQL Database jednotlivé databáze a databáze ve fondu. | Máte některé prostředky IT ke konfiguraci a správě. Některé poskytované automatizované funkce to značně zjednodušují. |
| **Celkové náklady na vlastnictví:** | Eliminuje náklady na hardware a snižuje náklady na správu. | Stejné jako SQL Database jednotlivé databáze a databáze ve fondu. | Eliminuje náklady na hardware. |
| **Kontinuita podnikových procesů:** |Kromě integrovaných [možností infrastruktury odolnosti proti chybám](sql-database-high-availability.md)poskytuje Azure SQL Database k dispozici funkce, jako například [Automatické zálohování](sql-database-automated-backups.md), [obnovení k bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore), [geografické obnovení](sql-database-recovery-using-backups.md#geo-restore), [aktivní geografickou replikaci](sql-database-active-geo-replication.md)a [ Skupiny automatického převzetí služeb při selhání](sql-database-auto-failover-group.md) pro zvýšení kontinuity podnikových služeb. Další informace najdete v tématu [Databáze SQL – kontinuita podnikových procesů (přehled)](sql-database-business-continuity.md). | Stejné jako SQL Database jediné a sdružené databáze, a navíc uživatelem iniciované zálohy, které jsou k dispozici pouze pro kopírování. | SQL Server na virtuálních počítačích Azure umožňuje nastavit vysoce dostupné řešení s možností zotavení po havárii pro konkrétní potřeby vaší databáze. Můžete tak mít systém, který je vysoce optimalizovaný pro vaši aplikaci. Sami podle potřeby můžete otestovat a spustit převzetí služeb při selhání. Další informace najdete v tématu [Vysoká dostupnost a zotavení po havárii pro SQL Server v Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md). |
| **Hybridní cloud:** |Vaše místní aplikace mohou přistupovat k datům v Azure SQL Database. | [Implementace nativní virtuální sítě](sql-database-managed-instance-vnet-configuration.md) a připojení k místnímu prostředí pomocí Azure Express Route nebo VPN Gateway. | V případě SQL Serveru na virtuálních počítačích Azure můžete mít aplikace, které běží částečně v cloudu a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Další informace o hybridních cloudových řešeních najdete v tématu [rozšíření místních datových řešení do cloudu](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |
|  | Podporuje [transakční replikaci systému SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) jako předplatitel k replikaci dat. | Pro spravovanou instanci je replikace podporovaná jako funkce ve verzi Preview. | Plně podporuje [SQL Server transakční replikace](https://msdn.microsoft.com/library/mt589530.aspx), [skupiny dostupnosti Always On](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md), integrační služby a přesouvání protokolů pro replikaci dat. Navíc jsou plně podporované tradiční zálohy systému SQL Server. |
|  | | |

## <a name="business-motivations-for-choosing-azure-sql-database-or-sql-server-on-azure-vms"></a>Motivace firem pro zvolení Azure SQL Database nebo SQL Serveru na virtuálních počítačích Azure

Existuje několik faktorů, které mohou mít vliv na rozhodnutí zvolit PaaS nebo IaaS pro hostování databází SQL:

- [Náklady](#cost) – obě možnosti PaaS a IaaS zahrnují základní cenu, která pokrývá základní infrastrukturu a licencování. S možností IaaS ale potřebujete investovat další čas a prostředky pro správu vaší databáze, zatímco v PaaS získáváte tyto funkce správy zahrnuté do ceny. Možnost IaaS vám umožňuje vypnout vaše prostředky, i když je nepoužíváte ke snížení nákladů, zatímco verze PaaS je vždy spuštěná, pokud je neodstraníte a znovu nevytvoříte, když budete potřebovat prostředky.
- [Správa](#administration) – možnosti PaaS omezují dobu potřebnou k investování do správy databáze. Omezuje ale také rozsah vlastních úloh správy a skriptů, které můžete provádět nebo spouštět. Například CLR není podporován pro databáze s jednou nebo ve fondu, ale podporuje se pro spravovanou instanci. Kromě toho žádné možnosti nasazení v PaaS nepodporují použití příznaků trasování.
- [Smlouva o úrovni služeb](#service-level-agreement-sla) – IaaS i PaaS poskytují vysokou a standardní smlouvu SLA pro obory. PaaS Option garantuje 99,99% SLA, zatímco IaaS garantuje 99,95% SLA pro infrastrukturu, což znamená, že musíte implementovat další mechanismy, abyste zajistili dostupnost svých databází. Řešení s vysokou dostupností můžete implementovat na 99,99% tím, že vytvoříte další SQL Server ve virtuálním počítači a nakonfigurujete skupiny dostupnosti AlwaysOn.
- [Čas přechodu do Azure](#market) -SQL Server na virtuálním počítači Azure je přesnou shodu vašeho prostředí, takže migrace z místního prostředí do Azure SQL VM se neliší od přesunu databází z jednoho místního serveru na jiný. Spravovaná instance také umožňuje extrémně jednoduchou migraci. před migrací do spravované instance ale můžou být nějaké změny, které musíte použít.

Tyto faktory budou podrobněji popsány v následujících částech.

### <a name="cost"></a>Náklady

Ať už jste začínající společnost, která nemá mnoho peněz, nebo tým v zavedené společnosti, který pracuje s limitovaným rozpočtem, je omezená výše prostředků často primárním rozhodujícím faktorem při volbě řešení pro hostování firemních databází. V této části se dozvíte o základech fakturace a licencování v Azure s ohledem na tyto dvě možnosti relační databáze: SQL Database a SQL Server na virtuálních počítačích Azure. Zjistíte také, jak vypočítat celkové náklady na aplikaci.

#### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování

V současné době se **SQL Database** prodává jako služba a je k dispozici s několika možnostmi nasazení a v různých úrovních služeb s různými cenami za prostředky, přičemž všechny z nich se účtují po hodinách za pevnou sazbu na základě úrovně služby a velikosti, kterou zvolíte. . Nejnovější informace o aktuálních podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [vCoreý model nákupu](sql-database-service-tiers-vcore.md).

- S SQL Database izolovanou databází můžete zvolit úroveň služby, která vyhovuje vašim potřebám, a to z široké škály cen od 5 $/měsíc pro úroveň Basic.
- Můžete vytvořit [elastické fondy](sql-database-elastic-pool.md) pro sdílení prostředků mezi instancemi databáze a snížit náklady a vyhovět špičkám využití.
- Pomocí SQL Database spravované instance můžete také využít vlastní licenci. Další informace o tom, jak používat vlastní licencování, najdete v tématu [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/) nebo pomocí programu [zvýhodněné hybridní využití Azure Kalkulačka](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) , kde najdete informace o tom, jak **ušetřit až 40%** .

Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Úrovně služeb a výpočetní velikosti můžete dynamicky upravovat tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace.

V případě **SQL Database** je databázový software konfigurován, opravován a upgradován automaticky společností Microsoft, což snižuje náklady na správu. Kromě toho vám [integrované funkce zálohování](sql-database-automated-backups.md) pomůžou dosáhnout výrazných úspor nákladů, hlavně v případě, že máte velký počet databází.

V případě systému **SQL Server na virtuálních počítačích Azure** můžete použít jakoukoli image systému SQL Server poskytovanou platformou (která zahrnuje licenci) nebo použít svou vlastní licenci systému SQL Server. K dispozici jsou všechny podporované verze systému SQL Server (2008R2, 2012, 2014, 2016) a jeho edice (Developer, Express, Web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL Server zaplatíte poplatky za licence na SQL Server a server s Windows nebo Linux, a to i náklady na Azure Storage pro disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud přenesete do Azure vlastní licenci SQL Server, budou se vám účtovat jenom náklady na server a úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci

Když začnete používat cloudovou platformu, náklady na spuštění vaší aplikace zahrnují náklady na nové náklady na vývoj a průběžnou správu a navíc náklady na službu veřejného cloudu.

**Při používání Azure SQL Database:**

- Vysoce minimalizované náklady na správu
- Omezené náklady na vývoj migrovaných aplikací (spravované instance)
- Náklady na službu SQL Database
- Žádné náklady na nákup hardwaru

**Při používání SQL Serveru na virtuálních počítačích Azure:**

- Vyšší náklady na správu
- Omezeno na žádné náklady na vývoj migrovaných aplikací
- Náklady na službu virtuálního počítače
- Žádné náklady na nákup hardwaru

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

- [Ceny SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Ceny za virtuální počítače](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. V IaaS a PaaS Microsoft spravuje základní infrastrukturu a automaticky replikuje všechna data, aby mohl poskytovat zotavení po havárii, nakonfiguruje a upgraduje databázový software, spravuje vyrovnávání zatížení a provádí transparentní převzetí služeb při selhání, pokud existuje selhání serveru v datovém centru.

- Pomocí **Azure SQL Database**můžete dál spravovat databázi, ale nebudete už muset spravovat databázový stroj, operační systém ani hardware. Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální konfiguraci a správu.
- V případě systému **SQL Server na virtuálních počítačích Azure** máte plnou kontrolu nad konfigurací operačního systému a instance systému SQL Server. V případě virtuálních počítačů je na vás, kdy se rozhodnete aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako například antivirový program. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)

Pro řadu IT oddělení je nejvyšší prioritou plnit povinnosti z hlediska garantované doby provozuschopnosti vyplývající ze Smlouvy o úrovni služeb (SLA). V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **SQL Database**poskytuje Microsoft smlouvu SLA s dostupností 99,99%. Nejnovější informace najdete v tématu věnovaném [Smlouvám o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/sql-database/).

Pro **SQL Server běžící na virtuálních počítačích Azure** poskytuje Microsoft smlouvu SLA s dostupností 99,95 %, která se vztahuje pouze na virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). V případě vysoké dostupnosti databáze (HA) v rámci virtuálních počítačů byste měli nakonfigurovat jednu z podporovaných možností vysoké dostupnosti v SQL Server, jako jsou například [skupiny dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

### <a name="market"></a>Čas přechodu do Azure

**SQL Database izolované databáze nebo elastické fondy** jsou správné řešení pro aplikace navržené pro Cloud, když je vývojářská produktivita a rychlý čas na uvedení na trh pro nová řešení důležitá. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze.

**SQL Database spravovaná instance** značně zjednodušuje migraci stávajících aplikací do Azure SQL Database, takže můžete migrovat migrované databázové aplikace na trh v Azure rychle.

**SQL Server běžící na virtuálních počítačích Azure** je ideální, pokud vaše stávající nebo nové aplikace vyžadují velké databáze nebo mají přístup ke všem funkcím v SQL Server nebo Windows/Linux a vy chcete se vyhnout času a nákladům na získání nového místního hardwaru. To je dobré i v případě, že chcete migrovat existující místní aplikace a databáze do Azure v případě, že je Azure SQL Database spravovaná instance nevyhovující. Vzhledem k tomu, že nepotřebujete měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet při opětovném vytvoření architektury stávajícího řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

## <a name="next-steps"></a>Další postup

- Pokud začínáte se službou SQL Database, přečtěte si téma [První Azure SQL Database](sql-database-single-database-get-started.md).
- Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
- Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
- [Identifikujte správnou Azure SQL Database/SKU spravované instance pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
