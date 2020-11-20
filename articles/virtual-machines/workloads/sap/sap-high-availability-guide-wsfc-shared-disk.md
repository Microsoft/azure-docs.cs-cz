---
title: Cluster SAP ASCS/SCS instance v WSFC pomocí sdíleného disku v Azure | Microsoft Docs
description: Naučte se, jak clusterovat instanci SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 079a1aed500014b5ec3f14c1cf6ece97e104558e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958519"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure

> ![Operační systém Windows][Logo_Windows] Windows
>

Clustering s podporou převzetí služeb při selhání ve Windows serveru je základem instalace ASCS/SCS SAP s vysokou dostupností a DBMS ve Windows.

Cluster s podporou převzetí služeb při selhání je skupina s jedním nebo více než nezávisle servery (uzly), které vzájemně spolupracují za účelem zvýšení dostupnosti aplikací a služeb. Pokud dojde k selhání uzlu, clustering s podporou převzetí služeb při selhání ve Windows serveru vypočítá počet selhání, ke kterým může dojít, a udržujte cluster v pořádku, aby poskytoval aplikace a služby. Pro zajištění clusteringu s podporou převzetí služeb při selhání můžete vybrat z různých režimů kvora.

## <a name="prerequisites"></a>Požadavky
Než začnete s úlohami v tomto článku, přečtěte si následující článek:

* [Architektura a scénáře s vysokou dostupností pro Azure Virtual Machines pro SAP NetWeaver][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Clustering s podporou převzetí služeb při selhání Windows serveru v Azure

Clustering s podporou převzetí služeb při selhání ve Windows serveru s Azure Virtual Machines vyžaduje další kroky konfigurace. Při sestavování clusteru je potřeba nastavit několik IP adres a názvy virtuálních hostitelů pro instanci SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Překlad názvů v Azure a název virtuálního hostitele clusteru

Cloudová platforma Azure nenabízí možnost konfigurace virtuálních IP adres, jako jsou například plovoucí IP adresy. K nastavení virtuální IP adresy pro dosažení prostředku clusteru v cloudu potřebujete alternativní řešení. 

Služba Azure Load Balancer poskytuje *interní nástroj pro vyrovnávání zatížení* pro Azure. S interním nástrojem pro vyrovnávání zatížení klienti dosáhnou clusteru přes virtuální IP adresu clusteru. 

Nasaďte interní nástroj pro vyrovnávání zatížení ve skupině prostředků, která obsahuje uzly clusteru. Pak nakonfigurujte všechna nezbytná pravidla předávání portů pomocí portů sondy interního nástroje pro vyrovnávání zatížení. Klienti se mohou připojit prostřednictvím názvu virtuálního hostitele. Server DNS přeloží IP adresu clusteru a interní nástroj pro vyrovnávání zatížení zpracovává přesměrování portu na aktivní uzel clusteru.

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

![Obrázek 1: Konfigurace clusteringu s podporou převzetí služeb při selhání Windows v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure bez sdíleného disku_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>SAP ASCS/SCS HA pomocí sdílených disků clusteru
V systému Windows instance SAP ASCS/SCS obsahuje službu SAP Central Services, server zpráv SAP, procesy serveru fronty a soubory globálního hostitele SAP. Soubory globálního hostitele SAP ukládají centrální soubory pro celý systém SAP.

Instance SAP ASCS/SCS má následující komponenty:

* Centrální služby SAP:
    * Dva procesy, server zpráv a zařazování a \<ASCS/SCS virtual host name> , který se používá pro přístup k těmto dvěma procesům.
    * Struktura souborů: S:\usr\sap \\ &lt; SID &gt; \ ASCS/SCS\<instance number\>


* Soubory globálního hostitele SAP:
  * Struktura souborů: S:\usr\sap \\ &lt; SID &gt; \SYS \. ..
  * Sdílená složka sapmnt, která umožňuje přístup k těmto globálním S:\usr\sap \\ &lt; SID &gt; \SYS \. .. soubory pomocí následující cesty UNC:

    \\\\<ASCS/SCS název virtuálního hostitele \> \Sapmnt \\ &lt; SID &gt; \SYS \. ..


![Obrázek 2: procesy, struktura souborů a globální sapmnt sdílené složky hostitele instance SAP ASCS/SCS][sap-ha-guide-figure-8001]

_Procesy, struktury souborů a globální sdílená složka hostitele sapmnt instance SAP ASCS/SCS_

V nastavení vysoké dostupnosti můžete clusterovat instance SAP ASCS/SCS. V našem příkladu používáme *sdílené disky v clusteru* (jednotky S, v našem příkladu) k umístění souborů SAP ASCS/SCS a SAP Global Hosts.

![Obrázek 3: architektura SAP ASCS/SCS HA se sdíleným diskem][sap-ha-guide-figure-8002]

_Architektura SAP ASCS/SCS HA se sdíleným diskem_


S architekturou replikace serveru fronty 1:
* Stejný \<ASCS/SCS virtual host name> postup se používá pro přístup k procesům zpráv SAP a zařazování do fronty a k souborům globálního hostitele SAP prostřednictvím sdílené složky sapmnt.
* Mezi nimi se sdílí stejná jednotka sdíleného disku clusteru.  

S architekturou replikace serveru fronty 2: 
* Stejný postup \<ASCS/SCS virtual host name> se používá pro přístup k procesu serveru zpráv SAP a k globálním hostitelům SAP přes sdílenou složku systému souborů sapmnt.
* Mezi nimi se sdílí stejná jednotka sdíleného disku clusteru.
* \<ERS virtual host name>Pro přístup k procesu serveru fronty je k dispozici samostatné oddělení.  

![Obrázek 4: architektura SAP ASCS/SCS HA se sdíleným diskem][sap-ha-guide-figure-8003]

_Architektura SAP ASCS/SCS HA se sdíleným diskem_

#### <a name="shared-disk-and-enqueue-replication-server"></a>Replikační Server sdíleného disku a fronty 

1. Sdílený disk se podporuje s architekturou replikace serveru fronty 1, kde je instance serveru služby Replication Server (OLAJÍCÍCH):   

   - není clusterovaný
   - používá `localhost` název
   - je nasazen na místních discích na jednotlivých uzlech clusteru.

2. Sdílený disk je také podporován s architekturou replikace serveru fronty 2, kde je instance serveru fronty replikace 2 (ERS2).  

   - je clusterovaný
   - používá vyhrazený název virtuálního nebo síťového hostitele
   - vyžaduje, aby byla IP adresa virtuálního hostitele OLAJÍCÍCH nakonfigurovaná na interním Load Balancer Azure, kromě IP adresy SCS (A).
   - aplikace je nasazená na **místních discích** na každém z clusterovaných uzlů, takže není potřeba sdílet disk.

   > [!TIP]
   > Další informace o zařazování serveru replikace 1 a 2 (ERS1 a ERS2) najdete tady:  
   > [Zařazování replikačního serveru v clusteru s podporou převzetí služeb při selhání Microsoftu](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [Nový Replikátor fronty v prostředích clusteru s podporou převzetí služeb](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>Možnosti sdíleného disku v Azure pro úlohy SAP

V clusteru s podporou převzetí služeb při selhání systému Windows v Azure jsou k dispozici dvě možnosti pro sdílený disk:

- [Sdílené disky Azure](../../disks-shared.md) – funkce, které umožňují připojení spravovaného disku Azure ke více virtuálním počítačům současně. 
- Vytvoření zrcadleného úložiště, které simuluje sdílené úložiště clusteru, pomocí softwaru třetí strany s [Clusterovou edicí DataKeeper](https://us.sios.com/products/datakeeper-cluster) 

Při výběru technologie pro sdílený disk mějte na paměti následující skutečnosti:

**Sdílený disk Azure pro úlohy SAP**
- Umožňuje připojit Azure Managed disk k několika virtuálním počítačům současně bez nutnosti dalšího softwaru udržovat a provozovat. 
- Budete pracovat s jedním sdíleným diskem Azure na jednom úložném clusteru. To má vliv na spolehlivost řešení SAP.
- V současné době je jediným podporovaným nasazením disk Azure Shared Premium v sadě dostupnosti. Sdílený disk Azure není podporován v nasazení Zona.     
- Ujistěte se, že zřizujete disk Azure Premium s minimální velikostí disku určenou v [SSD úrovně Premium rozsahy](../../disks-shared.md#disk-sizes) , aby bylo možné se připojit k požadovanému počtu virtuálních počítačů současně (obvykle 2 pro cluster s podporou převzetí služeb při selhání s Windows SAP ASCS Windows). 
- Azure Shared Ultra disk není podporován pro úlohy SAP, protože nepodporuje nasazení v rámci skupiny dostupnosti nebo nasazení v rámci prostředí.  
 
**FINÁL**
- Řešení s poskytuje synchronní replikaci dat v reálném čase mezi dvěma disky.
- S řešením s pracujete se dvěma spravovanými disky a pokud používáte buď skupiny dostupnosti nebo zóny dostupnosti, budou spravované disky na různé clustery úložiště. 
- Nasazení v zónách dostupnosti je podporováno.
- Vyžaduje instalaci a provozování softwaru třetích stran, které budete muset koupit navíc.

### <a name="shared-disk-using-azure-shared-disk"></a>Sdílený disk pomocí sdíleného disku Azure

Microsoft nabízí [sdílené disky Azure](../../disks-shared.md), které se dají použít k implementaci vysoké dostupnosti SAP ASCS/SCS s možností sdíleného disku.

#### <a name="prerequisites-and-limitations"></a>Požadavky a omezení

V současné době můžete pro instanci SAP ASCS/SCS použít disky Azure SSD úrovně Premium jako sdílený disk Azure. V současné době jsou k dismístě tato omezení:

-  [Azure Ultra disk](../../disks-types.md#ultra-disk) se nepodporuje jako sdílený disk Azure pro úlohy SAP. V současné době není možné umístit virtuální počítače Azure pomocí Azure Ultra disk v sadě dostupnosti.
-  [Sdílený disk Azure](../../disks-shared.md) s SSD úrovně Premium disky se podporuje jenom s virtuálními počítači ve skupině dostupnosti. V nasazení Zóny dostupnosti se nepodporuje. 
-  Hodnota sdíleného disku Azure [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) určuje, kolik uzlů clusteru může používat sdílený disk. V případě instance SAP ASCS/SCS nakonfigurujete dva uzly v clusteru s podporou převzetí služeb při selhání systému Windows, takže hodnota vlastnosti `maxShares` musí být nastavena na hodnotu dvě.
-  Všechny virtuální počítače s clustery SAP ASCS/SCS musí být nasazené ve stejné [skupině umístění služby Azure Proximity](../../windows/proximity-placement-groups.md).   
   I když můžete nasadit virtuální počítače clusterů Windows ve skupině dostupnosti se sdíleným diskem Azure bez PPG, PPG zajistí uzavření fyzické blízkosti sdílených disků Azure a virtuálních počítačů clusteru, čímž se dosáhne nižší latence mezi virtuálními počítači a vrstvou úložiště.    

Další podrobnosti o omezeních pro sdílený disk Azure najdete v části s přehledem [omezení](../../disks-shared.md#limitations) v dokumentaci ke sdíleným diskům Azure.

> [!IMPORTANT]
> Při nasazování clusteru SAP ASCS/SCS Windows s podporou převzetí služeb při selhání se sdíleným diskem Azure Pamatujte na to, že nasazení bude pracovat s jedním sdíleným diskem v jednom úložném clusteru. V případě problémů s clusterem úložiště, do kterého se nasadí sdílený disk Azure, by se měla ovlivnit instance SAP ASCS/SCS.    

> [!TIP]
> Při plánování nasazení SAP si přečtěte téma [Průvodce plánováním SAP NetWeaver v Azure](./planning-guide.md) a [Příručka pro Azure Storage pro úlohy SAP](./planning-guide-storage.md) , kde najdete důležité požadavky.

### <a name="supported-os-versions"></a>Podporované verze operačního systému

Podporovány jsou systémy Windows Server 2016 a 2019 (použijte nejnovější obrázky datového centra).

Důrazně doporučujeme používat **Windows Server 2019 Datacenter**, jako je:
- Clusterová služba s podporou převzetí služeb při selhání systému Windows 2019 podporuje Azure
- Monitorování pro události plánu Azure vám přidává integraci a povědomí o údržbě hostitelů Azure a zlepšení prostředí.
- Je možné použít název distribuované sítě (Jedná se o výchozí možnost). Proto není nutné mít vyhrazenou IP adresu pro síťový název clusteru. Kromě toho není potřeba konfigurovat tuto IP adresu v interních Load Balancer Azure. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>Sdílené disky v Azure s využitím DataKeeper

Další možností pro sdílený disk je použití softwaru třetí strany s procesorem DataKeeper Edition k vytvoření zrcadleného úložiště, které simuluje sdílené úložiště clusteru. Řešení s poskytuje synchronní replikaci dat v reálném čase.

Vytvoření prostředku sdíleného disku pro cluster:

1. Připojte další disk ke každému virtuálnímu počítači v konfiguraci clusteru Windows.
2. Spusťte rutinu s rutinou datakeep-cluster na obou uzlech virtuálních počítačů.
3. Nakonfigurujte s clusterem DataKeeper Edition tak, aby zrcadlí obsah dalšího svazku připojeného disku ze zdrojového virtuálního počítače na další svazek připojený k disku cílového virtuálního počítače. S tím, že služba datakeeps vyabstrakce zdrojové a cílové místní svazky a pak je prezentuje clusteringu Windows Server s podporou převzetí služeb při selhání jako jeden sdílený disk.

Získejte další informace o [Datakeepu](https://us.sios.com/products/datakeeper-cluster/).

![Obrázek 5: Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure s využitím][sap-ha-guide-figure-1002]

_Konfigurace clusteringu s podporou převzetí služeb při selhání Windows v Azure s využitím_

> [!NOTE]
> Pro zajištění vysoké dostupnosti s některými produkty DBMS, jako je SQL Server, nepotřebujete sdílené disky. SQL Server AlwaysOn replikuje DBMS data a soubory protokolu z místního disku jednoho uzlu clusteru na místní disk jiného uzlu clusteru. V takovém případě konfigurace clusteru Windows nepotřebuje sdílený disk.
>

## <a name="next-steps"></a>Další kroky

* [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]

* [Instalace SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfigurace s vysokou dostupností pro SAP multi-SID)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md