---
title: Vysoká dostupnost virtuálních počítačích Azure pro SAP NW v průvodci více SID RHEL | Dokumenty společnosti Microsoft
description: Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/24/2020
ms.author: radeltch
ms.openlocfilehash: 4f1bfd58e27f0cd677980ff9351d32d91a68e3e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247431"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP aplikace multi-SID průvodce

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

Tento článek popisuje, jak nasadit více SAP NetWeaver vysoce dostupné systémy (to znamená multi-SID) ve dvouuzlů clusteru na virtuálních počítačích Azure s Red Hat Enterprise Linux pro aplikace SAP.  

V ukázkových konfiguracích, příkazech instalace atd. SID systémů SAP jsou:
* **NW1**: Číslo instance ASCS **00** a název virtuálního hostitele **msnw1ascs**; ERS instance číslo **02** a název virtuálního hostitele **msnw1ers**.  
* **NW2**: Instance ASCS číslo **10** a virtuální název hostitele **msnw2ascs**; ERS instance číslo **12** a název virtuálního hostitele **msnw2ers**.  
* **NW3**: Instance ASCS číslo **20** a virtuální název hostitele **msnw3ascs**; ERS instance číslo **22** a název virtuálního hostitele **msnw3ers**.  

Článek nezahrnuje databázovou vrstvu a nasazení sdílených složek SAP NFS. V příkladech v tomto článku používáme **svazek sapMSID** [svazku Soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes) pro sdílené složky systému souborů NFS za předpokladu, že svazek je již nasazen. Jsme také za předpokladu, že svazek Soubory Azure NetApp je nasazen s protokolem NFSv3 a že existují následující cesty k souborům pro prostředky clusteru pro instance ASCS a ERS systémů SAP NW1, NW2 a NW3:  

* hlasitost sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* hlasitost sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* hlasitost sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* hlasitost sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* hlasitost sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* objem sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Než začnete, podívejte se nejprve na následující poznámky a dokumenty SAP:

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* [Dokumentace ke službě Azure NetApp Files][anf-azure-doc]
* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [SAP Netweaver v clusteru kardiostimulátorů](https://access.redhat.com/articles/3150081)
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/ERS pro SAP Netweaver se samostatnými prostředky v RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurace SAP S/4HANA ASCS/ERS pomocí samostatného enqueue serveru 2 (ENSA2) v kardiostimulátoru na RHEL](https://access.redhat.com/articles/3974941)
* Dokumentace RHEL specifické pro Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – virtuální počítače Microsoft Azure jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace vysoce dostupnosti red hatového linuxového linuxu 7.4 (a novějšího) v Microsoft Azure](https://access.redhat.com/articles/3252491)
* [Aplikace NetApp SAP v Microsoft Azure pomocí souborů Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Virtuální počítače, které se účastní v clusteru musí být dimenzována, aby bylo možné spustit všechny prostředky, v případě převzetí služeb při selhání dojde. Každý SAP SID může převzetí služeb při selhání nezávisle na sobě v clusteru s vysokou dostupností více SID.  

Pro dosažení vysoké dostupnosti vyžaduje SAP NetWeaver vysoce dostupné sdílené složky. V této dokumentaci uvádíme příklady sdílených složek SAP nasazených na [svazcích souborů NFS Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes). Je také možné hostovat akcie na vysoce dostupném [clusteru GlusterFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), který může být použit více systémy SAP.  

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> Podpora clusteringu SAP ASCS/ERS s Red Hat Linuxem jako hostem v azure virtuálních počítačích je omezená na **pět** SAP SID ve stejném clusteru. Každý nový SID zvyšuje složitost. Není **podporována**kombinace serveru SAP Enqueue Replication Server 1 a Enqueue Replication Server 2 ve stejném clusteru . Clustering s více SID popisuje instalaci více instancí SAP ASCS/ERS s různými SID v jednom clusteru Pacemaker. V současné době je clustering s více SID podporován pouze pro ASCS/ERS.  

> [!TIP]
> Clustering sap ASCS/ERS s více SID je řešení s vyšší složitostí. Je složitější implementovat. Zahrnuje také vyšší administrativní úsilí při provádění činností údržby (jako je oprava operačního systému). Než začnete s vlastní implementací, věnujte čas pečlivému plánování nasazení a všech zapojených součástí, jako jsou virtuální počítače, připojení systému NFS, virtuální ip adresy, konfigurace nástroje pro vyrovnávání zatížení a tak dále.  

SAP NetWeaver ASCS, SAP NetWeaver SCS a SAP NetWeaver ERS používají virtuální název hostitele a virtuální IP adresy. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. Doporučujeme používat [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal).  

V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení (A)SCS a ERS pro tento příklad clusteru s více SID se třemi systémy SAP. Budete potřebovat samostatné frontendOVÉ IP adresy, sondy stavu a pravidla vyrovnávání zatížení pro každou instanci ASCS a ERS pro každou z sidů. Přiřaďte všechny virtuální počítače, které jsou součástí clusteru ASCS/ASCS, do jednoho back-endového fondu jednoho ILB.  

### <a name="ascs"></a>(A) Scs

* Front-endová konfigurace
  * IP adresa pro NW1: 10.3.1.50
  * IP adresa pro NW2: 10.3.1.52
  * IP adresa pro NW3: 10.3.1.54

* Porty sondy
  * Port 620<strong>&lt;&gt;nr</strong>, tedy pro sondové porty NW1, NW2 a NW3 620**00**, 620**10** a 620**20**
* Pravidla vyrovnávání zatížení - vytvořte jednu pro každou instanci, tedy NW1/ASCS, NW2/ASCS a NW3/ASCS.
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * Pokud používáte základní vytápěč zatížení, vytvořte pravidla vyrovnávání zatížení pro následující porty
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Front-endová konfigurace
  * IP adresa pro NW1 10.3.1.51
  * IP adresa pro NW2 10.3.1.53
  * IP adresa pro NW3 10.3.1.55

* Port sondy
  * Port 621<strong>&lt;&gt;nr</strong>, tedy pro porty sondy NW1, NW2 a N3 621**02**, 621**12** a 621**22**
* Pravidla vyrovnávání zatížení - vytvořte jednu pro každou instanci, tedy NW1/ERS, NW2/ERS a NW3/ERS.
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * Pokud používáte základní vytápěč zatížení, vytvořte pravidla vyrovnávání zatížení pro následující porty
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Back-endová konfigurace
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A)SCS/ERS

> [!Note]
> Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů. Podrobnosti o tom, jak dosáhnout odchozí připojení viz [veřejné připojení koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích sap vysoké dostupnosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Nástroj pro vyrovnávání zatížení Azure. Povolení časových razítek TCP způsobí selhání sond y stavu. Nastavte parametr **net.ipv4.tcp_timestamps** na **0**. Podrobnosti viz [Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

## <a name="sap-shares"></a>Akcie SAP

SAP NetWeaver vyžaduje sdílené úložiště pro přenos, profil adresáře a tak dále. Pro vysoce dostupný systém SAP je důležité mít vysoce dostupné sdílené složky. Budete se muset rozhodnout o architektuře pro vaše akcie SAP. Jednou z možností je nasazení sdílených složek na [svazcích souborů NFS Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Se soubory Azure NetApp získáte integrovanou vysokou dostupnost pro sdílené složky SAP NFS.

Další možností je sestavení [GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs), které lze sdílet mezi více systémy SAP. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Nasazení prvního systému SAP v clusteru

Nyní, když jste se rozhodli pro architekturu pro sdílené složky SAP, nasaďte první systém SAP v clusteru podle odpovídající dokumentace.

* Pokud používáte svazky souborů NFS Azure NetApp, postupujte podle [vysoké dostupnosti virtuálních měn Azure pro SAP NetWeaver na Red Hat Enterprise Linux u souborů Azure NetApp pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* Pokud používáte cluster GlusterFS, postupujte podle [GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs).  

Výše uvedené dokumenty vás provedou kroky k přípravě potřebné infrastruktury, sestavení clusteru, přípravě operačního systému pro spuštění aplikace SAP.  

> [!TIP]
> Vždy otestujte funkce převzetí služeb při selhání clusteru po nasazení prvního systému před přidáním dalších sap sidů do clusteru. Tímto způsobem budete vědět, že funkce clusteru funguje, před přidáním složitosti dalších systémů SAP do clusteru.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Nasazení dalších systémů SAP v clusteru

V tomto příkladu předpokládáme, že systém **NW1** byl již nasazen v clusteru. Ukážeme nám, jak nasadit v clusteru SAP systémy **NW2** a **NW3**. 

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

### <a name="prerequisites"></a>Požadavky 

> [!IMPORTANT]
> Než se budeš řídit pokyny k nasazení dalších systémů SAP v clusteru, postupujte podle pokynů k nasazení prvního systému SAP v clusteru, protože existují kroky, které jsou nezbytné pouze při prvním nasazení systému.  

Tato dokumentace předpokládá, že:
* Cluster Pacemaker je již nakonfigurován a spuštěn.  
* Alespoň jeden systém SAP (instance ASCS / ERS) je již nasazen a běží v clusteru.  
* Funkce převzetí služeb při selhání clusteru byla testována.  
* Jsou nasazeny sdílené složky systému NFS pro všechny systémy SAP.  

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci sap netweaveru

1. Přidejte konfiguraci pro nově nasazený systém (tj. **NW2**, **NW3**) do stávajícího nástroje pro vyrovnávání zatížení Azure podle pokynů [Nasadit nástroj pro vyrovnávání zatížení Azure ručně prostřednictvím portálu Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal). Upravte IP adresy, porty sondy stavu, pravidla vyrovnávání zatížení pro vaši konfiguraci.  

2. **[A]** Nastavení překladu názvů pro další systémy SAP. Můžete použít server DNS `/etc/hosts` nebo upravit na všech uzlech. Tento příklad ukazuje, `/etc/hosts` jak použít soubor.  Přizpůsobte adresy IP a názvy hostitelů prostředí. 

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

3. **[A]** Vytvořte sdílené adresáře pro další systémy **NW2** a **NW3** SAP, které nasazujete do clusteru. 

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

4. **[A]** Přidejte položky připojení pro souborové systémy /sapmnt/SID a /usr/sap/SID/SYS pro další systémy SAP, které nasazujete do clusteru. V tomto příkladu **NW2** a **NW3**.  

   Aktualizujte `/etc/fstab` soubor pomocí systémů souborů pro další systémy SAP, které nasazujete do clusteru.  

   * Pokud používáte soubory Azure NetApp, postupujte podle [pokynů zde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)  
   * Používáte-li cluster GlusterFS, postupujte podle pokynů [zde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>Instalace ASCS / ERS

1. Vytvořte prostředky clusteru virtuální IP a sondy stavu pro instance ASCS dalších systémů SAP, které nasazujete do clusteru. Zde uvedený příklad je pro **NW2** a **NW3** ASCS, pomocí nfs na azure netapp soubory svazků s protokolem NFSv3.  

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

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.  

2. **[1]** Instalace systému ASCS aplikace SAP NetWeaver  

   Nainstalujte SAP NetWeaver ASCS jako root pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endové konfigurace nástroje pro nástroj pro vyrovnávání zatížení pro ASCS. Například pro systém **NW2**, virtuální název hostitele je <b>msnw2ascs</b>, <b>10.3.1.52</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>10</b>. Pro systém **NW3**, virtuální název hostitele je <b>msnw3ascs</b>, <b>10.3.1.54</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>20</b>. Poznamenejte si, na kterém uzlu clusteru jste nainstalovali ASCS pro každý SAP SID.  

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst. Parametr SAPINST_USE_HOSTNAME můžete použít k instalaci SAP pomocí názvu virtuálního hostitele.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**SID**/ASCS**Instance#**, zkuste nastavit vlastníka **sid**adm a group na sapsys**ascs instance#** a opakujte.

3. **[1]** Vytvořte prostředky clusteru virtuální IP a sondy stavu pro instanci ERS dalšího systému SAP, který nasazujete do clusteru. Zde uvedený příklad je pro **NW2** a **NW3** ERS, pomocí nfs na Azure NetApp soubory svazků s protokolem NFSv3.  

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

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky.  

   Dále se ujistěte, že prostředky nově vytvořené skupiny ERS jsou spuštěny v uzlu clusteru, naproti uzlu clusteru, kde byla nainstalována instance ASCS pro stejný systém SAP.  Pokud byl například `rhelmsscl1`nainstalován systém NW2 ASCS , zkontrolujte, `rhelmsscl2`zda je skupina NW2 ERS spuštěna na .  Skupinu NW2 ERS můžete `rhelmsscl2` migrovat spuštěním následujícího příkazu pro jeden z prostředků clusteru ve skupině: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** Instalace SAP NetWeaver ERS

   Nainstalujte SAP NetWeaver ERS jako root na druhý uzel pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro ERS. Například pro systém **NW2**bude název virtuálního hostitele <b>msnw2ers</b>, <b>10.3.1.53</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>12</b>. Pro systém **NW3**, název virtuálního hostitele <b>msnw3ers</b>, <b>10.3.1.55</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>22</b>. 

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst. Parametr SAPINST_USE_HOSTNAME můžete použít k instalaci SAP pomocí názvu virtuálního hostitele.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastavují oprávnění správně a instalace se nezdaří.

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**NW2**/ERS**Instance#**, zkuste nastavit vlastníka **na sid**adm a skupiny sapsys složky ERS**Instance#** a opakujte.

   Pokud bylo nutné migrovat skupinu ERS nově nasazeného systému SAP do jiného uzlu clusteru, nezapomeňte odebrat omezení umístění pro skupinu ERS. Omezení můžete odstranit spuštěním následujícího příkazu (příklad je uveden pro systémy SAP **NW2** a **NW3**). Nezapomeňte odebrat dočasná omezení pro stejný prostředek, který jste použili v příkazu k přesunutí skupiny clusterů ERS.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** Upravte profily instancí ASCS/SCS a ERS pro nově nainstalovaný systém (systémy) SAP. Příklad uvedený níže je pro NW2. Budete muset upravit profily ASCS/SCS a ERS pro všechny instance SAP přidané do clusteru.  
 
   * Profil ASCS/SCS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * PROFIL ERS

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** Aktualizace souboru /usr/sap/sapservices

   Chcete-li zabránit spuštění instancí skriptem sapinit spuštění, všechny instance spravované Pacemaker musí být zakomentovány ze `/usr/sap/sapservices` souboru.  Příklad uvedený níže je pro systémy SAP **NW2** a **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** Vytvořte prostředky clusteru SAP pro nově nainstalovaný systém SAP.  

   Pokud používáte architekturu enqueue server 1 (ENSA1), definujte prostředky pro systémy SAP **NW2** a **NW3** takto:

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

   SAP zavedl podporu pro enqueue server 2, včetně replikace, od SAP NW 7.52. Počínaje platformou ABAP 1809 je ve výchozím nastavení nainstalován server 2 fronty. Viz SAP poznámka [2630416](https://launchpad.support.sap.com/#/notes/2630416) pro zařazení do fronty server 2 podporu.
   Pokud používáte architekturu enqueue server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definujte prostředky pro systémy SAP **NW2** a **NW3** takto:

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

   Pokud upgradujete ze starší verze a přepájíte na server fronty 2, přečtěte si poznámku SAP [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > Časové topoukázky ve výše uvedené konfiguraci jsou pouze příklady a může být nutné přizpůsobit konkrétní nastavení SAP. 

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.
   Následující příklad ukazuje stav prostředků clusteru po sap systémy **NW2** a **NW3** byly přidány do clusteru. 

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

8. **[A]** Přidejte pravidla brány firewall pro ASCS a ERS na obou uzlech.  Následující příklad ukazuje pravidla brány firewall pro oba systémy SAP **NW2** a **NW3**.  

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
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Pokračujte v instalaci SAP 

Dokončete instalaci SAP takto:

* [Příprava aplikačních serverů SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Instalace instance DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [Instalace primárního aplikačního serveru SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* Instalace jedné nebo více dalších instancí aplikací SAP

## <a name="test-the-multi-sid-cluster-setup"></a>Otestujte nastavení clusteru s více SID

Následující testy jsou podmnožinou testovacích případů v doporučených postupech průvodců Red Hat. Jsou zahrnuty pro vaše pohodlí. Úplný seznam testů clusteru naleznete v následující dokumentaci:

* Pokud používáte svazky souborů NFS Azure NetApp, postupujte podle [vysoké dostupnosti virtuálních aplikací Azure pro SAP NetWeaver v RHEL se soubory Azure NetApp pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* Pokud používáte `GlusterFS`vysoce dostupné , postupujte podle [azure virtuálních počítačů vysoké dostupnosti pro SAP NetWeaver na RHEL pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel).  

Vždy si přečtěte příručky doporučených postupů red hat a proveďte všechny další testy, které mohly být přidány.  
Testy, které jsou prezentovány jsou ve dvou uzlů, multi-SID clusteru se třemi systémy SAP nainstalovány.  

1. Ručně migrovat instanci ASCS. Příklad ukazuje migraci instance ASCS pro systém SAP NW3.

   Stav prostředku před zahájením testu:

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

   Spusťte následující příkazy jako root pro migraci instance NW3 ASCS.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Stav zdroje po zkoušce:

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

1. Simulovat selhání uzlu

   Stav prostředku před zahájením testu:

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

   Spusťte následující příkaz jako root na uzlu, kde je spuštěna alespoň jedna instance ASCS. V tomto příkladu jsme provedli příkaz na `rhelmsscl1`, kde jsou spuštěny instance ASCS pro NW1, NW2 a NW3.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   Stav po testu a po uzlu, který byl havaroval začal znovu, by měl vypadat takto.

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

   Pokud existují zprávy pro neúspěšné prostředky, vyčistěte stav neúspěšných prostředků. Například:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
