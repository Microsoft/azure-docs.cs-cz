---
title: Virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver v SLES – příručka pro více SID | Microsoft Docs
description: Průvodce vysokou dostupností s více SID pro SAP NetWeaver v SUSE Linux Enterprise Server pro aplikace SAP
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 1ba6a19b271943c7ecbe2254ef2544a5f576ad3d
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167419"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro Průvodce pro aplikace SAP s více SID

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Tento článek popisuje, jak nasadit více systémů SAP NetWeaver nebo S4HANA s vysokou dostupností (to znamená více SID) v clusteru se dvěma uzly na virtuálních počítačích Azure s SUSE Linux Enterprise Server pro aplikace SAP.  

V ukázkových konfiguracích jsou instalační příkazy atd. tři systémy SAP NetWeaver 7,50 jsou nasazeny v jednom clusteru s vysokou dostupností se dvěma uzly. Identifikátory SID systémů SAP jsou:
* **NW1**: ASCS instance číslo **00** a název virtuálního hostitele **msnw1ascs**; OLAJÍCÍCH instance číslo **02** a název virtuálního hostitele **msnw1ers**.  
* **NW2**: ASCS instance číslo **10** a virtuální název hostitele **msnw2ascs**; OLAJÍCÍCH instance číslo **12** a název virtuálního hostitele **msnw2ers**.  
* **NW3**: ASCS instance číslo **20** a virtuální název hostitele **msnw3ascs**; OLAJÍCÍCH instance číslo **22** a název virtuálního hostitele **msnw3ers**.  

Článek nepokrývá databázovou vrstvu a nasazení sdílených složek SAP NFS. V příkladech v tomto článku používáme virtuální názvy NW2-NFS pro sdílené složky NFS NW2 a NW3-NFS pro sdílené složky NFS pro NW3 NFS za předpokladu, že byl nasazený cluster systému souborů NFS.  

Než začnete, přečtěte si následující poznámky a dokumenty SAP jako první:

* Poznámka [1928533][1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553][2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro aplikace SAP Poznámka [2205917][2205917] se doporučuje nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP.
* Poznámka SAP Poznámka [1944799][1944799] obsahuje pokyny pro SAP HANA SUSE Linux Enterprise Server pro aplikace SAP.
* Pro SAP Note [2178632][2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498][2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692][2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* Poznámka SAP poznámky [1984787][1984787] obsahuje obecné informace o SUSE Linux Enterprise Server 12.
* V části SAP Note [1999351][1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Průvodce osvědčenými postupy pro SUSE SAP ha][suse-ha-guide] Příručky obsahují všechny požadované informace pro nastavení NetWeaver HA a SAP HANA místní replikace systému. Použijte tyto příručky jako obecné standardní hodnoty. Poskytují mnohem podrobnější informace.
* [Zpráva k vydání verze SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [SUSE průvodce clusterem s více SID pro SLES 12 a SLES 15](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]
## <a name="overview"></a>Přehled

Pro virtuální počítače, které jsou součástí clusteru, musí být velikost, aby bylo možné spouštět všechny prostředky, pokud dojde k převzetí služeb při selhání. Každé rozhraní SAP SID může převzít služby při selhání nezávisle na sobě v clusteru s vysokou dostupností více identifikátorů SID.  Pokud používáte SBD oplocení, můžou být zařízení SBD sdílená mezi několika clustery.  

Pro zajištění vysoké dostupnosti vyžaduje SAP NetWeaver sdílené složky systému souborů NFS s vysokou dostupností. V tomto příkladu předpokládáme, že sdílené složky SAP NFS se buď hostují na [souborovém serveru NFS](./high-availability-guide-suse-nfs.md)s vysokou dostupností, který můžou používat víc systémů SAP. Nebo jsou sdílené složky nasazené na [Azure NetApp Files SVAZCÍCH NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Podpora clusteringu s ASCS/olajících pro SAP/s SUSE Linux jako hostovaný operační systém na virtuálních počítačích Azure je omezená na **pět** identifikátorů SID SAP na stejném clusteru. Každé nové SID zvyšuje složitost. Kombinace replikačního serveru fronty SAP 1 a fronty replikačního serveru 2 ve stejném clusteru není **podporována**. Clustering s více SID popisuje instalaci více instancí SAP ASCS/OLAJÍCÍCH s různými identifikátory SID v jednom clusteru Pacemaker. Clustering s vícenásobnými identifikátory SID se aktuálně podporuje jenom pro ASCS/OLAJÍCÍCH.  

> [!TIP]
> Clustering s více identifikátory SID pro SAP ASCS/OLAJÍCÍCH je řešení s vyšší složitou složitostí. Implementaci je složitější. Zahrnuje také vyšší administrativní úsilí při provádění aktivit údržby (například opravy operačního systému). Než začnete se skutečnou implementací, věnujte přitom pečlivé plánování nasazení a všech zúčastněných komponent, jako jsou virtuální počítače, připojení NFS, VIP, konfigurace nástroje pro vyrovnávání zatížení atd.  

Server NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver OLAJÍCÍCH a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

Následující seznam obsahuje konfiguraci nástroje pro vyrovnávání zatížení (A) SCS a OLAJÍCÍCH pro tento příklad clusteru s více identifikátory SID se třemi systémy SAP. Pro každou z těchto identifikátorů SID budete potřebovat samostatné IP adresy front-endu, sondy stavu a pravidla vyrovnávání zatížení pro každou instanci ASCS a OLAJÍCÍCH. Přiřaďte všechny virtuální počítače, které jsou součástí clusteru ASCS/ASCS, do jednoho back-end fondu.  

### <a name="ascs"></a>Určitého SCS

* Konfigurace front-endu
  * IP adresa pro NW1:10.3.1.14
  * IP adresa pro NW2:10.3.1.16
  * IP adresa pro NW3:10.3.1.13
* Porty testu paměti
  * Port 620<strong> &lt; Nr &gt; </strong>, tedy pro NW1, NW2 a porty sondy NW3 620**00**, 620**10** a 620**20**
* Pravidla vyrovnávání zatížení – 
* Vytvořte jednu pro každou instanci, tj. NW1/ASCS, NW2/ASCS a NW3/ASCS.
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
  * Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 36<strong> &lt; Nr &gt; </strong> TCP
    * 39<strong> &lt; Nr &gt; </strong> TCP
    * 81<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong>. &lt; 16 TCP &gt; </strong>

### <a name="ers"></a>OLAJÍCÍCH

* Konfigurace front-endu
  * IP adresa pro NW1 10.3.1.15
  * IP adresa pro NW2 10.3.1.17
  * IP adresa pro NW3 10.3.1.19
* Port testu paměti
  * Port 621<strong> &lt; Nr &gt; </strong>, tedy pro NW1, NW2 a N # porty sondy 621**02**, 621**12** a 621**22**
* Pravidla vyrovnávání zatížení – pro každou instanci vytvořte jednu z těchto instancí: NW1/OLAJÍCÍCH, NW2/OLAJÍCÍCH a NW3/OLAJÍCÍCH.
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
  * Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong>. &lt; 16 TCP &gt; </strong>

* Konfigurace back-endu
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A) SCS/OLAJÍCÍCH

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-nfs-shares"></a>Sdílené složky SAP NFS

SAP NetWeaver vyžaduje sdílené úložiště pro přenos, adresář profilů a tak dále. Pro vysoce dostupný systém SAP je důležité mít vysoce dostupné sdílené složky NFS. Budete se muset rozhodnout pro architekturu pro sdílené složky SAP NFS. Jednou z možností je vytvořit [vysoce dostupný cluster systému souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server][nfs-ha], který se dá sdílet mezi několika systémy SAP. 

Další možností je nasadit sdílené složky na [Azure NetApp Files SVAZCÍCH NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Díky Azure NetApp Files získáte integrovanou vysokou dostupnost pro sdílené složky SAP NFS.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Nasazení prvního systému SAP v clusteru

Teď, když jste se rozhodli o architekturu pro sdílené složky SAP NFS, nasaďte v clusteru první systém SAP, a to podle příslušné dokumentace.

* Pokud používáte server systému souborů NFS s vysokou [dostupností, postupujte podle vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md).  
* Pokud používáte Azure NetApp Files svazky NFS, dodržujte [vysokou dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) .

Výše uvedené dokumenty vás provede kroky pro přípravu potřebných infrastruktur, sestavení clusteru a příprava operačního systému na spuštění aplikace SAP.  

> [!TIP]
> Před přidáním dalších identifikátorů zabezpečení SAP do clusteru vždy testujte funkce převzetí služeb při selhání clusteru po nasazení prvního systému. Tímto způsobem zjistíte, že fungování clusteru funguje ještě před tím, než do clusteru přidáte složitost dalších systémů SAP.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Nasazení dalších systémů SAP v clusteru

V tomto příkladu předpokládáme, že systém **NW1** už je v clusteru nasazený. Ukážeme, jak nasadit v clusterech SAP Systems **NW2** a **NW3**. 

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

### <a name="prerequisites"></a>Předpoklady 

> [!IMPORTANT]
> Než budete postupovat podle pokynů k nasazení dalších systémů SAP v clusteru, postupujte podle pokynů pro nasazení prvního systému SAP v clusteru, protože existují kroky, které jsou nezbytné pouze při prvním nasazení systému.  

Tato dokumentace předpokládá, že:
* Cluster Pacemaker je už nakonfigurovaný a spuštěný.  
* Nejméně jeden systém SAP (instance ASCS/OLAJÍCÍCH) je již nasazen a spuštěn v clusteru.  
* Byla testována funkce převzetí služeb při selhání clusteru.  
* Sdílené složky NFS pro všechny systémy SAP jsou nasazené.  

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci SAP NetWeaver

1. Přidejte konfiguraci pro nově nasazený systém (to znamená, **NW2**, **NW3**) do existujícího Azure Load Balancer podle pokynů k [nasazení Azure Load Balancer ručně prostřednictvím Azure Portal](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal). Upravte IP adresy, porty sondy stavu, pravidla vyrovnávání zatížení pro vaši konfiguraci.  

2. **[A]** nastavte překlad názvů pro další systémy SAP. Můžete buď použít server DNS, nebo upravit `/etc/hosts` na všech uzlech. Tento příklad ukazuje, jak použít `/etc/hosts` soubor.  Přizpůsobte IP adresy a názvy hostitelů vašemu prostředí. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** vytvořte sdílené adresáře pro další systémy SAP **NW2** a **NW3** , které nasazujete do clusteru. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** nakonfigurujte `autofs` pro připojení systémů souborů/sapmnt/SID a/usr/SAP/SID/sys pro další systémy SAP, které nasazujete do clusteru. V tomto příkladu **NW2** a **NW3**.  

   Aktualizujte soubor `/etc/auto.direct` pomocí systémů souborů pro další systémy SAP, které nasazujete do clusteru.  

   * Pokud používáte souborový server NFS, postupujte [podle pokynů.](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation)
   * Pokud používáte Azure NetApp Files, postupujte podle pokynů uvedených [tady](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) . 

   Aby bylo možné `autofs` nově přidané sdílené složky připojit, budete muset službu restartovat.  

### <a name="install-ascs--ers"></a>Nainstalovat ASCS/OLAJÍCÍCH

1. Vytvořte prostředky clusteru virtuální IP a stavové sondy pro instanci ASCS dalšího systému SAP, který nasazujete do clusteru. Příklad zobrazený tady je pro **NW2** a **NW3** ASCS pomocí serveru NFS s vysokou dostupností.  

   > [!IMPORTANT]
   > Nedávné testování odhalilo situace, kde NetCat přestane reagovat na požadavky z důvodu nevyřízených položek a omezení zpracování pouze jednoho připojení. Prostředek NetCat přestane naslouchat požadavkům nástroje pro vyrovnávání zatížení Azure a plovoucí IP adresa přestane být k dispozici.  
   > Pro existující clustery Pacemaker doporučujeme v minulosti nahradit NetCat pomocí Socat. V současné době doporučujeme použít agenta prostředků Azure-, který je součástí prostředků balíčku – agenti s následujícími požadavky na verzi balíčku:
   > - Pro SLES 12 SP4/SP5 musí být ve verzi aspoň Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 musí být verze aspoň Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Všimněte si, že tato změna bude vyžadovat krátké výpadky.  
   > U existujících clusterů Pacemaker se v případě, že konfigurace již změnila tak, aby používala socat, jak je popsáno v tématu [posílení zabezpečení azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), neexistuje žádný požadavek na přepnutí přímo do agenta prostředků Azure-No.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Při vytváření prostředků, které můžou být přiřazené k různým prostředkům clusteru. Když je rozřadíte do skupin, migrují se na jeden z uzlů clusteru. Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky. Není důležité, na kterém uzlu jsou prostředky spuštěné.

2. **[1]** instalace SAP NetWeaver ASCS  

   Nainstalujte SAP NetWeaver ASCS jako kořenový adresář pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS. Například pro System **NW2**je virtuální název hostitele <b>msnw2ascs</b>, <b>10.3.1.16</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>10</b>. pro System **NW3**je virtuální název hostitele <b>msnw3ascs</b>, <b>10.3.1.13</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>20</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst. Pomocí parametru SAPINST_USE_HOSTNAME můžete nainstalovat SAP s použitím názvu virtuálního hostitele.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**SID**/ASCS**instance #**, zkuste nastavit vlastníka na **hodnotu ADM a skupinu na sapsys****instance** ASCS a zkuste to znovu.

3. **[1]** vytvořte prostředky clusteru virtuální IP adresy a stavu testu pro instanci olajících dalšího systému SAP, který nasazujete do clusteru. Příklad zobrazený tady je pro **NW2** a **NW3** olajících pomocí serveru NFS s vysokou dostupností. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Při vytváření prostředků, které můžou být přiřazené k různým uzlům clusteru. Když je rozřadíte do skupin, migrují se na jeden z uzlů clusteru. Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky.  

   V dalším kroku se ujistěte, že jsou na uzlu clusteru spuštěné prostředky nově vytvořené skupiny OLAJÍCÍCH, a to proti uzlu clusteru, ve kterém je nainstalovaná instance ASCS pro stejný systém SAP.  Pokud jste například nainstalovali NW2 ASCS, ujistěte se `slesmsscl1` , že je skupina NW2 olajících spuštěná `slesmsscl2` .  Skupinu NW2 OLAJÍCÍCH můžete migrovat tak, `slesmsscl2` že spustíte následující příkaz: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** instalace SAP NetWeaver olajících

   Nainstalujte SAP NetWeaver OLAJÍCÍCH jako root na druhém uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro OLAJÍCÍCH. Například pro System **NW2**bude název virtuálního hostitele <b>msnw2ers</b>, <b>10.3.1.17</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>12</b>. Pro System **NW3**se jedná o název virtuálního hostitele <b>msnw3ers</b>, <b>10.3.1.19</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>22</b>. 

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst. Pomocí parametru SAPINST_USE_HOSTNAME můžete nainstalovat SAP s použitím názvu virtuálního hostitele.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastaví správně oprávnění a instalace se nezdaří.

   Pokud se při instalaci nepovede vytvořit podsložku ve složce/usr/SAP/**NW2**/ERS**instance #**, zkuste nastavit vlastníka na hodnotu ADM **SID**a skupinu na sapsys ze složky olajících**instance** a zkuste to znovu.

   Pokud bylo nutné migrovat skupinu OLAJÍCÍCH nově nasazeného systému SAP na jiný uzel clusteru, nezapomeňte odebrat omezení umístění pro skupinu OLAJÍCÍCH. Omezení můžete odebrat spuštěním následujícího příkazu (příklad je uveden pro SAP Systems **NW2** a **NW3**).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** Přizpůsobte profily instancí ASCS/SCS a olajících pro nově nainstalované systémy SAP. Níže zobrazený příklad je určen pro NW2. Pro všechny instance SAP přidané do clusteru budete muset upravit profily ASCS/SCS a OLAJÍCÍCH.  
 
 * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Pro ENSA1 i ENSA2 se ujistěte, že `keepalive` jsou nastavené parametry operačního systému, jak je popsáno v tématu SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

 * Profil OLAJÍCÍCH

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** NAKONFIGURUJTE uživatele SAP pro nově nasazený systém SAP, v tomto příkladu **NW2** a **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Přidejte služby SAP ASCS a OLAJÍCÍCH pro nově instalovaný systém SAP do `sapservice` souboru. Níže zobrazený příklad slouží pro systémy SAP **NW2** a **NW3**.  

   Přidejte položku služby ASCS do druhého uzlu a zkopírujte položku služby OLAJÍCÍCH do prvního uzlu. Spusťte příkazy pro každý systém SAP na uzlu, kde byla nainstalována instance ASCS pro systém SAP.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** vytvořte prostředky clusteru SAP pro nově instalovaný systém SAP. 

   Pokud používáte architekturu serveru front Server 1 (ENSA1), definujte prostředky pro systémy SAP **NW2** a **NW3** následujícím způsobem:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP představilo podporu pro front-Server 2, včetně replikace, od SAP NW 7,52. Počínaje platformou ABAP 1809 se ve výchozím nastavení nainstaluje služba fronty serveru 2. Podporu služby zařazení serveru 2 pro frontu najdete v tématu SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Pokud používáte architekturu serveru fronty 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definujte prostředky pro systémy SAP **NW2** a **NW3** následujícím způsobem:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Pokud provádíte upgrade ze starší verze a přejdete na server fronty 2, přečtěte si článek SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.
   Následující příklad ukazuje stav prostředků clusteru, poté, co byly do clusteru přidány systémy SAP **NW2** a **NW3** . 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   Následující obrázek ukazuje, jak by prostředky vypadaly jako ve webovém Konsoleu HA (Hawk) s rozšířenými prostředky pro SAP System **NW2** Expanded.  

   [![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Pokračování v instalaci SAP 

Dokončete instalaci SAP pomocí:

* [Příprava aplikačních serverů SAP NetWeaver](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalace instance systému DBMS](./high-availability-guide-suse.md#install-database)
* [Instalace primárního aplikačního serveru SAP](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* Instalace jedné nebo více dalších instancí aplikace SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testování nastavení clusteru s více SID

Následující testy jsou podmnožinou testovacích případů v průvodcích osvědčenými postupy SUSE. Jsou zahrnuty pro usnadnění práce. Úplný seznam testů clusteru najdete v následující dokumentaci:

* Pokud používáte server systému souborů NFS s vysokou [dostupností, postupujte podle vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md).  
* Pokud používáte Azure NetApp Files svazky NFS, dodržujte [vysokou dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) .

Vždy si přečtěte Příručky k osvědčeným postupům SUSE a proveďte všechny další testy, které mohou být přidány.  
Zobrazené testy jsou ve dvou uzlech, cluster s více identifikátory SID se třemi nainstalovanými systémy SAP.  

1. Test HAGetFailoverConfig a HACheckFailoverConfig

   <sapsid>Na uzlu, kde je aktuálně spuštěná instance ASCS, spusťte následující příkazy jako ADM. Pokud se příkazy nezdaří s CHYBou: nedostatek paměti, může to být způsobeno pomlčkami ve vašem názvu hostitele. Jedná se o známý problém, který bude opraven nástrojem SUSE v balíčku SAP-SUSE-cluster-Connector.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Ručně migrujte instanci ASCS. Tento příklad ukazuje migraci instance ASCS pro SAP System NW2.  
   Stav prostředku před spuštěním testu:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Spusťte následující příkazy jako kořen pro migraci instance NW2 ASCS.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Stav prostředku po testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. HAFailoverToNode testu. Zde uvedený test ukazuje migraci instance ASCS pro SAP System NW2.  

   Stav prostředku před spuštěním testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Spusťte následující příkazy jako **NW2**ADM pro migraci instance NW2 ASCS.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Stav prostředku po testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Simulace havárie uzlu

   Stav prostředku před spuštěním testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Spusťte následující příkaz jako kořen v uzlu, ve kterém je spuštěná aspoň jedna instance ASCS. V tomto příkladu jsme provedli příkaz na `slesmsscl2` , kde jsou spuštěné instance ASCS pro NW1 a NW3.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Pokud používáte SBD, Pacemaker by se neměl automaticky spustit na ukončeném uzlu. Stav po spuštění uzlu by měl vypadat takto.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Pomocí následujících příkazů spusťte Pacemaker na ukončeném uzlu, vyčistěte zprávy SBD a vyčistěte prostředky, které selhaly.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Stav prostředku po testu:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
