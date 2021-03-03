---
title: Instalace SAP HANA na virtuálních počítačích Azure | Microsoft Docs
description: Průvodce instalací SAP HANA na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 54bfd7c6e691efbd754e9ea2b804e16b3e07ebbe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670968"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Instalace SAP HANA na virtuálních počítačích Azure
## <a name="introduction"></a>Úvod
Tato příručka vám pomůže nasměrovat na správné prostředky pro úspěšné nasazení HANA na virtuálních počítačích Azure. Tato příručka vás před instalací SAP HANA na virtuálním počítači Azure povede na prostředky dokumentace, které potřebujete ověřit. Takže je možné provést správné kroky, abyste ukončili podporovanou konfiguraci SAP HANA ve virtuálních počítačích Azure.  

> [!NOTE]
> Tato příručka popisuje nasazení SAP HANA do virtuálních počítačů Azure. Informace o tom, jak nasadit SAP HANA do velkých instancí HANA, najdete v tématu [Jak nainstalovat a nakonfigurovat SAP Hana (velké instance) v Azure](./hana-installation.md).
 
## <a name="prerequisites"></a>Požadavky
Tato příručka také předpokládá, že jste obeznámeni s:
* SAP HANA a SAP NetWeaver a jak je nainstalovat místně.
* Jak nainstalovat a provozovat instance aplikací SAP HANA a SAP v Azure
* Koncepty a postupy zdokumentované v:
   * Plánování nasazení SAP v Azure, které zahrnuje plánování a Azure Storage využití Azure Virtual Network. Přečtěte si článek [SAP NetWeaver on Azure Virtual Machines – Průvodce plánováním a implementací](./planning-guide.md)
   * Zásady nasazení a způsoby nasazení virtuálních počítačů v Azure. Viz [nasazení Azure Virtual Machines pro SAP](./deployment-guide.md) .
   * Koncepty vysoké dostupnosti pro SAP HANA popsané v [SAP HANA vysoké dostupnosti pro virtuální počítače Azure](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Krok za krokem před nasazením
V této části jsou uvedeny různé kroky, které je třeba provést před zahájením instalace SAP HANA na virtuálním počítači Azure. Pořadí je vyhodnoceno a jako takový by mělo následovat jako na výčtu:

1. V Azure se nepodporují všechny možné scénáře nasazení. Proto byste měli ve scénářích, které máte v úmyslu k nasazení SAP HANA, ve scénářích [podporovaném virtuálním počítačem Azure ověřit zatížení](./sap-planning-supported-configurations.md) v dokumentu SAP. Pokud tento scénář není uveden, je nutné předpokládat, že nebyl testován a v důsledku toho není podporován.
2. Za předpokladu, že máte přibližnou představu o požadavcích na paměť pro nasazení SAP HANA, potřebujete najít virtuální počítač Azure. Ne všechny virtuální počítače, které jsou certifikované pro SAP NetWeaver, jak je popsáno v části [SAP Support note #1928533](https://launchpad.support.sap.com/#/notes/1928533), jsou SAP HANA certifikovány. Zdrojem pravdy pro SAP HANA certifikovaných virtuálních počítačů Azure je web, který [SAP HANA hardwarový adresář](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Jednotky, které začínají na **s** , jsou jednotky [velkých instancí Hana](./hana-overview-architecture.md) a ne virtuální počítače Azure.
3. Různé typy virtuálních počítačů Azure mají jiné minimální verze operačního systému pro SUSE Linux nebo Red Hat Linux. Na webu [SAP HANA v adresáři hardwaru](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)musíte kliknout na položku v seznamu SAP HANA certifikovaných jednotek a získat tak podrobná data této jednotky. Kromě podporované úlohy HANA jsou uvedené verze operačních systémů, které jsou podporované s jednotkami pro SAP HANA.
4. V případě verzí operačního systému je potřeba vzít v úvahu určité minimální vydané verze jádra. Tyto minimální verze jsou zdokumentovány v těchto poznámkách k podpoře SAP:
    - [Poznámka k podpoře SAP Poznámka #2814271 SAP HANA zálohování v Azure selhalo s chybou kontrolního součtu](https://launchpad.support.sap.com/#/notes/2814271)
    - [Poznámka k podpoře SAP #2753418 potenciálního snížení výkonu kvůli záložnímu použití časovače](https://launchpad.support.sap.com/#/notes/2753418)
    - [Podpora SAP Poznámka #2791572 snížení výkonu kvůli chybějící podpoře VDSO pro Hyper-V v Azure](https://launchpad.support.sap.com/#/notes/2791572)
4. V závislosti na verzi operačního systému, která je podporovaná pro zvolený typ virtuálního počítače, musíte ověřit, jestli je vaše požadovaná verze SAP HANA podporovaná v této verzi operačního systému. Přečtěte [si poznámku ke službě SAP support #2235581](https://launchpad.support.sap.com/#/notes/2235581) pro matrici podpory SAP HANA verzí s různými verzemi operačního systému.
5. Jak jste zjistili platnou kombinaci typu virtuálního počítače Azure, verze operačního systému a SAP HANA vydání, je nutné vrátit se změnami v matici dostupnost produktu SAP. V matici dostupnost SAP můžete zjistit, jestli je podporovaný produkt SAP, který chcete spustit s databází SAP HANA.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Podrobné pokyny k nasazení virtuálních počítačů a operačním systému hosta
V této fázi potřebujete projít kroky nasazení virtuálních počítačů k instalaci HANA a nakonec optimalizovat zvolený operační systém po instalaci.

1. Zvolte základní image z Galerie Azure. Pokud chcete vytvořit vlastní image operačního systému pro SAP HANA, potřebujete znát všechny různé balíčky, které jsou nezbytné pro úspěšnou SAP HANA instalaci. V opačném případě se doporučuje použití imagí SUSE a Red Hat pro SAP nebo SAP HANA z Galerie imagí Azure. Tyto image zahrnují balíčky potřebné pro úspěšnou instalaci HANA. Na základě vaší smlouvy o podpoře s poskytovatelem operačního systému musíte vybrat image, u které přinesete vlastní licenci. Nebo zvolíte bitovou kopii operačního systému, která zahrnuje podporu.
2. Pokud jste zvolili bitovou kopii operačního systému hosta, která vyžaduje, abyste získali vlastní licenci, musíte si zaregistrovat image operačního systému s vaším předplatným, takže si můžete stáhnout a nainstalovat nejnovější opravy. Tento krok bude vyžadovat veřejný přístup k Internetu. Pokud jste nestavili soukromou instanci, například server SMT v Azure.
3. Určete konfiguraci sítě virtuálního počítače. Další informace najdete v dokumentu [SAP HANA konfiguracích infrastruktury a operacích v Azure](./hana-vm-operations.md). Mějte na paměti, že neexistují žádné kvóty propustnosti sítě, které můžete přiřadit k virtuálním síťovým kartám v Azure. Výsledkem je, že jediný účel směrování provozu prostřednictvím různých virtuální síťové adaptéry je založený na bezpečnostních faktorech. Důvěřujeme vám, že vám pomůžeme najít podporu pro kompromis mezi složitostí směrování provozu prostřednictvím několika virtuální síťové adaptéry a požadavky, které jsou vynutily bezpečnostní aspekty.
3. Po nasazení a registraci virtuálního počítače použijte nejnovější opravy na operační systém. Registrováno buď s vlastním předplatným. Nebo pokud jste zvolili Image, která zahrnuje podporu operačního systému, měl by mít virtuální počítač přístup k opravám, které už mají. 
4. Použijte ladění potřebné pro SAP HANA. Tato ladění jsou uvedená v těchto poznámkách k podpoře SAP:

    - [Poznámka k podpoře SAP #2694118 – Red Hat Enterprise Linux HA Add-On v Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [Poznámka k podpoře SAP #1984787 – SUSE LINUX Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787) 
    - [Poznámka k podpoře SAP #2578899-SUSE Linux Enterprise Server 15: Poznámka k instalaci](https://launchpad.support.sap.com/#/notes/2578899)
    - [Poznámka k podpoře SAP #2002167-Red Hat Enterprise Linux 7. x: instalace a upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
    - [Poznámka k podpoře SAP #2292690 – SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [Poznámka k podpoře SAP #2772999-Red Hat Enterprise Linux 8. x: instalace a konfigurace](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [Poznámka k podpoře SAP #2777782 – SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [Poznámka k podpoře SAP #2455582-Linux: spouštění aplikací SAP kompilovaných pomocí RSZ 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [Poznámka k podpoře SAP #2382421 – optimalizace konfigurace sítě v HANA a na úrovni operačního systému](https://launchpad.support.sap.com/#/notes/2382421)

1. Vyberte typ úložiště Azure pro SAP HANA. V tomto kroku se musíte rozhodnout pro rozložení úložiště pro SAP HANA instalaci. Budete používat buď připojené disky Azure, nebo nativní sdílené složky Azure NFS. Typy úložiště Azure, které se dají použít, a kombinace různých typů úložiště Azure, které se dají použít, jsou popsané v části [SAP HANA konfigurací úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md). Vezměte v úvahu konfigurace popsané jako výchozí bod. U neprodukčních systémů možná budete moct nakonfigurovat nižší propustnost nebo IOPS. Pro produkční účely možná budete muset nakonfigurovat trochu větší propustnost a IOPS.
2. Pokud používáte virtuální počítače řady M-Series nebo Mv2-Series, ujistěte se, že jste nakonfigurovali [Azure akcelerátor zápisu](../../how-to-enable-write-accelerator.md) pro svazky, které obsahují protokoly transakcí DBMS nebo protokoly opětovného provedení. Pamatujte na omezení pro Akcelerátor zápisu zdokumentované.
2. Ověřte, jestli je na nasazených virtuálních počítačích zapnuté [akcelerované síťové služby Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) .

> [!NOTE]
> Ne všechny příkazy v různých profilech SAP-Tune nebo popsané v poznámkách můžou úspěšně běžet v Azure. Příkazy, které by mohly manipulovat s režimem napájení virtuálních počítačů, se obvykle vracejí s chybou, protože není možné manipulovat s režimem napájení základního hardwaru Azure hosta.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Podrobné přípravy specifické pro virtuální počítače Azure
Jednou z konkrétních údajů Azure je instalace rozšíření virtuálního počítače Azure, které zajišťuje data monitorování pro agenta hostitele SAP. Podrobnosti o instalaci tohoto rozšíření pro monitorování jsou popsány v části:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) popisuje rozšířené monitorování SAP s virtuálními počítači se systémem Linux v Azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) popisuje informace o SAPOSCOL v systému Linux 
-  [SAP Note 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) popisuje klíčové metriky monitorování pro SAP v Microsoft Azure
-  [Nasazení Azure Virtual Machines pro SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Instalace SAP HANA
S nasazenými virtuálními počítači Azure a registrovanými a nakonfigurovanými operačními systémy můžete SAP HANA nainstalovat podle instalace SAP. Abyste se dostali do této dokumentace, začněte tím, že zahájíte tyto [prostředky](https://www.sap.com/products/s4hana-erp.html?btp=9d3e6f82-d8ab-4122-8d2d-bf4971217afd) SAP web Hana.

Pro SAP HANA konfigurací se škálováním na více instancí pomocí přímo připojených disků Azure Premium Storage nebo Ultra disk si přečtěte konkrétní informace v dokumentu [SAP HANA konfigurace infrastruktury a operace v Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out) .


## <a name="additional-resources-for-sap-hana-backup"></a>Další zdroje informací pro zálohování SAP HANA
Informace o tom, jak zálohovat SAP HANA databází na virtuálních počítačích Azure, najdete v těchto tématech:
* [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md)
* [SAP HANA Azure Backup na úrovni souborů](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Další kroky
Přečtěte si dokumentaci:

- [Konfigurace infrastruktury SAP HANA a operace v Azure](./hana-vm-operations.md)
- [Konfigurace úložiště virtuálních počítačů Azure SAP HANA](./hana-vm-operations-storage.md)
