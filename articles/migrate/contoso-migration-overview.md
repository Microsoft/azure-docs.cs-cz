---
title: Přehled migrace Contoso do Azure | Microsoft Docs
description: Poskytuje přehled strategie migrace a scénáře používané Contoso migrace jejich místní datacentra do Azure.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: raynew
ms.openlocfilehash: 659627eb6241bf31350b5a51c2e6c449c7f731c2
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35300799"
---
# <a name="contoso-migration-overview"></a>Migrace Contoso: Přehled


Toto je první článek v série, která ukazuje, jak smyšlenou organizaci Contoso migrujete svoji místní infrastrukturu do [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) cloudu. 

Řada článek poskytuje příklad nasazení, které vám pomohou pochopit a vyzkoušet různé možnosti pro migraci na Azure a pro práci v hybridním prostředí. Články ukazují, jak společnost Contoso dokončení migrace poslání, ale ukazatele pro konkrétní pokyny a obecné čtení jsou uvedeny v průběhu.

## <a name="introduction"></a>Úvod

Azure poskytuje přístup k komplexní sadu cloudové služby. Jako vývojáře a IT odborníky můžete tyto služby k vytvoření, nasazení a správu aplikací v rozsahu nástroje a rozhraní, prostřednictvím globální sítě datových center. Když vaše firma čelí výzvám souvisejícím s digitalizací, cloud Azure vám pomůže zjistit, jak optimalizovat prostředky a operace, zapojit zákazníky i zaměstnance a transformovat vaše produkty.

I přes všechny výhody, které cloud poskytuje z hlediska rychlosti a flexibility, minimalizace nákladů, vysokého výkonu a spolehlivost, si však v Azure uvědomujeme, že řada organizací bude potřebovat ještě nějakou dobu provozovat místní datacentra. V reakci na překážky přechodu do cloudu Azure poskytuje strategii hybridního cloudu, která propojí vaše místní datacentra s veřejným cloudem Azure. Pomocí cloudových prostředků Azure, jako je například služba Backup, můžete zajistit ochranu místních prostředků nebo pomocí Azure Analytics získat přehled o místních úlohách. 

Jako součást strategie hybridního cloudu Azure poskytuje rostoucí řešení pro migraci místní aplikace a úlohy do cloudu. Pomocí jednoduchých postupů můžete komplexně vyhodnotit své místní prostředky, abyste zjistili, jak si povedou v cloudu Azure. Když budete mít k dispozici podrobné posouzení, můžete bez obav migrovat prostředky do Azure. Po zprovoznění prostředků v Azure je můžete optimalizovat, abyste zachovali a vylepšili úroveň přístupu, flexibility, zabezpečení a spolehlivosti.

## <a name="migration-strategies"></a>Strategie migrace

Strategie pro migraci do cloudu lze rozdělit do čtyř kategorií: opětovným hostováním, Refaktorovat, rearchitect nebo znovu vytvořit. Strategie, kterou můžete přijmout, závisí na vaší obchodní faktory a migrace cíle. Může přijmout několik strategií. Může například rozhodnout opětovným hostováním jednoduché aplikace (navýšení a shift) nebo aplikace, které nejsou důležité pro vaši firmu, ale ty, které jsou složitější a důležitých rearchitect. Podívejme se na strategie.


**Strategie** | **Definice** | **Kdy použít** 
--- | --- | --- 
**Opětovným hostováním** | Často označuje jako "navýšení a shift" migraci. Tato možnost nevyžaduje změny kódu a umožňuje migraci vaše stávající aplikace do Azure rychle. Každá aplikace je migrovat, jak je využívat výhody cloudu, bez rizika a náklady spojené s změnami kódu. | Když potřebujete rychle přesouvat aplikace do cloudu.<br/><br/> Když chcete přesunout aplikace bez provádění úprav.<br/><br/> Pokud se vaše aplikace navržen tak, aby se můžete využít [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) škálovatelnost po migraci.<br/><br/> Když aplikace jsou důležité pro vaši firmu, ale nepotřebujete okamžitou změny možnosti aplikace.
**Refaktorovat** | Často označuje jako "opětovné vytvoření balíčku", refaktoring vyžaduje minimální změny aplikace, tak, aby se mohli přihlásit k [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/)a použít pro cloud.<br/><br/> Migrovat existující aplikace může například Azure App Service nebo Azure Kubernetes služby (AKS). Nebo může Refaktorovat relačních a nerelačních databáze do možnosti, jako je například Azure spravované Instance databáze SQL Azure Database pro databázi MySQL, databáze Azure PostgreSQL a Azure Cosmos DB. | Pokud vaše aplikace můžete snadno vytvořen nový balíček pro práci v Azure.<br/><br/> Pokud chcete použít postupy inovativní DevOps poskytovaný platformou Azure nebo uvažujete o DevOps pomocí strategie kontejner pro úlohy.<br/><br/> Pro refaktoring, budete muset vezměte v úvahu přenositelnost existující základu kódu, a k dispozici vývojářských znalostí.
**Rearchitect** | Vynaložit pro migraci se zaměřuje na změny a rozšířit funkce aplikace a kód za účelem optimalizace aplikace architekturu škálovatelnosti cloudu.<br/><br/> Například by mohlo způsobit narušení monolitický aplikace do skupiny mikroslužeb, které vzájemně spolupracují a snadno škálovat. Nebo, může rearchitect relačních a nerelačních databáze plně spravovaná DBaaS řešení, například Azure spravované Instance databáze SQL Azure Database pro databázi MySQL, databáze Azure pro PostgreSQL a Azure Cosmos DB. | Pokud vaše aplikace potřebují hlavní revize začlenit nové funkce nebo efektivně fungoval na cloudové platformy.<br/><br/> Pokud chcete použít existující investice do aplikace, požadavkům škálovatelnost nákladově efektivní způsob, použije inovativní postupy Azure DevOps a minimalizovat tak využití virtuálních počítačů.
**Opětovné sestavení** | Znovu sestavte má věcí a krok další podle znovu sestavit aplikaci od nuly s použitím technologie cloudu Azure. Například může vytvářet zelená pole aplikace s nativní cloudové technologie, jako je bez serveru, Azure AI, Azure spravované Instance databáze SQL a Azure Cosmos DB. | Když chcete rychlý vývoj a stávající aplikace mají omezenou funkčnost a životnost.<br/><br/> Až budete připraveni k urychlit obchodní inovace (včetně DevOps postupy poskytovaný platformou Azure), vytvářet nové aplikace pomocí nativní cloudové technologie a využít výhod rozvoj AI, blockchain a IoT.

## <a name="migration-articles"></a>Články týkající se migrace

Články, které tvoří řady Contoso migrace jsou shrnuté v následující tabulce.  

- Scénáře jsou zvyšuje složitost a právě přidávána do nich v čase.
- Každý scénář migrace doprovází mírně lišit obchodních cílů, které určují strategie migrace.
- Pro každý scénář nasazení jsme poskytují informace o obchodní faktory a cíle, navrhované architektura, kroky k provedení migrace a doporučení pro čištění a další kroky po dokončení migrace.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
Článek 1: Přehled (v tomto článku) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací používá. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-overview.md) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
[Článek 3: Vyhodnocení místních prostředků](contoso-migration-assessment.md) | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuální počítače Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů webové aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a aplikace databázi k instanci SQL spravované pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby. | K dispozici.
[Článek 5: Pro virtuální počítače Azure opětovným hostováním](contoso-migration-rehost-vm.md) | Ukazuje, jak Contoso migrovat své aplikace SmartHotel virtuálních počítačů pomocí služby Site Recovery.
[Článek 6: Opětovným hostováním do Azure virtuálních počítačů a skupin dostupnosti SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci virtuálních počítačů a aplikace službu migrace databáze, kterou chcete migrovat databázi aplikace do skupiny dostupnosti AlwaysOn serveru SQL. | K dispozici.
Článek 7: Opětovným hostováním Linux aplikace pro virtuální počítače Azure | Ukazuje, jak Contoso migruje aplikace virtuální počítače Azure pomocí Site Recovery. | Plánováno
Článek 8: Opětovným hostováním Linux aplikace pro virtuální počítače Azure a Azure MySQL Server | Ukazuje, jak Contoso migruje aplikace virtuálního počítače pomocí Site Recovery a používá MySQL Workbench migrovat do instance serveru MySQL Azure. | Plánováno



### <a name="demo-apps"></a>Ukázkové aplikace

Články používat dva ukázkové aplikace - SmartHotel a osTicket.

- SmartHotel360: Tato aplikace byla vyvinutá společností Microsoft jako testování aplikace, které můžete použít při práci se službou Azure. Je dodáván jako s otevřeným zdrojem a si můžete stáhnout z [Githubu](https://github.com/Microsoft/SmartHotel360). Je aplikace ASP.NET pro připojení k databázi systému SQL Server. Aplikace se v současné době na dva virtuální počítače VMware se systémem Windows Server 2008 R2 a SQL Server 2008 R2. Aplikace virtuální počítače jsou hostované na místě a spravovat přes vCenter Server.
- osTicket je open-source technickou podporu lístků pro aplikaci, která běží na systému Linux. Si můžete stáhnout z [Githubu](https://github.com/osTicket/osTicket). Aktuální aplikace je na dva virtuální počítače VMware s Ubuntu 16.04LTS, pomocí Apache 2, PHP 7.0 a databáze MySQL 5.7
    

## <a name="next-steps"></a>Další postup

[Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastaví jejich místní a infrastrukturu Azure při přípravě na migraci.



