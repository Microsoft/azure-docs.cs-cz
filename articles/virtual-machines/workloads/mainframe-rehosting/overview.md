---
title: Změna mainframových hostování na Azure virtual machines
description: Změna hostitele vašeho mainframových úlohy, jako jsou systémy pro IBM Z využití virtuálních počítačů (VM) v Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: c1d7b52bdce77ca108781a999a8a85b3e3fca0b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61487444"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>Změna mainframových hostování na Azure virtual machines

Úlohy migrace mainframů prostředí do cloudu vám umožní od modernizujte svou infrastrukturu a často ušetřit na nákladech. Mnoho úloh lze přenést do Azure s jen drobné úpravy v kódu změny, např. aktualizují se názvy databází.

Obecně mluvený, termín *mainframových* znamená, že velké počítačového systému. Konkrétně většinu aktuálně používaných jsou servery Z systému IBM nebo IBM plug-compatible systémů, na kterých běží MVS, DOS, VSE, operačního systému/390 nebo z/OS.

Virtuální počítač Azure (VM) se používá k izolaci a řízení prostředků pro konkrétní aplikaci na jednu instanci. Sálové počítače, jako je například IBM z/OS pomocí logické oddíly (LPARS) pro tento účel. Sálového počítače použít jeden LPAR CICS oblasti s přidružené programy COBOL a samostatné LPAR k databázi IBM Db2. Typické [n vrstvou aplikaci v Azure](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) nasadí virtuální počítače Azure do virtuální sítě, které je možné segmentovat do podsítí pro každou vrstvu.

Virtuální počítače Azure můžete spouštět mainframových emulace prostředí a kompilátory, které podporují scénáře lift and shift. Vývoj a testování jsou často mezi první úlohy pro migraci z sálového počítače do prostředí Azure pro vývoj/testování. Běžné komponenty serveru, které mohou napodobovat zahrnují proces online transakcí (OLTP), batch a systémy ingestování dat jak ukazuje následující obrázek.

![Emulace prostředí v Azure umožňuje spouštět z/OS – systémy.](media/01-overview.png)

