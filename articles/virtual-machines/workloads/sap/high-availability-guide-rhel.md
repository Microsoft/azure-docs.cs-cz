---
title: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux | Dokumentace Microsoftu
description: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: c7805e64c4f387b870922dcb63e20d86f691092a
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119012"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux

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

Tento článek popisuje, jak nasadit virtuální počítače, konfigurace virtuálních počítačů, instalaci rozhraní clusteru a instalace s vysokou dostupností systému SAP NetWeaver 7.50.
V ukázkové konfigurace příkazy instalace atd. Číslo instance ASCS 00 Lajících instance číslo 02 a používá se ID NW1 systému SAP. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu se předpokládá, že jste použili [šablony ASCS/SCS] [ template-multisid-xscs] s prostředků předpony NW1 vytvoříte prostředky.

Přečtěte si následující poznámky SAP a Paper nejprve

* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2002167] má doporučené nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP [2009879] obsahuje pokyny pro SAP HANA pro Red Hat Enterprise Linux
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Azure Virtual Machines, plánování a implementace SAP na platformě Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [Dokumentaci k produktu pro Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver v pacemaker clusteru](https://access.redhat.com/articles/3150081)
* Dokumentace ke službě obecné RHEL
  * [Přehled doplňků vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňku vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/Lajících pro SAP Netweaver pomocí samostatné prostředky v RHEL 7.5](https://access.redhat.com/articles/3569681)
* Konkrétní RHEL dokumentace k Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – Microsoft Azure Virtual Machines jako členů clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7.4 (a novější) vysokou dostupnost clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Přehled

Abyste dosáhli vysoké dostupnosti, SAP NetWeaver vyžaduje sdílené úložiště. GlusterFS nakonfigurovaná na jiném clusteru a mohou být využívána více systémů SAP.

![Přehled SAP NetWeaver vysoké dostupnosti](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Lajících a databáze SAP HANA pomocí virtuální název hostitele a virtuální IP adresy. V Azure je potřeba nástroj pro vyrovnávání zatížení pomocí virtuální IP adresu. Následující seznam obsahuje konfiguraci (A) SCS a Lajících nástroj pro vyrovnávání zatížení.

### <a name="ascs"></a>(A)SCS

* Konfiguraci front-endu
  * IP adresa 10.0.0.7
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 620**&lt;nr&gt;**
* Pravidla Vyrovnávání zatížení
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>LAJÍCÍCH

* Konfiguraci front-endu
  * IP adresa 10.0.0.8
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 621**&lt;nr&gt;**
* Pravidla Vyrovnávání zatížení
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-glusterfs"></a>Nastavení GlusterFS

SAP NetWeaver vyžaduje sdílené úložiště pro přenos a profil adresář. Čtení [GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver] [ glusterfs-ha] o tom, jak nastavit GlusterFS pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

Nasadit všechny požadované prostředky Azure, včetně virtual machines můžete použít šablony Azure z githubu, dostupnosti, nastavte a nástroj pro vyrovnávání zatížení nebo ručně nasadit prostředky.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu pomocí šablony Azure

Na webu Azure Marketplace obsahuje bitovou kopii pro Red Hat Enterprise Linux, který můžete použít k nasazení nových virtuálních počítačů. Můžete některou ze šablon quickstart na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti, atd. Postupujte podle těchto kroků a nasaďte šablonu:

1. Otevřít [šablony ASCS/SCS] [ template-multisid-xscs] na portálu Azure portal  
1. Zadejte následující parametry
   1. Předpona prostředků  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazené.
   1. Typ zásobníku  
      Vyberte typ zásobníku SAP NetWeaver
   1. Typ operačního systému  
      Vyberte jednu z Linuxových distribucí. V tomto příkladu vyberte RHEL 7
   1. Typ databáze  
      Vyberte HANA
   1. Počet systému SAP  
      Číslo systému SAP, který běží v tomto clusteru. Vyberte 1.
   1. Dostupnost systému  
      Vyberte HA
   1. Klíč pro uživatelské jméno správce, správce hesla nebo klíče SSH  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   1. ID podsítě  
   Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá /subscriptions/**&lt;ID předplatného&gt;**/resourceGroups/**&lt;název skupiny prostředků&gt;**/poskytovatelé/ Microsoft.Network/virtualNetworks/**&lt;název virtuální sítě&gt;**/subnets/**&lt;název podsítě&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu prostřednictvím webu Azure portal

Nejprve musíte vytvořit virtuální počítače pro tento cluster. Potom vytvořte nástroj pro vyrovnávání zatížení a použijte virtuální počítače v back-endové fondy.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti  
   Nastavení maximální aktualizační doména
1. Vytvoření virtuálního počítače 1  
   Použijte alespoň RHEL 7, v tomto obrázku Red Hat Enterprise Linux 7.4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vyberte dříve vytvořenou skupinu dostupnosti  
1. Vytvoření virtuálního počítače 2  
   Použijte alespoň RHEL 7, v tomto obrázku Red Hat Enterprise Linux 7.4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vyberte dříve vytvořenou skupinu dostupnosti  
1. Přidejte alespoň jeden datový disk pro oba virtuální počítače  
   Datové disky se používajípro/USR/sap/`<SAPSID`> adresáře
1. Vytvořte nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvoření front-endové IP adresy
      1. IP adresa 10.0.0.7 ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-endový fond IP adres a klikněte na tlačítko Přidat
         1. Zadejte název nové front-endový fond IP (například **nw1. ascs front-end**)
         1. Nastavení přiřazení do statické a zadejte IP adresu (například **10.0.0.7**)
         1. Klikněte na tlačítko OK
      1. IP adresa 10.0.0.8 ASCS Lajících
         * Zopakujte výše uvedené kroky a vytvořte IP adresu pro Lajících (například **10.0.0.8** a **nw1-aers-backend**)
   1. Vytvoření back-endové fondy
      1. Vytvoření back-endový fond pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového back-endového fondu (například **nw1-ascs-backend**)
         1. Klikněte na tlačítko Přidat virtuální počítač.
         1. Vyberte skupinu dostupnosti jste vytvořili dříve
         1. Vyberte virtuální počítače (A) SCS clusteru
         1. Klikněte na tlačítko OK
      1. Vytvoření back-endový fond pro ASCS Lajících
         * Zopakujte výše uvedené kroky a vytvořte back-endový fond pro Lajících (například **nw1-aers-backend**)
   1. Vytvoření sondy stavu
      1. Port 620**00** pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení vyberte testy stavu a klikněte na tlačítko Přidat
         1. Zadejte název nového stavu testu (například **nw1. ascs hp**)
         1. Jako protokol, port 620 vyberte TCP**00**, Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 621**02** pro ASCS Lajících
         * Zopakujte výše uvedené kroky a vytvořte sondu stavu pro Lajících (například 621**02** a **nw1. aers hp**)
   1. Pravidla Vyrovnávání zatížení
      1. 32**00** TCP pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, pravidel Vyrovnávání zatížení vyberte a klikněte na tlačítko Přidat
         1. Zadejte název nového pravidla služby load balancer (například **nw1-lb-3200**)
         1. Vyberte IP adresu front-endu, back-endového fondu a sondu stavu, které jste vytvořili dříve (například **nw1. ascs front-end**)
         1. Zachovat protokol **TCP**, zadejte port **3200**
         1. Zvyšte časový limit nečinnosti až 30 minut.
         1. **Ujistěte se, že chcete povolit plovoucí IP adresy**
         1. Klikněte na tlačítko OK
      1. Další porty pro ASC
         * Opakujte předchozí kroky pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASC
      1. Další porty pro ASCS Lajících
         * Opakujte předchozí kroky pro porty 33**02**, 5**02**13, 5**02**14, 5**02**16 a TCP pro ASCS Lajících

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) pro vytvoření základní Pacemaker clusteru pro tento server (A) SCS.

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava instalace SAP NetWeaver

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   </code></pre>

1. **[A]**  Vytvořit sdílené složky

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

1. **[A]**  GlusterFS instalace klienta a další požadavky

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]**  Zkontrolujte verzi prostředku. agenti sap

   Ujistěte se, že je verze nainstalovaného balíčku prostředků. agenti sap alespoň 3.9.5-124.el7
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


1. **[A]**  Položky přidat připojení

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Připojení nových sdílených složek

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]**  Nakonfigurovat ODKLÁDACÍHO souboru

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Restartujte agenta k aktivaci změny

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]**  RHEL konfigurace

   Konfigurace RHEL, jak je popsáno v Poznámka SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/Lajících

1. **[1]**  Vytvoření virtuální IP prostředku a sondu stavu pro instance ASC

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.11</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

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

1. **[1]**  Instalace SAP NetWeaver ASCS  

   Instalace SAP NetWeaver ASCS jako uživatel root na prvním uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS, například <b>nw1 ascs</b>, <b>10.0.0.11</b> a instance číslo, které jste použili pro test paměti nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalace nezdaří vytvořit podsložku v umístění/USR/sap/**NW1**/ASCS**00**, zkuste nastavit vlastníka a skupiny ASCS**00** složky a zkuste to znovu.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Vytvoření virtuální IP prostředku a sondu stavu pro instance Lajících

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.12</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

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

1. **[2]**  Instalace SAP NetWeaver Lajících  

   Instalace SAP NetWeaver Lajících jako uživatel root na druhém uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro Lajících, například <b>nw1 aers</b>, <b>10.0.0.12</b> a instance číslo, které jste použili pro test paměti nástroje pro vyrovnávání zatížení, například <b>02</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalace nezdaří vytvořit podsložku v umístění/USR/sap/**NW1**/ERS**02**, zkuste nastavit vlastníka a skupiny Lajících**02** složky a zkuste to znovu.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]**  Adapt ASCS/SCS a Lajících instance profily

   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil Lajících

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]**  Konfigurace zachování

   Komunikace mezi aplikační server SAP NetWeaver a ASCS/SCS je směrován přes nástroj pro vyrovnávání zatížení softwaru. Nástroje pro vyrovnávání zatížení odpojí neaktivní připojení po Konfigurovatelný časový limit. Chcete-li tomu zabránit, budete muset nastavit parametr v SAP NetWeaver ASCS/SCS profilu a změnit nastavení systému Linux. Čtení [1410736 Poznámka SAP] [ 1410736] Další informace.

   ASCS/SCS profilu parametr modul/encni/set_so_keepalive byl již přidán v posledním kroku.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Aktualizovat soubor /usr/sap/sapservices

   Chcete-li zabránit spuštění instance sapinit spouštěcí skript, musí všechny instance spravuje Pacemaker zakomentované ze souboru /usr/sap/sapservices. Pokud se použije s HANA Senior není komentář si instanci SAP HANA

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]**  Vytvořit prostředky clusteru SAP

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

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

1. **[A]**  Přidat pravidla brány firewall pro ASCS a Lajících na oba uzly

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
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver aplikace server přípravy

Některé databáze vyžadovat, aby instalaci instance databáze spuštěné na aplikační server. Příprava virtuálních počítačů pro server aplikace bude moct používat v těchto případech.

Kroky zmeškáte Předpokládejme, že nainstalujete aplikační server na serveru se liší od ASC/SCS a HANA servery. V opačném případě nejsou potřeba některé z kroků (např. konfigurace rozlišení názvu hostitele).

1. Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.11 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.12 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.8 nw1-di-0</b>
   <b>10.0.0.7 nw1-di-1</b>
   </code></pre>

1. Vytvořit adresář sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Nainstalujte klienta GlusterFS a další požadavky

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Přidat položky připojení

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Připojení nových sdílených složek

   <pre><code>sudo mount -a
   </code></pre>

1. Konfigurace STRÁNKOVACÍHO souboru
 
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

   Restartujte agenta k aktivaci změny

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je nainstalován SAP NetWeaver na systému SAP HANA. Všechny podporované databáze můžete použít pro tuto instalaci. Další informace o tom, jak instalace SAP HANA v Azure najdete v tématu [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux][sap-hana-ha]. Seznam podporovaných databází najdete v tématu [SAP 1928533 Poznámka][1928533].

