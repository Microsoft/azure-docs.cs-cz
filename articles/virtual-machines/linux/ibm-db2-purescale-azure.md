---
title: IBM Db2 pureScale v Azure
description: V tomto článku ukážeme architekturu pro spuštění prostředí pureScale IBM Db2 v Azure.
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
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977543"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM Db2 pureScale v Azure

Prostředí pureScale IBM Db2 poskytuje databáze Clusterové řešení pro Azure s vysokou dostupností a škálovatelností v operačních systémech Linux. V tomto článku ukážeme architekturu pro spuštění Db2 pureScale v Azure.

## <a name="overview"></a>Přehled

Podniky dlouho použili systém (RDBMS) platformami pro správu tradičních relačních databází pro online zpracování potřebám (OLTP) transakcí. V dnešních dnech mnoho provádějí migraci jejich prostředí na základě mainframových databáze do Azure jako způsob, jak rozšířit kapacitu, snížit náklady a udržovat strukturu stabilní provozní náklady.

Migrace je často prvním krokem při modernizaci starší verze platformy. Například jedna organizace nedávno rehosted prostředí svoje IBM Db2 používají z/OS a pureScale IBM Db2 v Azure. Zatímco není stejný jako původní prostředí, pureScale IBM Db2 v Linuxu nabízí podobné vysokou dostupnost a škálovatelnost funkce jako IBM Db2 pro spuštění v paralelní Sysplex konfigurace v hlavním z/OS.

Tento článek popisuje architekturu pro tuto migraci do Azure. Red Hat Linux 7.4 byl použit k testování této konfigurace. Tato verze je k dispozici na webu Azure Marketplace. Před výběrem Linuxová distribuce vytvořená, nezapomeňte ověřit aktuálně podporované verze. Podrobnosti najdete v tématu v dokumentaci k [IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) a [GlusterFS](https://docs.gluster.org/en/latest/).

Tento článek je jednoduše výchozí bod pro váš plán implementace Db2. Vaše obchodní požadavky se budou lišit, ale platí stejné základní vzor. Tento vzor architektury slouží také pro aplikace online analytického zpracování (OLAP) v Azure.

Tento článek nepopisuje rozdíly a možné-migrační úkoly pro přesun IBM Db2 z/OS databáze IBM Db2 pureScale běžící na Linuxu. Ani poskytuje odhady ekvivalentní velikosti a analýzy úloh pro přechod do Db2 pureScale Db2 z/OS. Při rozhodování o nejvhodnější architektura pureScale Db2 pro vaše prostředí, důrazně doporučujeme provést úplnou velikosti odhad výkonu a vytvořit hypotézu. Mezi další faktory ve zdrojovém systému ujistěte se, že do Db2 z/OS paralelní zvažte Sysplex se architektura sdílení dat, konfigurací párování zařízení a statistiky o využití DDF.

> [!NOTE]
> Tento článek popisuje jeden ze způsobů migrace Db2, ale existují i další. PureScale Db2. můžete také spustit ve virtualizované v místních prostředích. IBM podporuje Db2 na Microsoft Hyper-V v různých konfiguracích. Další informace najdete v tématu [architektura virtualizace pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) v centru IBM znalostní báze.

## <a name="architecture"></a>Architektura

Pro podporu vysoké dostupnosti a škálovatelnosti v Azure, horizontální navýšení kapacity, architektura sdílených dat je možné pro Db2 pureScale. Tato architektura příklad se použil pro naše zákazníky migrace.

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "PureScale Db2 na Azure virtual machines zobrazení úložiště a sítě")


Diagram architektury znázorňuje logické vrstvy, třeba do Db2 pureScale clusteru. Patří mezi ně virtuální počítače pro klienta pro správu, pro ukládání do mezipaměti, pro databázový stroj a sdílené úložiště. Kromě databázové stroje uzly diagram také obsahuje dva uzly, které se používají pro co jsou označovány jako ukládání do mezipaměti zařízení (CFs) clusteru. Minimálně dva uzly, které se používají pro databázový stroj, sama o sobě; Db2 server, který patří do clusteru pureScale se nazývá člena. Cluster je připojené přes iSCSI na cluster tříuzlový GlusterFS sdílené úložiště, který poskytuje horizontální navýšení kapacity úložiště a vysokou dostupnost. Db2 pureScale je nainstalován na virtuálních počítačích Azure s Linuxem.

Tento přístup je jednoduše šablonu, která můžete upravit tak, aby odpovídala velikosti a škálování potřeba ve vaší organizaci a je založené na těchto faktorech:

-   Dva nebo více členů databáze se sloučí s alespoň dva uzly CF, které Správa fondu s globální vyrovnávací paměti (GBP) pro sdílenou paměť a služby uzamknout globální správce (GLM) na ovládací prvek sdíleného přístupu a zámek kolize z více aktivních členů. Jeden uzel CF funguje jako primární a druhý jako sekundární uzel CF převzetí služeb při selhání. Vyhnout se vytváření prostředí, která má jediný bod selhání, jsou požadovány pro cluster Db2 pureScale minimálně čtyři uzly.

-   Vysoce výkonné sdílené úložiště (viz P30 velikost na obrázku 1), který se používá ve všech uzlech Gluster FS.

-   Vysoce výkonné sítě pro datové členy a sdílené úložiště.

### <a name="compute-considerations"></a>Důležité informace o COMPUTE

Tato architektura spouští aplikace, úložiště a data úrovní, na virtuálních počítačích Azure. [Instalačních skriptů nasazení](http://aka.ms/db2onazure) vytvořte následující:

-   Db2 pureScale clusteru. Typ výpočetní prostředky, které budete potřebovat v Azure závisí na nastavení. Obecně jsou dva přístupy, které můžete použít:

    -   Použít několika uzly, vysoce výkonné výpočetní prostředí (HPC) – styl sítě, pokud více instancí malé a střední přístup ke sdílenému úložišti. Pro tento typ HPC konfigurace Azure paměťově optimalizované řady E nebo L-series optimalizovaným úložištěm [virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) poskytují výpočetní výkon potřebný.

    -   Použijte méně instancí velký virtuální počítač pro datových modulů. Pro velké instance největší paměťově optimalizované [řady M-series](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuální počítače jsou ideální pro náročné úlohy v paměti, ale vyhrazenou instanci se může vyžadovat, v závislosti na velikosti z logický oddíl (LPAR), který se používá ke spuštění Db2.

-   Db2 CF používá paměťově optimalizované virtuálních počítačů, jako je například E-series nebo L-series.

-   GlusterFS úložiště používá standardní\_DS4\_v2 virtuální počítače s Linuxem.

-   GlusterFS jumpbox je Standard\_DS2\_v2 virtuálního počítače s Linuxem.

-   Klient je Standard\_DS3\_v2 virtuálního počítače se systémem Windows (používá se pro účely testování).

-   Monitorovací server je Standard\_DS3\_v2 virtuálního počítače s Linuxem (používá se pro Db2 pureScale).

> [!NOTE]
> Minimálně dvě instance Db2 vyžadovaných do Db2 pureScale clusteru. Vyžaduje se také instance mezipaměti a Správce zámků instance.

### <a name="storage-considerations"></a>Aspekty úložišť

Podobně jako Oracle RAC je Db2 pureScale vysoce výkonné bloku vstupně-výstupních operací, horizontální navýšení kapacity databáze. Doporučujeme použít největší [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) k dispozici, který vyhovuje vašim potřebám. Menší možnosti úložiště například může být vhodný pro vývojová a testovací prostředí, zatímco produkční prostředí často vyžadují větší kapacitu úložiště. Architektura příklad používá [P30](https://azure.microsoft.com/pricing/details/managed-disks/) z důvodu jeho poměru vstupně-výstupních operací na velikosti a ceny. Bez ohledu na velikost použijte Premium Storage pro zajištění nejlepšího výkonu.

Db2 pureScale používá sdílený všechno, co architektura, ve kterém všechna data jsou přístupná ze všech uzlů clusteru. Storage úrovně Premium, musí se sdílet napříč několika instancemi – ať už na vyžádání nebo na vyhrazených instancích.

Velký cluster pureScale Db2 můžou vyžadovat, aby 200 terabajtů (TB) nebo vyšší úrovně Premium sdílené úložiště, přičemž 100 000 IOPS. Db2 pureScale podporuje bloku rozhraní iSCSI, které lze použít v Azure. Rozhraní iSCSI vyžaduje sdílené úložiště clusteru, který je možné implementovat pomocí GlusterFS, S2D nebo jiný nástroj. Zařízení virtuálního úložiště oblasti sítě (vSAN) tohoto typu řešení vytvoří v Azure. Pomocí sítě vSAN do Db2 pureScale nainstaluje clusterového souborového systému, který se používá ke sdílení dat mezi několika virtuálními počítači.

Pro tuto architekturu jsme použili GlusterFS systému souborů, systém souborů zdarma, škálovatelné open source distribuovaná speciálně optimalizovaný pro cloudové úložiště.

### <a name="networking-considerations"></a>Aspekty sítí

IBM doporučuje síťovými možnostmi InfiniBand pro všechny členy do Db2 pureScale clusteru; Db2 pureScale také používá vzdálený přímý přístup do paměti (RDMA), pokud je k dispozici, pro CFs použít.

Během instalace, Azure [skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) se vytvoří tak, aby obsahovala všechny virtuální počítače. Obecně platí prostředky jsou seskupit na základě jejich životního cyklu a který bude je spravovat. Virtuální počítače v této architektuře vyžadují [akcelerovanými síťovými službami](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/), funkce služby Azure, která zajišťuje síťové konzistentní vzhledem k aplikacím, mimořádně nízkou latenci prostřednictvím virtualizaci (rozhraní SR-IOV) k virtuálnímu počítači.

Každý virtuální počítač Azure je nasazený do virtuální sítě, který je segmentovat do několika podsítí: hlavní, Gluster FS front-endu (gfsfe), Gluster FS back-endu (bfsbe), Db2 pureScale (db2be) a Db2 purescale front end (db2fe). Instalační skript také vytvoří primární [síťových adaptérů](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) na virtuálních počítačích v hlavní podsítě.

[Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (Nsg) slouží k omezení síťového provozu ve virtuální síti a k izolování podsítí.

V Azure musí používat protokol TCP/IP jako připojení k síti pro úložiště Db2 pureScale.

## <a name="next-steps"></a>Další postup

-   [Nasazení této architektury v Azure](deploy-ibm-db2-purescale-azure.md)