Některé úlohy sálové počítače můžete migrovat do Azure s relativní snadností, zatímco jiné můžou rehosted v Azure pomocí partnerského řešení. Podrobné informace o výběru partnerského řešení [migrace Mainframů Azure center](https://azure.microsoft.com/migration/mainframe/) může pomoct.

## <a name="mainframe-migration"></a>Migrace mainframů

Změna hostitele, znovu sestavit, nahrazení nebo vyřadit z provozu? PaaS nebo IaaS? Určení strategie správné migrace mainframů aplikace, najdete v článku [migrace Mainframů](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview) průvodce v Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>Platforma změna hostování Micro fokus

Micro fokus Enterprise Server je jednou z největších mainframových změna hostování platforem, které jsou k dispozici. Můžete spouštět úlohy z/OS na levnější x86 platform v Azure.

Jak začít:

- [Nainstalujte Enterprise Server a Enterprise Developer v Azure](./microfocus/set-up-micro-focus-azure.md)
- [Nastavit CICS BankDemo pro podnikové vývojáře v Azure](./microfocus/demo.md)
- [Spuštění podnikových serveru v kontejneru Dockeru v Azure](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame v Azure

TmaxSoft OpenFrame je Oblíbené mainframových rehosting řešení používá ve scénářích lift and shift. OpenFrame prostředí v Azure je vhodný pro vývoj ukázky, testování a produkční úlohy.

Jak začít:

- [Začínáme s TmaxSoft OpenFrame](./tmaxsoft/get-started.md)
- [Stažení elektronické knihy](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM zD & T 12.0

IBM Z vývojové a testovací prostředí (IBM zD & T) nastaví mimo produkční prostředí v Azure, kterou můžete použít pro vývoj, testování a ukázky aplikací založených na z/OS.

Emulace prostředí v Azure můžete hostovat různé druhy Z instance prostřednictvím aplikace vývojáři řídit distribuce (ADCDs). ZD & T osobní edice, zD & T paralelní Sysplex a zD & T Enterprise Edition lze spustit v Azure a Azure Stack.

Jak začít:

- [Nastavení IBM zD & T 12.0 v Azure](./ibm/install-ibm-z-environment.md)
- [Nastavení ADCD na zD & T](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale v Azure

Prostředí pureScale IBM DB2 poskytuje cluster databáze pro Azure. Není stejný jako původní prostředí, ale poskytuje podobné dostupností a škálovatelností jako IBM DB2 pro spuštění v instalačním programu paralelní Sysplex z/OS.

Abyste mohli začít, najdete v článku [pureScale IBM DB2 v Azure](/azure/virtual-machines/linux/ibm-db2-purescale-azure).

## <a name="considerations"></a>Požadavky

Když vám migrace mainframových úloh do infrastruktury Azure jako služba (IaaS), můžete z několika typů na vyžádání a škálovatelné výpočetní prostředky, včetně virtuálních počítačů Azure. Azure nabízí celou řadu [Linux](/azure/virtual-machines/linux/overview) a [Windows](/azure/virtual-machines/windows/overview) virtuálních počítačů.

### <a name="compute"></a>Compute

Azure výpočetní výkon porovnává příznivě ke kapacitě mainframů. Pokud uvažujete o přesunu mainframových úlohy do Azure, porovnejte metrika mainframových jednoho milionu instrukcí za sekundu (MIPS) na virtuální procesory. 

Zjistěte, jak [do Azure přesunout výpočetní mainframových](./concepts/mainframe-compute-azure.md).

### <a name="high-availability-and-failover"></a>Zajištění vysoké dostupnosti a převzetí služeb při selhání

Azure nabízí na základě závazku smlouvy o úrovni služeb (SLA). Více nines dostupnost je výchozí a smlouvy o úrovni služeb, lze optimalizovat místní nebo založené na geografické replikaci služby. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Díky Azure IaaS, jako je například virtuální počítač, konkrétní systémové funkce poskytovat podporu převzetí služeb při selhání – například převzetí služeb clusteringu instancí a [dostupnosti](/azure/virtual-machines/windows/regions-and-availability#availability-sets). Při použití Azure platforma jako služba (PaaS) prostředky se stará platforma a převzetí služeb při selhání automaticky. Mezi příklady patří [Azure SQL Database](/azure/sql-database/sql-database-technical-overview) a [služby Azure Cosmos DB](/azure/cosmos-db/introduction).

### <a name="scalability"></a>Škálovatelnost

Sálové počítače obvykle vertikálně navýšit kapacitu, při cloudových prostředích pro horizontální navýšení kapacity. Azure nabízí celou řadu [Linux](/azure/virtual-machines/linux/sizes) a [Windows](/azure/virtual-machines/windows/sizes) velikosti podle vašich potřeb. Cloud také škálování nahoru nebo dolů specifikací přesně uživatele shoda. Výpočetní výkon, úložiště a službám [škálování](/azure/architecture/best-practices/auto-scaling) na požádání v rámci modelu fakturace podle využití.

### <a name="storage"></a>Úložiště

V cloudu mají celou řadu možností úložiště flexibilní, škálovatelný a platíte jenom za to co potřebujete. [Azure Storage](/azure/storage/common/storage-introduction) nabízí široce škálovatelné úložiště objektů pro datové objekty, službu systému souborů pro cloud, spolehlivé úložiště a úložiště NoSQL. Pro virtuální počítače spravované a nespravované disky poskytují trvalé a zabezpečené diskové úložiště.

Zjistěte, jak [do Azure přesunout úložiště mainframových](./concepts/mainframe-storage-azure.md).

### <a name="backup-and-recovery"></a>Backup a obnovení

Udržování vašeho vlastního webu pro zotavení po havárii může být nákladné návrh. Azure nabízí snadno implementovat a nákladově efektivní možnosti pro [zálohování](/azure/backup/backup-introduction-to-azure-backup), [obnovení](/azure/site-recovery/site-recovery-overview), a [redundance](/azure/storage/common/storage-redundancy) na místní nebo regionální úrovni nebo prostřednictvím geografickou redundancí.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government pro migrace mainframů

Mnoho instituce ve veřejném sektoru rádi přesunout jejich mainframových aplikací do Modernější a flexibilní platformu. Microsoft Azure Government je fyzicky oddělená instance globální platformy Microsoft Azure – zabalí pro státní správu federální, státní i místní systémy. Nabízí prvotřídní funkce pro zabezpečení, ochrany a dodržování předpisů služby speciálně pro instituce státní správy USA a jejich partneři.

Azure Government vytvořené Provisional Authority to Operate (P-ATO) pro vysoký dopad FedRAMP pro systémy, které potřebují tento typ prostředí.

Abyste mohli začít, stáhněte si [cloudu Microsoft Azure Government pro mainframových aplikací](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Další postup

Požádejte naše [partneři](partner-workloads.md) při migraci nebo opětovným hostováním mainframových aplikací. Podrobné informace o výběru partnerského řešení, najdete v článku [platformy modernizaci Alliance](https://www.platformmodernization.org/pages/mainframe.aspx) webu.

Viz také:

- [Dokumenty White Paper o tématech sálového počítače](mainframe-white-papers.md)
- [Migrace mainframů](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Odstraňování potíží](/azure/virtual-machines/troubleshooting/)
- [Uvedení mainframových migrace do Azure](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md
