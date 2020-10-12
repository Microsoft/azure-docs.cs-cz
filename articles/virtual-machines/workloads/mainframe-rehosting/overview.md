---
title: Opětovné hostování sálového počítače na virtuálních počítačích Azure
description: Využijte virtuální počítače na Microsoft Azure a znovu hostovat své sálové úlohy, jako jsou systémy založené na IBM Z.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: d754f27a1510d9db0837eabb96b3a7cf75f76fc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835523"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Opětovné hostování sálového počítače na virtuálních počítačích Azure

Migrace úloh z sálových prostředí do cloudu vám umožní modernizovat infrastrukturu a často si ušetřit náklady. Řadu úloh je možné přenést do Azure jenom s drobnými změnami kódu, jako je třeba aktualizace názvů databází.

Obecně řečeno pojem *sálový* počítač znamená velký počítačový systém. Konkrétně Velká většina aktuálně používaného systému je systémy IBM System Z nebo IBM kompatibilní s modulem plug-in, které používají MVS, DOS, VSE, OS/390 nebo Z/OS.

Virtuální počítač Azure slouží k izolaci a správě prostředků konkrétní aplikace v jediné instanci. Sálové počítače, jako je například IBM z/OS, používají pro tento účel logické oddíly (LPARS). Sálový disk může používat jeden LPAR pro oblast CICS s přidruženými COBOL programy a samostatnou LPAR pro databázi IBM Db2. Typická [n-vrstvá aplikace v Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) nasazuje virtuální počítače Azure do virtuální sítě, která se dá rozdělit do podsítí pro každou vrstvu.

Virtuální počítače Azure můžou spouštět prostředí pro emulaci sálového počítače a kompilátory, které podporují scénáře navýšení a posunutí. Vývoj a testování jsou často mezi prvními úlohami, které je možné migrovat z sálového počítače do prostředí Azure dev/test. Mezi běžné serverové komponenty, které je možné emulovat, patří systémy OLTP (online Transaction Process), Batch a ingestování dat, jak ukazuje následující obrázek.

![Emulace prostředí v Azure umožňují spouštět systémy z/OS.](media/01-overview.png)

Některé úlohy z sálových počítačů je možné migrovat do Azure s relativním zachováním, zatímco jiné můžou být v Azure znovu hostovat pomocí partnerských řešení. Podrobné pokyny týkající se výběru partnerského řešení získáte prostřednictvím [centra migrace pro sálové počítače Azure](https://azure.microsoft.com/migration/mainframe/) .

## <a name="mainframe-migration"></a>Migrace mainframů

Znovu hostovat, znovu sestavit, nahradit nebo vyřadit? IaaS nebo PaaS? Pro určení správné strategie migrace pro vaši sálovou aplikaci si Projděte příručku [migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  v cetrum architektury Azure.

## <a name="micro-focus-rehosting-platform"></a>Platforma pro opětovné hostování platformy Micro

Micro Enterprise Server je jednou z největších dostupných platforem pro opětovné hostování sálových počítačů. Můžete ho použít ke spuštění úloh z/OS na levnější platformě x86 v Azure.

Jak začít:

- [Instalace podnikového serveru a podnikového vývojáře v Azure](./microfocus/set-up-micro-focus-azure.md)
- [Nastavení CICS BankDemo pro podnikovou vývojáře v Azure](./microfocus/demo.md)
- [Provozování podnikového serveru v kontejneru Docker v Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame v Azure

TmaxSoft OpenFrame je oblíbené řešení pro opětovné hostování sálového počítače používané ve scénářích navýšení a posunutí. Prostředí OpenFrame v Azure je vhodné pro vývoj, ukázky, testování nebo produkční úlohy.

Jak začít:

- [Začínáme s TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Stažení e-knihy](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12,0

Vývojové a testovací prostředí IBM Z (IBM zD&T) nastaví v Azure neprodukční prostředí, které můžete použít pro vývoj, testování a ukázky aplikací z/OS.

Prostředí emulace v Azure může hostovat různé druhy instancí Z prostřednictvím řízených distribucí aplikací (ADCDs). V Azure a Azure Stack můžete spustit zD&T Personal Edition, zD&T Parallel Sysplex a zD&T Enterprise Edition.

Jak začít:

- [Nastavení IBM zD&T 12,0 v Azure](./ibm/install-ibm-z-environment.md)
- [Nastavení ADCD na zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale v Azure

Prostředí IBM DB2 pureScale poskytuje databázový cluster pro Azure. Není totožný s původním prostředím, ale nabízí podobnou dostupnost a škálování jako IBM DB2 pro z/OS běžící v paralelní instalaci Sysplex.

Informace o tom, jak začít, najdete v článku [IBM DB2 pureScale v Azure](../../linux/ibm-db2-purescale-azure.md).

## <a name="considerations"></a>Důležité informace

Když migrujete sálové úlohy do infrastruktury Azure jako služby (IaaS), můžete si vybrat z několika typů škálovatelných výpočetních prostředků na vyžádání, včetně virtuálních počítačů Azure. Azure nabízí řadu virtuálních počítačů se systémy [Linux](../../linux/overview.md) a [Windows](../../windows/overview.md) .

### <a name="compute"></a>Compute

Azure COMPUTE vyrovnává favorably s kapacitou sálového počítače. Pokud uvažujete o přesunutí úlohy sálového počítače do Azure, porovnejte metriku sálových počítačů s 1 000 000 instrukcí za sekundu (MIPS) na virtuální procesory. 

Naučte se, jak [přesunout výpočetní sálové počítače do Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Vysoká dostupnost a převzetí služeb při selhání

Azure nabízí smlouvy o úrovni služeb (SLA) založené na závazku. Dostupnost s více devěty je výchozí a SLA je možné optimalizovat pomocí místní nebo geografické replikace služeb. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

V případě Azure IaaS, jako je třeba virtuální počítač, poskytují konkrétní systémové funkce podporu převzetí služeb při selhání, například instance clusteringu s podporou převzetí služeb při selhání a sady dostupnosti. Když použijete prostředky Azure Platform as a Service (PaaS), platforma zpracovává převzetí služeb při selhání automaticky. Příklady zahrnují [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) a [Azure Cosmos DB](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Škálovatelnost

Při horizontálním navýšení kapacity cloudových prostředí se většinou škálují sálové počítače. Azure nabízí řadu velikostí pro [Linux](../../sizes.md) a [Windows](../../sizes.md) , které vyhovují vašim potřebám. Cloud se také škáluje nahoru nebo dolů, aby odpovídal přesně zadaným uživatelským specifikacím. Výpočetní výkon, úložiště a služby se [škálují](/azure/architecture/best-practices/auto-scaling) na vyžádání v rámci fakturačního modelu založeného na využití.

### <a name="storage"></a>Storage

V cloudu máte řadu flexibilních a škálovatelných možností úložiště a platíte jenom za to, co potřebujete. [Azure Storage](../../../storage/common/storage-introduction.md) nabízí rozsáhle škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro Cloud, spolehlivé úložiště pro zasílání zpráv a NoSQL úložiště. U virtuálních počítačů poskytují spravované a nespravované disky Trvalé a zabezpečené úložiště na disku.

Přečtěte si, jak [přesunout sálové úložiště do Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup a obnovení

Udržování vlastního webového serveru pro zotavení po havárii může být nákladným umístěním. Azure má snadno implementované a nákladově efektivní možnosti pro [zálohování](../../../backup/backup-overview.md), [obnovu](../../../site-recovery/site-recovery-overview.md)a [redundanci](../../../storage/common/storage-redundancy.md) v místní nebo regionální úrovni nebo prostřednictvím geografické redundance.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government pro migrace sálového počítače

Řada entit veřejného sektoru by chtěla přesunout své sálové aplikace do moderní, flexibilní platformy. Microsoft Azure Government je fyzicky oddělená instance globální Microsoft Azure platformy, zabalená pro systémy federální, státní a místní správy. Poskytuje špičkové služby zabezpečení, ochrany a dodržování předpisů, které jsou určené pro USA státní úřady a jejich partnery.

Azure Government získala dočasnou autoritu pro provoz (P-ATO) pro FedRAMP vysoký dopad pro systémy, které tento typ prostředí potřebují.

Začněte stažením [Microsoft Azure Government cloudu pro aplikace pro sálové počítače](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Další kroky

Zeptejte se našich [partnerů](partner-workloads.md) , aby vám pomohly migrovat nebo znovu hostovat vaše sálové aplikace. 

Viz také:

- [Dokumenty White paper o tématech z sálového počítače](mainframe-white-papers.md)
- [Migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Řešení potíží](../../troubleshooting/index.yml)
- [Migrace Demystifying z sálového počítače do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md