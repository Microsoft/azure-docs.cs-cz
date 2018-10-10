---
title: Contoso – škálování migraci do Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso zpracovává škálovaná migrace do Azure.
services: azure-migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: raynew
ms.openlocfilehash: 82674aec8d62388d0d0f26e730f66b8b078a5d75
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904164"
---
# <a name="contoso---scale-a-migration-to-azure"></a>Contoso – škálování migrace do Azure

V tomto článku se provádí Contoso migrace ve velkém měřítku do Azure. Zvažte, jak naplánovat a provést migraci více než 3 000 úloh, 8000 databáze a víc než 10 000 virtuálních počítačů.


Tento článek je jednou z řady článků, které dokumentují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje na pozadí a informace o plánování a nasazení scénářů, které ukazují, jak nastavit infrastrukturu migrace vyhodnotit vhodnost migrace místních prostředků a spouštět různé druhy migrace. Scénáře jejich složitost v. Přidáme do série článků v čase.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Contoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.   
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje do aplikace pomocí služby Site Recovery k migraci virtuálních počítačů aplikace a Database Migration Service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso se dokončí migrace lift and shift jeho Linux osTicket aplikace na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje svou aplikaci osTicket Linux do virtuálních počítačů Azure pomocí Site Recovery. Migrace databáze aplikace ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace do webové aplikace Azure a Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migraci své aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure pomocí Pomocníka s migrací databáze. | K dispozici.    
[Článek 10: Refaktorujte Linuxovou aplikaci v Azure webové aplikace a služby Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje jeho Linuxovou aplikaci osTicket do webové aplikace Azure v několika lokalitách. Webová aplikace je integrovaná se službou GitHub pro průběžné doručování. Migrace databáze aplikace ke službě Azure Database for MySQL instance. | K dispozici.
[Článku 11: Refaktorujte Team Foundation Server na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a app databáze s využitím Azure SQL Database. | K dispozici.    
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici. 
Článek 14: Škálování migrace do Azure | Po vyzkoušení kombinace migrace, Contoso je nyní probíhá příprava škálovat na plné migraci do Azure. | V tomto článku

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste, způsobí tlak na místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zjednodušte procesy pro vývojáře a uživatele. Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku. Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: růstem podniku zvládla úspěšně, Contoso IT tým musí poskytovat systémy, které mohou rozšířit stejným tempem.
- **Zlepšení nákladový model**: Contoso chce snížit kapitálové požadavky v rozpočtu na IT.  Contoso chce používat cloudové možnosti škálování a snížit nutnost drahého hardware.
- **Nižší náklady na licencování**: Contoso chce minimalizovat náklady na cloud.


## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používá k určení nejlepší metody migrace.

**Požadavky** | **Podrobnosti**
--- | --- 
**Rychle přesunout do Azure** | Contoso chce tak rychle přesouvat virtuální počítače a aplikace do Azure.
**Kompilace zobrazí úplný výpis** | Contoso chce, aby se úplný seznam všech aplikací, databází a virtuální počítače v organizaci.
**Vyhodnocení a klasifikaci aplikací** | Contoso chce, aby se plně využít cloud. Jako výchozí Contoso předpokládá, že všechny služby se spustí jako PaaS. IaaS se použije, kde není vhodné PaaS. 
**Trénování a přesunout na DevOps** | Contoso chce přejít na DevOps modelu. Contoso bude poskytovat školení Azure a DevOps a týmy reorganizovat podle potřeby. 


Po Připnutí dolů cíle a požadavky, Contoso kontroly IT zázemí a identifikuje proces migrace.

## <a name="current-app"></a>Aktuální aplikaci

Po plánování a nastavení [infrastruktury Azure](contoso-migration-infrastructure.md) a vyzkoušejte si různé kombinace testování konceptu (POC) migrace popsané v předchozí tabulce, Contoso je připravený k provedení úplné migrace do Azure ve velkém měřítku. Tady je Contoso chce migrovat.

**Položka** | **Svazek** | **Podrobnosti**
--- | --- | ---
**Úlohy** | Více než 3000 aplikace | Aplikace běží na virtuálních počítačích.<br/><br/>  Aplikace jsou Windows, na základě SQL a OSS LAMP.
**Databáze** | Přibližně 8,500 | Databáze zahrnují SQL Server, MySQL, PostgreSQL.
**Virtuální počítače** | Více než 10 000 operací | Virtuální počítače spustit na hostiteli VMware a spravované přes vCenter servery.


## <a name="migration-process"></a>Proces migrace

Teď, když Contoso jste připnuli obchodních faktorů a cílů migrace, určuje čtyři kombinovaným přístup pro proces migrace:

- **Fáze 1 – posouzení**: Přečtěte si o aktuální prostředky a zjistit, jestli jsou vhodné k migraci do Azure.
- **Fáze 2 – Migrace**: přesunutí prostředků do Azure. Jak se přesouvají aplikace a objektů do Azure bude záviset na aplikaci, a to, co chtějí dosáhnout.
- **Fáze 3 – optimalizace**: Po přesunutí prostředků do Azure, Contoso musí zlepšit a zjednodušit maximálního výkonu a účinnosti.
- **Fáze 4 zabezpečení a správu**: všechno v místě společnosti Contoso nyní používá Azure zabezpečení a správu prostředků a služeb k řízení, zabezpečení a monitorovat své cloudové aplikace v Azure.


Tyto fáze nejsou sériového portu v organizaci. Každá část společnosti Contoso projekt migrace budou v různých fází procesu vyhodnocení a migraci. Optimalizace, zabezpečení a správu bude probíhající v čase.


## <a name="phase-1-assess"></a>Fáze 1: posouzení

Contoso zahajuje proces zjištěním a posouzením místních aplikací, dat a infrastruktury. Zde je, co udělá Contoso:

- Contoso potřebuje ke zjištění aplikací, mapování závislostí napříč aplikacemi a při rozhodování o pořadí migrace a priority.
- Jak vyhodnocuje Contoso se sestaví si ucelený soupis prvků aplikacím a prostředkům. Spolu s novou inventáře Contoso použije a aktualizovat existující konfigurace správy databáze (CMDB) a katalogu služeb.
    - Databáze CMDB obsahuje technické konfigurace pro aplikace Contoso.
    - Katalog služeb dokumenty provozní údaje aplikací, včetně přidružených obchodními partnery a smlouvy o úrovni služeb (SLA)

### <a name="discover-apps"></a>Objevení aplikací

Contoso spustí tisíce aplikací napříč různými servery. Contoso potřebuje nejen CMDB a katalogu služeb, nástrojů pro zjišťování a posouzení. 

- Nástroje musíte zadat mechanismus, který bude zajišťovat přísun dat hodnocení do procesu migrace.
- Nástroje pro vyhodnocení, musíte zadat data, která pomáhá vytvářet inteligentní inventář fyzických a virtuálních prostředků společnosti Contoso. Data by měla obsahovat informace o profilu a metriky výkonu.
- Po dokončení zjišťování Contoso by měl mít ucelený inventář prostředků a metadata spojená s nimi. Tento seznam se použije k definování plánu migrace.

### <a name="identify-classifications"></a>Identifikujte klasifikace

Contoso identifikuje některé běžné kategorie ke klasifikaci prostředků v inventáři. Tyto klasifikace jsou důležité pro rozhodnutí společnosti Contoso pro migraci. Seznam klasifikací pomáhá určit priority migrace a identifikovat složité problémy.

**Kategorie** | **Přiřazené hodnoty** | **Podrobnosti**
--- | --- | ---
Obchodní skupina | Seznam názvů skupin firmy | Která skupina je zodpovědný za skladové položky?
Kandidát POC | A/N | Aplikace slouží jako POC nebo typu early adopter pro migraci do cloudu?
Technický dluh | Žádný/některé/závažnost | Skladová položka běží nebo pomocí produktu na více instancí, platformy nebo operační systém?
Důsledky brány firewall | A/N | Aplikace komunikovat s Internetem/mimo provoz?  Zajistíte jejich integraci s bránou firewall?
Problémy se zabezpečením | A/N | Jsou známy problémy se zabezpečením s aplikací?  Používá aplikaci zastaralé platformy nebo nešifrovaná data?


### <a name="discover-app-dependencies"></a>Zjištění závislosti aplikace

Contoso jako součást procesu posouzení, musí identifikovat, kdy aplikace běží a zjistit, závislosti a připojení mezi servery aplikace. Contoso mapuje prostředí v krocích.

1. Jako první krok Contoso zjistí, jak mapovat na jednotlivými aplikacemi, umístění v síti a skupiny serverů a počítačů.
2. Pomocí těchto informací můžete Contoso tak jasně identifikovat aplikace, které mají několik závislosti a jsou proto vhodné pro rychlé migrace.
3. Contoso můžete použít mapování mohli identifikovat složitější závislosti a komunikace mezi servery aplikací. Contoso můžete skupinu těchto serverů logicky k reprezentaci aplikací a plánování strategie migrace na základě těchto skupin.


Mapování bylo dokončeno, můžete zajistit Contoso identifikovaná a zahrnuté při sestavování plánu migrace všech komponent aplikace. 

![Mapování závislostí](./media/contoso-migration-scale/dependency-map.png)


### <a name="evaluate-apps"></a>Hodnocení aplikace

V posledním kroku v procesu zjišťování a posouzení můžete vyhodnotit Contoso výsledky posouzení a mapování zjistit, jak migrovat každou aplikaci v katalogu služeb. 

K zachycení tento proces hodnocení, přidávají do inventáře pár dalších klasifikace.

**Kategorie** | **Přiřazené hodnoty** | **Podrobnosti**
--- | --- | ---
Obchodní skupina | Seznam názvů skupin firmy | Která skupina je zodpovědný za skladové položky?
Kandidát POC | A/N | Aplikace slouží jako POC nebo typu early adopter pro migraci do cloudu?
Technický dluh | Žádný/některé/závažnost | Skladová položka běží nebo pomocí produktu na více instancí, platformy nebo operační systém?
Důsledky brány firewall | A/N | Aplikace komunikovat s Internetem/mimo provoz?  Zajistíte jejich integraci s bránou firewall?
Problémy se zabezpečením | A/N | Jsou známy problémy se zabezpečením s aplikací?  Používá aplikaci zastaralé platformy nebo nešifrovaná data?
Strategie migrace | Metody opětovného hostování nebo Refaktorujte/změna architektury nebo opětovné sestavení | Jaký typ migrace je potřeba pro aplikaci? Jak se v Azure nasadí aplikaci? [Další informace](contoso-migration-overview.md#migration-strategies).
Technické složitost | 1-5 | Jak komplexní je migrace? Tato hodnota musí být definován ve společnosti Contoso DevOps a relevantní partnery.
Obchodní závažnosti | 1-5 | Jak důležité je aplikace pro firmy? Například aplikace malé pracovní skupiny může být přiřazeno skóre, zatímco kritické aplikace používané v organizaci může být přiřazeno skóre pět. Toto skóre bude mít vliv migrace úroveň priority.
Priorita migrace | 1/2/3 | Jaké migrace prioritu pro aplikaci?
Migrace rizika  | 1-5 | Co je úroveň rizika pro migraci aplikace? Tato hodnota by měla dohodnutých Contoso DevOps a relevantní partnery.




### <a name="figure-out-costs"></a>Zjistit náklady

Zjistěte, náklady a potenciální úspory migrace do Azure, můžete použít Contoso [Kalkulačka celkové náklady o vlastnictví (TCO)](https://azure.microsoft.com/pricing/tco/calculator/) pro výpočet a porovnejte celkové náklady na vlastnictví pro Azure s srovnatelné v místním nasazení.

### <a name="identify-assessment-tools"></a>Identifikujte posuzovací nástroje

Contoso rozhodne, který nástroj pro zjišťování, vyhodnocení a vytváření skladu. Contoso identifikuje kombinaci nástrojů Azure a služby, nástroje pro nativní aplikace a skripty a nástroje partnera. Contoso je zejména zajímá jak Azure Migrate můžete použít k vyhodnocení ve velkém měřítku.


#### <a name="azure-migrate"></a>Azure Migrate


Služba Azure Migrate vám umožní zkoumat a posuzovat virtuální počítače VMware v místním, v rámci přípravy na migraci do Azure. Zde je, co Azure Migrate umožňuje:

1. Vyhledávání: Zjišťování místních virtuálních počítačů VMware.
    - Azure Migrate podporuje zjišťování z více servery vCenter (sériově) a v samostatné projekty Azure Migrate můžete spustit zjišťování.
    - Azure Migrate provádí zjišťování, a to znamená, že na virtuálním počítači VMware Migrate Collector spuštěná. Stejné kolekce můžete zjištění virtuálních počítačů na jiný vCenter servery a odesílání dat do různých projektů.
1. Vyhodnocení připravenosti: vyhodnoťte, jestli se místní počítače jsou vhodné pro spuštění v Azure. Posouzení obsahuje:
    - Doporučená velikost: Získejte doporučení k velikosti pro virtuální počítače Azure, na základě historie výkonu místních virtuálních počítačů.
    - Odhadované měsíční náklady: Získejte odhadované náklady na provoz místních počítačů v Azure.
2. Určení závislostí: vizualizací závislostí místních počítačů k vytváření skupin počítačů optimální pro vyhodnocení a migraci.


![Azure Migrate](./media/contoso-migration-scale/azure-migrate.png)


##### <a name="migrate-at-scale"></a>Škálovaná migrace

Contoso je potřeba správně používat službu Azure Migrate poskytnou škálování migrace. 

- Contoso provede posouzení aplikací – pomocí služby Azure Migrate. Tím se zajistí, že Azure Migrate vrací včas data na portál Azure Portal.
- Přečtěte si správce společnosti Contoso [nasazení Azure Migrate ve velkém měřítku](how-to-scale-assessment.md)
- Contoso – zpráva k Azure Migrate omezení uvedené v následující tabulce.


**Akce** | **Limit**
--- | ---
Vytvoření projektu Azure Migrate | 1 500 virtuálních počítačů
Zjišťování | 1 500 virtuálních počítačů
Posouzení | 1 500 virtuálních počítačů

Contoso budete používat Azure Migrate následujícím způsobem:

- V systému vCenter bude Contoso uspořádání virtuálních počítačů do složek. To usnadní jim zaměřit při jejich spuštění posouzení ve virtuálních počítačích v určité složce.
- Azure Migrate využívá mapu služeb Azure k vyhodnocení závislostí mezi počítači. To vyžaduje agenty nainstalovat na virtuální počítače, které použit k vyhodnocení. 
    - Contoso použije automatizované skripty pro instalaci požadovaných agentů Windows nebo Linux.
    - Pomocí skriptů, nasdílet Contoso instalace na virtuální počítače v rámci složky serveru vCenter.


#### <a name="database-tools"></a>Databázové nástroje

Kromě Azure Migrate Contoso se soustředí na používání nástroje speciálně pro účely posouzení databáze. Nástroje, jako [pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) vám pomůže vyhodnotit databáze systému SQL Server na migraci.

Data Migration Assistant (DMA) může pomoct Contoso zjistili, jestli jsou kompatibilní s řešeními pro databázi oblast Azure, jako je Azure SQL Database, SQL Server běžící na virtuálním počítači Azure IaaS a spravované Instance Azure SQL místních databází. 

Kromě DMS Contoso má jiné skripty, které používají ke zjištění a dokumentace databází systému SQL Server. Jsou umístěny v úložišti na Githubu.

#### <a name="partner-tools"></a>Partnerské nástroje

Existuje několik dalších partnerů nástroje, které společnost Contoso může pomoct při posuzování v místním prostředí pro migraci do Azure. [Další informace](https://azure.microsoft.com/migration/partners/) o partnerů Azure Migration.  

## <a name="phase-2-migrate"></a>Fáze 2: migrace

Jejich hodnocení kompletní Contoso potřebuje k identifikaci nástroje pro přesun jejich aplikací, dat a infrastruktury do Azure. 




### <a name="migration-strategies"></a>Strategie migrace

Existují čtyři strategie široké migrace, které můžete zvážit Contoso. 

**Strategie** | **Podrobnosti** | **Využití**
--- | --- | ---
**Opětovným hostováním**  | Často označuje jako migrace "metodou lift and shift", to je možnost bez kódu pro migraci stávajících aplikací do Azure rychle.<br/><br/> Aplikace se migrují jako-je výhody cloudu bez rizik nebo nákladů spojených se změny kódu. | Contoso můžete opětovným hostováním méně strategické aplikace, vyžadování bez jediné změny kódu.
**Refaktoring** |  Označuje se také jako "opakovanému balení", tato strategie vyžaduje minimální aplikaci kód nebo je nutné změny konfigurace pro připojení aplikace k Azure PaaS a lépe využívat možnosti cloudu. | Společnost Contoso může Refaktorovat strategické aplikace chcete zachovat stejné základní funkce, ale jejich spuštění na platformě Azure jako je například Azure App Services přesunout.<br/><br/> To vyžaduje změny minimální kód.<br/><br/> Na druhé straně Contoso muset udržovat platforma virtuálního počítače, protože to nebude spravovaný microsoftem.
**Změna architektury** | Tato strategie upraví nebo rozšiřuje základní optimalizace architektury aplikace pro škálování a možnosti cloudu kódu aplikace.<br/><br/> Je Správa modernizuje aplikace do odolné, vysoce škálovatelné a umožňují nezávislé nasazení architektury.<br/><br/> Služby Azure můžete urychlit proces, škálovat aplikace s jistotou a spravovat aplikace s lehkostí a elegancí.
**Opětovné sestavení** | Tato strategie znovu sestaví aplikaci úplně od začátku pomocí nativních cloudových technologií.<br/><br/> Platforma jako služba (PaaS) Azure poskytuje úplné prostředí vývoje a nasazení v cloudu. Eliminuje nějaké náklady a složitost softwarových licencí a tím eliminuje potřeba základní infrastruktury aplikace, middleware a další prostředky. | Contoso můžete přepsat kritické aplikace od základů, využívat výhod cloudových technologií, například v počítači bez serveru, nebo mikroslužeb.<br/><br/> Contoso budou spravovat aplikace a služby, které se vyvíjí a Azure spravuje všechno ostatní.


Data musíte také zvážit, zejména s svazku databáze, které má společnost Contoso. Společnosti Contoso výchozí přístupem je použití služeb PaaS, jako je Azure SQL Database plně využít cloudové funkce. Přechodem na službu PaaS pro databáze Contoso bude mít jenom zachovat data, byste museli opustit základní platformy společnosti Microsoft.

### <a name="evaluate-migration-tools"></a>Hodnocení nástroje pro migraci

Contoso jsou primárně pomocí několika služeb Azure a nástroje pro migraci:

- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview): Orchestruje zotavení po havárii a migraci místních virtuálních počítačů do Azure.
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview): migraci místních databází, jako je například SQL Server, MySQL a Oracle do Azure.


#### <a name="azure-site-recovery"></a>Azure Site Recovery

Azure Site Recovery je primární služby Azure pro orchestraci zotavení po havárii a migraci z v rámci Azure a z místní lokality do Azure.

1. Site Recovery umožňuje, orchestruje replikaci z místních serverů do Azure.
2. Při replikaci je nastavený a spuštěný, pro místní počítače mohou být přebrány do Azure, dokončení migrace.

Contoso již [dokončit POC](contoso-migration-rehost-vm.md) zobrazíte Site Recovery může přispět jak provést migraci do cloudu.

##### <a name="using-site-recovery-at-scale"></a>Pomocí Site Recovery ve velkém měřítku

Plány společnosti Contoso na spouštění více migrací lift and shift. K zajištění, že to funguje, Site Recovery budete replikovat dávky přibližně 100 virtuálních počítačů najednou. Zjistěte, jak to fungovalo, Contoso potřebuje k plánování kapacit pro navrhovaná migrace Site Recovery.

- Contoso potřebuje ke shromažďování informací o svazcích jejich provoz. Zejména:
    - Contoso potřebuje k určení frekvence změn pro virtuální počítače chce replikovat.
    - Contoso potřebuje také zohledňují připojení k síti z místní lokality do Azure.
- V reakci na kapacitu a požadavky na svazek bude Contoso nutné přidělit dostatečnou šířku pásma v závislosti na denní frekvenci změn dat u požadované virtuální počítače, které vyhoví cíl bodu obnovení (RPO).
- A konečně musí zjistit, kolik serverů jsou potřeba ke spuštění součásti Site Recovery, které jsou potřebné pro nasazení.

###### <a name="gather-on-premises-information"></a>Shromažďování informací v místním
Můžete použít contoso [Site Recovery Deployment Planner](https://docs.microsoft.com/azure/site-recovery/site-recovery-deployment-planner) nástroj k provedení těchto kroků:

- Contoso můžete použít nástroj pro vzdáleně Profilovat virtuální počítače bez vlivu na produkční prostředí. To pomáhá identifikovat požadavky na šířku pásma a úložiště pro replikaci a převzetí služeb při selhání.
- Nástroj můžete spustit contoso bez jakékoli Site Recovery komponenty místní instalace.
- Nástroj shromažďuje informace o kompatibilních a nekompatibilních virtuálních počítačích, discích jednotlivých virtuálních počítačů, a četnost změn dat na disku. Také identifikuje požadavky na šířku pásma sítě a infrastrukturu Azure potřebnou pro úspěšnou replikaci a převzetí služeb při selhání.
- Contoso je potřeba zajistit, aby, spusťte nástroj Plánovač počítače s Windows serverem, které odpovídá minimální požadavky pro konfigurační server Site Recovery. Konfigurační server je počítač Site Recovery, který je nezbytný, aby bylo možné replikovat místní virtuální počítače VMware.


###### <a name="identify-site-recovery-requirements"></a>Identifikujte požadavky služby Site Recovery

Kromě replikuje virtuální počítače Site Recovery vyžaduje několik součástí pro migraci VMware.

**Komponenta** | **Podrobnosti**
--- | ---
**Konfigurační server** | Obvykle nastavení virtuálního počítače VMware pomocí šablony OVF.<br/><br/> Komponenta konfiguračního serveru koordinuje komunikaci mezi místním prostředím a Azure a spravuje replikaci dat.
**Procesový server** | Obvykle se instaluje na konfigurační server.<br/><br/> Procesní server přijímá data replikace; optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odesílá je do úložiště Azure.<br/><br/> Procesní server nainstaluje služba Mobility Azure Site Recovery na virtuálních počítačích, které chcete replikovat, a provádí automatické zjišťování místních počítačů.<br/><br/> Dokončeno škálování nasazení je nutný další, samostatné procesní servery pro zpracování velkých objemů objemu replikačních přenosů.
**Služba mobility** | Na každý virtuální počítač VMware, které se budou migrovat pomocí Site Recovery se nainstaluje agent služby Mobility.  

Contoso potřebuje zjistit, jak nasadit tyto komponenty založené na důležité informace o kapacitě.

**Komponenta** | **Požadavky na kapacitu**
--- | ---
**Maximální denní frekvenci změn** | Jeden procesový server dokáže zpracovat denní změnu přenosové rychlosti až do velikosti 2 TB. Protože virtuální počítač můžete použít pouze jeden procesový server, je maximální denní četnost změn dat, která je podporována pro replikovaný virtuální počítač 2 TB.
**Maximální propustnost** | Účet úložiště Azure úrovně standard dokáže zpracovat až 20 000 požadavků za sekundu a vstupně výstupní operace za sekundu (IOPS) napříč replikaci virtuálního počítače musí být v rámci tohoto limitu. Například pokud virtuální počítač má 5 disků a každý disk generuje 120 vstupně-výstupních operací (velikosti 8 kB) na virtuálním počítači, pak bude v rámci Azure za maximální IOPS na disku 500.<br/><br/> Všimněte si, že počet účtů úložiště potřebných je rovna celkové zdrojový počítač vstupně-výstupních operací, dělený 20 000. Replikované počítače můžou patřit pouze do jednoho účtu úložiště v Azure.
**Konfigurační server** | Podle společnosti Contoso odhad replikace 100 = 200 virtuálních počítačů najednou a [požadavky na nastavení velikosti konfiguračního serveru](https://docs.microsoft.com/azure/site-recovery/site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server), Contoso odhadnout je musí počítač s konfigurací serveru následujícím způsobem:<br/><br/> Využití procesoru: 16 virtuálních procesorů (2 sockets * @ 2,5 GHz 8 jader)<br/><br/> Paměti: 32 GB<br/><br/> Mezipaměť disku: 1 TB<br/><br/> Frekvence změny dat: 1 TB na 2 TB.<br/><br/> Kromě požadavků na změny velikosti Contoso muset Ujistěte se, že je konfigurační server optimálně umístěné ve stejné síti a segment sítě LAN jsou virtuální počítače, které se budou migrovat.
**Procesový server** | Contoso se nasadit samostatný vyhrazené procesní server s možností replikace 100 až 200 virtuálních počítačů:<br/><br/> Využití procesoru: 16 virtuálních procesorů (2 sockets * @ 2,5 GHz 8 jader)<br/><br/> Paměti: 32 GB<br/><br/> Mezipaměť disku: 1 TB<br/><br/> Frekvence změny dat: 1 TB na 2 TB.<br/><br/> Procesový server bude obtížné pracovní a jako takové se musí nacházet na hostiteli ESXi, který dokáže zpracovat vstup/výstup disku, síťový provoz a procesoru vyžadované pro replikaci. Contoso zohlední vyhrazeného hostitele pro tento účel. 
**Sítě** | Contoso má zkontrolovat aktuální infrastrukturu sítě VPN site-to-site a rozhodla implementovat Azure ExpressRoute. Implementace je důležité, protože bude nižší latenci a zvýšení šířky pásma pro primární oblast východní USA 2 platformě Azure společnosti Contoso.<br/><br/> **Monitorování**: Contoso potřeba pečlivě monitorovat data přenášejí z procesového serveru. Pokud data přetížení šířky pásma sítě bude předpokládat, že Contoso [omezení šířky pásma proces serveru](https://docs.microsoft.com/azure/site-recovery/site-recovery-plan-capacity-vmware.md#control-network-bandwidth).
**Úložiště Azure** | Pro migraci musí identifikovat Contoso správný typ a počet cílových účtů služby Azure storage.  Site Recovery replikuje data virtuálního počítače do služby Azure storage.<br/><br/> Site Recovery můžete replikovat do účtů úložiště (SSD) úrovně standard nebo premium.<br/><br/> Při rozhodování o úložišti, musíte zkontrolovat Contoso [omezení úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)a zvážit očekávaný růst a zvyšováním využití v čase. Vzhledem k rychlost a priority migrace, Contoso se rozhodl používat účty úložiště úrovně premium.<br/><br/> Uživatelé budou vytvářet a opakovaně používat více účtů úložiště během procesu migrace.
Contoso se rozhodli použít spravované disky pro všechny virtuální počítače, které jsou nasazeny do Azure.  Vstupně-výstupních operací, vyžaduje se určí, jestli se disky být Standard (HDD) nebo Premium (SSD).<br/>.<br/>


#### <a name="data-migration-service"></a>Data Migration Service 

Azure Database Migration Service (DMS), je plně spravovaná služba, která umožňuje bezproblémovou migraci z několika databázových zdrojů na platformu dat Azure s minimálními prostoji.

- DMS integruje funkce existujících nástrojů a služeb. Data Migration Assistant (DMA), použije k vygenerování sestavy vyhodnocení, které přesně určit doporučení týkající se kompatibility databáze a všechny požadované změny.
- DMS používá jednoduchého, samostatně prováděného migračního procesu, inteligentní hodnocení, která pomáhá podchycení potenciálních problémů před migrací.
- DMS můžete migrovat ve velkém měřítku z několika zdrojů do cílové databáze Azure.
- DMS poskytuje podporu z SQL serveru 2005 na SQL Server 2017.

DMS není pouze Microsoft databáze nástroj pro migraci. Získání [porovnání nástrojů a služeb](https://blogs.msdn.microsoft.com/datamigration/2017/10/13/differentiating-microsofts-database-migration-tools-and-services/).

###### <a name="using-dms-at-scale"></a>Pomocí DMS ve velkém měřítku

Společnost Contoso použije DMS při migraci z SQL serveru.  

- Při zřizování DMS, je potřeba zajistit, že má správnou velikost a nastavit za účelem optimalizace výkonu pro migrace dat Contoso. Contoso vybere možnost "s 4 virtuální jádra pro důležité obchodní informace vrstvy", což umožní službě a využijte výhod více virtuálních procesorů pro paralelizaci a rychlejší přenos dat.

    ![DMS škálování](./media/contoso-migration-scale/dms.png)

- Další škálování taktika pro doménu Contoso je dočasně vertikálního navýšení cílová instance Azure SQL nebo MySQL Database na úrovni Premium SKU během migrace dat. Tím se minimalizují databáze omezení, které můžou ovlivnit aktivity přenos dat při použití SKU nižší úrovně.



##### <a name="using-other-tools"></a>Pomocí jiných nástrojů

Kromě DMS použít Contoso ostatními nástroji a službami pro informace o virtuálním počítači.

- Mají skripty, které pomůžou s ruční migrace. Jsou dostupné v úložišti na Githubu.
- Řadu [partnerské nástroje](https://azure.microsoft.com/migration/partners/) lze také použít pro migraci.


## <a name="phase-3-optimize"></a>Fáze 3: optimalizace

Po Contoso přesunutí prostředků do Azure, které potřebují k zjednodušení je můžete zvyšovat výkon a zajišťovat maximální návratnost investice pomocí nástrojů pro správu nákladů. Zadaný, že Azure je služba platby pro použití, je velmi důležité pro společnost Contoso, abyste pochopili, jaký výkon systémů a ujistěte se, že jsou správně velká.


### <a name="azure-cost-management"></a>Azure cost management

Chcete-li své cloudové investice na maximum, bude Contoso využívat bezplatný nástroj Azure Cost Management.

- Toto licencovaný řešení vytvořených Cloudyn, pobočka Microsoftu, umožní spravovat cloudové výdaje transparentně a přesně Contoso.  Poskytuje nástroje pro monitorování, přidělování a omezte náklady na cloud.
- Azure Cost Management poskytuje sestavy jednoduchého řídicího panelu, které pomáhají s přidělováním nákladů, chargeback a započítávání pohledávek.
- Služba Cost Management můžete optimalizovat výdaje za cloud díky identifikaci nevyužitých prostředků, které Contoso pak můžete spravovat a upravovat.
- [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.

    
![Správa nákladů](./media/contoso-migration-scale/cost-management.png)  
    
 
### <a name="native-tools"></a>Nativních nástrojů

Společnost Contoso použije také skripty pro vyhledání nevyužité prostředky.

- Během migrace velký často existují zbylé části data, jako jsou virtuální pevné disky (VHD), které se vám účtovat poplatek, ale žádná hodnota poskytovat společnosti. Skripty jsou dostupné v úložišti na Githubu.
- Contoso bude využívat práci od Microsoftu v IT oddělení a zvažte implementaci sada nástrojů optimalizace prostředků Azure (ARO).
- Contoso můžete nasadit účet Azure Automation s předem nakonfigurované sady runbook a plány do svého předplatného a začněte šetřit peníze. Optimalizace prostředků Azure se stane automaticky v rámci předplatného po plánu je povolený nebo vytvořili, včetně optimalizaci pro nové prostředky.
- To poskytuje možnosti decentralizované automatizace ke snížení nákladů. K funkcím patří:
    - Nízké využití procesoru podle virtuálních počítačů Azure automaticky možnost odložit.
    - Plánování virtuálních počítačů Azure a připomenout znovu unsnooze.
    - Plánování virtuálních počítačů Azure připomenout znovu nebo unsnooze ve vzestupném a sestupném pořadí pomocí značky Azure.
    - Hromadné odstranění prostředku skupiny na vyžádání.
- Začínáme se sadou nástrojů ARO v tomto [úložiště GitHub se vzorovými](https://github.com/Azure/azure-quickstart-templates/tree/master/azure-resource-optimization-toolkit).

### <a name="partner-tools"></a>Partnerské nástroje

Partnerské nástroje, jako například [Hanu](https://hanu.com/insight/) a [Scalr]( https://www.scalr.com/cost-optimization/) můžete využít.


## <a name="phase-4-secure--manage"></a>Fáze 4: Zabezpečení a Správa

V této fázi Contoso využívá zabezpečení a správu prostředků Azure k řízení, zabezpečení a monitorování cloudových aplikací v Azure. Tyto prostředky pomoc při spuštění prostředí zabezpečený a dobře spravovaný při používání produktů, které jsou k dispozici na webu Azure Portal. Contoso začne používat tyto služby během migrace a díky podpoře Azure hybrid pokračuje v používání spousta z nich zajistili konzistentní prostředí napříč hybridním cloudem.


### <a name="security"></a>Zabezpečení
Contoso spoléhat na Azure Security Center pro jednotnou správu zabezpečení a pokročilou ochranu před hrozbami napříč hybridními cloudovými úlohami.

- Security Center poskytuje úplný přehled o a kontrolu nad, zabezpečení cloudových aplikací v Azure.
- Contoso můžete rychle zjišťovat a provést akci v reakci na hrozby a snížila zranitelnost zabezpečení tím, že adaptivní ochranu proti.

[Další informace](https://azure.microsoft.com/services/security-center/) o službě Security Center. 


### <a name="monitoring"></a>Monitorování

Contoso potřebuje přehled o stavu a výkonu nově vydaný, migrovaný aplikací, infrastruktury a dat teď s Azure. Contoso bude využívat integrovanou cloudu Azure monitoring nástroje, jako je Azure Monitor, Log Analytics a Application Insights.
 
- Pomocí těchto nástrojů Contoso můžete snadno shromažďovat data ze zdroje a získat podrobné informace. Contoso například můžete odhadnout, disků a paměť využití procesoru pro virtuální počítače, zobrazit aplikace a síťové závislosti napříč několika virtuálních počítačů a sledovat výkon aplikací.
- Contoso použije tyto cloudové nástroje pro monitorování bude akce a integraci s řešeními služby.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) o monitorování Azure.

### <a name="bcdr"></a>BCDR 

Contoso potřebovat kontinuity podnikových procesů a po havárii (BCDR) strategii zotavení pro svoje prostředky Azure.

- Azure poskytuje [integrované funkcemi BCDR](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications) zachováte data bezpečné a aplikací nebo služeb Team Foundation rychle zprovoznit. 
- Kromě předdefinovaných funkcí Contoso chce zajistit, že může zotavení z chyb, vyhnout se nákladnému přerušení služeb, splnit cíle dodržování předpisů a chránit data před ransomwarem a lidskou chybou. Provedete to tak
    - Contoso nasadí Azure Backup jako nákladově úsporné řešení pro zálohování prostředků Azure. Protože je integrované, Contoso můžete nastavit cloudové zálohy v pár jednoduchých kroků.
    - Contoso se nastavit zotavení po havárii pro virtuální počítače Azure pomocí Azure Site Recovery pro replikaci, převzetí služeb při selhání a navrácení služeb po obnovení mezi oblastmi Azure, které určuje.  Tím se zajistí, že aplikace běžící na virtuálních počítačích Azure zůstane k dispozici v sekundární oblasti, které vyberete společnosti Contoso, pokud dojde k výpadku v primární oblasti. [Další informace](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart).

 
## <a name="conclusion"></a>Závěr

V tomto článku Contoso plánovaná pro migraci do Azure ve velkém měřítku.  Proces migrace jsou rozdělené na čtyři fáze. Z vyhodnocení a migraci prostřednictvím optimalizace, zabezpečení a správy poté, co byla migrace dokončit. Nejčastěji je důležité k plánování migrace projektu jako celého procesu, ale migrace systémů v rámci organizace podle sady rozdělit do klasifikace a čísla, které dávají smysl pro firmy. Vyhodnocení dat a použití klasifikace, a projekt je možné rozdělit do řady menší migrace, které můžou běžet rychle a bezpečně.  Součet těchto menších migrace rychle promění velké do velké úspěšná migrace do Azure.
