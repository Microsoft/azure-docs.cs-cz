---
title: Změna mainframových hostování na Azure virtual machines | Dokumentace Microsoftu
description: Změna hostitele vašeho mainframových úlohy, jako jsou systémy pro IBM Z využití virtuálních počítačů (VM) v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
keywords: ''
ms.openlocfilehash: 8be0ebc486739f8826e8a1d5a5307a219ba71b6f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57192713"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Změna mainframových hostování na Azure virtual machines

Úlohy migrace mainframů prostředí do cloudu vám umožní od modernizujte svou infrastrukturu a často ušetřit na nákladech. Mnoho úloh lze přenést do Azure s jen drobné úpravy v kódu změny, např. aktualizují se názvy databází.

Termín *mainframových* obecně označuje velké počítačového systému, ale většinu o aktuálně nasazených Z systému IBM servery nebo IBM plug-compatible systémy s operačním systémem MVS, DOS, VSE, operačního systému/390 nebo z/OS.

Virtuální počítač Azure (VM) se používá k izolaci a řízení prostředků pro konkrétní aplikaci na jednu instanci. Sálové počítače, jako je například IBM z/OS pomocí logické oddíly (LPARS) pro tento účel. Sálového počítače použít jeden LPAR CICS oblasti s přidružené programy COBOL a samostatné LPAR k databázi IBM Db2. Typické [n vrstvou aplikaci v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) nasadí virtuální počítače Azure do virtuální sítě, které je možné segmentovat do podsítí pro každou vrstvu.

Virtuální počítače Azure můžete spouštět mainframových emulace prostředí a kompilátory, které podporují scénáře lift and shift. Vývoj a testování jsou často mezi první úlohy pro migraci z sálového počítače do prostředí Azure pro vývoj/testování. Běžné komponenty serveru, které mohou napodobovat zahrnují proces online transakcí (OLTP), batch a systémy ingestování dat jak ukazuje následující obrázek.

![Emulace prostředí v Azure umožňuje spouštět z/OS – systémy.](media/01-overview.png)

Některé úlohy sálové počítače můžete migrovat do Azure s relativní snadností, zatímco jiné můžou rehosted v Azure pomocí partnerského řešení. Podrobné informace o výběru partnerského řešení [migrace Mainframů Azure center](https://azure.microsoft.com/migration/mainframe/) může pomoct.

## <a name="set-up-devtest-environment-using-a-micro-focus-rehosting-platform"></a>Nastavení prostředí pro vývoj/testování rehosting platformě Micro fokus

Micro fokus Enterprise Server je jednou z největších mainframových změna hostování platforem, které jsou k dispozici. Můžete spouštět úlohy z/OS na levnější x86 platform v Azure.

Abyste mohli začít, najdete v následujících článcích:

- [Instalace Micro fokus Enterprise Server 4.0 a podnikový vývojář 4.0 v Azure](./microfocus/set-up-micro-focus-on-azure.md)
- [Nastavit Micro fokus CICS BankDemo Micro fokus Enterprise Developer 4.0 v Azure](./microfocus/demo.md)

## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame v Azure

TmaxSoft OpenFrame je Oblíbené mainframových změna hostování řešení, které umožňuje snadno přenést stávající prostředky mainframových a přesunout do Azure. OpenFrame prostředí v Azure je vhodný pro vývoj ukázky, testování a produkční úlohy.

Abyste mohli začít, stáhněte si [nainstalovat TmaxSoft OpenFrame v Azure](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/) elektronické knihy.

## <a name="set-up-a-devtest-environment-using-ibm-z-devtest-120"></a>Nastavení vývojového a testovacího prostředí pomocí 12.0 pro vývoj/testování Z IBM

IBM Z vývojové a testovací prostředí (IBM zD & T) nastaví mimo produkční prostředí v Azure, kterou můžete použít pro vývoj, testování a ukázky aplikací založených na z/OS.

Emulace prostředí v Azure můžete hostovat různé instance Z prostřednictvím aplikace vývojáři řídit distribuce (ADCDs). ZD & T osobní edice, zD & T paralelní Sysplex a zD & T Enterprise Edition lze spustit v Azure a Azure Stack.

Abyste mohli začít, najdete v následujících článcích:

-   [Nastavení IBM zD & T 12.0 v Azure](./ibm/install-ibm-z-environment.md)
-   [Nastavení ADCD na zD & T](./ibm/demo.md)

## <a name="migrate-ibm-db2-purescale-to-azure"></a>IBM DB2 pureScale migrace do Azure

Prostředí pureScale IBM DB2 poskytuje cluster databáze pro Azure s vysokou dostupností a škálovatelností v operačních systémech Linux. I když není stejný jako původní prostředí, IBM DB2 pureScale v Linuxu nabízí podobné vysoké dostupnosti a škálovatelnosti jako IBM DB2 pro spuštění v paralelní Sysplex konfigurace v hlavním z/OS.

Abyste mohli začít, najdete v článku [pureScale IBM DB2 v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Požadavky

Když vám migrace mainframových úloh do infrastruktury Azure jako služba (IaaS), můžete z několika typů na vyžádání a škálovatelné výpočetní prostředky, včetně virtuálních počítačů Azure. Azure nabízí celou řadu [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/overview) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/overview) virtuálních počítačů.

### <a name="high-availability-and-failover"></a>Zajištění vysoké dostupnosti a převzetí služeb při selhání

Azure nabízí na základě závazku smlouvy o úrovni služeb (SLA), kde více 9s dostupnost je výchozí nastavení, optimalizace s místním nebo založená na poloze replikace služeb. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Pro Azure IaaS, jako jsou virtuální počítače, převzetí služeb při selhání závisí na konkrétní systém funkce, jako je clustering instance převzetí služeb při selhání a [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability#availability-sets). Při použití platformy Azure jako služba (PaaS) prostředky, jako například [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) a [služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction), se stará platforma a převzetí služeb při selhání automaticky.

### <a name="scalability"></a>Škálovatelnost

Sálové počítače obvykle vertikálně navýšit kapacitu, při cloudové prostředí pro horizontální navýšení kapacity. Azure nabízí celou řadu [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) velikosti podle vašich potřeb. Cloud také škálování nahoru nebo dolů specifikací přesně uživatele shoda. Výpočetní výkon, úložiště a službám [škálování](https://docs.microsoft.com/azure/architecture/best-practices/auto-scaling) na požádání v rámci modelu fakturace podle využití.

### <a name="storage"></a>Storage

V cloudu mají celou řadu možností úložiště flexibilní, škálovatelný a platíte jenom za to co potřebujete. [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction) nabízí široce škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro cloud, spolehlivé úložiště a úložiště NoSQL. Pro virtuální počítače spravované a nespravované disky poskytují trvalé a zabezpečené diskové úložiště.

### <a name="backup-and-recovery"></a>Backup a obnovení

Udržování vašeho vlastního webu pro zotavení po havárii může být nákladné návrh. Azure nabízí snadno implementovat a nákladově efektivní možnosti pro [zálohování](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup), [obnovení](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview), a [redundance](https://docs.microsoft.com/azure/storage/common/storage-redundancy) na místní nebo regionální úrovni nebo prostřednictvím geografickou redundancí.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government pro migrace mainframů

Mnoho instituce ve veřejném sektoru rádi přesunout jejich mainframových aplikací do Modernější a flexibilní platformu. Microsoft Azure Government je fyzicky oddělená instance cloud Services podle globální platformy Microsoft Azure, ale zabalit pro státní správu federální, státní i místní systémy. Nabízí prvotřídní funkce pro zabezpečení, ochrany a dodržování předpisů služby speciálně pro instituce státní správy USA a jejich partneři.

Azure Government vytvořené Provisional Authority to Operate (P-ATO) pro vysoký dopad FedRAMP pro systémy, které potřebují tento typ prostředí. 

Abyste mohli začít, stáhněte si [cloudu Microsoft Azure Government pro mainframových aplikací](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="learn-more"></a>Další informace

Pokud zvažujete migrace mainframů, naši rozsáhlou [partnera](partner-workloads.md) ekosystému je vám k dispozici. Podrobné informace o výběru partnerského řešení, najdete [platformy modernizaci Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

Viz také:

-   [Migrace mainframů](https://docs.microsoft.com/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
-   [Řešení potíží](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/)
-   [Uvedení mainframových migrace do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/en-us/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-on-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
