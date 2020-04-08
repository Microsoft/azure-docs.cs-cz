---
title: Nastavení kardiostimulátoru na SLES v Azure | Dokumenty společnosti Microsoft
description: Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure
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
ms.date: 04/07/2020
ms.author: radeltch
ms.openlocfilehash: 06ee1b6184e69ace68adcbfa36ad2384dc9fdd99
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811575"
---
# <a name="setting-up-pacemaker-on-suse-linux-enterprise-server-in-azure"></a>Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[virtual-machines-linux-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[virtual-machines-windows-maintenance]:../../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot
[sles-nfs-guide]:high-availability-guide-suse-nfs.md
[sles-guide]:high-availability-guide-suse.md

Existují dvě možnosti nastavení clusteru Pacemaker v Azure. Můžete buď použít agenta oplocení, který se postará o restartování neúspěšného uzlu prostřednictvím rozhraní AZURE API, nebo můžete použít zařízení SBD.

Zařízení SBD vyžaduje alespoň jeden další virtuální počítač, který funguje jako cílový server iSCSI a poskytuje zařízení SBD. Tyto cílové servery iSCSI však mohou být sdíleny s jinými clustery Pacemaker. Výhodou použití zařízení SBD je rychlejší převzetí služeb při selhání a pokud používáte zařízení SBD místně, nevyžaduje žádné změny ve způsobu, jakým cluster kardiostimulátoru funguje. Pro cluster Kardiostimulátoru můžete použít až tři zařízení SBD, která umožní, aby zařízení SBD bylo nedostupné, například během opravy operačního systému cílového serveru iSCSI. Pokud chcete použít více než jedno zařízení SBD na kardiostimulátoru, nezapomeňte nasadit více cílových serverů iSCSI a připojit jeden SBD z každého cílového serveru iSCSI. Doporučujeme použít buď jedno zařízení SBD nebo tři. Kardiostimulátor nebude moci automaticky plot uzlu clusteru, pokud nakonfigurujete pouze dvě zařízení SBD a jeden z nich není k dispozici. Pokud chcete mít možnost plot, když jeden cílový server iSCSI je dole, musíte použít tři zařízení SBD a proto tři cílové servery iSCSI.

Pokud nechcete investovat do jednoho dalšího virtuálního počítače, můžete také použít agenta Azure Fence. Nevýhodou je, že převzetí služeb při selhání může trvat 10 až 15 minut, pokud dojde k selhání zastavení prostředku nebo uzly clusteru již nemohou komunikovat, které navzájem.

![Kardiostimulátor na SLES přehled](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Při plánování a nasazování clusterovaných uzlů Linux pacemakeru a zařízení SBD je pro celkovou spolehlivost kompletní konfigurace clusteru nezbytné, aby směrování mezi zapojenými virtuálními počítači a virtuálními počítači hostujícími zařízení SBD neprocházelo žádnými jinými zařízeními, jako jsou [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/). V opačném případě mohou mít problémy a události údržby se službou NVA negativní dopad na stabilitu a spolehlivost celkové konfigurace clusteru. Abyste se těmto překážkám vyhnuli, nedefinujte pravidla směrování pro síť ová l.A nebo [uživatelem definovaná pravidla směrování,](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) která směrují provoz mezi clusterovanými uzly a zařízeními SBD prostřednictvím zařízení NVA a podobných zařízení při plánování a nasazování clusterovaných uzlů linuxového kardiostimulátoru a zařízení SBD. 
>

## <a name="sbd-fencing"></a>Oplocení SBD

Pokud chcete pro oplocení použít zařízení SBD, postupujte takto.

### <a name="set-up-iscsi-target-servers"></a>Nastavení cílových serverů iSCSI

Nejprve je třeba vytvořit cílové virtuální počítače iSCSI. Cílové servery iSCSI lze sdílet s více clustery Pacemaker.

1. Nasaďte nové virtuální počítače SLES 12 SP1 nebo vyšší a připojte se k nim přes ssh. Stroje nemusí být velké. Velikost virtuálního počítače, jako je Standard_E2s_v3 nebo Standard_D2s_v3, je dostatečná. Ujistěte se, že používáte úložiště Premium na disku operačního systému.

Spusťte následující příkazy na všech **cílových virtuálních počítačích iSCSI**.

1. Aktualizovat SLES

   <pre><code>sudo zypper update
   </code></pre>

   > [!NOTE]
   > Po upgradu nebo aktualizaci operačního systému může být nutné operační systém restartovat. 

1. Odebrání balíčků

   Chcete-li se vyhnout známému problému s targetcli a SLES 12 SP3, odinstalujte následující balíčky. Můžete ignorovat chyby u balíčků, které nelze najít.

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalace cílových balíčků iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Povolení cílové služby iSCSI

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Vytvoření zařízení iSCSI na cílovém serveru iSCSI

Spusťte následující příkazy na všech **cílových virtuálních počítačích iSCSI** a vytvořte disky iSCSI pro clustery používané systémy SAP. V následujícím příkladu jsou vytvořeny zařízení SBD pro více clusterů. Ukazuje, jak byste použili jeden cílový server iSCSI pro více clusterů. Zařízení SBD jsou umístěna na disku operačního systému. Ujistěte se, že máte dostatek místa.

**`nfs`** slouží k identifikaci clusteru nfs, **ascsnw1** se používá k identifikaci clusteru ASCS **NW1**, **dbnw1** se používá k identifikaci databázového clusteru **NW1**, **NFS-0** a **NFS-1** jsou názvy hostitelů uzlů clusteru NFS, **nw1-xscs-0** a **nw1-xscs-1** jsou názvy hostitelů uzlů clusteru **NW1** ASCS a **nw1-db-0** a **nw1-db-1** jsou názvy hostitelů uzlů clusteru databáze. Nahraďte je názvy hostitelů uzlů clusteru a SID systému SAP.

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

Můžete zkontrolovat, zda bylo vše správně nastaveno pomocí

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
Spusťte následující příkazy v uzlech nového clusteru, který chcete vytvořit.
Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Připojení k zařízením iSCSI

   Nejprve povolte služby iSCSI a SBD.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]** Změna názvu iniciátoru v prvním uzlu

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změňte obsah souboru tak, aby odpovídal seznamu ACL, který jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI, například pro server systému souborů NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]** Změna názvu iniciátoru na druhém uzlu

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změna obsahu souboru tak, aby odpovídal seznamu ACL, který jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]** Restartujte službu iSCSI

   Nyní restartujte službu iSCSI a použijte změnu

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Připojte zařízení iSCSI. V níže uvedeném příkladu je 10.0.0.17 IP adresa cílového serveru iSCSI a 3260 je výchozí port. <b>iqn.2006-04.nfs.local:nfs</b> je jedním z cílových názvů, který je uveden při spuštění prvního příkazu níže (iscsiadm -m discovery).

   <pre><code>sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.17:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.17:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.17:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the second iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.18:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.18:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.18:3260</b> --op=update --name=node.startup --value=automatic
   
   # If you want to use multiple SBD devices, also connect to the third iSCSI target server
   sudo iscsiadm -m discovery --type=st --portal=<b>10.0.0.19:3260</b>   
   sudo iscsiadm -m node -T <b>iqn.2006-04.nfs.local:nfs</b> --login --portal=<b>10.0.0.19:3260</b>
   sudo iscsiadm -m node -p <b>10.0.0.19:3260</b> --op=update --name=node.startup --value=automatic
   </code></pre>

   Ujistěte se, že zařízení iSCSI jsou k dispozici a poznamenejte si název zařízení (v následujícím příkladu /dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Nyní načtěte ID zařízení iSCSI.

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

   Příkaz uvádí tři ID zařízení pro každé zařízení SBD. Doporučujeme používat ID, které začíná scsi-3, v příkladu výše je to

   * **/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]** Vytvoření zařízení SBD

   Pomocí ID zařízení zařízení iSCSI vytvořte nová zařízení SBD na prvním uzlu clusteru.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]** Přizpůsobení konfigurace SBD

   Otevření konfiguračního souboru SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Změňte vlastnost zařízení SBD, povolte integraci kardiostimulátoru a změňte režim spuštění sbd.

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

   Nyní načtěte modul

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalace clusteru

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Aktualizace SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** Instalace komponenty potřebné pro prostředky clusteru

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** Instalace komponenty azure-lb potřebné pro prostředky clusteru

   <pre><code>sudo zypper in resource-agents
   </code></pre>

   > [!NOTE]
   > Zkontrolujte verzi agenty prostředků balíčku a ujistěte se, že jsou splněny minimální požadavky na verzi:  
   > - Pro SLES 12 SP4/SP5 verze musí být alespoň agenty prostředků-4.3.018.a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 verze musí být alespoň agenty prostředků-4.3.0184.6ee15eb2-4.13.1.  

