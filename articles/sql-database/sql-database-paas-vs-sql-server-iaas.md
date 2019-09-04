---
title: Volba správného nasazení v Azure SQL | Microsoft Docs
description: Naučte se vybírat z možností nasazení v Azure SQL mezi databázemi SQL, spravovanými instancemi SQL a SQL Server na virtuálních počítačích Azure.
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
ms.date: 08/22/2019
ms.openlocfilehash: 998318ad1418c1076676725af03bd7f33c963b3a
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70279932"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>Volba správného nasazení v Azure SQL

Naučte se, jak jednotlivé možnosti nasazení zapadají do datové platformy Microsoft Azure SQL, a získáte nápovědu odpovídající správné možnosti pro vaše obchodní požadavky. Bez ohledu na to, jestli upřednostňujete úspory nákladů nebo minimální správu, vám tento článek může pomáhat při rozhodování, jakým způsobem se většina obchodních požadavků zajímá.

## <a name="microsofts-azure-sql-data-platform"></a>Datová platforma Azure SQL pro Microsoft

Azure SQL je moderní platforma SQL, která nabízí několik možností nasazení využívajících přední Microsoft SQL Server modul, od migrace z převedených a posunutí až po modernizaci stávajících aplikací za účelem vytváření moderních cloudových služeb. Azure SQL je navržený tak, aby podporoval širokou škálu různých vzorů aplikací s různými úrovněmi kontroly nad základní platformou tak, aby splňovala nejnáročnější požadavky na migraci a modernizaci. Azure SQL eliminuje složitost správy nejrůznějších aplikací SQL Server ve velkém měřítku tím, že poskytuje jednotné jednotné prostředí pro správu.

Jednou z nejdůležitějších věcí, které je potřeba chápat, pokud se porovnávají výhody databází na Azure oproti využívání místních databází SQL Serveru, je to, že použitelné jsou všechny z nich. Datová platforma Microsoftu využívá SQL Serverou technologii a zpřístupňuje ji napříč fyzickými místními počítači, prostředími privátního cloudu, prostředími privátního cloudu hostovanými třetí stranou a veřejným cloudem. SQL Server na virtuálních počítačích Azure (virtuální počítače SQL) umožňují vyhovět jedinečným a různorodým obchodním potřebám prostřednictvím kombinace místních a cloudových nasazení a zároveň používat stejnou sadu serverových produktů, vývojářských nástrojů a odbornosti. v těchto prostředích.

   ![Možnosti cloudového SQL Server: SQL Server v IaaS nebo SaaS SQL Database v cloudu.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak je vidět v diagramu, jednotlivé nabídky je možné charakterizovat podle úrovně správy, kterou máte v infrastruktuře, a podle stupně efektivity nákladů.

V Azure můžete mít vaše SQL Server úlohy spuštěné jako hostovanou službu ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) nebo hostovanou infrastrukturu ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). V rámci PaaS máte v rámci každé možnosti nasazení více možností nasazení a úrovní služeb. Klíčovou otázkou, kterou potřebujete klást při rozhodování mezi PaaS nebo IaaS, chcete spravovat svou databázi, použít opravy a provádět zálohy, nebo chcete tyto operace delegovat na Azure?

V závislosti na odpovědi máte následující možnosti:

- [**Databáze SQL**](sql-database-technical-overview.md): Nejvhodnější pro moderní cloudové aplikace, které chtějí využívat nejnovější stabilní funkce SQL Server a mají časová omezení pro vývoj a marketing. Plně spravovaný stroj SQL Database založený na nejnovější stabilní verzi Enterprise SQL Server. Toto je relační databáze jako služba (DBaaS) hostovaná v cloudu Azure, která spadá do oborové kategorie *platformy jako služby (PaaS)* . SQL Database má několik možností nasazení, z nichž každý je postaven na standardizovaném hardwaru a softwaru, který je vlastněn, hostován a spravován společností Microsoft. Pomocí SQL Server můžete používat integrované funkce, které vyžadují rozsáhlou konfiguraci (buď místně, nebo na virtuálním počítači Azure). Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení. SQL Database obsahuje některé další funkce, které nejsou dostupné v SQL Server, jako je například integrovaná vysoká dostupnost, inteligentní funkce a správa.


  Databáze nabízí následující možnosti nasazení:
  - Jako [***jediná databáze***](sql-database-single-database.md) s vlastní sadou prostředků spravovaných prostřednictvím databázového serveru. Jedna databáze je podobná databázi s [omezením](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) v SQL Server. Tato možnost je optimalizovaná pro vývoj moderních aplikací pro nové aplikace, které se zanesou do cloudu. K dispozici jsou možnosti s možností [škálování a bez](sql-database-service-tier-hyperscale.md) [serveru](sql-database-serverless.md) .
  - [***Elastický fond***](sql-database-elastic-pool.md), což je kolekce databází se sdílenou sadou prostředků spravovaných prostřednictvím databázového serveru. Jednotlivé databáze lze přesunout do elastického fondu nebo z něj. Tato možnost je optimalizovaná pro vývoj moderních aplikací pro nové aplikace v cloudu pomocí modelu aplikace SaaS pro více tenantů. Elastické fondy poskytují cenově výhodné řešení pro správu výkonu více databází, které mají vzorce použití proměnných.
  - [***Databázový server***](sql-database-servers.md), který se používá ke správě skupin izolovaných databází a elastických fondů. Databázové servery slouží jako centrální bod správy pro více databází s jednou nebo ve fondu, [přihlášení](sql-database-manage-logins.md), [pravidla brány firewall](sql-database-firewall-configure.md), [pravidla auditování](sql-database-auditing.md), [zásady detekce hrozeb](sql-database-threat-detection.md)a [skupiny převzetí služeb při selhání](sql-database-auto-failover-group.md).

- [**Spravované instance SQL**](sql-database-managed-instance.md): Nejvhodnější pro většinu migrací do cloudu. Spravovaná instance je kolekce systémových a uživatelských databází se sdílenou sadou prostředků, které jsou připravené k přebírání a posunutí. Nejvhodnější pro nové aplikace nebo stávající místní aplikace, které chtějí využívat nejnovější stabilní funkce SQL Server a které jsou migrovány do cloudu s minimálními změnami. Spravovaná instance je podobná instanci [databázového stroje Microsoft SQL Server](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) , který nabízí sdílené prostředky pro databáze a další funkce v rozsahu instance. Spravovaná instance podporuje migraci databáze z místního prostředí s minimálními nároky na změnu databáze. Tato možnost poskytuje všechny PaaS výhody Azure SQL Database, ale přidává funkce, které byly dříve dostupné jenom ve virtuálních počítačích SQL. To zahrnuje nativní virtuální síť (VNet) a téměř 100% kompatibilitu s místními SQL Server. Spravované instance poskytují úplný přístup SQL Server a kompatibilitu funkcí pro migraci serverů SQL do Azure.


- [**Virtuální počítače SQL**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md): Nejvhodnější pro migrace a aplikace vyžadující přístup na úrovni operačního systému. Virtuální počítače SQL jsou připravené pro existující aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami nebo žádnými změnami. Virtuální počítače SQL nabízejí úplnou administrativní kontrolu nad instancí SQL Server a podkladovým operačním systémem pro migraci do Azure. Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. Virtuální počítače SQL spadají do kategorie odvětví *jako služby (IaaS)* a umožňují spouštět SQL Server v plně spravovaném virtuálním počítači (VM) v cloudu Azure. Virtuální počítače SQL se také spouštějí na standardizovaném hardwaru, který je vlastněn, hostován a spravován společností Microsoft. Pokud používáte virtuální počítače s SQL, můžete použít průběžné platby SQL Server licence, která už je zahrnutá v SQL Server imagi, nebo snadno použít stávající licenci. Virtuální počítač můžete také zastavit nebo obnovit podle potřeby. SQL Server nainstalovaná a hostovaná v cloudu běží na virtuálních počítačích s Windows serverem nebo Linux, které běží na Azure, označované taky jako infrastruktura jako služba (IaaS). Virtuální počítače SQL jsou vhodnou možností pro migraci místních SQL Server databází a aplikací bez změny v databázi. Všechny poslední verze a edice SQL Server jsou k dispozici pro instalaci na virtuálním počítači s IaaS. Nejvýznamnějším rozdílem od databází SQL a spravovaných instancí SQL je to, že SQL Server virtuální počítače umožňují plnou kontrolu nad databázovým strojem. Můžete zvolit, kdy se má spustit Údržba/opravy, změnit model obnovení na jednoduché nebo Hromadně protokolované, pozastavit nebo spustit službu v případě potřeby a můžete plně přizpůsobit SQL Server databázový stroj. Tento další ovládací prvek přinese zodpovědnost za správu virtuálního počítače.

  Optimalizováno pro migraci stávajících aplikací do Azure nebo rozšíření stávajících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. S virtuálními počítači SQL máte plná práva správce pro vyhrazenou instanci SQL Server a cloudový virtuální počítač. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.


