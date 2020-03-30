---
title: GlusterFS na virtuálních počítačích Azure na RHEL pro SAP NetWeaver | Dokumenty společnosti Microsoft
description: GlusterFS na virtuálních počítačích Azure s Red Hat Enterprise Linuxem pro SAP NetWeaver
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
ms.date: 08/16/2018
ms.author: radeltch
ms.openlocfilehash: 388a2db2c888be541d89c5f4274bd38b37e4ca28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591910"
---
# <a name="glusterfs-on-azure-vms-on-red-hat-enterprise-linux-for-sap-netweaver"></a>GlusterFS na virtuálních počítačích Azure s Red Hat Enterprise Linuxem pro SAP NetWeaver

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

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače a nainstalovat cluster GlusterFS, který lze použít k ukládání sdílených dat vysoce dostupného systému SAP.
Tato příručka popisuje, jak nastavit GlusterFS, který používá dva systémy SAP, NW1 a NW2. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste použili [šablonu souborového serveru SAP][template-file-server] s **glust**předponou prostředků .

Nejprve si přečtěte následující poznámky a dokumenty SAP

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu (tento článek)][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [Dokumentace k produktu pro red hat gluster skladování](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Dokumentace RHEL specifické pro Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – virtuální počítače Microsoft Azure jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace vysoce dostupnosti red hatového linuxového linuxu 7.4 (a novějšího) v Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Přehled

Pro dosažení vysoké dostupnosti vyžaduje SAP NetWeaver sdílené úložiště. GlusterFS je konfigurován v samostatném clusteru a může být používán více systémy SAP.

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-rhel-glusterfs/rhel-glusterfs.png)

## <a name="set-up-glusterfs"></a>Nastavení GlusterFS

Buď můžete použít šablonu Azure z githubu k nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, sady dostupnosti a síťových rozhraní, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu přes šablonu Azure

Azure Marketplace obsahuje image pro Red Hat Enterprise Linux, kterou můžete použít k nasazení nových virtuálních počítačů.
Můžete použít jednu ze šablon rychlého startu na githubu k nasazení všech požadovaných prostředků. Šablona nasazuje virtuální počítače, sadu dostupnosti atd. Chcete-li šablonu nasadit, postupujte takto:

1. Otevření [šablony souborového serveru SAP][template-file-server] na webu Azure Portal
1. Zadejte následující parametry
   1. Předpona zdroje  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazeny.
   2. Počet systémů SAP Zadejte počet systémů SAP, které budou používat tento souborový server. Tím se nasadí požadovaný počet disků atd.
   3. Typ operačního příkazu  
      Vyberte jednu z distribucí Linuxu. V tomto příkladu vyberte RHEL 7
   4. Uživatelské jméno správce, heslo správce nebo klíč SSH  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   5. ID podsítě  
      Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k čemuž by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako /subscriptions/**&lt;&gt;ID předplatného**/resourceGroups/**&lt;název&gt;skupiny prostředků**/providers/Microsoft.Network/virtualNetworks/**&lt;název&gt;virtuální sítě**/podsítě/**&lt;název&gt; podsítě**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu přes portál Azure

Nejprve je třeba vytvořit virtuální počítače pro tento cluster. Poté vytvoříte nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondech back-end. Doporučujeme [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).  

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření sady dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1  
   Použijte alespoň RHEL 7, v tomto příkladu Red Hat Enterprise Linux 7.4 image<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Vytvořit virtuální počítač 2  
   Použijte alespoň RHEL 7, v tomto příkladu Red Hat Enterprise Linux 7.4 image<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Přidejte jeden datový disk pro každý systém SAP do obou virtuálních počítačů.

### <a name="configure-glusterfs"></a>Konfigurace GlusterFS

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1, **[2]** - platí pouze pro uzel 2, **[3]** - platí pouze pro uzel 3.

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí

   <pre><code># IP addresses of the Gluster nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   </code></pre>

1. **[A]** Registr

   Zaregistrujte své virtuální počítače a připojte je do fondu, který obsahuje úložiště pro RHEL 7 a GlusterFS

   <pre><code>sudo subscription-manager register
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

1. **[A]** Povolit repo zúžení GlusterFS

   Chcete-li nainstalovat požadované balíčky, povolte následující úložiště.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rh-gluster-3-for-rhel-7-server-rpms
   </code></pre>
  
1. **[A]** Instalace balíčků GlusterFS

   Nainstalujte tyto balíčky na všechny uzly GlusterFS

   <pre><code>sudo yum -y install redhat-storage-server
   </code></pre>

   Po instalaci restartujte uzly.

1. **[A]** Změnit bránu firewall

   Přidejte pravidla brány firewall, která umožní přenos klienta do uzlů GlusterFS.

   <pre><code># list the available zones
   firewall-cmd --get-active-zones
   
   sudo firewall-cmd --zone=public --add-service=glusterfs --permanent
   sudo firewall-cmd --zone=public --add-service=glusterfs
   </code></pre>

1. **[A]** Povolení a spuštění služby GlusterFS

   Spusťte službu GlusterFS na všech uzlech.

   <pre><code>sudo systemctl start glusterd
   sudo systemctl enable glusterd
   </code></pre>

1. **[1]** Vytvořit GluserFS

   Vytvoření clusteru GlusterFS spuštěním následujících příkazů

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

1. **[2]** Test peer status

   Testování stavu partnera na druhém uzlu

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

1. **[3]** Test stavu partnera

   Testování stavu partnera na třetím uzlu

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

1. **[A]** Vytvořit LVM

   V tomto příkladu se GlusterFS používá pro dva systémy SAP, NW1 a NW2. K vytvoření konfigurací LVM pro tyto systémy SAP použijte následující příkazy.

   Tyto příkazy použijte pro NW1

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

   Tyto příkazy použijte pro NW2

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

1. **[1]** Vytvoření distribuovaného svazku

   Pomocí následujících příkazů vytvořte svazek GlusterFS pro NW1 a spusťte jej.

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

   Pomocí následujících příkazů vytvořte objem GlusterFS pro NW2 a spusťte jej.

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

## <a name="next-steps"></a>Další kroky

* [Instalace SAP ASCS a databáze](high-availability-guide-rhel.md)
* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
