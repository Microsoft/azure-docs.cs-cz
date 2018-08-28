---
title: Přehled Contoso migrace do Azure | Dokumentace Microsoftu
description: Poskytuje přehled scénářů společností Contoso používá k migraci jejich místní datacentra do Azure a strategie migrace.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: raynew
ms.openlocfilehash: 2dff49fb389ba220aae4dab0fd60b01fe2cada75
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43047587"
---
# <a name="contoso-migration-overview"></a>Migrace Contoso: Přehled


Tento článek ukazuje, jak smyšlenou organizaci Contoso migruje místní infrastrukturu [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) cloudu. 

Tento dokument je první z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci do Azure. Obsahuje informace a scénáře, které ukazují, jak nastavit migraci infrastruktury a spouštět různé druhy migrace. Scénáře jejich složitost v a přidáme další články v čase. Články popisují, jak společnosti Contoso dokončení poslání migrace, ale ukazatele Obecné čtení a konkrétní pokyny jsou k dispozici v průběhu.

## <a name="introduction"></a>Úvod

Azure poskytuje přístup ke komplexní sadě cloudových služeb. Jako vývojáři a odborníci na IT můžete tyto služby používat k vytváření, nasazení a správě aplikací na mnoha nástrojů a architektur díky globální síti datových center. Když vaše firma čelí výzvám souvisejícím s digitalizací, cloud Azure vám pomůže zjistit, jak optimalizovat prostředky a operace, zapojit zákazníky i zaměstnance a transformovat vaše produkty.

I přes všechny výhody, které cloud poskytuje z hlediska rychlosti a flexibility, minimalizace nákladů, vysokého výkonu a spolehlivost, si však v Azure uvědomujeme, že řada organizací bude potřebovat ještě nějakou dobu provozovat místní datacentra. V reakci na překážky přechodu do cloudu Azure poskytuje strategii hybridního cloudu, která propojí vaše místní datacentra s veřejným cloudem Azure. Například pomocí cloudových prostředků Azure, jako je Azure Backup k ochraně místní prostředky nebo pomocí Azure analytics získat přehled o místních úlohách. 

Jako součást hybridní cloudové strategie poskytuje Azure rozšiřující se řešení pro migraci místních aplikací a úloh do cloudu. Pomocí jednoduchých postupů můžete komplexně vyhodnotit své místní prostředky, abyste zjistili, jak si povedou v cloudu Azure. Když budete mít k dispozici podrobné posouzení, můžete bez obav migrovat prostředky do Azure. Po zprovoznění prostředků v Azure je můžete optimalizovat, abyste zachovali a vylepšili úroveň přístupu, flexibility, zabezpečení a spolehlivosti.

## <a name="migration-strategies"></a>Strategie migrace

Strategie pro migraci do cloudu dělí do čtyř kategorií: opětovným hostováním, refaktoring, úprava architektury nebo znovu sestavit. Strategie, kterou použijete, závisí na vaší obchodní faktory a migrace cíle. Několik strategií, které můžou přijmout. Například může chtít opětovným hostováním aplikací jednoduché (lift and shift) nebo aplikace, které nejsou zásadně důležité pro vaši firmu, ale úprava architektury ty, které jsou složitější a důležité obchodní informace. Podívejme se na strategie.


**Strategie** | **Definice** | **Kdy použít** 
--- | --- | --- 
**Opětovným hostováním** | Často označuje jako migrace "lift and shift". Tato možnost nevyžaduje změny kódu a umožňuje migraci stávajících aplikací do Azure rychle. Každé aplikaci, která se migrovaly jako je těžit z výhod cloudu bez rizik a náklady spojené s změny kódu. | Když potřebujete rychle přesunout aplikace do cloudu.<br/><br/> Pokud chcete přesunout aplikace bez její změny.<br/><br/> Pokud vaše aplikace dokáže tak, aby můžete využít [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) škálovatelnost po migraci.<br/><br/> Pokud aplikace jsou důležité pro vaši firmu, ale není nutné okamžité změny možnosti aplikace.
**Refaktoring** | Často označuje jako "opakovanému balení", refaktoring vyžaduje minimální změny aplikace, tak, aby se mohli připojit k [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/)a používání cloudových nabídek.<br/><br/> Například můžete migrovat existující aplikace do služby Azure App Service nebo Azure Kubernetes Service (AKS).<br/><br/> Nebo do možnosti, například Azure SQL Database Managed Instance, Azure Database for MySQL – Azure Database for PostgreSQL a Azure Cosmos DB může Refaktorovat relačních a nerelačních databází. | Pokud vaše aplikace můžete snadno vytvořen nový balíček pro práci v Azure.<br/><br/> Pokud chcete použít inovativních postupů DevOps poskytovaný platformou Azure, nebo uvažujete o DevOps s využitím kontejnerové strategie pro úlohy.<br/><br/> Pro operaci refaktoringu, je potřeba uvažovat o přenositelnost vaší existující základ kódu a dovednosti v oblasti k dispozici vývoje.
**Změna architektury** | Změna architektury pro migraci se zaměřuje na úpravách a rozšíření funkcí aplikace a kódové základny pro optimalizaci aplikací architektura škálovatelnosti cloudu.<br/><br/> Můžete třeba rozdělit jednolitou aplikací na skupinu mikroslužeb, které spolupracují a snadno škálovat.<br/><br/> Nebo může úprava architektury relačních a nerelačních databází do plně spravované řešení DBaaS, jako je Azure SQL Database Managed Instance, Azure Database for MySQL – Azure Database for PostgreSQL a Azure Cosmos DB. | Když aplikace potřebují hlavní revize začlenit nové funkce, nebo efektivně pracovat na cloudové platformě.<br/><br/> Pokud chcete použít stávající investice do aplikací, splnění požadavků na škálovatelnost, zavádět inovativní postupy Azure DevOps a minimalizovat použití virtuálních počítačů.
**Opětovné sestavení** | Opětovné sestavení má věci a krok ještě znovu sestavit aplikaci od začátku pomocí technologií, cloudu Azure.<br/><br/> Například může vytvořit zelené louce aplikací pomocí cloudových technologií, jako je Azure Functions, Azure AI, Azure SQL Database Managed Instance a Azure Cosmos DB. | Pokud chcete rychlý vývoj a stávající aplikace mají omezené funkce a životnost.<br/><br/> Až budete připravení urychlit obchodní inovace (včetně postupů DevOps, které poskytuje Azure), sestavovat nové aplikace pomocí technologií nativní pro cloud a využijte výhod rozvoj AI, Blockchain a IoT.

## <a name="migration-articles"></a>Články týkající se migrace

Články v této sérii jsou shrnuty v následující tabulce.  

- Každý scénář migrace doprovází mírně odlišné obchodních cílů, které určují strategii migrace.
- Pro každý scénář nasazení zajišťuje informace o obchodní faktory a cíle, navrhované architektura, postup k provedení migrace a doporučení pro čištění a další kroky po dokončení migrace.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
Článek 1: Přehled | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | V tomto článku
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Contoso spuštění posouzení jeho místní SmartHotel aplikaci běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní SmartHotel aplikaci. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.  
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje svou aplikaci SmartHotel virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici [článku 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje svou aplikaci SmartHotel virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje SmartHotel aplikace. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. | K dispozici [článku 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso se dokončí migrace lift and shift jeho Linux osTicket aplikace na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure Database for MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje svou aplikaci osTicket Linux do virtuálních počítačů Azure pomocí Site Recovery. Migrace databáze aplikace ke službě Azure Database for MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace do webové aplikace Azure a Azure SQL Database](contoso-migration-refactor-web-app-sql.md) | Contoso migruje jeho SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure pomocí Pomocníka s migrací databáze. | K dispozici.   
[Článek 10: Refaktorujte Linuxovou aplikaci v Azure webové aplikace a služby Azure Database for MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | K dispozici.
[Článku 11: Refaktorujte Team Foundation Server ve službě Visual Studio Team Services](contoso-migration-tfs-vsts.md) | Contoso migruje své místní nasazení serveru Team Foundation Server pro Visual Studio Team Services v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. | K dispozici.    
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici. 


V tomto článku, který Contoso nastaví všechny prvky infrastruktury potřebné k dokončení všech scénářů migrace. 







### <a name="demo-apps"></a>Ukázkové aplikace

V článcích používá dvě ukázkové aplikace - SmartHotel a osTicket.

- **SmartHotel360**: Tato aplikace byla vyvinutá společností Microsoft jako aplikace pro test, který vám pomůže při práci s Azure. Je k dispozici jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360). Je aplikace ASP.NET se připojení k databázi serveru SQL Server. Aplikace je aktuálně pro dva virtuální počítače VMware systémem Windows Server 2008 R2 a SQL Server 2008 R2. Aplikace virtuální počítače jsou hostované místně a spravované přes vCenter Server.
- **osTicket**: oddělení služeb open source lístků pro aplikaci, která běží na systému Linux. Můžete ji stáhnout [Githubu](https://github.com/osTicket/osTicket). Aktuální aplikace je na dva virtuální počítače VMware systémem Ubuntu 16.04 LTS, pomocí Apache 2, PHP 7.0 a MySQL 5.7
    

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastaví místní a infrastrukturu Azure a příprava na migraci.



