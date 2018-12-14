---
title: GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver | Dokumentace Microsoftu
description: GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver
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
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 484a0043b9b5eefa5491dee75e87244d1c001620
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342319"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md

Tento článek popisuje, jak nasadit virtuální počítače, konfigurace virtuálních počítačů a instalace GlusterFS clusteru, který slouží k ukládání sdílených dat s vysokou dostupností systému SAP.
Tato příručka popisuje, jak nastavit GlusterFS, který se používá dva systémy SAP NW1 a NW2. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu se předpokládá, že jste použili [šablony serveru SAP souboru] [ template-file-server] s předponou prostředků **glust**.

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
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [Dokumentaci k produktu pro Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Dokumentace ke službě obecné RHEL
  * [Přehled doplňků vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňku vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Konkrétní RHEL dokumentace k Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – Microsoft Azure Virtual Machines jako členů clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7.4 (a novější) vysokou dostupnost clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Přehled

Abyste dosáhli vysoké dostupnosti, SAP NetWeaver vyžaduje sdílené úložiště. GlusterFS nakonfigurovaná na jiném clusteru a mohou být využívána více systémů SAP.

![Přehled SAP NetWeaver vysoké dostupnosti](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>Nastavit GlusterFS

Chcete-li nasadit všechny požadované prostředky Azure můžete použít šablony Azure z githubu, včetně virtual machines, dostupnosti a síťových rozhraní nebo můžete nasadit prostředky ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu pomocí šablony Azure

Na webu Azure Marketplace obsahuje bitovou kopii pro Red Hat Enterprise Linux, který můžete použít k nasazení nových virtuálních počítačů.
Můžete některou ze šablon quickstart na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, dostupnosti, atd. Postupujte podle těchto kroků a nasaďte šablonu:

1. Otevřít [šablony serveru SAP souboru] [ template-file-server] na webu Azure Portal
1. Zadejte následující parametry
   1. Předpona prostředků  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazené.
   2. Zadejte počet systémů SAP počet systémů SAP, které budou používat tento souborový server. To nasadí požadovaný počet disků atd.
   3. Typ operačního systému  
      Vyberte jednu z Linuxových distribucí. V tomto příkladu vyberte RHEL 7
   4. Klíč pro uživatelské jméno správce, správce hesla nebo klíče SSH  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   5. ID podsítě  
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
1. Přidejte jeden datový disk pro každý systém SAP do obou virtuálních počítačů.

### <a name="configure-glusterfs"></a>Konfigurace GlusterFS

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1, **[2]** – platí jenom pro uzel 2, **[3].**  – platí jenom pro uzel 3.

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]**  Zaregistrovat

   Registrace virtuálních počítačů a připojí ho k fondu, který obsahuje úložiště pro RHEL 7, GlusterFS

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]**  Povolit GlusterFS úložišť

   Abyste mohli nainstalovat požadované balíčky, povolte následující úložiště.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]**  GlusterFS nainstalovat balíčky

   Tyto balíčky nainstalovat na všechny uzly GlusterFS

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Po instalaci restartování uzlů.

1. **[A]**  Upravit brány Firewall

   Přidání pravidla brány firewall pro povolení komunikace s klienty na GlusterFS uzly.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]**  Povolit a spustit službu GlusterFS

   Spusťte službu GlusterFS na všech uzlech.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]**  Vytvořit GluserFS

   Spusťte následující příkazy k vytvoření clusteru GlusterFS

   <pre><code>sudo gluster peer probe glust-1
   sudo gluster peer probe glust-2
   
   # Check gluster peer status
   sudo gluster peer status
   
   # Number of Peers: 2
   # 
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Accepted peer request (Connected)
   # 
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Accepted peer request (Connected)
   </code></pre>

1. **[2]**  Peer stav testu

   Testovat stav partnerského na druhém uzlu

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-2
   # Uuid: 9e340385-12fe-495e-ab0f-4f851b588cba
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[3]**  Peer stav testu

   Stav partnerského uzlu třetí testování

   <pre><code>sudo gluster peer status
   # Number of Peers: 2
   #
   # Hostname: glust-0
   # Uuid: 6bc6927b-7ee2-461b-ad04-da123124d6bd
   # State: Peer in Cluster (Connected)
   #
   # Hostname: glust-1
   # Uuid: 10d43840-fee4-4120-bf5a-de9c393964cd
   # State: Peer in Cluster (Connected)
   </code></pre>

1. **[A]**  Vytvořit LVM

   V tomto příkladu se používá GlusterFS pro dva systémy SAP NW1 a NW2. Pomocí následujících příkazů vytvořte LVM konfigurace pro tyto systémy SAP.

   Pomocí těchto příkazů pro NW1

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun0
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW1</b> /dev/disk/azure/scsi1/lun0
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW1</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW1</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW1</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW1</b>/aers
   sudo lvscan
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW1</b>/aers
   
   sudo mkdir -p /rhs/<b>NW1</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW1</b>/trans
   sudo mkdir -p /rhs/<b>NW1</b>/sys
   sudo mkdir -p /rhs/<b>NW1</b>/ascs
   sudo mkdir -p /rhs/<b>NW1</b>/aers
   
   sudo chattr +i /rhs/<b>NW1</b>/sapmnt
   sudo chattr +i /rhs/<b>NW1</b>/trans
   sudo chattr +i /rhs/<b>NW1</b>/sys
   sudo chattr +i /rhs/<b>NW1</b>/ascs
   sudo chattr +i /rhs/<b>NW1</b>/aers

   echo -e "/dev/rhgs-<b>NW1</b>/sapmnt\t/rhs/<b>NW1</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/trans\t/rhs/<b>NW1</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/sys\t/rhs/<b>NW1</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/ascs\t/rhs/<b>NW1</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW1</b>/aers\t/rhs/<b>NW1</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

   Pomocí těchto příkazů pro NW2

   <pre><code>sudo pvcreate --dataalignment 1024K /dev/disk/azure/scsi1/lun1
   sudo pvscan
   sudo vgcreate --physicalextentsize 256K rhgs-<b>NW2</b> /dev/disk/azure/scsi1/lun1
   sudo vgscan
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/sapmnt
   sudo lvcreate -l 20%FREE -n rhgs-<b>NW2</b>/trans
   sudo lvcreate -l 10%FREE -n rhgs-<b>NW2</b>/sys
   sudo lvcreate -l 50%FREE -n rhgs-<b>NW2</b>/ascs
   sudo lvcreate -l 100%FREE -n rhgs-<b>NW2</b>/aers
   
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sapmnt
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/trans
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/sys
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/ascs
   sudo mkfs.xfs -f -K -i size=512 -n size=8192 /dev/rhgs-<b>NW2</b>/aers
   
   sudo mkdir -p /rhs/<b>NW2</b>/sapmnt
   sudo mkdir -p /rhs/<b>NW2</b>/trans
   sudo mkdir -p /rhs/<b>NW2</b>/sys
   sudo mkdir -p /rhs/<b>NW2</b>/ascs
   sudo mkdir -p /rhs/<b>NW2</b>/aers
   
   sudo chattr +i /rhs/<b>NW2</b>/sapmnt
   sudo chattr +i /rhs/<b>NW2</b>/trans
   sudo chattr +i /rhs/<b>NW2</b>/sys
   sudo chattr +i /rhs/<b>NW2</b>/ascs
   sudo chattr +i /rhs/<b>NW2</b>/aers
   sudo lvscan
   
   echo -e "/dev/rhgs-<b>NW2</b>/sapmnt\t/rhs/<b>NW2</b>/sapmnt\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/trans\t/rhs/<b>NW2</b>/trans\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/sys\t/rhs/<b>NW2</b>/sys\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/ascs\t/rhs/<b>NW2</b>/ascs\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   echo -e "/dev/rhgs-<b>NW2</b>/aers\t/rhs/<b>NW2</b>/aers\txfs\tdefaults,inode64,nobarrier,noatime,nouuid 0 2" | sudo tee -a /etc/fstab
   sudo mount -a
   </code></pre>

1. **[1]**  Vytváření distribuovaných svazků

   Pomocí následujících příkazů vytvořte svazek GlusterFS pro NW1 a spusťte ji.

   <pre><code>sudo gluster vol create <b>NW1</b>-sapmnt replica 3 glust-0:/rhs/<b>NW1</b>/sapmnt glust-1:/rhs/<b>NW1</b>/sapmnt glust-2:/rhs/<b>NW1</b>/sapmnt force
   sudo gluster vol create <b>NW1</b>-trans replica 3 glust-0:/rhs/<b>NW1</b>/trans glust-1:/rhs/<b>NW1</b>/trans glust-2:/rhs/<b>NW1</b>/trans force
   sudo gluster vol create <b>NW1</b>-sys replica 3 glust-0:/rhs/<b>NW1</b>/sys glust-1:/rhs/<b>NW1</b>/sys glust-2:/rhs/<b>NW1</b>/sys force
   sudo gluster vol create <b>NW1</b>-ascs replica 3 glust-0:/rhs/<b>NW1</b>/ascs glust-1:/rhs/<b>NW1</b>/ascs glust-2:/rhs/<b>NW1</b>/ascs force
   sudo gluster vol create <b>NW1</b>-aers replica 3 glust-0:/rhs/<b>NW1</b>/aers glust-1:/rhs/<b>NW1</b>/aers glust-2:/rhs/<b>NW1</b>/aers force
   
   sudo gluster volume start <b>NW1</b>-sapmnt
   sudo gluster volume start <b>NW1</b>-trans
   sudo gluster volume start <b>NW1</b>-sys
   sudo gluster volume start <b>NW1</b>-ascs
   sudo gluster volume start <b>NW1</b>-aers
   </code></pre>

   Pomocí následujících příkazů vytvořte svazek GlusterFS pro NW2 a spusťte ji.

   <pre><code>sudo gluster vol create <b>NW2</b>-sapmnt replica 3 glust-0:/rhs/<b>NW2</b>/sapmnt glust-1:/rhs/<b>NW2</b>/sapmnt glust-2:/rhs/<b>NW2</b>/sapmnt force
   sudo gluster vol create <b>NW2</b>-trans replica 3 glust-0:/rhs/<b>NW2</b>/trans glust-1:/rhs/<b>NW2</b>/trans glust-2:/rhs/<b>NW2</b>/trans force
   sudo gluster vol create <b>NW2</b>-sys replica 3 glust-0:/rhs/<b>NW2</b>/sys glust-1:/rhs/<b>NW2</b>/sys glust-2:/rhs/<b>NW2</b>/sys force
   sudo gluster vol create <b>NW2</b>-ascs replica 3 glust-0:/rhs/<b>NW2</b>/ascs glust-1:/rhs/<b>NW2</b>/ascs glust-2:/rhs/<b>NW2</b>/ascs force
   sudo gluster vol create <b>NW2</b>-aers replica 3 glust-0:/rhs/<b>NW2</b>/aers glust-1:/rhs/<b>NW2</b>/aers glust-2:/rhs/<b>NW2</b>/aers force
   
   sudo gluster volume start <b>NW2</b>-sapmnt
   sudo gluster volume start <b>NW2</b>-trans
   sudo gluster volume start <b>NW2</b>-sys
   sudo gluster volume start <b>NW2</b>-ascs
   sudo gluster volume start <b>NW2</b>-aers
   </code></pre>

## <a name="next-steps"></a>Další postup

* [Instalace SAP ASCS a databáze](high-availability-guide-rhel.md)
* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
