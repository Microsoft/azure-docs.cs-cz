---
title: Vysoká dostupnost virtuálních počítačů Azure pro SAP NW v RHEL – příručka pro více SID | Microsoft Docs
description: Navažte vysokou dostupnost pro SAP NW na virtuálních počítačích Azure (virtuální počítače) RHEL s více SID.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: ec2121754a24a44288c158e630a4e84219c744e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676911"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v Red Hat Enterprise Linux pro Průvodce pro aplikace SAP s více SID

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Tento článek popisuje, jak nasadit více systémů SAP NetWeaver s vysokou dostupností (tj. více SID) v clusteru se dvěma uzly na virtuálních počítačích Azure s Red Hat Enterprise Linux pro aplikace SAP.  

V ukázkových konfiguracích jsou instalační příkazy atd. tři systémy SAP NetWeaver 7,50 jsou nasazeny v jednom clusteru s vysokou dostupností se dvěma uzly. Identifikátory SID systémů SAP jsou:
* **NW1**: ASCS instance číslo **00** a název virtuálního hostitele **msnw1ascs**; OLAJÍCÍCH instance číslo **02** a název virtuálního hostitele **msnw1ers**.  
* **NW2**: ASCS instance číslo **10** a virtuální název hostitele **msnw2ascs**; OLAJÍCÍCH instance číslo **12** a název virtuálního hostitele **msnw2ers**.  
* **NW3**: ASCS instance číslo **20** a virtuální název hostitele **msnw3ascs**; OLAJÍCÍCH instance číslo **22** a název virtuálního hostitele **msnw3ers**.  

Článek nepokrývá databázovou vrstvu a nasazení sdílených složek SAP NFS. V příkladech v tomto článku používáme [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  volume **sapMSID** pro sdílené složky NFS za předpokladu, že je svazek už nasazený. Předpokládáme, že Azure NetApp Files svazek je nasazený pomocí protokolu NFSv3 a že pro prostředky clusteru pro instance ASCS a OLAJÍCÍCH pro instance SAP Systems NW1, NW2 a NW3 existují následující cesty k tomuto souboru:  

* Volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ASCS)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>olajících)
* Volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ASCS)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>olajících)
* Volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ASCS)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* Volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>olajících)

Než začnete, přečtěte si následující poznámky a dokumenty SAP jako první:

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* [Dokumentace k Azure NetApp Files][anf-azure-doc]
* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Poznámka SAP Poznámka [2002167] obsahuje doporučená nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP Poznámka [2009879] obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [SAP NetWeaver v clusteru Pacemaker](https://access.redhat.com/articles/3150081)
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/OLAJÍCÍCH pro SAP NetWeaver se samostatnými prostředky v RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Konfigurace SAP S/4HANA ASCS/OLAJÍCÍCH se samostatným serverem fronty 2 (ENSA2) v Pacemaker v RHEL](https://access.redhat.com/articles/3974941)
* Dokumentace k RHEL specifické pro Azure:
  * [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Pro virtuální počítače, které jsou součástí clusteru, musí být velikost, aby bylo možné spouštět všechny prostředky, pokud dojde k převzetí služeb při selhání. Každé rozhraní SAP SID může převzít služby při selhání nezávisle na sobě v clusteru s vysokou dostupností více identifikátorů SID.  

Pro zajištění vysoké dostupnosti vyžaduje SAP NetWeaver sdílené složky s vysokou dostupností. V této dokumentaci uvádíme příklady u sdílených složek SAP nasazených na [Azure NetApp Files SVAZCÍCH NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Sdílené složky je taky možné hostovat na [clusteru GlusterFS](./high-availability-guide-rhel-glusterfs.md)s vysokou dostupností, který můžou používat víc systémů SAP.  

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Podpora clusteringu olajících pro SAP ASCS/s Red Hat Linux jako hostovaného operačního systému na virtuálních počítačích Azure je omezená na **pět** identifikátorů SID SAP na stejném clusteru. Každé nové SID zvyšuje složitost. Kombinace replikačního serveru fronty SAP 1 a fronty replikačního serveru 2 ve stejném clusteru není **podporována**. Clustering s více SID popisuje instalaci více instancí SAP ASCS/OLAJÍCÍCH s různými identifikátory SID v jednom clusteru Pacemaker. Clustering s vícenásobnými identifikátory SID se aktuálně podporuje jenom pro ASCS/OLAJÍCÍCH.  

> [!TIP]
> Clustering s více identifikátory SID pro SAP ASCS/OLAJÍCÍCH je řešení s vyšší složitou složitostí. Implementaci je složitější. Zahrnuje také vyšší administrativní úsilí při provádění aktivit údržby (například opravy operačního systému). Než začnete se skutečnou implementací, věnujte přitom pečlivé plánování nasazení a všech zúčastněných komponent, jako jsou virtuální počítače, připojení NFS, VIP, konfigurace nástroje pro vyrovnávání zatížení atd.  

SAP NetWeaver ASCS, SAP NetWeaver SCS a SAP NetWeaver OLAJÍCÍCH používají virtuální název hostitele a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md).  

Následující seznam obsahuje konfiguraci nástroje pro vyrovnávání zatížení (A) SCS a OLAJÍCÍCH pro tento příklad clusteru s více identifikátory SID se třemi systémy SAP. Pro každou z těchto identifikátorů SID budete potřebovat samostatné IP adresy front-endu, sondy stavu a pravidla vyrovnávání zatížení pro každou instanci ASCS a OLAJÍCÍCH. Přiřaďte všechny virtuální počítače, které jsou součástí clusteru ASCS/ASCS, do jednoho back-endového fondu s jedním interního nástroje.  

### <a name="ascs"></a>Určitého SCS

* Konfigurace front-endu
  * IP adresa pro NW1:10.3.1.50
  * IP adresa pro NW2:10.3.1.52
  * IP adresa pro NW3:10.3.1.54

* Porty testu paměti
  * Port 620 <strong> &lt; Nr &gt;</strong>, tedy pro NW1, NW2 a porty sondy NW3 620 **00**, 620 **10** a 620 **20**
* Pravidla vyrovnávání zatížení – pro každou instanci vytvořte jednu z těchto instancí: NW1/ASCS, NW2/ASCS a NW3/ASCS.
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
  * IP adresa pro NW1 10.3.1.51
  * IP adresa pro NW2 10.3.1.53
  * IP adresa pro NW3 10.3.1.55

* Port testu paměti
  * Port 621 <strong> &lt; Nr &gt;</strong>, tedy pro porty sondy NW1, NW2 a N3 621 **02**, 621 **12** a 621 **22**
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
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="sap-shares"></a>Sdílené složky SAP

SAP NetWeaver vyžaduje sdílené úložiště pro přenos, adresář profilů a tak dále. Pro vysoce dostupný systém SAP je důležité mít vysoce dostupné sdílené složky. Budete se muset rozhodnout pro architekturu pro sdílené složky SAP. Jednou z možností je nasadit sdílené složky na [Azure NetApp Files SVAZCÍCH NFS](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Díky Azure NetApp Files získáte integrovanou vysokou dostupnost pro sdílené složky SAP NFS.

Další možností je sestavit [GlusterFS na virtuálních počítačích Azure v Red Hat Enterprise Linux pro SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md), které se dají sdílet mezi několika systémy SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Nasazení prvního systému SAP v clusteru

Teď, když jste se rozhodli o architekturu pro sdílené složky SAP, nasaďte první systém SAP v clusteru, a to podle příslušné dokumentace.

* Pokud používáte Azure NetApp Files svazky NFS, postupujte podle [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md) .  
* Pokud používáte cluster GlusterFS, Sledujte [GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md).  

Výše uvedené dokumenty vás provede kroky pro přípravu potřebné infrastruktury, sestavení clusteru a příprava operačního systému na spuštění aplikace SAP.  

> [!TIP]
> Před přidáním dalších identifikátorů zabezpečení SAP do clusteru vždy testujte funkce převzetí služeb při selhání clusteru po nasazení prvního systému. Tímto způsobem zjistíte, že fungování clusteru funguje ještě před tím, než do clusteru přidáte složitost dalších systémů SAP.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Nasazení dalších systémů SAP v clusteru

V tomto příkladu předpokládáme, že systém **NW1** už je v clusteru nasazený. Ukážeme, jak nasadit v clusterech SAP Systems **NW2** a **NW3**. 

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

### <a name="prerequisites"></a>Požadavky 

> [!IMPORTANT]
> Než budete postupovat podle pokynů k nasazení dalších systémů SAP v clusteru, postupujte podle pokynů pro nasazení prvního systému SAP v clusteru, protože existují kroky, které jsou nezbytné pouze při prvním nasazení systému.  

Tato dokumentace předpokládá, že:
* Cluster Pacemaker je už nakonfigurovaný a spuštěný.  
* Nejméně jeden systém SAP (instance ASCS/OLAJÍCÍCH) je již nasazen a spuštěn v clusteru.  
* Byla testována funkce převzetí služeb při selhání clusteru.  
* Sdílené složky NFS pro všechny systémy SAP jsou nasazené.  

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci SAP NetWeaver

1. Přidejte konfiguraci pro nově nasazený systém (to znamená, **NW2**, **NW3**) do existujícího Azure Load Balancer podle pokynů k [nasazení Azure Load Balancer ručně prostřednictvím Azure Portal](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Upravte IP adresy, porty sondy stavu, pravidla vyrovnávání zatížení pro vaši konfiguraci.  

2. **[A]** nastavení rozlišení názvů pro další systémy SAP. Můžete buď použít server DNS, nebo upravit `/etc/hosts` na všech uzlech. Tento příklad ukazuje, jak použít `/etc/hosts` soubor.  Přizpůsobte IP adresy a názvy hostitelů vašemu prostředí. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
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

4. **[A]** přidejte položky připojení pro systémy souborů/Sapmnt/SID a/usr/SAP/SID/sys pro další systémy SAP, které nasazujete do clusteru. V tomto příkladu **NW2** a **NW3**.  

   Aktualizujte soubor `/etc/fstab` pomocí systémů souborů pro další systémy SAP, které nasazujete do clusteru.  

   * Pokud používáte Azure NetApp Files, postupujte podle pokynů uvedených [tady](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation) .  
   * Pokud používáte cluster GlusterFS, postupujte podle pokynů uvedených [tady](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation) .  

### <a name="install-ascs--ers"></a>Nainstalovat ASCS/OLAJÍCÍCH

1. Vytvořte prostředky clusteru virtuální IP a stavové sondy pro instance ASCS dalších systémů SAP, které nasazujete do clusteru. Níže zobrazený příklad je určen pro **NW2** a **NW3** ASCS pomocí systému souborů NFS na Azure NetApp Files svazcích s protokolem NFSv3.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky. Není důležité, na kterém uzlu jsou prostředky spuštěné.  

2. **[1]** instalace SAP NetWeaver ASCS  

   Nainstalujte SAP NetWeaver ASCS jako kořenový adresář pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS. Například pro System **NW2** je virtuální název hostitele <b>msnw2ascs</b>, <b>10.3.1.52</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>10</b>. Pro System **NW3** je virtuální název hostitele <b>msnw3ascs</b>, <b>10.3.1.54</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>20</b>. Poznamenejte si, který uzel clusteru jste nainstalovali ASCS pro každé SAP SID.  

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst. Pomocí parametru SAPINST_USE_HOSTNAME můžete nainstalovat SAP s použitím názvu virtuálního hostitele.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**SID**/ASCS **instance #**, zkuste nastavit vlastníka na **hodnotu ADM a skupinu na sapsys****instance** ASCS a zkuste to znovu.

3. **[1]** vytvořte prostředky clusteru virtuální IP adresy a stavu testu pro instanci olajících dalšího systému SAP, který nasazujete do clusteru. Níže zobrazený příklad je určen pro **NW2** a **NW3** olajících pomocí systému souborů NFS na Azure NetApp Files svazcích s protokolem NFSv3.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky.  

   V dalším kroku se ujistěte, že jsou na uzlu clusteru spuštěné prostředky nově vytvořené skupiny OLAJÍCÍCH, a to proti uzlu clusteru, ve kterém je nainstalovaná instance ASCS pro stejný systém SAP.  Pokud jste například nainstalovali NW2 ASCS, ujistěte se `rhelmsscl1` , že je skupina NW2 olajících spuštěná `rhelmsscl2` .  Skupinu NW2 OLAJÍCÍCH můžete migrovat tak, že `rhelmsscl2` spustíte následující příkaz pro jeden z prostředků clusteru ve skupině: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** instalace SAP NetWeaver olajících

   Nainstalujte SAP NetWeaver OLAJÍCÍCH jako root na druhém uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro OLAJÍCÍCH. Například pro System **NW2** bude název virtuálního hostitele <b>msnw2ers</b>, <b>10.3.1.53</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>12</b>. Pro System **NW3** se jedná o název virtuálního hostitele <b>msnw3ers</b>, <b>10.3.1.55</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>22</b>. 

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst. Pomocí parametru SAPINST_USE_HOSTNAME můžete nainstalovat SAP s použitím názvu virtuálního hostitele.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastaví správně oprávnění a instalace se nezdaří.

   Pokud se při instalaci nepovede vytvořit podsložku ve složce/usr/SAP/**NW2**/ERS **instance #**, zkuste nastavit vlastníka na hodnotu ADM **SID** a skupinu na sapsys ze složky olajících **instance** a zkuste to znovu.

   Pokud bylo nutné migrovat skupinu OLAJÍCÍCH nově nasazeného systému SAP na jiný uzel clusteru, nezapomeňte odebrat omezení umístění pro skupinu OLAJÍCÍCH. Omezení můžete odebrat spuštěním následujícího příkazu (příklad je uveden pro SAP Systems **NW2** a **NW3**). Nezapomeňte odebrat dočasná omezení pro stejný prostředek, který jste použili v příkazu k přesunutí skupiny clusteru OLAJÍCÍCH.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Přizpůsobte profily instancí ASCS/SCS a olajících pro nově nainstalované systémy SAP. Níže zobrazený příklad je určen pro NW2. Pro všechny instance SAP přidané do clusteru budete muset upravit profily ASCS/SCS a OLAJÍCÍCH.  
 
   * Profil ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

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
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** aktualizace souboru/usr/SAP/sapservices

   Aby se zabránilo spuštění instancí pomocí spouštěcího skriptu sapinit, musí být všechny instance spravované pomocí Pacemaker odkomentovat ze `/usr/sap/sapservices` souboru.  Níže zobrazený příklad slouží pro systémy SAP **NW2** a **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** vytvořte prostředky clusteru SAP pro nově instalovaný systém SAP.  

   Pokud používáte architekturu serveru front Server 1 (ENSA1), definujte prostředky pro systémy SAP **NW2** a **NW3** následujícím způsobem:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP představilo podporu pro front-Server 2, včetně replikace, od SAP NW 7,52. Počínaje platformou ABAP 1809 se ve výchozím nastavení nainstaluje služba fronty serveru 2. Podporu služby zařazení serveru 2 pro frontu najdete v tématu SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Pokud používáte architekturu serveru fronty 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definujte prostředky pro systémy SAP **NW2** a **NW3** následujícím způsobem:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Pokud provádíte upgrade ze starší verze a přejdete na server fronty 2, přečtěte si článek SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Časové limity ve výše uvedené konfiguraci jsou jenom příklady a možná je budete muset přizpůsobit konkrétnímu nastavení SAP. 

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.
   Následující příklad ukazuje stav prostředků clusteru, poté, co byly do clusteru přidány systémy SAP **NW2** a **NW3** . 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** přidejte do obou uzlů pravidla brány firewall pro ASCS a olajících.  Níže uvedený příklad ukazuje pravidla brány firewall pro SAP Systems **NW2** a **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3212/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3212/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3222/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3222/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Pokračování v instalaci SAP 

Dokončete instalaci SAP pomocí:

* [Příprava aplikačních serverů SAP NetWeaver](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalace instance systému DBMS](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Instalace primárního aplikačního serveru SAP](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Instalace jedné nebo více dalších instancí aplikace SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Testování nastavení clusteru s více SID

Následující testy jsou podmnožinou testovacích případů v průvodcích osvědčených postupů Red Hat. Jsou zahrnuty pro usnadnění práce. Úplný seznam testů clusteru najdete v následující dokumentaci:

* Pokud používáte Azure NetApp Files svazky NFS, postupujte podle [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v RHEL s Azure NetApp Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md) .
* Pokud používáte vysoce dostupný `GlusterFS` , postupujte podle [vysoké dostupnosti virtuálních počítačů Azure pro SAP NETWEAVER v RHEL pro aplikace SAP](./high-availability-guide-rhel.md).  

Vždy si přečtěte Příručky k osvědčeným postupům Red Hat a proveďte všechny další testy, které by mohly být přidané.  
Zobrazené testy jsou ve dvou uzlech, cluster s více identifikátory SID se třemi nainstalovanými systémy SAP.  

1. Ručně migrujte instanci ASCS. Tento příklad ukazuje migraci instance ASCS pro SAP System NW3.

   Stav prostředku před spuštěním testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Spusťte následující příkazy jako kořen pro migraci instance NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Stav prostředku po testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Simulace havárie uzlu

   Stav prostředku před spuštěním testu:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Spusťte následující příkaz jako kořenový adresář v uzlu, kde je spuštěná alespoň jedna instance ASCS. V tomto příkladu jsme provedli příkaz na `rhelmsscl1` , kde jsou spuštěné instance ASCS pro NW1, NW2 a NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Stav po testu a za uzlem, u kterého došlo k chybě, by měl vypadat nějak takto.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Pokud existují zprávy o neúspěšných prostředcích, vyčistěte stav neúspěšných prostředků. Například:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .