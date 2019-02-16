---
title: IBM DB2 pureScale v Azure
description: V tomto článku ukážeme architekturu pro spuštění prostředí pureScale IBM DB2 v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 901afc8f28b617eb5bada2a0f58761ddb9f67607
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56327404"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale v Azure

Prostředí pureScale IBM DB2 poskytuje cluster databáze pro Azure s vysokou dostupností a škálovatelností v operačních systémech Linux. Tento článek popisuje architekturu pro spuštění DB2 pureScale v Azure.

## <a name="overview"></a>Přehled

Podniky využívají dlouho systém (RDBMS) platformami pro správu relačních databází pro jejich zpracování (OLTP) potřebám online transakcí. V dnešních dnech mnoho provádějí migraci jejich prostředí na základě mainframových databáze do Azure jako způsob, jak rozšířit kapacitu, snížit náklady a udržovat strukturu stabilní provozní náklady.

Migrace je často prvním krokem při modernizaci starší platformu. Například jeden z podnikových zákazníků nedávno rehosted IBM DB2 prostředí používají z/OS a pureScale IBM DB2 v Azure. I když není stejný jako původní prostředí, IBM DB2 pureScale v Linuxu nabízí podobné vysoké dostupnosti a škálovatelnosti jako IBM DB2 pro spuštění v paralelní Sysplex konfigurace v hlavním z/OS.