1. **[A]** Konfigurace operačního systému

   V některých případech kardiostimulátor vytváří mnoho procesů a tím vyčerpává povolený počet procesů. V takovém případě může dojít k selhání prezenčního signálu mezi uzly clusteru a vést k převzetí služeb při selhání prostředků. Doporučujeme zvýšit maximální povolené procesy nastavením následujícího parametru.

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

   Zmenšete velikost nečisté mezipaměti. Další informace naleznete [v tématu Nízký výkon zápisu na serverech SLES 11/12 s velkou paměti RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** Konfigurace cloud-netconfig-azure pro cluster HA

   >[!NOTE]
   > Zkontrolujte nainstalovanou verzi balíčku **cloud-netconfig-azure** spuštěním **zypper info cloud-netconfig-azure**. Pokud je verze ve vašem prostředí 1.3 nebo vyšší, již není nutné potlačit správu síťových rozhraní pomocí modulu plug-in cloudové sítě. Pokud je verze nižší než 1.3, doporučujeme aktualizovat balíček **cloud-netconfig-azure** na nejnovější dostupnou verzi.  

   Změňte konfigurační soubor pro síťové rozhraní, jak je znázorněno níže, abyste zabránili modulu plug-in cloudové sítě v odebrání virtuální IP adresy (Pacemaker musí řídit přiřazení VIP). Další informace naleznete [v tématu SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]** Povolit přístup ssh

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]** Povolit přístup ssh

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

1. **[1]** Povolit přístup ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]** Instalace agentů plotu
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Pokud používáte Suse Linux Enterprise Server pro SAP 15, uvědomte si, že je třeba aktivovat další modul a nainstalovat další součást, která je předpokladem pro použití Azure Fence Agent. Další informace o modulech a rozšířeních SUSE naleznete [v tématu Moduly a rozšíření vysvětleno](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Chcete-li nainstalovat azure python sdk, postupujte podle níže uvedených pokynů. 

   Následující pokyny k instalaci sady Azure Python SDK jsou použitelné jenom pro Suse Enterprise Server pro SAP **15**.  

    - Pokud používáte předplatné Bring-Your-Own-Subscription, postupujte podle těchto pokynů  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Pokud používáte předplatné s průběžným platbou, postupujte podle těchto pokynů  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech. Výhodou použití /etc/hosts je, že váš cluster se stane nezávislým na DNS, což může být také jediný bod selhání.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]** Instalace clusteru

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]** Přidání uzlu do clusteru

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]** Změna hesla hacluster na stejné heslo

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Upravte nastavení korosynchronizace.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Pokud hodnoty neexistují nebo se liší, přidejte do souboru následující tučný obsah. Ujistěte se, že změnit token na 30000 povolit údržbu zachování paměti. Další informace naleznete v [tomto článku pro Linux][virtual-machines-linux-maintenance] nebo [Windows][virtual-machines-windows-maintenance].

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

   Potom restartujte službu corosync

   <pre><code>sudo service corosync restart
   </code></pre>

## <a name="create-azure-fence-agent-stonith-device"></a>Vytvoření zařízení Agent a určovatel Azure Fence STONITH

Zařízení STONITH používá instanční objekt k autorizaci proti Microsoft Azure. Podle těchto kroků vytvořte instanční objekt.

1. Přejděte na <https://portal.azure.com>.
1. Otevření okna Služby Azure Active Directory  
   Přejděte na vlastnosti a poznamenejte si ID adresáře. Toto je **ID klienta**.
1. Klikněte na Registrace aplikací.
1. Klikněte na Nová registrace.
1. Zadejte název, vyberte možnost Účty pouze v tomto adresáři organizace. 
2. Vyberte typ aplikace "Web", zadejte přihlašovací adresu\/URL (například http: /localhost) a klepněte na tlačítko Přidat  
   Přihlašovací adresa URL se nepoužívá a může se jedná o libovolnou platnou adresu URL.
1. Vyberte certifikáty a tajné klíče a klikněte na Nový tajný klíč klienta.
1. Zadejte popis nového klíče, vyberte "Nikdy nevyprší" a klikněte na Přidat.
1. Poznamenejte si hodnotu. Používá se jako **heslo** pro instanční objekt
1. Vyberte Přehled. Poznamenejte si ID aplikace. Používá se jako uživatelské jméno **(login ID** v níže uvedených krocích) instančního objektu

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]** Vytvoření vlastní role pro agenta plotu

Instanční objekt nemá oprávnění k přístupu k prostředkům Azure ve výchozím nastavení. Je třeba udělit instanční objekt oprávnění ke spuštění a zastavení (navrátit) všechny virtuální počítače clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete ji vytvořit pomocí [PowerShellu](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) nebo [Azure CLI](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Pro vstupní soubor použijte následující obsah. Obsah je třeba přizpůsobit vašim předplatným, které je, nahradit c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 s ID vašeho předplatného. Pokud máte pouze jedno předplatné, odeberte druhou položku v AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to deallocate and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/deallocate/action",
    "Microsoft.Compute/virtualMachines/start/action", 
    "Microsoft.Compute/virtualMachines/powerOff/action" 
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Přiřazení vlastní role instančnímu objektu service instančního objektu

Přiřaďte vlastní roli "Linux Fence Agent Role", která byla vytvořena v poslední kapitole instančního objektu. Už nepoužívejte roli vlastníka!

1. Přejít na[https://portal.azure.com](https://portal.azure.com)
1. Otevření okna Všechny prostředky
1. Výběr virtuálního počítače prvního uzlu clusteru
1. Klikněte na ovládací prvek přístupu (IAM)
1. Klikněte na Přidat přiřazení role.
1. Vyberte roli "Linux Fence Agent Role"
1. Zadejte název aplikace, kterou jste vytvořili výše.
1. Kliknutí na Uložit

Opakujte výše uvedené kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]** Vytvoření zařízení STONITH

Po úpravě oprávnění pro virtuální počítače můžete nakonfigurovat zařízení STONITH v clusteru.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Výchozí konfigurace kardiostimulátoru pro SBD

1. **[1]** Povolte použití zařízení STONITH a nastavte zpoždění plotu

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

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Konfigurace kardiostimulátoru pro naplánované události Azure

Azure nabízí [naplánované události](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Naplánované události jsou poskytovány prostřednictvím služby metadat dat a umožňují aplikaci čas na přípravu na události, jako je vypnutí virtuálního počítače, opětovné nasazení virtuálního počítače atd. Agent prostředků **[azure-události](https://github.com/ClusterLabs/resource-agents/pull/1161)** monitoruje naplánované události Azure. Pokud jsou zjištěny události, agent se pokusí zastavit všechny prostředky na ovlivněném virtuálním počítači a přesunout je do jiného uzlu v clusteru. Chcete-li dosáhnout, že další kardiostimulátor prostředky musí být nakonfigurován. 

1. **[A]** Ujistěte se, že balíček pro agenta **azure událostí** je již nainstalován a aktuální. 

<pre><code>sudo zypper info resource-agents
</code></pre>

2. **[1]** Konfigurace prostředků v kardiostimulátoru. 

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
   > Po konfiguraci prostředků kardiostimulátoru pro agenta Azure událostí, když umístíte cluster do nebo z režimu údržby, může se zobrazí varovné zprávy, jako je:  
     UPOZORNĚNÍ: cib-bootstrap-options: neznámý atribut 'hostName_ <strong>název hostitele</strong>'  
     UPOZORNĚNÍ: cib-bootstrap-options: neznámý atribut 'azure-events_globalPullState'  
     UPOZORNĚNÍ: cib-bootstrap-options: neznámý atribut 'hostName_ <strong>název hostitele</strong>'  
   > Tyto varovné zprávy mohou být ignorovány.

## <a name="next-steps"></a>Další kroky

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* [Vysoká dostupnost pro systém windows na virtuálních počítačích Azure na SUSE Linux Enterprise Server][sles-nfs-guide]
* [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP][sles-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
