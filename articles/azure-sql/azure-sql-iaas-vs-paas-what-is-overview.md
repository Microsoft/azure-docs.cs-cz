---
title: Co je Azure SQL?
description: 'Seznamte se s různými možnostmi v rámci řady služeb Azure SQL: Azure SQL Database, Azure SQL Managed instance a SQL Server na virtuálním počítači Azure.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: overview
keywords: SQL Server Cloud, SQL Server v cloudu, databázi PaaS, cloudové SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/27/2020
ms.openlocfilehash: 6961c0ffc0d5b9c6559d20b00bfb5510f7247adb
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103600977"
---
# <a name="what-is-azure-sql"></a>Co je Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL je rodina spravovaných, zabezpečených a inteligentních produktů, které používají databázový stroj SQL Server v cloudu Azure.

- **Azure SQL Database**: podporují moderní cloudové aplikace na inteligentní, spravované databázové službě, která zahrnuje výpočetní výkon bez serveru. 
- **Spravovaná instance Azure SQL**: modernizovat své stávající aplikace SQL Server ve velkém měřítku s využitím inteligentní plně spravované instance jako služby s téměř 100% paritou funkcí s databázovým strojem SQL Server. Nejvhodnější pro většinu migrací do cloudu.
- **SQL Server na virtuálních počítačích Azure**: snadno a znovu shift úlohy SQL Server a udržovat 100% SQL Server kompatibility a přístup na úrovni operačního systému. 
 
Azure SQL je založený na známém SQL Server Engine, takže můžete snadno migrovat aplikace a dál používat nástroje, jazyky a prostředky, které znáte. Vaše dovednosti a zkušenosti se přenášejí do cloudu, takže můžete dělat ještě víc s tím, co už máte. 

Přečtěte si, jak každý produkt zapadá do datové platformy Microsoft Azure SQL, aby odpovídal správné možnosti pro vaše podnikové požadavky. Bez ohledu na to, jestli upřednostňujete úspory nákladů nebo minimální správu, vám tento článek může pomáhat při rozhodování, jakým způsobem se většina obchodních požadavků zajímá.

Pokud s Azure SQL začínáte, podívejte se na video *co je Azure SQL* z naší podrobné [série videí Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/What-is-Azure-SQL-3-of-61/player]

> [!TIP]
> Jak můžeme Azure SQL lépe využít? [Proveďte průzkum](https://aka.ms/AzureSQLSurvey).

## <a name="overview"></a>Přehled

V dnešním světě založeném na datech je výkon digitální transformace stále více závislý na naší schopnosti spravovat obrovské objemy dat a využít jejich potenciál. Ale dnešní data Estates jsou stále složitá, s daty hostovanými místně, v cloudu nebo na hranici sítě. Vývojáři, kteří sestavují inteligentní a moderní aplikace, můžou najít samy omezené omezením, které může mít za důsledek dopad na jejich prostředí. Omezení vyplývající z nekompatibilních platforem, nedostatečné zabezpečení dat, nedostatečné prostředky a bariéry týkající se zvýšení cen vytvářejí složitost, která může bránit modernizaci a vývoji aplikací. 

Jednou z nejdůležitějších věcí, které je potřeba chápat, pokud se porovnávají výhody databází na Azure oproti využívání místních databází SQL Serveru, je to, že použitelné jsou všechny z nich. Datová platforma Microsoftu využívá SQL Serverou technologii a zpřístupňuje ji napříč fyzickými místními počítači, prostředími privátního cloudu, prostředími privátního cloudu hostovanými třetí stranou a veřejným cloudem. 


### <a name="fully-managed-and-always-up-to-date"></a>Plně spravovaná a vždy aktuální 

Věnujte více času inovacím a méně častým opravám, aktualizacím a zálohováním databází. Azure je jediný Cloud s doručoval SQL, který automaticky aplikuje nejnovější aktualizace a opravy, aby vaše databáze byly pořád aktuální, a eliminují tak nepříjemnosti na konci podpory. I složité úlohy, jako je optimalizace výkonu, vysoká dostupnost, zotavení po havárii a zálohování, jsou automatizované, takže se můžete soustředit na aplikace.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Ochrana dat pomocí integrovaného inteligentního zabezpečení 

Azure průběžně monitoruje vaše data pro hrozby. V případě Azure SQL můžete:

- Opravte potenciální hrozby v reálném čase díky inteligentním [funkcím pro detekci hrozeb](../security/fundamentals/threat-detection.md#threat-protection-features-other-azure-services) a proaktivním výstrahám posouzení ohrožení zabezpečení. 
- Využijte špičkovou ochranu s více vrstvami s [integrovanými ovládacími prvky zabezpečení](https://azure.microsoft.com/overview/security/) , jako je T-SQL, ověřování, sítě a Správa klíčů. 
- Využijte nejkomplexnější pokrytí [dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud/compliance/) u libovolné cloudové databázové služby. 


### <a name="business-motivations"></a>Motivace v podniku

Existuje několik faktorů, které mohou mít vliv na vaše rozhodnutí zvolit mezi různými nabídkami dat:

- [Náklady](#cost): obě možnosti PaaS i IaaS zahrnují základní cenu, která pokrývá základní infrastrukturu a licencování. S možností IaaS ale potřebujete investovat další čas a prostředky pro správu vaší databáze, zatímco v PaaS získáte tyto funkce správy zahrnuté do ceny. IaaS vám umožňuje vypnout prostředky, když je nepoužíváte ke snížení nákladů, zatímco PaaS je vždy spuštěný, dokud neodstraníte a znovu nevytvoříte prostředky, které jsou potřeba.
- [Správa](#administration): možnosti PaaS omezují dobu potřebnou k investování do správy databáze. Omezuje ale také rozsah vlastních úloh správy a skriptů, které můžete provádět nebo spouštět. Například CLR není podporován u SQL Database, ale je podporován pro instanci spravované instance SQL. Kromě toho žádné možnosti nasazení v PaaS nepodporují použití příznaků trasování.
- [Smlouva o úrovni služeb](#service-level-agreement-sla): IaaS i PaaS poskytují vysokou a standardní smlouvu SLA pro obory. PaaS Option garantuje 99,99% SLA, zatímco IaaS garantuje 99,95% SLA pro infrastrukturu, což znamená, že musíte implementovat další mechanismy, abyste zajistili dostupnost svých databází. Můžete dosáhnout 99,99% SLA vytvořením dalšího virtuálního počítače SQL a implementací řešení vysoké dostupnosti skupiny dostupnosti Always On SQL Server. 
- [Čas přechodu do Azure](#market): SQL Server na virtuálním počítači Azure je přesnou shodu vašeho prostředí, takže migrace z místního počítače na virtuální počítač Azure se neliší od přesunu databází z jednoho místního serveru na jiný. Spravovaná instance SQL taky umožňuje snadnou migraci. před migrací ale můžou být nějaké změny, které musíte použít. 


## <a name="service-comparison"></a>Porovnání služeb

   ![Možnosti cloudového SQL Server: SQL Server na IaaS nebo v cloudu SQL Database SaaS.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Jak je vidět v diagramu, jednotlivé nabídky služeb je možné charakterizovat podle úrovně správy, kterou máte v infrastruktuře, a podle stupně efektivity nákladů.

V Azure můžete mít vaše SQL Server úlohy spuštěné jako hostovanou službu ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) nebo hostovanou infrastrukturu ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). V rámci PaaS máte několik možností produktu a úrovně služeb v rámci jednotlivých možností. Klíčovou otázkou, kterou potřebujete klást při rozhodování mezi PaaS nebo IaaS, chcete spravovat svou databázi, použít opravy a provádět zálohy, nebo chcete tyto operace delegovat na Azure?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL Database](database/sql-database-paas-overview.md) je relační databáze jako služba (DBaaS) hostovaná v Azure, která spadá do kategorie odvětví typu *platforma jako služba (PaaS)*. 
- Nejvhodnější pro moderní cloudové aplikace, které chtějí využívat nejnovější stabilní funkce SQL Server a mají časová omezení pro vývoj a marketing. 
- Plně spravovaný databázový stroj SQL Server založený na nejnovější stabilní verzi Enterprise SQL Server. SQL Database má dvě možnosti nasazení založené na standardizovaném hardwaru a softwaru, který vlastní, hostovaný a spravovaný Microsoftem. 

Pomocí SQL Server můžete používat integrované funkce, které vyžadují rozsáhlou konfiguraci (buď místně, nebo na virtuálním počítači Azure). Při použití SQL Database platíte průběžnými platbami s možností vertikálního nebo horizontálního navyšování kapacity pro dosažení vyššího výkonu bez přerušení. SQL Database obsahuje některé další funkce, které nejsou dostupné v SQL Server, jako je například integrovaná vysoká dostupnost, inteligentní funkce a správa.


Azure SQL Database nabízí následující možnosti nasazení:
  - Jako [*jediná databáze*](database/single-database-overview.md) s vlastní sadou prostředků spravovaných prostřednictvím [logického SQL serveru](database/logical-servers.md). Jedna databáze je podobná databázi s [omezením](/sql/relational-databases/databases/contained-databases) v SQL Server. Tato možnost je optimalizovaná pro vývoj moderních aplikací pro nové aplikace, které se zanesou do cloudu. K dispozici jsou možnosti s možností [škálování a bez](database/service-tier-hyperscale.md) [serveru](database/serverless-tier-overview.md) .
  - [*Elastický fond*](database/elastic-pool-overview.md), což je kolekce databází se sdílenou sadou prostředků spravovaných prostřednictvím [logického SQL serveru](database/logical-servers.md). Jednotlivé databáze lze přesunout do elastického fondu nebo z něj. Tato možnost je optimalizovaná pro vývoj moderních aplikací pro nové aplikace v cloudu pomocí modelu aplikace SaaS pro více tenantů. Elastické fondy poskytují cenově výhodné řešení pro správu výkonu více databází, které mají vzorce použití proměnných.

### <a name="azure-sql-managed-instance"></a>Spravovaná instance Azure SQL

[Azure SQL Managed instance](managed-instance/sql-managed-instance-paas-overview.md) spadá do oborové kategorie *PaaS (Platform as-a-Service)*, která je nejlepší pro většinu migrací do cloudu. Spravovaná instance SQL je kolekce systémových a uživatelských databází se sdílenou sadou prostředků, která je připravená k přebírání a posunutí.  
- Nejvhodnější pro nové aplikace nebo stávající místní aplikace, které chtějí využívat nejnovější stabilní funkce SQL Server a které jsou migrovány do cloudu s minimálními změnami. Instance spravované instance SQL je podobná instanci [databázového stroje Microsoft SQL Server](/sql/database-engine/sql-server-database-engine-overview) , který nabízí sdílené prostředky pro databáze a další funkce v rozsahu instance. 
- Spravovaná instance SQL podporuje migraci databáze z místního prostředí s minimálními nároky na změnu databáze. Tato možnost poskytuje všechny PaaS výhody Azure SQL Database, ale přidává funkce, které byly dříve dostupné jenom v SQL Server virtuálních počítačích. To zahrnuje nativní virtuální síť a téměř 100% kompatibilitu s místními SQL Server. Instance spravované instance SQL poskytují úplný přístup SQL Server a kompatibilitu funkcí pro migraci serverů SQL do Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server na virtuálním počítači Azure

[SQL Server na virtuálním počítači Azure](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) spadají do kategorie odvětví *jako služby (IaaS)* a umožňuje spouštět SQL Server v plně spravovaném virtuálním počítači (VM) v Azure. 
- Nejvhodnější pro migrace a aplikace vyžadující přístup na úrovni operačního systému. Virtuální počítače SQL v Azure jsou připravené pro existující aplikace, které vyžadují rychlou migraci do cloudu s minimálními změnami nebo žádnými změnami. Virtuální počítače SQL nabízejí úplnou administrativní kontrolu nad instancí SQL Server a podkladovým operačním systémem pro migraci do Azure. 
- Scénáře rychlého vývoje a testování, když si nechcete koupit hardware pro místní neprodukční SQL Server. Virtuální počítače SQL se také spouštějí na standardizovaném hardwaru, který je vlastněn, hostován a spravován společností Microsoft. Pokud používáte virtuální počítače s SQL, můžete použít průběžné platby SQL Server licence, která už je zahrnutá v SQL Server imagi, nebo snadno použít stávající licenci. Virtuální počítač můžete také zastavit nebo obnovit podle potřeby. 
- SQL Server nainstalovaná a hostovaná v cloudu běží na virtuálních počítačích s Windows serverem nebo Linux, které běží na Azure, označované taky jako infrastruktura jako služba (IaaS). Virtuální počítače SQL jsou vhodnou možností pro migraci místních SQL Server databází a aplikací bez změny v databázi. Všechny poslední verze a edice SQL Server jsou k dispozici pro instalaci na virtuálním počítači s IaaS. 

    Nejvýznamnějším rozdílem od SQL Database a SQL spravované instance je to, že SQL Server v Azure Virtual Machines umožňuje úplnou kontrolu nad databázovým strojem. Můžete zvolit, kdy se má spustit Údržba/opravy, změnit model obnovení na jednoduché nebo Hromadně protokolované, pozastavit nebo spustit službu v případě potřeby a můžete plně přizpůsobit SQL Server databázový stroj. Tento další ovládací prvek přinese zodpovědnost za správu virtuálního počítače.
- Optimalizováno pro migraci stávajících aplikací do Azure nebo rozšíření stávajících místních aplikací do cloudu v hybridních nasazeních. Kromě toho lze systém SQL Server na virtuálním počítači použít k vývoji a testování tradičních aplikací systému SQL Server. S virtuálními počítači SQL máte plná práva správce pro vyhrazenou instanci SQL Server a cloudový virtuální počítač. Je ideální volbou v případě, že organizace už má dostupné IT zdroje pro údržbu virtuálních počítačů. Tyto schopnosti umožňují sestavit vysoce přizpůsobený systém pro plnění specifických požadavků vaší aplikace na výkon a dostupnost.

V následující tabulce jsou uvedeny další rozdíly, ale *SQL Database i spravované instance SQL jsou optimalizované tak, aby se snížily celkové náklady na správu na minimum pro zřizování a správu velkého počtu databází.* Průběžné náklady na správu se sníží, protože nemusíte spravovat žádné virtuální počítače, operační systémy nebo databázový software. Není nutné spravovat upgrady, vysokou dostupnost nebo [zálohy](database/automated-backups-overview.md). 

Obecně může SQL Database a SQL Managed instance významně zvýšit počet databází spravovaných jedním IT nebo vývojovým prostředkem. [Elastické fondy](database/elastic-pool-overview.md) také podporují SaaS architektury víceklientské aplikace s funkcemi, jako je izolace klientů, a schopnost škálování, aby se snížily náklady sdílením prostředků napříč databázemi. [Spravovaná instance SQL](managed-instance/sql-managed-instance-paas-overview.md) poskytuje podporu pro funkce s rozsahem instancí, které umožňují snadnou migraci stávajících aplikací a také sdílení prostředků mezi databázemi.

### <a name="comparison-table"></a>Srovnávací tabulka

| Azure SQL Database | Spravovaná instance Azure SQL | SQL Server na virtuálním počítači Azure |
| :--- | :--- | :--- |
|Podporuje většinu místních funkcí na úrovni databáze. K dispozici jsou nejčastěji používané SQL Server funkce.<br/>je zaručeno 99,995% dostupnosti.<br/>Předdefinované zálohy, opravy a obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Možnost přiřazení nezbytných prostředků (CPU/úložiště) k jednotlivým databázím.<br/>Integrovaná Pokročilá logika a zabezpečení<br/>Online Změna prostředků (CPU/Storage).| Podporuje téměř všechny místní funkce na úrovni instance a databáze. Vysoká kompatibilita s SQL Server.<br/>je zaručeno 99,99% dostupnosti.<br/>Předdefinované zálohy, opravy a obnovení.<br/>Nejnovější stabilní verze databázového stroje.<br/>Snadná migrace z SQL Server.<br/>Privátní IP adresa v rámci Azure Virtual Network.<br/>Integrovaná Pokročilá logika a zabezpečení<br/>Online Změna prostředků (CPU/Storage).| Máte plnou kontrolu nad SQL Server modulem. Podporuje všechny místní funkce.<br/>Až 99,99% dostupnost.<br/>Úplná parita se stejnou verzí místních SQL Server.<br/>Opravená, dobře známá verze databázového stroje.<br/>Snadná migrace z SQL Server.<br/>Privátní IP adresa v rámci Azure Virtual Network.<br/>Máte možnost nasazovat aplikace nebo služby na hostitele, kde je umístěn SQL Server.|
|Migrace z SQL Server může být náročná.<br/>Některé funkce SQL Server nejsou k dispozici.<br/>Žádná záruka přesné doby údržby (ale skoro transparentní).<br/>Kompatibilitu s SQL Server verzí lze dosáhnout pouze pomocí úrovní kompatibility databáze.<br/>Podpora privátních IP adres pomocí [privátního propojení Azure](database/private-endpoint-overview.md).|Stále je k dispozici nějaký minimální počet funkcí SQL Server, které nejsou k dispozici.<br/>Žádná záruka přesné doby údržby (ale skoro transparentní).<br/>Kompatibilitu s SQL Server verzí lze dosáhnout pouze pomocí úrovní kompatibility databáze.|Musíte spravovat zálohy a opravy.<br>Je nutné implementovat vlastní řešení High-Availability.<br/>Při změně prostředků (CPU/úložiště) dochází k výpadku.|
| Databáze o až 100 TB. | Až 8 TB. | Instance SQL Server s až 256 TB úložiště. Instance může podporovat tolik databází, kolik je potřeba. |
| Místní aplikace má přístup k datům v Azure SQL Database. | [Implementace nativní virtuální sítě](managed-instance/vnet-existing-add-subnet.md) a připojení k místnímu prostředí pomocí Azure Express Route nebo VPN Gateway. | S virtuálními počítači SQL můžete mít aplikace, které běží částečně v cloudu, a částečně místně. Můžete si například rozšířit místní síť a služby Active Directory Domain do cloudu přes [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Další informace o hybridních cloudových řešeních najdete v tématu [rozšíření místních datových řešení do cloudu](/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud). |


## <a name="cost"></a>Náklady

Bez ohledu na to, jestli jste spuštění Strapped pro hotovost nebo tým v zřízené společnosti, který pracuje s přísnými rozpočtovými omezeními, je při rozhodování, jak hostovat vaše databáze, často omezené finanční ovladače. V této části se dozvíte o základech fakturace a licencování v Azure přidružených k rodině služeb Azure SQL.  Zjistíte také, jak vypočítat celkové náklady na aplikaci.

### <a name="billing-and-licensing-basics"></a>Základy fakturace a licencování

V současné době se obě **SQL Database** a **SQL Managed instance** prodávají jako služba a jsou dostupné s několika možnostmi a v různých úrovních služeb s různými cenami za prostředky, přičemž všechny z nich se účtují po hodinách za pevnou sazbu na základě úrovně služby a zvolené velikosti. Nejnovější informace o aktuálních podporovaných úrovních služby, velikostech výpočtů a částkách úložiště najdete v tématu [nákupní model založený na DTU pro SQL Database](database/service-tiers-dtu.md) a [vCoreý model pro SQL Database i pro spravovanou instanci SQL](database/service-tiers-vcore.md).

- Pomocí SQL Database můžete zvolit úroveň služby, která vyhovuje vašim potřebám, z široké škály cen od 5 $/měsíc pro úroveň Basic a můžete vytvořit [elastické fondy](database/elastic-pool-overview.md) pro sdílení prostředků mezi databázemi a snížit náklady a vyhovět špičkám využití.
- Pomocí spravované instance SQL můžete také využít vlastní licenci. Další informace o tom, jak používat vlastní licencování, najdete v tématu [License mobility prostřednictvím Software Assurance v Azure](https://azure.microsoft.com/pricing/license-mobility/) , nebo pomocí [zvýhodněné hybridní využití Azure kalkulačky](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) , abyste viděli, jak **ušetřit až 40%**.

Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/). Úrovně služeb a výpočetní velikosti můžete dynamicky upravovat tak, aby odpovídaly různým požadavkům na propustnost vaší aplikace.

V případě **SQL Database** a **SQL spravované instance** se databázový software automaticky nakonfiguruje, opraví a upgraduje pomocí Azure, což snižuje náklady na správu. Kromě toho vám její [integrované funkce zálohování](database/automated-backups-overview.md) pomohou dosáhnout výrazných úspor nákladů, zejména v případě, že máte velký počet databází.

Pomocí **SQL na virtuálních počítačích Azure** můžete použít kteroukoli z SQL serverch imagí poskytovaných platformou (včetně licence) nebo přenést licenci SQL Server. K dispozici jsou všechny podporované verze SQL Server (2008 R2, 2012, 2014, 2016, 2017, 2019) a edice (Developer, Express, web, Standard, Enterprise). Kromě toho jsou k dispozici verze imagí modelu používání vlastní licence (BYOL). Pokud budete používat image poskytované v rámci Azure, budou provozní náklady záviset na velikosti virtuálního počítače a na edici systému SQL Server, kterou zvolíte. Bez ohledu na velikost virtuálního počítače nebo edici SQL Server zaplatíte poplatky za licence na SQL Server a server s Windows nebo Linux, a to i náklady na Azure Storage pro disky virtuálních počítačů. Možnost fakturace po minutách vám umožňuje používat SQL Server tak dlouho, jak budete potřebovat, bez nutnosti dokupovat další licence na SQL Server. Pokud přenesete do Azure vlastní licenci SQL Server, budou se vám účtovat jenom náklady na server a úložiště. Další informace o používání vlastní licence (BYOL) najdete v tématu [Mobilita licencí v rámci programu Software Assurance na Azure](https://azure.microsoft.com/pricing/license-mobility/). Kromě toho se vám účtuje odchozí přenos přes internet podle běžných [sazeb za přenos dat](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>Výpočet celkových nákladů na aplikaci

Když začnete používat cloudovou platformu, náklady na spuštění vaší aplikace zahrnují náklady na nové náklady na vývoj a průběžnou správu a navíc náklady na službu veřejného cloudu.

Další informace o cenách najdete v následujících zdrojích informací a materiálech:

- [SQL Database & ceny spravované instance SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Ceny za virtuální počítače](https://azure.microsoft.com/pricing/details/virtual-machines/) pro [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) a pro [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Cenová kalkulačka funkcí Azure](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Správa

Pro mnoho firem je rozhodnutí o přechodu na využívání cloudové služby nejen otázkou nízkých nákladů, ale také snížení složitosti správy. Díky IaaS a PaaS spravuje Azure základní infrastrukturu a automaticky replikuje všechna data, aby poskytovala zotavení po havárii, nakonfiguruje a upgraduje databázový software, spravuje vyrovnávání zatížení a provádí transparentní převzetí služeb při selhání v případě selhání serveru v rámci datového centra.

- Pomocí **SQL Database** a **spravované instance SQL** můžete dál spravovat svou databázi, ale nebudete už muset spravovat databázový stroj, operační systém ani hardware. Příklady položek, které můžete dál spravovat: databáze a přihlašovací údaje, ladění indexů nebo dotazů a auditování a zabezpečení. Kromě toho konfigurace vysoké dostupnosti v jiném datovém centru vyžaduje minimální konfiguraci a správu.
- S **SQL na virtuálním počítači Azure** máte plnou kontrolu nad operačním systémem a konfigurací instance SQL Server. Pomocí virtuálního počítače se můžete rozhodnout, kdy aktualizovat nebo upgradovat operační systém a software databáze a kdy se má nainstalovat další software, jako je například anti-virus. K dispozici jsou některé automatizované funkce ke značnému zjednodušení použití dílčích oprav, zálohování a zajištění vysoké dostupnosti. Kromě toho můžete nastavovat velikost virtuálního počítače, počet disků a jejich konfigurace úložiště. Azure umožňuje měnit velikost virtuálního počítače podle potřeby. Informace najdete v tématu věnovaném [velikostem virtuálních počítačů a cloudových služeb pro Azure](../virtual-machines/sizes.md).

## <a name="service-level-agreement-sla"></a>Smlouva o úrovni služeb (SLA)

V případě mnoha IT oddělení jsou povinnosti smlouvy o úrovni služeb (SLA) nejvyšší prioritou. V této části se podíváme na to, jaká smlouva SLA se vztahuje na každou z možností hostování databáze.

Pro **Azure SQL Database** i pro **Azure SQL Managed instance** poskytuje Microsoft smlouvu SLA s dostupností 99,99%. Nejnovější informace najdete v tématu [Smlouva o úrovni služeb](https://azure.microsoft.com/support/legal/sla/sql-database/).

**V případě SQL na virtuálním počítači Azure** poskytuje Microsoft smlouvu SLA s dostupností 99,95%, která pokrývá jenom virtuální počítač. Tato smlouva SLA nepokrývá procesy (například SQL Server) běžící na virtuálním počítači a vyžaduje, abyste v rámci skupiny dostupnosti hostovali minimálně dvě instance virtuálních počítačů. Nejnovější informace najdete v tématu věnovaném smlouvám [SLA k virtuálním počítačům](https://azure.microsoft.com/support/legal/sla/virtual-machines/). V případě vysoké dostupnosti databáze (HA) v rámci virtuálních počítačů byste měli nakonfigurovat jednu z podporovaných možností vysoké dostupnosti v SQL Server, jako jsou například [skupiny dostupnosti Always On](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Používání podporované možnosti vysoké dostupnosti neposkytuje další SLA, ale umožňuje dosáhnout více než 99.99% dostupnosti databáze.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Čas přechodu do Azure

**Azure SQL Database** je správné řešení pro aplikace navržené pro Cloud, když je vývojářská produktivita a rychlé uvedení na trh pro nová řešení důležitá. V případě programových funkcí podobných DBA jde o ideální řešení pro cloudové architekty a vývojáře, protože snižuje potřebu správy příslušného operačního systému a databáze.

**Spravovaná instance Azure SQL** značně zjednodušuje migraci stávajících aplikací do Azure a umožňuje tak rychle přenést migrované databázové aplikace do trhu Azure.

**SQL na virtuálním počítači Azure** je ideální, pokud vaše stávající nebo nové aplikace vyžadují velké databáze nebo mají přístup ke všem funkcím v SQL Server nebo Windows/Linux a chcete se vyhnout času a nákladům na získání nového místního hardwaru. To je dobré i v případě, že chcete migrovat existující místní aplikace a databáze do Azure v případě, že se SQL Database nebo spravovaná instance SQL nehodí. Vzhledem k tomu, že nepotřebujete měnit vrstvy prezentace, aplikace a dat, ušetříte čas a rozpočet při opětovném vytvoření architektury stávajícího řešení. Místo toho se můžete soustředit na migraci všech svých řešení do Azure a provedení některých optimalizací výkonu, která mohou být platformou Azure vyžadována. Další informace najdete v tématu [Osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Další kroky

- Pokud začínáte se službou SQL Database, přečtěte si téma [První Azure SQL Database](database/single-database-create-quickstart.md).
- Podívejte se na [první spravovanou instanci Azure SQL](managed-instance/instance-create-quickstart.md) , abyste mohli začít se službou SQL Managed instance. 
- Viz [SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/)
- Informace o tom, jak začít s SQL Serverem na virtuálních počítačích Azure, najdete v tématu věnovaném [zřízení virtuálního počítače s SQL Serverem v Azure](virtual-machines/windows/create-sql-vm-portal.md).
- [Identifikujte správnou SQL Database nebo SKU spravované instance SQL pro vaši místní databázi](/sql/dma/dma-sku-recommend-sql-db/).