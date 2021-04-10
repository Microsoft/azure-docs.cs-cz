---
title: Nastavení Pacemaker v SLES v Azure | Microsoft Docs
description: Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure
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
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: aa2006ecfad91e21ac13a1e63be23302b2a70399
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551029"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Existují dvě možnosti, jak nastavit cluster Pacemaker v Azure. Můžete použít buď agenta pro oplocení, který postará restartování uzlu, který selhal, přes rozhraní API Azure, nebo můžete použít zařízení SBD.

Zařízení SBD vyžaduje aspoň jeden další virtuální počítač, který funguje jako cílový server iSCSI a poskytuje zařízení SBD. Tyto cílové servery iSCSI je možné sdílet s jinými Pacemaker clustery. Výhodou použití zařízení SBD je, pokud už používáte zařízení SBD v místním prostředí, nevyžaduje žádné změny v tom, jak provozovat cluster Pacemaker. Pro cluster Pacemaker můžete použít až tři SBD zařízení, která umožní, aby se zařízení SBD nedostupné, například během oprav operačního systému cílového serveru iSCSI. Pokud chcete používat více než jedno zařízení SBD na Pacemaker, nezapomeňte nasadit několik cílových serverů iSCSI a připojit jeden SBD od každého cílového serveru iSCSI. Doporučujeme použít buď jedno zařízení SBD, nebo tři. Pokud nakonfigurujete jenom dvě zařízení SBD a jedna z nich není dostupná, Pacemaker nebude moct uzel clusteru automaticky navýšit. Pokud chcete být schopni si je vymezit, když jeden cílový server iSCSI nefunguje, musíte použít tři zařízení SBD, a proto tři cílové servery iSCSI, což je nejpružnější konfigurace při použití SBDs.

Agent Azure plot nevyžaduje nasazení dalších virtuálních počítačů.   

![Pacemaker on SLES – přehled](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Když naplánujete a nasazujete clusterované uzly Pacemaker pro Linux a SBD zařízení, je nezbytné zajistit celkovou spolehlivost kompletní konfigurace clusteru, že směrování mezi jednotlivými virtuálními počítači a virtuálními počítači hostujícími zařízení SBD neprojde žádná jiná zařízení, jako je [Síťová virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/). V opačném případě problémy a události údržby s síťové virtuální zařízení můžou mít negativní dopad na stabilitu a spolehlivost celkové konfigurace clusteru. Abyste se vyhnuli takovým překážkám, nedefinujte pravidla směrování pro síťová virtuální zařízení nebo [uživatelsky definovaná pravidla směrování](../../../virtual-network/virtual-networks-udr-overview.md) , která směrují provoz mezi clusterovanými uzly a zařízeními SBD prostřednictvím síťová virtuální zařízení a podobných zařízení při plánování a nasazení clusterových uzlů pro Linux Pacemaker a SBD zařízení. 
>

## <a name="sbd-fencing"></a>SBDé oplocení

Použijte následující postup, pokud chcete zařízení SBD použít ke oplocení.

### <a name="set-up-iscsi-target-servers"></a>Nastavení cílových serverů iSCSI

Nejprve je třeba vytvořit virtuální počítače cíle iSCSI. cílové servery iSCSI je možné sdílet s několika Pacemaker clustery.

1. Nasaďte nové virtuální počítače s SLES 12 SP1 nebo novější a připojte se k nim přes SSH. Počítače nemusí být velké. Velikost virtuálního počítače, jako je Standard_E2s_v3 nebo Standard_D2s_v3, je dostatečná. Ujistěte se, že používáte disk s operačním systémem Premium Storage.

Na všech **virtuálních počítačích cíle iSCSI** spusťte následující příkazy.

1. Aktualizovat SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Po upgradu nebo aktualizaci operačního systému může být nutné restartovat operační systém. 

1. Odebrat balíčky

   Chcete-li se vyhnout známému problému s targetcli a SLES 12 SP3, odinstalujte následující balíčky. Můžete ignorovat chyby balíčků, které se nenašly.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Nainstalovat cílové balíčky iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Povolení služby iSCSI Target

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Vytvoření zařízení iSCSI na cílovém serveru iSCSI

Spusťte následující příkazy na všech **virtuálních počítačích cíle iSCSI** a vytvořte tak disky iSCSI pro clustery, které používají vaše systémy SAP. V následujícím příkladu se vytvoří SBD zařízení pro několik clusterů. Ukáže vám, jak byste pro několik clusterů použili jeden cílový server iSCSI. Zařízení SBD se umístí na disk s operačním systémem. Ujistěte se, že máte dostatek místa.

**`nfs`** slouží k identifikaci clusteru systému souborů NFS, **ascsnw1** se používá k identifikaci clusteru ASCS **NW1**, **dbnw1** se používá k identifikaci databázového clusteru **NW1**, **NFS-0** a **NFS-1** jsou názvy hostitelů uzlů clusteru NFS, **NW1-xscs-0** a **NW1-xscs-1** jsou názvy hostitelů hostitelských uzlů **NW1** ASCS a **NW1-DB-0** a **NW1-DB-1** jsou názvy hostitelů uzlů databázového clusteru. Nahraďte je názvy hostitelů uzlů clusteru a identifikátorem SID vašeho systému SAP.

<pre><code># Create the root folder for all SBD devices
sudo mkdir /sbd

# Create the SBD device for the NFS server
sudo targetcli backstores/fileio create sbdnfs /sbd/sbdnfs 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.nfs.local:nfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/luns/ create /backstores/fileio/sbdnfs
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-0.local:nfs-0</b>
sudo targetcli iscsi/iqn.2006-04.nfs.local:nfs/tpg1/acls/ create iqn.2006-04.<b>nfs-1.local:nfs-1</b>

# Create the SBD device for the ASCS server of SAP System NW1
sudo targetcli backstores/fileio create sbdascs<b>nw1</b> /sbd/sbdascs<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbdascs<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-0.local:nw1-xscs-0</b>
sudo targetcli iscsi/iqn.2006-04.ascs<b>nw1</b>.local:ascs<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-xscs-1.local:nw1-xscs-1</b>

# Create the SBD device for the database cluster of SAP System NW1
sudo targetcli backstores/fileio create sbddb<b>nw1</b> /sbd/sbddb<b>nw1</b> 50M write_back=false
sudo targetcli iscsi/ create iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/luns/ create /backstores/fileio/sbddb<b>nw1</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-0.local:nw1-db-0</b>
sudo targetcli iscsi/iqn.2006-04.db<b>nw1</b>.local:db<b>nw1</b>/tpg1/acls/ create iqn.2006-04.<b>nw1-db-1.local:nw1-db-1</b>

# save the targetcli changes
sudo targetcli saveconfig
</code></pre>

Můžete zjistit, jestli se všechno správně nastavilo s

<pre><code>sudo targetcli ls

o- / .......................................................................................................... [...]
  o- backstores ............................................................................................... [...]
  | o- block ................................................................................... [Storage Objects: 0]
  | o- fileio .................................................................................. [Storage Objects: 3]
  | | o- <b>sbdascsnw1</b> ................................................ [/sbd/sbdascsnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbddbnw1</b> .................................................... [/sbd/sbddbnw1 (50.0MiB) write-thru activated]
  | | | o- alua .................................................................................... [ALUA Groups: 1]
  | | |   o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | | o- <b>sbdnfs</b> ........................................................ [/sbd/sbdnfs (50.0MiB) write-thru activated]
  | |   o- alua .................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ........................................................ [ALUA state: Active/optimized]
  | o- pscsi ................................................................................... [Storage Objects: 0]
  | o- ramdisk ................................................................................. [Storage Objects: 0]
  o- iscsi ............................................................................................. [Targets: 3]
  | o- <b>iqn.2006-04.ascsnw1.local:ascsnw1</b> .................................................................. [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-xscs-0.local:nw1-xscs-0</b> ............................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-xscs-1.local:nw1-xscs-1</b> ............................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 ............................................................ [lun0 fileio/<b>sbdascsnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .......................................... [fileio/sbdascsnw1 (/sbd/sbdascsnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.dbnw1.local:dbnw1</b> ...................................................................... [TPGs: 1]
  | | o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  | |   o- acls ........................................................................................... [ACLs: 2]
  | |   | o- <b>iqn.2006-04.nw1-db-0.local:nw1-db-0</b> ................................................... [Mapped LUNs: 1]
  | |   | | o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   | o- <b>iqn.2006-04.nw1-db-1.local:nw1-db-1</b> ................................................... [Mapped LUNs: 1]
  | |   |   o- mapped_lun0 .............................................................. [lun0 fileio/<b>sbddbnw1</b> (rw)]
  | |   o- luns ........................................................................................... [LUNs: 1]
  | |   | o- lun0 .............................................. [fileio/sbddbnw1 (/sbd/sbddbnw1) (default_tg_pt_gp)]
  | |   o- portals ..................................................................................... [Portals: 1]
  | |     o- 0.0.0.0:3260 ...................................................................................... [OK]
  | o- <b>iqn.2006-04.nfs.local:nfs</b> .......................................................................... [TPGs: 1]
  |   o- tpg1 ................................................................................ [no-gen-acls, no-auth]
  |     o- acls ........................................................................................... [ACLs: 2]
  |     | o- <b>iqn.2006-04.nfs-0.local:nfs-0</b> ......................................................... [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     | o- <b>iqn.2006-04.nfs-1.local:nfs-1</b> ......................................................... [Mapped LUNs: 1]
  |     |   o- mapped_lun0 ................................................................ [lun0 fileio/<b>sbdnfs</b> (rw)]
  |     o- luns ........................................................................................... [LUNs: 1]
  |     | o- lun0 .................................................. [fileio/sbdnfs (/sbd/sbdnfs) (default_tg_pt_gp)]
  |     o- portals ..................................................................................... [Portals: 1]
  |       o- 0.0.0.0:3260 ...................................................................................... [OK]
  o- loopback .......................................................................................... [Targets: 0]
  o- vhost ............................................................................................. [Targets: 0]
  o- xen-pvscsi ........................................................................................ [Targets: 0]
</code></pre>

### <a name="set-up-sbd-device"></a>Nastavení zařízení SBD

Připojte se k zařízení iSCSI, které bylo vytvořeno v posledním kroku z clusteru.
Na uzlech nového clusteru, který chcete vytvořit, spusťte následující příkazy.
Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** připojit k zařízením iSCSI

   Nejdřív povolte služby iSCSI a SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** změnit název iniciátoru na prvním uzlu

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změňte obsah souboru tak, aby odpovídal seznamům ACL, které jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI, například pro server NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** změnit název iniciátoru na druhém uzlu

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změňte obsah souboru tak, aby odpovídal seznamům ACL, které jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** restartování služby iSCSI

   Nyní restartujte službu iSCSI, aby se změna projevila.

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Připojte zařízení iSCSI. V následujícím příkladu je 10.0.0.17 IP adresa cílového serveru iSCSI a 3260 je výchozí port. <b>IQN. 2006-04. NFS. local: NFS</b> je jeden z cílových názvů, který je uveden při spuštění prvního příkazu níže (iscsiadm-m Discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> -T <b>iqn.2006-04.nfs.local:nfs</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Ujistěte se, že jsou k dispozici zařízení iSCSI, a poznamenejte si název zařízení (v následujícím příkladu/dev/SDE).

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Teď načtěte ID zařízení iSCSI.

   <pre><code>ls -l /dev/disk/by-id/scsi-* | grep <b>sdd</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03 -> ../../sdd</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:20 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_afb0ba8d-3a3c-413b-8cc2-cca03e63ef42 -> ../../sdd
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sde</b>
   
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-1LIO-ORG_cl1:3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   # <b>lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df -> ../../sde</b>
   # lrwxrwxrwx 1 root root  9 Feb  7 12:39 /dev/disk/by-id/scsi-SLIO-ORG_cl1_3fe4da37-1a5a-4bb6-9a41-9a4df57770e4 -> ../../sde
   
   ls -l /dev/disk/by-id/scsi-* | grep <b>sdf</b>
   
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-1LIO-ORG_sbdnfs:f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   # <b>lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf -> ../../sdf</b>
   # lrwxrwxrwx 1 root root  9 Aug  9 13:32 /dev/disk/by-id/scsi-SLIO-ORG_sbdnfs_f88f30e7-c968-4678-bc87-fe7bfcbdb625 -> ../../sdf
   </code></pre>

   Seznam příkazů pro každé zařízení SBD se třemi identifikátory zařízení. Doporučujeme použít ID, které začíná na rozhraní SCSI-3, v předchozím příkladu je to

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** vytvoření zařízení SBD

   Pomocí ID zařízení zařízení iSCSI vytvořte nová zařízení SBD na prvním uzlu clusteru.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** přizpůsobení konfigurace SBD

   Otevřít konfigurační soubor SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Změňte vlastnost zařízení SBD, povolte integraci Pacemaker a změňte režim spuštění SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   </code></pre>

   Vytvoření `softdog` konfiguračního souboru

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Teď načtěte modul.

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalace clusteru

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** aktualizace SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** instalace komponenty potřebné pro prostředky clusteru

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** instalace součásti Azure-pro clustery, která je potřeba pro prostředky clusteru

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Zkontrolujte verzi balíčku Resource-Agents a ujistěte se, že jsou splněny minimální požadavky na verzi:  
   > - Pro SLES 12 SP4/SP5 musí být ve verzi aspoň Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 musí být verze aspoň Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  

1. **[A]** konfigurace operačního systému

   V některých případech Pacemaker vytvoří mnoho procesů a tím vyčerpá povolený počet procesů. V takovém případě může dojít k selhání prezenčního signálu mezi uzly clusteru a výsledkem převzetí služeb při selhání vašich prostředků. Doporučujeme zvýšit maximální povolený počet procesů nastavením následujícího parametru.

   <pre><code># Edit the configuration file
   sudo vi /etc/systemd/system.conf
   
   # Change the DefaultTasksMax
   #DefaultTasksMax=512
   DefaultTasksMax=4096
   
   #and to activate this setting
   sudo systemctl daemon-reload
   
   # test if the change was successful
   sudo systemctl --no-pager show | grep DefaultTasksMax
   </code></pre>

   Snižte velikost nečisté mezipaměti. Další informace najdete v tématu [nízký výkon zápisu na serverech SLES 11/12 s velkou pamětí RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** konfigurace Cloud-netconfig – Azure pro cluster ha

   >[!NOTE]
   > Ověřte nainstalovanou verzi balíčku **Cloud-netconfig-Azure** spuštěním **zypperu info Cloud-netconfig-Azure**. Pokud je verze ve vašem prostředí 1,3 nebo vyšší, není už nutné potlačit správu síťových rozhraní modulem plug-in cloudové sítě. Pokud je verze nižší než 1,3, doporučujeme aktualizace balíčku **Cloud-netconfig-Azure** na nejnovější dostupnou verzi.  

   Změňte konfigurační soubor pro síťové rozhraní, jak je vidět níže, aby modul plug-in cloudové sítě neodebral virtuální IP adresu (Pacemaker musí řídit přiřazení VIP). Další informace najdete v tématu [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** povolit přístup přes SSH

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** povolit přístup přes SSH

   <pre><code>
   sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # insert the public key you copied in the last step into the authorized keys file on the second server
   sudo vi /root/.ssh/authorized_keys   
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[1]** povolit přístup přes SSH

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** nainstalujte balíček agentů plotu, pokud používáte zařízení STONITH založené na agentu Azure plot.  
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Nainstalovaná verze **ochranného balíčku – agenti** musí být aspoň **4.4.0**  , aby mohli využívat rychlejší časy převzetí služeb při selhání s agentem Azure plot, pokud je potřeba vytvořit uzly clusteru. Pokud používáte nižší verzi, doporučujeme balíček aktualizovat.  


1. **[A]** instalace sady Azure Python SDK 
   - V SLES 12 SP4 nebo SLES 12 SP5
   <pre><code>
    # You may need to activate the Public cloud extention first
    SUSEConnect -p sle-module-public-cloud/12/x86_64
    sudo zypper install python-azure-mgmt-compute
   </code></pre> 

   - V SLES 15 a vyšších 
   <pre><code>
    # You may need to activate the Public cloud extention first. In this example the SUSEConnect command is for SLES 15 SP1
    SUSEConnect -p sle-module-public-cloud/15.1/x86_64
    sudo zypper install python3-azure-mgmt-compute
   </code></pre> 
 
   >[!IMPORTANT]
   >V závislosti na vaší verzi a typu image možná budete muset před instalací Azure Python SDK aktivovat rozšíření veřejného cloudu pro vaši verzi operačního systému.
   >Rozšíření můžete ověřit spuštěním SUSEConnect---rozšíření seznamu.  
   >Chcete-li dosáhnout rychlejšího převzetí služeb při selhání pomocí agenta Azure Plot:
   > - v SLES 12 SP4 nebo SLES 12 SP5 nainstalujte verzi **4.6.2** nebo vyšší z balíčku Python – Azure-Správa – Compute.  
   > - v SLES 15 nainstalujte verzi **4.6.2** nebo vyšší z balíčku Python **3**– Azure-Správa – Compute. 

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   >[!IMPORTANT]
   > Pokud používáte názvy hostitelů v konfiguraci clusteru, je důležité mít spolehlivé rozlišení názvu hostitele. Pokud názvy nejsou k dispozici a to může vést k prodlevám při převzetí služeb při selhání clusteru, komunikace clusteru selže.
   > Výhodou použití/etc/hosts je to, že váš cluster bude nezávislý na službě DNS, což může být jen jeden bod selhání.  
     
   <pre><code>sudo vi /etc/hosts

   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** nainstalovat cluster
- Pokud se pro oplocení používá zařízení SBD
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

- Pokud *nepoužíváte* zařízení SBD pro oplocení
   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # Do you wish to use SBD (y/n)? <b>n</b>
   #WARNING: Not configuring SBD - STONITH will be disabled.
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** přidat uzel do clusteru

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Změna hesla hacluster na stejné heslo

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** upravit nastavení Corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Pokud tyto hodnoty nejsou nebo jsou odlišné, přidejte do souboru následující tučný obsah. Ujistěte se, že jste token změnili na 30000, aby se zajistila údržba paměti. Další informace najdete v [tomto článku pro Linux][virtual-machines-linux-maintenance] nebo [Windows][virtual-machines-windows-maintenance].

   <pre><code>[...]
     <b>token:          30000
     token_retransmits_before_loss_const: 10
     join:           60
     consensus:      36000
     max_messages:   20</b>
     
     interface { 
        [...] 
     }
     transport:      udpu
   } 
   nodelist {
     node {
      ring0_addr:10.0.0.6
     }
     node {
      ring0_addr:10.0.0.7
     } 
   }
   logging {
     [...]
   }
   quorum {
        # Enable and configure quorum subsystem (default: off)
        # see also corosync.conf.5 and votequorum.5
        provider: corosync_votequorum
        <b>expected_votes: 2</b>
        <b>two_node: 1</b>
   }
   </code></pre>

   Pak restartujte službu Corosync.

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Výchozí konfigurace Pacemaker pro SBD

Konfigurace v této části je platná, pouze pokud používáte SBD STONITH.  

1. **[1]** povolit použití zařízení STONITH a nastavit zpoždění plotu

<pre><code>sudo crm configure property stonith-timeout=144
sudo crm configure property stonith-enabled=true

# List the resources to find the name of the SBD device
sudo crm resource list
sudo crm resource stop stonith-sbd
sudo crm configure delete <b>stonith-sbd</b>
sudo crm configure primitive <b>stonith-sbd</b> stonith:external/sbd \
   params pcmk_delay_max="15" \
   op monitor interval="15" timeout="15"
</code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Vytvoření zařízení STONITH s agentem Azure plot

Tato část dokumentace je k dispozici, pouze pokud používáte STONITH, a to na základě agenta Azure plot.
Zařízení STONITH používá instanční objekt k autorizaci proti Microsoft Azure. Pomocí těchto kroků můžete vytvořit instanční objekt.

1. Přejděte na <https://portal.azure.com>.
1. Otevřete okno Azure Active Directory  
   Přejděte na vlastnosti a zapište ID adresáře. Toto je **ID tenanta**.
1. Klikněte na Registrace aplikací
1. Klikněte na nová registrace.
1. Zadejte název, vyberte účty pouze v tomto adresáři organizace. 
2. Vyberte typ aplikace "Web", zadejte adresu URL pro přihlášení (například http: \/ /localhost) a klikněte na Přidat.  
   Přihlašovací adresa URL se nepoužívá a může to být libovolná platná adresa URL.
1. Vyberte certifikáty a tajné klíče a pak klikněte na nový tajný klíč klienta.
1. Zadejte popis nového klíče, vyberte možnost "nikdy vyprší platnost" a klikněte na tlačítko Přidat.
1. Zapište hodnotu. Používá se jako **heslo** instančního objektu.
1. Vyberte Přehled. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno objektu služby.

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** vytvoření vlastní role pro agenta plotu

Objekt služby nemá ve výchozím nastavení oprávnění pro přístup k prostředkům Azure. Musíte přidělit instančnímu objektu oprávnění ke spouštění a zastavování (navrácení) všech virtuálních počítačů v clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete ji vytvořit pomocí [PowerShellu](../../../role-based-access-control/custom-roles-powershell.md#create-a-custom-role) nebo rozhraní příkazového [řádku Azure CLI](../../../role-based-access-control/custom-roles-cli.md) .

Pro vstupní soubor použijte následující obsah. Je potřeba upravit obsah pro vaše předplatná, která jsou, nahraďte c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 ID vašeho předplatného. Pokud máte jenom jedno předplatné, odeberte druhou položku v AssignableScopes.

```json
{
    "properties": {
        "roleName": "Linux Fence Agent Role",
        "description": "Allows to power-off and start virtual machines",
        "assignableScopes": [
            "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
            "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
        ],
        "permissions": [
            {
                "actions": [
                    "Microsoft.Compute/*/read",
                    "Microsoft.Compute/virtualMachines/powerOff/action",
                    "Microsoft.Compute/virtualMachines/start/action"
                ],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ]
    }
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** přiřazení vlastní role k instančnímu objektu

Přiřaďte vlastní roli "role ochrany systému Linux" vytvořenou v poslední kapitole objektu služby. Tuto roli vlastníka už nepoužívejte!

1. přejděte na [https://portal.azure.com](https://portal.azure.com)
1. Otevřete okno všechny prostředky.
1. Vyberte virtuální počítač prvního uzlu clusteru.
1. Klikněte na řízení přístupu (IAM).
1. Klikněte na přidat přiřazení role.
1. Vyberte roli "role agenta pro ochrannou část Linux".
1. Zadejte název aplikace, kterou jste vytvořili výše.
1. Kliknutí na Uložit

Opakujte výše uvedené kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]** vytvoření zařízení STONITH

Po úpravě oprávnění pro virtuální počítače můžete nakonfigurovat zařízení STONITH v clusteru.

> [!NOTE]
> Možnost ' pcmk_host_map ' je požadována pouze v příkazu, pokud názvy hostitelů a názvy virtuálních počítačů Azure nejsou stejné. Zadejte mapování ve formátu název **hostitele: VM-Name**.
> Přečtěte si část tučné v příkazu.

<pre><code>sudo crm configure property stonith-enabled=true
crm configure property concurrent-fencing=true
# replace the bold string with your subscription ID, resource group of the VM, tenant ID, service principal application ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
  params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>application ID</b>" passwd="<b>password</b>" \
  pcmk_monitor_retries=4 pcmk_action_limit=3 power_timeout=240 pcmk_reboot_timeout=900 <b>pcmk_host_map="prod-cl1-0:prod-cl1-0-vm-name;prod-cl1-1:prod-cl1-1-vm-name"</b> \
  op monitor interval=3600 timeout=120

sudo crm configure property stonith-timeout=900

</code></pre>

> [!IMPORTANT]
> Operace monitorování a oplocení jsou rozserializovány. Výsledkem je, že pokud už existuje již běžící operace monitorování a současná událost, dojde k převzetí služeb při selhání clusteru z důvodu již běžící operace monitorování.

> [!TIP]
>Agent Azure plotu vyžaduje odchozí připojení k veřejným koncovým bodům, jak je popsáno, spolu s možnými řešeními ve veřejných koncových bodech [pro virtuální počítače s využitím Standard interního nástroje](./high-availability-guide-standard-load-balancer-outbound-connections.md).  

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Konfigurace Pacemaker pro plánované události Azure

Azure nabízí [naplánované události](../../linux/scheduled-events.md). Naplánované události se poskytují prostřednictvím služby meta-data Service a umožňují, aby se aplikace připravila na události, jako je třeba vypnutí virtuálního počítače, opětovné nasazení virtuálního počítače atd. Agenti prostředků **[Azure – monitorování událostí](https://github.com/ClusterLabs/resource-agents/pull/1161)** pro plánované události Azure Pokud se zjistí události, Agent se pokusí zastavit všechny prostředky na ovlivněném virtuálním počítači a přesunout je do jiného uzlu v clusteru. Aby bylo možné dosáhnout dalších prostředků Pacemaker, musí být nakonfigurovány. 

1. **[A]** Ujistěte se, že balíček pro agenta **Azure-Events** je už nainstalovaný a aktuální. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** nakonfigurujte prostředky v Pacemaker. 

<pre><code>
#Place the cluster in maintenance mode
sudo crm configure property maintenance-mode=true

#Create Pacemaker resources for the Azure agent
sudo crm configure primitive rsc_azure-events ocf:heartbeat:azure-events op monitor interval=10s
sudo crm configure clone cln_azure-events rsc_azure-events

#Take the cluster out of maintenance mode
sudo crm configure property maintenance-mode=false
</code></pre>

   > [!NOTE]
   > Po nakonfigurování prostředků Pacemaker pro agenta Azure-Events při umístění clusteru do režimu údržby nebo z něj dostanete varovné zprávy, jako například:  
     Upozornění: CIB-Bootstrap-Options: neznámý atribut hostName_ <strong>název hostitele</strong>  
     Upozornění: CIB-Bootstrap-Options: neznámý atribut ' Azure-events_globalPullState '  
     Upozornění: CIB-Bootstrap-Options: neznámý atribut hostName_ <strong>název hostitele</strong>  
   > Tyto zprávy upozornění je možné ignorovat.

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server][sles-nfs-guide]
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP][sles-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