Tento článek popisuje architekturu pro tuto migraci do Azure. Kdy zákazník využil Red Hat Linux 7.4 k testování této konfigurace. Tato verze je k dispozici na webu Azure Marketplace. Než se rozhodnete Linuxová distribuce vytvořená, nezapomeňte ověřit aktuálně podporované verze. Podrobnosti najdete v tématu v dokumentaci k [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) a [GlusterFS](https://docs.gluster.org/en/latest/).

Tento článek je výchozím bodem pro váš plán implementace DB2. Vaše obchodní požadavky se budou lišit, ale platí stejné základní vzor. Tento vzor architektury můžete použít také pro online analytického zpracování (OLAP) aplikace v Azure.

Tento článek nepopisuje rozdíly a možné-migrační úkoly pro přesun IBM DB2 z/OS databáze IBM DB2 pureScale běžící na Linuxu. A neposkytuje odhady velikosti a analýzy úloh pro přechod do DB2 pureScale DB2 z/OS. 

Vám pomohou rozhodnout na nejvhodnější architektura pureScale DB2 pro vaše prostředí, doporučujeme, abyste plně odhadu velikosti a ujistěte se, hypotézu. Ve zdrojovém systému, ujistěte se, že do DB2 z/OS paralelní zvažte Sysplex s architekturou sdílení dat, konfigurace párování zařízení a statistiky o využití zařízení (DDF) distribuovaných datech.

> [!NOTE]
> Tento článek popisuje jeden ze způsobů migrace DB2, ale existují i další. PureScale DB2. můžete také spustit ve virtualizované v místních prostředích. IBM podporuje DB2 na Microsoft Hyper-V v různých konfiguracích. Další informace najdete v tématu [architektura virtualizace pureScale DB2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) v centru IBM znalostní báze.

## <a name="architecture"></a>Architektura

Pro podporu vysoké dostupnosti a škálovatelnosti v Azure, můžete použít škálování na víc systémů, sdílené datové architektury pro DB2 pureScale. Migrace zákazníků používá následující příklad architektury.

![PureScale DB2 na Azure virtual machines zobrazení úložiště a sítě](media/db2-purescale-on-azure/pureScaleArchitecture.png "pureScale DB2 na Azure virtual machines zobrazení úložiště a sítě")


Diagram znázorňuje logické vrstvy, třeba do DB2 pureScale clusteru. Patří mezi ně virtuální počítače pro klienta pro správu, pro ukládání do mezipaměti, pro databázový stroj a sdílené úložiště. 

Kromě databázové stroje uzly diagram obsahuje dva uzly, které se používají pro clustery ukládání do mezipaměti zařízení (CFs). Alespoň dva uzly se používají pro databázový stroj, samotného. DB2 server, který patří do clusteru pureScale se nazývá člena. 

Cluster je připojené přes iSCSI na cluster tříuzlový GlusterFS sdílené úložiště, který poskytuje horizontální navýšení kapacity úložiště a vysokou dostupnost. DB2 pureScale je nainstalován na virtuálních počítačích Azure s Linuxem.

Tento přístup je šablonu, která můžete změnit pro dimenzování a škálování vaší organizace. Je založen na následující:

-   Dva nebo více členů databáze spolu s alespoň dva uzly CF. Uzly Správa fondu s globální vyrovnávací paměti (GBP) pro sdílené paměti a služby uzamknout globální správce (GLM) k řízení sdíleného přístupu a zámek kolize z aktivních členů. Jeden uzel CF funguje jako primární a druhý jako sekundární uzel CF převzetí služeb při selhání. Aby se zabránilo jediný bod selhání v prostředí, do DB2 pureScale clusteru vyžaduje minimálně čtyři uzly.

-   Vysoce výkonné sdílené úložiště (viz P30 velikost v diagramu). Každý z uzlů Gluster FS využívá toto úložiště.

-   Vysoce výkonné sítě pro datové členy a sdílené úložiště.

### <a name="compute-considerations"></a>Důležité informace o COMPUTE

Tato architektura spouští aplikace, úložiště a data úrovní, na virtuálních počítačích Azure. [Instalačních skriptů nasazení](http://aka.ms/db2onazure) vytvořte následující:

-   DB2 pureScale clusteru. Typ výpočetní prostředky, které budete potřebovat v Azure závisí na nastavení. Obecně platí můžete použít dvě metody:

    -   Použít několika uzly, vysoce výkonné výpočetní prostředí (HPC) – styl sítě, pokud malá až středně velké instance přístup ke sdílenému úložišti. Pro tento typ HPC konfigurace Azure paměťově optimalizované řady E nebo L-series optimalizovaným úložištěm [virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) poskytují potřebné výpočetní výkon.

    -   Použijte méně instancí velký virtuální počítač pro datových modulů. Pro velké instance největší paměťově optimalizované [řady M-series](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuální počítače jsou ideální pro náročné úlohy v paměti. Může být nutné vyhrazenou instanci, v závislosti na velikost logického oddílu (LPAR), která se používá ke spouštění DB2.

-   DB2 CF používá paměťově optimalizované virtuálních počítačů, jako je například E-series nebo L-series.

-   GlusterFS úložiště používá standardní\_DS4\_v2 virtuální počítače s Linuxem.

-   GlusterFS jumpbox je Standard\_DS2\_v2 virtuálního počítače s Linuxem.

-   Klient je Standard\_DS3\_v2 virtuálního počítače se systémem Windows (používá se pro účely testování).

-   Monitorovací server je Standard\_DS3\_v2 virtuálního počítače s Linuxem (používá se pro DB2 pureScale).

> [!NOTE]
> Do DB2 pureScale clusteru vyžaduje aspoň dvě instance DB2. Také vyžaduje instance mezipaměti a Uzamknout instanci správce.

### <a name="storage-considerations"></a>Aspekty úložišť

Podobně jako Oracle RAC je DB2 pureScale vysoce výkonné bloku vstupně-výstupních operací, horizontální navýšení kapacity databáze. Doporučujeme použít největší [Azure premium SSD](disks-types.md) možnost, která vyhovuje vašim potřebám. Menší možnosti úložiště může být vhodný pro vývojová a testovací prostředí, zatímco produkční prostředí často potřebujete víc kapacity úložiště. Architektura příklad používá [P30](https://azure.microsoft.com/pricing/details/managed-disks/) z důvodu jeho poměru vstupně-výstupních operací na velikosti a ceny. Bez ohledu na velikost použijte Premium Storage pro zajištění nejlepšího výkonu.

Používá sdílený DB2 pureScale – všechno, co architektura, ve kterém všechna data jsou přístupná ze všech uzlů clusteru. Storage úrovně Premium musí být sdíleny napříč instance, zda na vyžádání nebo na vyhrazených instancích.

Velký cluster pureScale DB2 můžou vyžadovat, aby 200 terabajtů (TB) nebo z úrovně premium sdílené úložiště, s 100 000 IOPS. DB2 pureScale podporuje bloku rozhraní iSCSI, které můžete použít v Azure. Rozhraní iSCSI vyžaduje sdílené úložiště clusteru, který je možné implementovat pomocí GlusterFS, S2D nebo jiný nástroj. Zařízení virtuálního úložiště oblasti sítě (vSAN) tohoto typu řešení vytvoří v Azure. Pomocí sítě vSAN do DB2 pureScale nainstaluje clusterového souborového systému, který se používá ke sdílení dat mezi virtuálními počítači.

Architektura příklad používá GlusterFS, bezplatný, škálovatelné a open source distribuovaný systém souborů, která je optimalizovaná pro cloudové úložiště.

### <a name="networking-considerations"></a>Aspekty sítí

IBM doporučuje síťovými možnostmi InfiniBand pro všechny členy do DB2 pureScale clusteru. DB2 pureScale také používá vzdálený přímý přístup do paměti (RDMA), pokud je k dispozici, pro CFs.

Během instalace, vytvoříte Azure [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) tak, aby obsahovala všechny virtuální počítače. Obecně platí skupiny prostředků na základě jejich životního cyklu a který bude je spravovat. Virtuální počítače v této architektuře vyžadují [akcelerované síťové služby](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). To je funkce služby Azure, která zajišťuje síťové konzistentní vzhledem k aplikacím, mimořádně nízkou latenci prostřednictvím single-root I/O virtualization (rozhraní SR-IOV) k virtuálnímu počítači.

Každý virtuální počítač Azure je nasazený do virtuální sítě s podsítí: hlavní, Gluster FS front-endu (gfsfe), Gluster FS back-endu (bfsbe), DB2 pureScale (db2be) a DB2 pureScale front end (db2fe). Instalační skript také vytvoří primární [síťových adaptérů](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) na virtuálních počítačích v hlavní podsítě.

Použití [skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) možné omezit síťový provoz ve virtuální síti a k izolování podsítí.

V Azure musí používat protokol TCP/IP jako připojení k síti pro úložiště DB2 pureScale.

## <a name="next-steps"></a>Další postup

-   [Nasazení této architektury v Azure](deploy-ibm-db2-purescale-azure.md)