1. Spuštění instalace instancí databáze SAP

   Nainstalujte instanci databáze SAP NetWeaver jako kořen virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro databázi například pomocí <b>nw1 db</b> a <b>10.0.0.13</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace serveru aplikace SAP NetWeaver

Postupujte podle těchto kroků nainstalujte aplikační server SAP.

1. Příprava aplikace serveru

   Postupujte podle kroků v kapitole [Příprava serveru aplikace SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) výše a připravte je aplikační server.

1. Instalace aplikační server SAP NetWeaver

   Instalace serveru primární nebo dalších aplikací SAP NetWeaver.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Zabezpečené úložiště pověření aktualizace SAP HANA

   Aktualizujte SAP HANA zabezpečeného úložiště tak, aby odkazoval na virtuální název tohoto nastavení systémové replikace SAP HANA.

   Spusťte následující příkaz pro položky jako seznamu \<sapsid > adm

   <pre><code>hdbuserstore List
   </code></pre>

   To by měla všechny položky seznamu a by měl vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položku odkazuje k virtuálnímu počítači a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka je potřeba změnit tak, aby odkazoval na virtuální název hostitele z nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše) a název databáze (**HN1** ve výstupu výše).

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Test nastavení clusteru

1. ASCS instance přenášet ručně

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

   Spuštěním následujících příkazů jako uživatel root migrovat instance ASC.

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

1. Simulace selhání uzlu

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

   Spuštěním následujícího příkazu jako uživatel root v uzlu, kde je spuštěná instance ASC

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Stav po spuštění uzlu znovu by měl vypadat takto.

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

   Použijte následující příkaz k vyčištění nevydařené zdroje.

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

1. Ukončit proces serveru zpráv

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

   Spuštěním následujících příkazů jako kořenového adresáře Identifikujte proces serveru zpráv a ukončete ji.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Pokud jste pouze kill server zpráv jednou, se restartuje tak sapstart. Pokud je často dostatečné Pacemaker budou nakonec kill přesunout do jiného uzlu ASCS instance. Spuštěním následujících příkazů jako kořenový adresář pro vyčištění prostředků stavu instance ASCS a Lajících po testu.

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

1. Ukončení procesu zařazení do fronty serveru

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

   Spuštěním následujících příkazů jako uživatel root v uzlu, kde je spuštěná ASCS instance k ukončení serveru zařadit do fronty.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   ASCS instance by měl bezprostředně převzetí služeb při selhání do jiného uzlu. Instance Lajících by měl také převzetí služeb při selhání po spuštění instance ASC. Spuštěním následujících příkazů jako kořenový adresář pro vyčištění prostředků stavu instance ASCS a Lajících po testu.

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

1. Ukončení procesu zařazení do fronty replikace serveru

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

   Spusťte následující příkaz jako uživatel root v uzlu, kde je spuštěná instance Lajících k ukončení procesu zařazení do fronty replikace serveru.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Pokud spustíte pouze příkaz jednou, restartuje sapstart procesu. Při spuštění často dostatečné, bude sapstart restart procesu a prostředek bude v zastaveném stavu. Spuštěním následujících příkazů jako kořenový adresář pro vyčištění prostředků stavu instance Lajících po testu.

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

1. Ukončení procesu sapstartsrv zařadit do fronty

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

   Spuštěním následujících příkazů jako kořenového adresáře na uzlu se spuštěným ASC.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Proces sapstartsrv by měla být vždy restartována agentem Pacemaker prostředků jako součást monitorování. Stav prostředku po testu:

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

## <a name="next-steps"></a>Další postup

* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
