---
title: Virtuální počítače Azure s vysokou dostupností pro SAP NW v RHEL | Microsoft Docs
description: Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux
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
ms.openlocfilehash: 2f55df70b2ebdaed9b7f15c940d3f4f0553a2856
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676906"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7,50.
V ukázkových konfiguracích instalační příkazy atd. Je použito číslo instance ASCS 00, OLAJÍCÍCH instance 2 a SAP System ID NW1. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste k vytvoření prostředků použili [šablonu ASCS/SCS][template-multisid-xscs] s předponou prostředků NW1.

Nejprve si přečtěte následující poznámky a dokumenty SAP

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

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
* [Dokumentace produktu pro úložiště Red Hat Gluster](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP NetWeaver v clusteru Pacemaker](https://access.redhat.com/articles/3150081)
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/OLAJÍCÍCH pro SAP NetWeaver se samostatnými prostředky v RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Konfigurace SAP S/4HANA ASCS/OLAJÍCÍCH se samostatným serverem fronty 2 (ENSA2) v Pacemaker v RHEL ](https://access.redhat.com/articles/3974941)
* Dokumentace k RHEL specifické pro Azure:
  * [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Přehled

Pro zajištění vysoké dostupnosti vyžaduje SAP NetWeaver sdílené úložiště. GlusterFS je nakonfigurovaný v samostatném clusteru a dá se použít v několika systémech SAP.

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver OLAJÍCÍCH a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Následující seznam uvádí konfiguraci (A) SCS a nástroj pro vyrovnávání zatížení OLAJÍCÍCH.

### <a name="ascs"></a>Určitého SCS

* Konfigurace front-endu
  * 10.0.0.7 IP adres
* Port testu paměti
  * Port 620<strong> &lt; Nr &gt; </strong>
* Pravidla vyrovnávání zatížení
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
  * 10.0.0.8 IP adres
* Port testu paměti
  * Port 621<strong> &lt; Nr &gt; </strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
  * Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong>. &lt; 16 TCP &gt; </strong>

* Konfigurace back-endu
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A) SCS/OLAJÍCÍCH

## <a name="setting-up-glusterfs"></a>Nastavení GlusterFS

SAP NetWeaver vyžaduje pro přenos a profilový adresář sdílené úložiště. Přečtěte si [GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver][glusterfs-ha] o tom, jak nastavit GLUSTERFS pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

K nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, skupiny dostupnosti a nástroje pro vyrovnávání zatížení, můžete použít buď šablonu Azure z GitHubu, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení systému Linux prostřednictvím šablony Azure

Azure Marketplace obsahuje obrázek pro Red Hat Enterprise Linux, který můžete použít k nasazení nových virtuálních počítačů. K nasazení všech požadovaných prostředků můžete použít jednu z šablon pro rychlý Start na GitHubu. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti atd. Pomocí těchto kroků nasaďte šablonu:

1. Otevřete [šablonu ASCS/SCS][template-multisid-xscs] na Azure Portal  
1. Zadejte následující parametry.
   1. Předpona prostředku  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro nasazené prostředky.
   1. Typ zásobníku  
      Vyberte typ zásobníku SAP NetWeaver.
   1. Typ operačního systému  
      Vyberte jednu ze distribucí systému Linux. V tomto příkladu vyberte RHEL 7.
   1. Typ databáze  
      Vybrat HANA
   1. Počet systémů SAP  
      Počet systémů SAP, které jsou spuštěny v tomto clusteru. Vyberte 1.
   1. Dostupnost systému  
      Vybrat HA
   1. Uživatelské jméno správce, heslo správce nebo klíč SSH  
      Vytvoří se nový uživatel, který se dá použít k přihlášení k počítači.
   1. ID podsítě  
   Pokud chcete virtuální počítač nasadit do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako/Subscriptions/**&lt; ID &gt; odběru**/ResourceGroups/název **&lt; &gt; skupiny prostředků****&lt; &gt;** /Providers/Microsoft.Network/virtualNetworks/název **&lt; virtuální sítě &gt;**/subnets/název podsítě

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linux pomocí Azure Portal

Nejprve je třeba vytvořit virtuální počítače pro tento cluster. Následně vytvoříte Nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondu back-end.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1  
   Použijte minimálně RHEL 7. v tomto příkladu se jedná o bitovou kopii Red Hat Enterprise Linux 7,4. <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vybrat skupinu dostupnosti vytvořenou dříve  
1. Vytvořit virtuální počítač 2  
   Použijte minimálně RHEL 7. v tomto příkladu se jedná o bitovou kopii Red Hat Enterprise Linux 7,4. <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vybrat skupinu dostupnosti vytvořenou dříve  
1. Přidejte alespoň jeden datový disk do obou virtuálních počítačů.  
   Datové disky se používají pro `<SAPSID`> adresář/usr/SAP/
1. Vytvořit nástroj pro vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **NW1-ASCS-front-end**).
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.0.0.7**).
         1. Klikněte na OK.
      1. 10.0.0.8 IP adres pro ASCS OLAJÍCÍCH
         * Zopakováním výše uvedených kroků vytvořte IP adresu pro OLAJÍCÍCH (například **10.0.0.8** a **NW1-aers-front-end**).
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **NW1-back-end**).
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte Virtuální počítač.
      1. Vyberte virtuální počítače v clusteru (A) SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620 **00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **NW1-ASCS-HP**).
         1. Vybrat TCP as Protocol, port 620 **00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621 **02** pro ASCS olajících
         * Zopakováním výše uvedených kroků vytvořte sondu stavu pro OLAJÍCÍCH (například 621 **02** a **NW1-aers-HP**)
   1. Pravidla vyrovnávání zatížení
      1. Pravidla vyrovnávání zatížení pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **NW1-9,1-ASCS**).
         1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, které jste vytvořili dříve (například **NW1-ASCS-front**-end, **NW1-back-end** a **NW1-ASCS-HP**).
         1. Vybrat **porty ha**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
         * Zopakováním výše uvedených kroků vytvořte pravidla vyrovnávání zatížení pro OLAJÍCÍCH (například **NW1-9,1-olajících**).
1. Případně, pokud váš scénář vyžaduje základní nástroj pro vyrovnávání zatížení (interní), postupujte podle následujících kroků:  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **NW1-ASCS-front-end**).
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.0.0.7**).
         1. Klikněte na OK.
      1. 10.0.0.8 IP adres pro ASCS OLAJÍCÍCH
         * Zopakováním výše uvedených kroků vytvořte IP adresu pro OLAJÍCÍCH (například **10.0.0.8** a **NW1-aers-front-end**).
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **NW1-back-end**).
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte skupinu dostupnosti, kterou jste vytvořili dříve.
      1. Vyberte virtuální počítače v clusteru (A) SCS.
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620 **00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **NW1-ASCS-HP**).
         1. Vybrat TCP as Protocol, port 620 **00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621 **02** pro ASCS olajících
         * Zopakováním výše uvedených kroků vytvořte sondu stavu pro OLAJÍCÍCH (například 621 **02** a **NW1-aers-HP**)
   1. Pravidla vyrovnávání zatížení
      1. 32 **00** TCP pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **NW1-kg-3200**).
         1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **NW1-ASCS-front-end**).
         1. Zachovejte protokol **TCP**, zadejte port **3200**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky pro porty 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 a TCP pro ASCS
      1. Další porty pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky pro porty 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 a TCP pro ASCS olajících

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte pro tento server (a) SCS základní cluster Pacemaker.

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci SAP NetWeaver

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** vytvoření sdílených adresářů

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** instalace klienta GlusterFS a dalších požadavků

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Podívejte se na verzi prostředků-Agents-SAP

   Zajistěte, aby byla verze nainstalovaného balíčku Resource-Agents-SAP aspoň 3.9.5 -124. el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** přidat položky připojení

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Připojit nové sdílené složky

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** konfigurace odkládacího souboru

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Restartujte agenta, aby se změna aktivovala.

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** konfigurace RHEL

   Nakonfigurujte RHEL podle popisu v tématu SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/OLAJÍCÍCH

1. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci ASCS

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** instalace SAP NetWeaver ASCS  

   Nainstalujte SAP NetWeaver ASCS jako kořen v prvním uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS, například <b>NW1-ASCS</b>, <b>10.0.0.7</b> a číslo instance, kterou jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**NW1**/ASCS **00**, zkuste nastavit vlastníka a skupinu složky ASCS **00** a zkuste to znovu.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci olajících

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** instalace SAP NetWeaver olajících  

   Nainstalujte SAP NetWeaver OLAJÍCÍCH jako kořenovou složku na druhém uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro OLAJÍCÍCH, například <b>NW1-aers</b>, <b>10.0.0.8</b> a číslo instance, kterou jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>02</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**NW1**/ERS **02**, zkuste nastavit vlastníka a skupinu ve složce olajících **02** a zkuste to znovu.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** přizpůsobení profilů instancí ASCS/SCS a olajících

   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Pro ENSA1 i ENSA2 se ujistěte, že `keepalive` jsou nastavené parametry operačního systému, jak je popsáno v tématu SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * Profil OLAJÍCÍCH

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** konfigurace Keep Alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím nástroje pro vyrovnávání zatížení softwaru. Nástroj pro vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém limitu. Abyste tomu předešli, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS, pokud používáte ENSA1, a změnit nastavení systému Linux `keepalive` na všech serverech SAP pro ENSA1/ENSA2. Další informace najdete v tématu [SAP Note 1410736][1410736] .

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** aktualizace souboru/usr/SAP/sapservices

   Aby se zabránilo spuštění instancí pomocí spouštěcího skriptu sapinit, musí být všechny instance spravované pomocí Pacemaker odkomentovatelné ze souboru/usr/SAP/sapservices. Nekomentovat instanci SAP HANA, pokud se použije s HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** vytvoření prostředků clusteru SAP

  Pokud používáte architekturu serveru front Server 1 (ENSA1), definujte prostředky následujícím způsobem:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP představilo podporu pro front-Server 2, včetně replikace, od SAP NW 7,52. Počínaje platformou ABAP 1809 se ve výchozím nastavení nainstaluje služba fronty serveru 2. Podporu služby zařazení serveru 2 pro frontu najdete v tématu SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Pokud používáte architekturu Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), nainstalujte agenta prostředků – agenti-SAP-4.1.1-12.el7.x86_64 nebo novější a definujte prostředky následujícím způsobem:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Pokud provádíte upgrade ze starší verze a přejdete na server fronty 2, přečtěte si článek SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Časové limity ve výše uvedené konfiguraci jsou jenom příklady a možná je budete muset přizpůsobit konkrétnímu nastavení SAP. 

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** přidejte do obou uzlů pravidla brány firewall pro ASCS a olajících.

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver

Některé databáze vyžadují, aby se instalace instance databáze spustila na aplikačním serveru. Příprava virtuálních počítačů aplikačního serveru, aby je bylo možné používat v těchto případech.

Postup níže předpokládá, že instalujete aplikační server na jiný server než servery ASCS/SCS a HANA. Jinak se některé z následujících kroků (třeba konfigurace překladu názvů hostitelů) nevyžadují.

1. Nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Vytvoření adresáře sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Nainstalovat klienta GlusterFS a další požadavky

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Přidat položky připojení

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Připojit nové sdílené složky

   <pre><code>sudo mount -a
   </code></pre>

1. Nakonfigurovat odkládací soubor
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Restartujte agenta, aby se změna aktivovala.

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalovaný na SAP HANA. Pro tuto instalaci můžete použít každou podporovanou databázi. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v tématu [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Spusťte instalaci instance databáze SAP.

   K instalaci instance databáze SAP NetWeaver jako kořenového adresáře použijte virtuální název hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro databázi, například <b>NW1-DB</b> a <b>10.0.0.13</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Pomocí těchto kroků nainstalujete aplikační Server SAP.

1. Příprava aplikačního serveru

   K přípravě aplikačního serveru použijte postup v části [Příprava aplikačního serveru SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) .

1. Instalace aplikačního serveru SAP NetWeaver

   Nainstalujte primární nebo další server aplikace SAP NetWeaver.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizace zabezpečeného úložiště SAP HANA

   Aktualizujte SAP HANA zabezpečené úložiště tak, aby odkazovalo na virtuální název nastavení replikace SAP HANA systému.

   Spusťte následující příkaz, který vypíše položky jako \<sapsid> ADM.

   <pre><code>hdbuserstore List
   </code></pre>

   Mělo by se zobrazit seznam všech položek, které by měly vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položky odkazuje na virtuální počítač, a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka musí být změněna tak, aby odkazovala na virtuální název hostitele nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše) a název databáze (**HN1** ve výstupu výše).

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

1. Ruční migrace instance ASCS

   Stav prostředku před spuštěním testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Pro migraci instance ASCS spusťte následující příkazy jako kořen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Simulace havárie uzlu

   Stav prostředku před spuštěním testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkaz jako kořen v uzlu, ve kterém je spuštěná instance ASCS.

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Stav po spuštění uzlu by měl vypadat takto.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   K vyčištění neúspěšných prostředků použijte následující příkaz.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Ukončit proces serveru zprávy

   Stav prostředku před spuštěním testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře Identifikujte proces serveru a potom ho ukončete.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -f ms.sapNW1 | xargs kill -9
   </code></pre>

   Pokud server pouze jednou zadáte, bude restartován nástrojem `sapstart` . Pokud jste ho ASCS dostatečně přesunuli, Pacemaker se nakonec přesune instance na jiný uzel. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Proces serveru dezaktivačního zařazení do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu, ve kterém je spuštěná instance ASCS, aby se mohl ukončit server fronty.

   <pre><code>
    #If using ENSA1 
    [root@nw1-cl-1 ~]# pgrep -f en.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enq.sapNW1 | xargs kill -9
   </code></pre>

   Instance ASCS by měla okamžitě převzít služby na jiný uzel. Instance OLAJÍCÍCH by také měla převzít služby při selhání po spuštění instance ASCS. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Proces replikace replikačního serveru do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkaz jako kořenový adresář v uzlu, ve kterém je spuštěná instance OLAJÍCÍCH, čímž se ukončí proces serveru replikace ve frontě.

   <pre><code>
    #If using ENSA1
    [root@nw1-cl-1 ~]# pgrep -f er.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enqr.sapNW1 | xargs kill -9
   </code></pre>

   Pokud příkaz spouštíte pouze jednou, `sapstart` proces se restartuje. Pokud je spuštěno dostatečně často, `sapstart` proces nebude restartován a prostředek bude zastaven. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředku instance OLAJÍCÍCH po testu.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Ukončit proces sapstartsrv zařazování do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu, kde je spuštěný ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Proces sapstartsrv by měl být v rámci monitorování vždy restartován agentem prostředků Pacemaker. Stav prostředku po testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [HA pro SAP NW na virtuálních počítačích Azure v RHEL pro aplikace SAP – příručka pro multi-SID](./high-availability-guide-rhel-multi-sid.md)
* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .