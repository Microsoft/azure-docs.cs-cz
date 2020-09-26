---
title: Cluster SAP ASCS/SCS ve službě WSFC s použitím sdílené složky v Azure | Microsoft Docs
description: Naučte se, jak clusterovat instanci SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdílené složky v Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/24/2019
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 721389b557fde41b1461654b03299601e2384108
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361326"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdílené složky v Azure

> ![Logo Windows][Logo_Windows] Windows
>

Clustering s podporou převzetí služeb při selhání ve Windows serveru je základem instalace ASCS/SCS SAP s vysokou dostupností a DBMS ve Windows.

Cluster s podporou převzetí služeb při selhání je skupina s 1 + n nezávislými servery (uzly), které vzájemně spolupracují za účelem zvýšení dostupnosti aplikací a služeb. Pokud dojde k selhání uzlu, clustering s podporou převzetí služeb při selhání ve Windows serveru vypočítá počet selhání, ke kterým může dojít, a udržujte cluster v pořádku, aby poskytoval aplikace a služby. Pro zajištění clusteringu s podporou převzetí služeb při selhání můžete vybrat z různých režimů kvora.

## <a name="prerequisites"></a>Požadavky
Než začnete s úkoly popsanými v tomto článku, přečtěte si tento článek:

* [Architektura a scénáře s vysokou dostupností pro Azure Virtual Machines pro SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Clustering SAP ASCS/SCS Instances pomocí sdílené složky se podporuje pro SAP NetWeaver 7,40 (a novější) pomocí SAP kernel 7,49 (a novější).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Clustering s podporou převzetí služeb při selhání Windows serveru v Azure

V porovnání s nasazením z holého nebo privátního cloudu vyžaduje Azure Virtual Machines další kroky ke konfiguraci clusteringu s podporou převzetí služeb při selhání Windows serveru. Při sestavování clusteru je potřeba nastavit několik IP adres a názvy virtuálních hostitelů pro instanci SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Překlad názvů v Azure a název virtuálního hostitele clusteru

Cloudová platforma Azure nenabízí možnost konfigurace virtuálních IP adres, jako jsou například plovoucí IP adresy. K nastavení virtuální IP adresy pro dosažení prostředku clusteru v cloudu potřebujete alternativní řešení. 

Služba Azure Load Balancer poskytuje *interní nástroj pro vyrovnávání zatížení* pro Azure. S interním nástrojem pro vyrovnávání zatížení klienti dosáhnou clusteru přes virtuální IP adresu clusteru. 

Nasaďte interní nástroj pro vyrovnávání zatížení ve skupině prostředků, která obsahuje uzly clusteru. Pak nakonfigurujte všechna nezbytná pravidla předávání portů pomocí portů sondy interního nástroje pro vyrovnávání zatížení. Klienti se mohou připojit prostřednictvím názvu virtuálního hostitele. Server DNS přeloží IP adresu clusteru. Interní nástroj pro vyrovnávání zatížení zpracovává přesměrování portu na aktivní uzel clusteru.

![Obrázek 1: Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure bez sdíleného disku][sap-ha-guide-figure-1001]

_**Obrázek 1:** Konfigurace clusteringu s podporou převzetí služeb při selhání Windows serveru v Azure bez sdíleného disku_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA se sdílením souborů

SAP vyvinul nový přístup a alternativu ke sdíleným diskům clusteru pro clustering instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows. Místo používání sdílených disků clusteru můžete použít sdílenou složku SMB k nasazení souborů globálního hostitele SAP.

> [!NOTE]
> Sdílená složka SMB je alternativou k použití sdílených disků clusteru ke clusteringu instancí SAP ASCS/SCS.  
>

Tato architektura je specifická v následujících ohledech:

* Služby SAP Central Services (s vlastní strukturou souborů a procesy zpráv a zařazování do fronty) jsou oddělené od globálních hostitelských souborů SAP.
* Služby SAP Central Services běží pod instancí SAP ASCS/SCS.
* Instance SAP ASCS/SCS je clusterovaná a je přístupná pomocí \<ASCS/SCS virtual host name\> názvu virtuálního hostitele.
* Globální soubory SAP jsou umístěné ve sdílené složce SMB a jsou k nim přistupované pomocí \<SAP global host\> názvu hostitele: \\ \\ &lt; SAP Global host &gt; \sapmnt \\ &lt; SID &gt; \SYS \. ..
* Instance SAP ASCS/SCS je nainstalovaná na místním disku na obou uzlech clusteru.
* \<ASCS/SCS virtual host name\>Název sítě se liší od &lt; globálního hostitele SAP &gt; .

![Obrázek 2: architektura SAP ASCS/SCS HA se sdílenou složkou SMB][sap-ha-guide-figure-8004]

_**Obrázek 2:** Nová architektura SAP ASCS/SCS HA se sdílenou složkou SMB_

Předpoklady pro sdílenou složku SMB:

* Protokol SMB 3,0 (nebo novější).
* Možnost nastavit seznamy řízení přístupu (ACL) služby Active Directory pro skupiny uživatelů služby Active Directory a `computer$` objekt počítače.
* Sdílená složka musí mít povolený HA:
    * Disky používané k ukládání souborů nesmí být jediným bodem selhání.
    * Výpadky serveru nebo virtuálního počítače nezpůsobí výpadkům sdílené složky.

\<SID\>Role clusteru SAP neobsahuje sdílené disky clusteru ani obecný prostředek clusteru Shared File.


![Obrázek 3: \< \> prostředky role clusteru SAP SID pro použití sdílené složky][sap-ha-guide-figure-8005]

_**Obrázek 3:** &lt; &gt; Prostředky clusterové role SAP SID pro použití sdílené složky_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Sdílené složky se škálováním na více instancí s Prostory úložiště s přímým přístupem v Azure jako sdílená složka SAPMNT

Sdílenou složku se škálováním na více instancí můžete použít k hostování a ochraně globálních hostitelských souborů SAP. Sdílená složka se škálováním na více instancí nabízí také vysoce dostupnou službu sdílení souborů SAPMNT.

![Obrázek 4: sdílená složka se škálováním na více instancí používaná k ochraně souborů globálního hostitele SAP][sap-ha-guide-figure-8006]

_**Obrázek 4:** Sdílená složka se škálováním na víc instancí, která se používá k ochraně globálních hostitelských souborů SAP_

> [!IMPORTANT]
> Sdílené složky se škálováním na více instancí jsou plně podporované v Microsoft Azure cloudu a v místních prostředích.
>

Sdílená složka se škálováním na více instancí nabízí vysoce dostupnou a horizontálně škálovatelnou sdílenou složku SAPMNT.

Prostory úložiště s přímým přístupem se používá jako sdílený disk pro sdílenou složku se škálováním na víc instancí. Prostory úložiště s přímým přístupem můžete použít k vytvoření vysoce dostupného a škálovatelného úložiště pomocí serverů s místním úložištěm. Sdílené úložiště, které se používá pro sdílenou složku se škálováním na více instancí, například pro soubory globálního hostitele SAP, není jediným bodem selhání.

Při volbě Prostory úložiště s přímým přístupem zvažte tyto případy použití:

- Virtuální počítače, které se používají k sestavení Prostory úložiště s přímým přístupem clusteru, se musí nasadit v sadě dostupnosti Azure.
- Pro zotavení po havárii Prostory úložiště s přímým přístupem clusteru můžete použít [služby Azure Site Recovery](../../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage).
- Roztažení clusteru prostorů úložiště s přímým přístupem v různých Zóny dostupnosti Azurech se nepodporuje.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Požadavky SAP pro sdílené složky se škálováním na více instancí v Azure

Chcete-li použít sdílenou složku se škálováním na více systémů, musí systém splňovat následující požadavky:

* Alespoň dva uzly clusteru pro sdílenou složku se škálováním na více instancí.
* Každý uzel musí mít alespoň dva místní disky.
* Z důvodu výkonu je nutné použít odolnost proti chybám *zrcadlení*:
    * Obousměrné zrcadlení pro sdílenou složku se škálováním na více instancí se dvěma uzly clusteru.
    * Třícestný zrcadlení pro sdílenou složku se škálováním na více instancí se třemi (nebo více) uzly clusteru.
* Pro sdílenou složku se škálováním na více instancí doporučujeme tři (nebo víc) uzly clusteru s třícestným zrcadlením.
    Tato instalace nabízí větší škálovatelnost a větší odolnost úložiště než nastavení sdílené složky se škálováním na více instancí se dvěma uzly clusteru a obousměrným zrcadlením.
* Musíte použít prémiové disky Azure.
* Doporučujeme, abyste používali Azure Managed Disks.
* Doporučujeme formátovat svazky pomocí odolného systému souborů (ReFS).
    * Další informace najdete v tématu [SAP Note 1869038 – podpora SAP pro systém souborů ReFs][1869038] a [Výběr části systém souborů][planning-volumes-s2d-choosing-filesystem] v článku plánování svazků v prostory úložiště s přímým přístupem.
    * Ujistěte se, že jste nainstalovali [kumulativní aktualizaci Microsoft KB4025334][kb4025334].
* Můžete použít velikosti virtuálních počítačů Azure řady DS-Series nebo DSv2-Series.
* Pro dobrý výkon sítě mezi virtuálními počítači, který je potřeba pro Prostory úložiště s přímým přístupem synchronizaci disku, použijte typ virtuálního počítače, který má alespoň vysokou šířku pásma sítě.
    Další informace najdete v tématu Specifikace [DSv2-Series][dv2-series] a [DS-Series][ds-series] .
* Doporučujeme, abyste si vyhradi nějakou nepřidělenou kapacitu ve fondu úložiště. Když ponecháte nějakou nepřidělenou kapacitu ve fondu úložiště, zajistíte místo na disku možnost opravit, pokud dojde k chybě jednotky. Tím se zlepší zabezpečení a výkon dat.  Další informace najdete v tématu [Volba velikosti svazku][choosing-the-size-of-volumes-s2d].
* Nemusíte konfigurovat interní nástroj pro vyrovnávání zatížení Azure pro síťový název sdílené složky se škálováním na více instancí, například pro \<SAP global host\> . To se provádí pro \<ASCS/SCS virtual host name\> instanci SAP ASCS/SCS nebo pro DBMS. Sdílená složka se škálováním na více instancí škáluje zatížení na všech uzlech clusteru. \<SAP global host\> používá místní IP adresu pro všechny uzly clusteru.


> [!IMPORTANT]
> Nemůžete přejmenovat sdílenou složku SAPMNT, která odkazuje na \<SAP global host\> . SAP podporuje pouze název sdílené složky "sapmnt".
>
> Další informace najdete v tématu [SAP Note 2492395 – můžete změnit název sdílené složky sapmnt?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Konfigurace instancí SAP ASCS/SCS a sdílené složky se škálováním na více systémů ve dvou clusterech

Instance SAP ASCS/SCS můžete nasadit v jednom clusteru s vlastní \<SID\> rolí clusteru SAP. V takovém případě nakonfigurujete sdílenou složku se škálováním na více instancí v jiném clusteru s jinou rolí clusteru.

> [!IMPORTANT]
>V tomto scénáři je instance SAP ASCS/SCS nakonfigurovaná pro přístup k globálnímu hostiteli SAP pomocí cesty UNC, \\ \\ &lt; globální hostitel SAP &gt; \sapmnt \\ &lt; SID &gt; \SYS\.
>

![Obrázek 5: instance SAP ASCS/SCS a sdílená složka se škálováním na více systémů nasazená ve dvou clusterech][sap-ha-guide-figure-8007]

_**Obrázek 5:** Instance SAP ASCS/SCS a sdílená složka se škálováním na více systémů nasazená ve dvou clusterech_

> [!IMPORTANT]
> V cloudu Azure musí být každý cluster, který se používá pro SAP a sdílené složky se škálováním na více instancí, nasazený ve vlastní skupině dostupnosti Azure nebo v rámci Zóny dostupnosti Azure. Tím se zajistí distribuované umístění virtuálních počítačů clusteru v rámci příslušné infrastruktury Azure. Tato technologie podporuje nasazení zón dostupnosti.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Obecná sdílená složka s úložištěm datakeep jako se sdílenými disky clusteru


Obecná sdílená složka je další možnost pro dosažení sdílené složky s vysokou dostupností.

V takovém případě můžete jako sdílený disk clusteru použít řešení jiného výrobce.

## <a name="next-steps"></a>Další kroky

* [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instalace SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Nasazení Prostory úložiště s přímým přístupem souborového serveru se škálováním na více uzlů pro úložiště UPD v Azure][deploy-sofs-s2d-in-azure]
* [Prostory úložiště s přímým přístupem ve Windows Serveru 2016][s2d-in-win-2016]
* [Hluboká podrobně: svazky v Prostory úložiště s přímým přístupem][deep-dive-volumes-in-s2d]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

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

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 
