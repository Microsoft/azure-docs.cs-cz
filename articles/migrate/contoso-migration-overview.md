---
title: Přehled Contoso migrace do Azure | Dokumentace Microsoftu
description: Poskytuje přehled scénářů společností Contoso používá k migraci jejich místní datacentra do Azure a strategie migrace.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: be9302d47766ddc7d75496e28e9b18a8e870c878
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216236"
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
Článek 1: Přehled | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | Tento článek.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL Server pomocí Pomocníka s migrací databáze. | K dispozici.
[Článek 4: Změna hostitele aplikací na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro místní SmartHotel aplikaci. Contoso migruje front-endové aplikace virtuálního počítače pomocí Azure Site Recovery a database aplikace do spravované Instance SQL, pomocí Azure Database Migration Service. | K dispozici.
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak společnosti Contoso aplikaci SmartHotel virtuální počítače migrovat do virtuálních počítačů Azure pomocí služby Azure Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikací na virtuálních počítačích Azure a skupina dostupnosti AlwaysOn SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace pomocí Azure Site Recovery k migraci virtuálních počítačů aplikace a Azure Database Migration Service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí Azure Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Azure Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace do instance serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket webovou aplikaci Azure ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci serveru Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive Services a Cosmos DB. | K dispozici.






### <a name="demo-apps"></a>Ukázkové aplikace

V článcích používá dvě ukázkové aplikace - SmartHotel a osTicket.

- **SmartHotel360**: Tato aplikace byla vyvinutá společností Microsoft jako aplikace pro test, který vám pomůže při práci s Azure. Je k dispozici jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360). Je aplikace ASP.NET se připojení k databázi serveru SQL Server. Aplikace je aktuálně pro dva virtuální počítače VMware systémem Windows Server 2008 R2 a SQL Server 2008 R2. Aplikace virtuální počítače jsou hostované místně a spravované přes vCenter Server.
- **osTicket**: oddělení služeb open source lístků pro aplikaci, která běží na systému Linux. Můžete ji stáhnout [Githubu](https://github.com/osTicket/osTicket). Aktuální aplikace je na dva virtuální počítače VMware systémem Ubuntu 16.04 LTS, pomocí Apache 2, PHP 7.0 a MySQL 5.7
    

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastaví místní a infrastrukturu Azure a příprava na migraci.



