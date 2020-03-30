---
title: Rehosting sálových počítačů na virtuálních počítačích Azure
description: Znovu hostujte úlohy sálových počítačů, jako jsou systémy založené na IBM Z pomocí virtuálních počítačů (VM) v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: ccdeefabeedfca4959239696361ccce0bc6c1c78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76289794"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Rehosting sálových počítačů na virtuálních počítačích Azure

Migrace úloh z prostředí sálových počítačů do cloudu umožňuje modernizovat infrastrukturu a často ušetřit náklady. Řadu úloh je možné přenést do Azure jenom s drobnými změnami kódu, jako je třeba aktualizace názvů databází.

Obecně řečeno, termín *mainframe* znamená velký počítačový systém. Konkrétně drtivá většina v současné době používá ibm system z servery nebo IBM plug-kompatibilní systémy, které běží MVS, DOS, VSE, OS/390, nebo z / OS.

Virtuální počítač Azure (VM) se používá k izolovat a spravovat prostředky pro konkrétní aplikaci na jedné instanci. Sálové počítače, například IBM z/OS, používají pro tento účel logické oddíly (LPARS). Sálový počítač může použít jeden LPAR pro oblast CICS s přidruženými programy COBOL a samostatný LPAR pro databázi IBM Db2. Typická [n-vrstvá aplikace v Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) nasazuje virtuální počítače Azure do virtuální sítě, kterou lze segmentovat do podsítí pro každou vrstvu.

Virtuální počítače Azure můžete spustit prostředí emulace sálových počítačů a kompilátory, které podporují scénáře výtahu a posunu. Vývoj a testování jsou často mezi prvními úlohami pro migraci z sálového počítače do prostředí Azure pro vývoj a testování. Mezi běžné serverové součásti, které lze emulovat, patří online transakční proces (OLTP), dávkové a datové systémy pro ingestování dat, jak ukazuje následující obrázek.

![Prostředí emulace v Azure umožňují spouštět systémy založené na z/OS.](media/01-overview.png)

Některé úlohy sálových počítačů lze migrovat do Azure s relativní lehkostí, zatímco jiné lze rehosted v Azure pomocí partnerského řešení. Podrobné pokyny k výběru partnerského řešení vám může [poradit Centrum migrace sálových počítačů Azure.](https://azure.microsoft.com/migration/mainframe/)

## <a name="mainframe-migration"></a>Migrace mainframů

Rehost, přestavět, nahradit, nebo odejít do důchodu? IaaS nebo PaaS? Pokud chcete určit správnou strategii migrace pro vaši aplikaci sálových počítačů, přečtěte si průvodce [migrací sálových počítačů](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) v Centru architektury Azure.

## <a name="micro-focus-rehosting-platform"></a>Platforma pro rehosting Micro Focus

Micro Focus Enterprise Server je jednou z největších platforem rehostingu sálových počítačů, které jsou k dispozici. Můžete ji použít spouštět úlohy z/OS na levnější platformě x86 v Azure.

Jak začít:

- [Instalace podnikového serveru a podnikového vývojáře do Azure](./microfocus/set-up-micro-focus-azure.md)
- [Nastavení CICS BankDemo pro vývojáře pro podniky v Azure](./microfocus/demo.md)
- [Spuštění podnikového serveru v kontejneru Dockeru v Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame v Azure

TmaxSoft OpenFrame je populární řešení rehostingu sálových počítačů používané ve scénářích lift-and-shift. Prostředí OpenFrame v Azure je vhodné pro vývoj, ukázky, testování nebo produkční úlohy.

Jak začít:

- [Začínáme s TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Stáhněte si ebook](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD&T 12.0

Vývojové a testovací prostředí IBM Z (IBM zD&T) nastavuje v Azure neprodukční prostředí, které můžete použít pro vývoj, testování a ukázky aplikací založených na z/OS.

Prostředí emulace v Azure může hostovat různé druhy instancí Z prostřednictvím distribuce řízené vývojáři aplikací (ADCDs). Můžete spustit zD&T Personal Edition, zD&T Parallel Sysplex a zD&T Enterprise Edition v Azure a Azure Stack.

Jak začít:

- [Nastavení IBM zD&T 12.0 v Azure](./ibm/install-ibm-z-environment.md)
- [Nastavit ADCD na zD&T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale v Azure

Prostředí IBM DB2 pureScale poskytuje databázový cluster pro Azure. Není totožný s původním prostředím, ale poskytuje podobnou dostupnost a škálování jako IBM DB2 pro z/OS běžící v nastavení Parallel Sysplex.

Další informace najdete v tématu [IBM DB2 pureScale on Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Požadavky

Když migrujete úlohy sálových počítačů do infrastruktury Azure jako služby (IaaS), můžete si vybrat z několika typů škálovatelných výpočetních prostředků na vyžádání, včetně virtuálních počítačů Azure. Azure nabízí celou řadu [virtuálních](/azure/virtual-machines/linux/overview) aplikací pro Linux a [Windows.](/azure/virtual-machines/windows/overview)

### <a name="compute"></a>Služba Compute

Výpočetní výkon Azure se příznivě porovnává s kapacitou sálového počítače. Pokud uvažujete o přesunutí úlohy sálového počítače do Azure, porovnejte metriku sálových počítačů o milionových instrukcích za sekundu (MIPS) s virtuálními procesory. 

Přečtěte si, jak [přesunout výpočetní prostředky sálového počítače do Azure](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Vysoká dostupnost a převzetí služeb při selhání

Azure nabízí smlouvy o úrovni služeb založené na závazcích (SLA). Výchozí je dostupnost více devítky a služby SLA lze optimalizovat pomocí místní nebo geografické replikace služeb. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

S Azure IaaS, jako je například virtuální počítač, poskytují specifické systémové funkce podporu převzetí služeb při selhání – například instance clusteringu s podporou převzetí služeb při selhání a skupiny dostupnosti. Když používáte platformu Azure jako prostředky služby (PaaS), platforma automaticky zpracovává převzetí služeb při selhání. Příklady zahrnují [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) a Azure [Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Škálovatelnost

Sálové počítače obvykle vertikálně navýšit kapacitu, zatímco cloudová prostředí horizontální navýšení kapacity. Azure nabízí celou řadu velikostí [Linuxu](/azure/virtual-machines/linux/sizes) a [Windows,](/azure/virtual-machines/windows/sizes) které splní vaše potřeby. Cloud se také zvětšuje nahoru nebo dolů, aby odpovídal přesným uživatelským specifikacím. Výpočetní výkon, úložiště a služby [se škálují](/azure/architecture/best-practices/auto-scaling) na základě poptávky v rámci fakturačního modelu založeného na využití.

### <a name="storage"></a>Úložiště

V cloudu máte celou řadu flexibilních a škálovatelných možností úložiště a platíte jenom za to, co potřebujete. [Azure Storage](/azure/storage/common/storage-introduction) nabízí masivně škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro cloud, spolehlivé úložiště zpráv a úložiště NoSQL. Pro virtuální počítače poskytují spravované a nespravované disky trvalé a zabezpečené diskové úložiště.

Přečtěte si, jak [přesunout úložiště sálových počítačů do Azure](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup a obnovení

Udržování vlastního webu pro zotavení po havárii může být nákladná záležitost. Azure nabízí snadno implementovatelné a nákladově efektivní možnosti [zálohování](/azure/backup/backup-introduction-to-azure-backup), [obnovení](/azure/site-recovery/site-recovery-overview)a [redundance](/azure/storage/common/storage-redundancy) na místní nebo regionální úrovni nebo prostřednictvím geografické redundance.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government pro migrace sálových počítačů

Mnoho subjektů veřejného sektoru by rádo přesunulo své mainframové aplikace na modernější a flexibilnější platformu. Microsoft Azure Government je fyzicky oddělená instance globální platformy Microsoft Azure – zabalená pro federální, státní a místní systémy správy. Poskytuje služby zabezpečení, ochrany a dodržování předpisů světové třídy speciálně pro vládní agentury Spojených států a jejich partnery.

Azure Government získal prozatímní úřad pro provoz (P-ATO) pro FedRAMP high impact pro systémy, které potřebují tento typ prostředí.

Chcete-li začít, stáhněte si [cloud Microsoft Azure Government pro mainframové aplikace](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Další kroky

Požádejte naše [partnery,](partner-workloads.md) aby vám pomohli s migrací nebo opětovnou hostováním aplikací sálových počítačů. 

Viz také:

- [Bílé knihy o tématech sálových počítačů](mainframe-white-papers.md)
- [Migrace sálového počítače](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Řešení potíží](/azure/virtual-machines/troubleshooting/)
- [Demystifikace sálového počítače k migraci Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