Další rozdíly jsou uvedené v následující tabulce, ale ***obě databáze i spravované instance jsou optimalizované tak, aby se snížily celkové náklady na správu na minimum pro zřizování a správu velkého počtu databází.*** Snižuje náklady na průběžnou správu, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](sql-database-automated-backups.md). Obecně platí, že Azure SQL Database může výrazně zvýšit počet databází, které spravuje jeden IT nebo vývojářský zdroj. [Elastické fondy](sql-database-elastic-pool.md) také podporují SaaS architektury víceklientské aplikace s funkcemi, jako je izolace klientů, a schopnost škálování, aby se snížily náklady sdílením prostředků napříč databázemi. [Spravovaná instance](sql-database-managed-instance.md) poskytuje podporu pro funkce s rozsahem instancí, které umožňují snadnou migraci stávajících aplikací a také sdílení prostředků mezi databázemi.

| Databáze SQL | Spravované instance SQL | Virtuální počítače SQL |
| :--- | :--- | :--- |
|Podporuje většinu místních funkcí na úrovni databáze. K dispozici jsou nejčastěji používané SQL Server funkce.<br/>je zaručeno 99,995% dostupnosti.<br/>Předdefinované zálohy, opravy a obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Možnost přiřazení nezbytných prostředků (CPU/úložiště) k jednotlivým databázím.<br/>Integrovaná Pokročilá logika a zabezpečení<br/>Online Změna prostředků (CPU/Storage).| Podporuje téměř všechny místní funkce na úrovni instance a databáze. Vysoká kompatibilita s SQL Serverem v místním prostředí.<br/>je zaručeno 99,99% dostupnosti.<br/>Předdefinované zálohy, opravy a obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Snadná migrace z SQL Server.<br/>Privátní IP adresa v rámci virtuální sítě Azure.<br/>Integrovaná Pokročilá logika a zabezpečení<br/>Online Změna prostředků (CPU/Storage).| Máte plnou kontrolu nad SQL Server modulem. Podporuje všechny místní funkce.<br/>Až 99,99% dostupnost.<br/>Úplná parita se stejnou verzí místních SQL Server.<br/>Opravená, dobře známá verze databázového stroje.<br/>Snadná migrace z SQL Server v místním prostředí.<br/>Privátní IP adresa v rámci virtuální sítě Azure.<br/>Máte možnost nasadit aplikace nebo služby na hostitele, kde je umístěn SQL Server.|
|Migrace z SQL Server může být těžká.<br/>Některé funkce SQL Server nejsou k dispozici.<br/>Žádná záruka přesné doby údržby (ale skoro transparentní).<br/>Kompatibilitu s SQL Server verzí lze dosáhnout pouze pomocí úrovní kompatibility databáze.<br/>Privátní IP adresu nelze přiřadit (přístup můžete omezit pomocí pravidel brány firewall).|Stále je k dispozici nějaký minimální počet funkcí SQL Server, které nejsou k dispozici.<br/>Žádná záruka přesné doby údržby (ale skoro transparentní).<br/>Kompatibilitu s SQL Server verzí lze dosáhnout pouze pomocí úrovní kompatibility databáze.|Musíte spravovat zálohy a opravy.<br>Musíte implementovat vlastní řešení s vysokou dostupností.<br/>Při změně prostředků (CPU/úložiště) dochází k výpadku.|
| Databáze o až 100 TB. | Až 8 TB. | Instance SQL Server s až 256 TB úložiště. Instance může podporovat tolik databází, kolik je potřeba. |
| Místní aplikace má přístup k datům v Azure SQL Database. | [Implementace nativní virtuální sítě](sql-database-managed-instance-vnet-configuration.md) a připojení k místnímu prostředí pomocí Azure Express Route nebo VPN Gateway. | S virtuálními počítači SQL můžete mít aplikace, které běží částečně v cloudu, a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Další informace o hybridních cloudových řešeních najdete v tématu [rozšíření místních datových řešení do cloudu](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>Obchodní motivace pro výběr databází, spravovaných instancí nebo virtuálních počítačů SQL

Existuje několik faktorů, které mohou mít vliv na vaše rozhodnutí zvolit mezi různými nabídkami dat:

- [Náklady](#cost) – obě možnosti PaaS a IaaS zahrnují základní cenu, která pokrývá základní infrastrukturu a licencování. S možností IaaS ale potřebujete investovat další čas a prostředky pro správu vaší databáze, zatímco v PaaS získáváte tyto funkce správy zahrnuté do ceny. Možnost IaaS vám umožňuje vypnout vaše prostředky, zatímco je nepoužíváte ke snížení nákladů, zatímco verze PaaS je vždy spuštěná, pokud jste je nemuseli vyřadit a znovu vytvořit.
- [Správa](#administration) – možnosti PaaS omezují dobu potřebnou k investování do správy databáze. Omezuje ale také rozsah vlastních úloh správy a skriptů, které můžete provádět nebo spouštět. Například CLR není podporován pro databáze s jednou nebo ve fondu, ale podporuje se pro spravovanou instanci. Kromě toho žádné možnosti nasazení v PaaS nepodporují použití příznaků trasování.
- [Smlouva o úrovni služeb](#service-level-agreement-sla) – IaaS i PaaS poskytují vysokou a standardní smlouvu SLA pro obory. PaaS Option garantuje 99,99% SLA, zatímco IaaS garantuje 99,95% SLA pro infrastrukturu, což znamená, že musíte implementovat další mechanismy, abyste zajistili dostupnost svých databází. Řešení s vysokou dostupností můžete implementovat na 99,99% tím, že vytvoříte další SQL Server ve virtuálním počítači a nakonfigurujete skupiny dostupnosti AlwaysOn.
- [Čas přechodu do Azure](#market) -SQL Server na virtuálním počítači Azure je přesnou shodu vašeho prostředí, takže migrace z místního prostředí do Azure SQL VM se neliší od přesunu databází z jednoho místního serveru na jiný. Spravovaná instance také umožňuje extrémně jednoduchou migraci. před migrací do spravované instance ale můžou být nějaké změny, které musíte použít.

Tyto faktory budou podrobněji popsány v následujících částech.

### <a name="cost"></a>Náklady

Ať už jste začínající společnost, která nemá mnoho peněz, nebo tým v zavedené společnosti, který pracuje s limitovaným rozpočtem, je omezená výše prostředků často primárním rozhodujícím faktorem při volbě řešení pro hostování firemních databází. V této části se dozvíte o základech fakturace a licencování v Azure s ohledem na tyto dvě možnosti relační databáze: Virtuální počítače s SQL Database a SQL. Zjistíte také, jak vypočítat celkové náklady na aplikaci.

#### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování

V současné době se **SQL Database** prodává jako služba a je k dispozici s několika možnostmi nasazení a v různých úrovních služeb s různými cenami za prostředky, přičemž všechny z nich se účtují po hodinách za pevnou sazbu na základě úrovně služby a velikosti, kterou zvolíte. . Nejnovější informace o aktuálních podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) a [vCoreý model nákupu](sql-database-service-tiers-vcore.md).

- Ve službě SQL Database můžete zvolit úroveň služby, která vyhovuje vašim potřebám, z široké škály cen od 5 $/měsíc pro úroveň Basic.
- Můžete vytvořit [elastické fondy](sql-database-elastic-pool.md) pro sdílení prostředků mezi instancemi databáze a snížit náklady a vyhovět špičkám využití.
- Pomocí spravované instance SQL můžete také využít vlastní licenci. Další informace o tom, jak používat vlastní licencování, najdete v tématu [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/) nebo pomocí programu [zvýhodněné hybridní využití Azure Kalkulačka](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) , kde najdete informace o tom, jak **ušetřit až 40%** .

Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Úrovně služeb a výpočetní velikosti můžete dynamicky upravovat tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace.

U **databází SQL a spravovaných instancí SQL**je databázový software automaticky nakonfigurovaný, je opravený a upgradovaný Microsoftem, což snižuje náklady na správu. Kromě toho vám [integrované funkce zálohování](sql-database-automated-backups.md) pomůžou dosáhnout výrazných úspor nákladů, hlavně v případě, že máte velký počet databází.

S **virtuálními počítači SQL**můžete použít kteroukoli z SQL serverch imagí poskytovaných platformou (včetně licence) nebo přenést licenci SQL Server. K dispozici jsou všechny podporované verze systému SQL Server (2008R2, 2012, 2014, 2016) a jeho edice (Developer, Express, Web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL Server zaplatíte poplatky za licence na SQL Server a server s Windows nebo Linux, a to i náklady na Azure Storage pro disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud přenesete do Azure vlastní licenci SQL Server, budou se vám účtovat jenom náklady na server a úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci

Když začnete používat cloudovou platformu, náklady na spuštění vaší aplikace zahrnují náklady na nové náklady na vývoj a průběžnou správu a navíc náklady na službu veřejného cloudu.

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

- [Ceny SQL Database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Ceny za virtuální počítače](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Správa

Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. V IaaS a PaaS Microsoft spravuje základní infrastrukturu a automaticky replikuje všechna data, aby mohl poskytovat zotavení po havárii, nakonfiguruje a upgraduje databázový software, spravuje vyrovnávání zatížení a provádí transparentní převzetí služeb při selhání, pokud existuje selhání serveru v datovém centru.

- U **databází SQL a spravovaných instancí SQL**můžete dál spravovat databázi, ale už nepotřebujete spravovat databázový stroj, operační systém ani hardware. Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální konfiguraci a správu.
- S **virtuálními počítači SQL**máte plnou kontrolu nad operačním systémem a konfigurací instance SQL Server. V případě virtuálních počítačů je na vás, kdy se rozhodnete aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako například antivirový program. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Smlouvy o úrovni služeb (SLA)

Pro řadu IT oddělení je nejvyšší prioritou plnit povinnosti z hlediska garantované doby provozuschopnosti vyplývající ze Smlouvy o úrovni služeb (SLA). V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **SQL Database**poskytuje Microsoft smlouvu SLA s dostupností 99,99%. Nejnovější informace najdete v tématu věnovaném [Smlouvám o úrovni služeb (SLA)](https://azure.microsoft.com/support/legal/sla/sql-database/).

Pro **virtuální počítače s SQL**poskytuje Microsoft smlouvu SLA s dostupností 99,95%, která pokrývá jenom virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). V případě vysoké dostupnosti databáze (HA) v rámci virtuálních počítačů byste měli nakonfigurovat jednu z podporovaných možností vysoké dostupnosti v SQL Server, jako jsou například [skupiny dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

### <a name="market"></a>Čas přechodu do Azure

**Databáze SQL** (izolované databáze nebo elastické fondy) jsou správné řešení pro aplikace navržené pro Cloud, když je vývojářská produktivita a rychlý čas na uvedení na trh pro nová řešení důležitá. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze.

**Spravovaná instance SQL** značně zjednodušuje migraci stávajících aplikací do Azure SQL, což vám umožní rychle přenést migrované databázové aplikace na trh v Azure.

**Virtuální počítače SQL** jsou ideální, pokud vaše stávající nebo nové aplikace vyžadují velké databáze nebo mají přístup ke všem funkcím v SQL Server nebo Windows/Linux a vy chcete zabránit času a nákladům na získání nového místního hardwaru. To je dobré i v případě, že chcete migrovat existující místní aplikace a databáze do Azure v případě, že je Azure SQL Database spravovaná instance nevyhovující. Vzhledem k tomu, že nepotřebujete měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet při opětovném vytvoření architektury stávajícího řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Další postup

- Pokud chcete začít s SQL Database, podívejte se do [první databáze Azure SQL](sql-database-single-database-get-started.md) .
- Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
- Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md).
- [Identifikujte správnou položku SQL Database nebo SKU spravované instance SQL pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).
