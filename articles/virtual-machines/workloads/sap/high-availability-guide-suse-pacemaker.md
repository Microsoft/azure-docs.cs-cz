---
title: Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure | Dokumentace Microsoftu
description: Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: gwallace
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 32865b84de2dc1c1f8a3fd6beca80a2659f1e3d9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75370761"
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

Existují dvě možnosti, jak nastavit Pacemaker clusteru v Azure. Můžete použít buď agenta monitorování geografických zón, které se postará o restartování neúspěšných uzlu prostřednictvím rozhraní API služby Azure nebo můžete použít SBD zařízení.

Zařízení SBD vyžaduje alespoň jeden další virtuální počítač, který funguje jako cílový server iSCSI a poskytuje SBD zařízení. Může ale být tyto cílové servery iSCSI s další clustery Pacemaker sdílí. Výhodou použití zařízení SBD je rychlejší doba převzetí služeb při selhání a pokud používáte místní zařízení SBD, nevyžaduje žádné změny v tom, jak provozovat cluster Pacemaker. Povolit zařízení s SBD přestanou být dostupné, například při použití dílčích oprav operačního systému cílového serveru iSCSI můžete použít až tři SBD zařízení Pacemaker clusteru. Pokud chcete použít více než jedno zařízení SBD za Pacemaker, nezapomeňte nasadit více cílové servery iSCSI a připojit jeden SBD z každý cílový server iSCSI. Doporučujeme používat jedno zařízení SBD nebo tři. Pacemaker nebude možné automaticky plotu uzlem clusteru, pokud nakonfigurujete dvě SBD zařízení a jeden z nich není k dispozici. Pokud chcete být schopni plotu při jeden cílový server iSCSI je mimo provoz, budete muset použít tři SBD zařízení a proto tři cílové servery iSCSI.

Pokud nechcete investovat do jednoho dalšího virtuálního počítače, můžete také použít agenta Azure plot. Nevýhodou je, že převzetí služeb při selhání můžete provést mezi 10 až 15 minut v případě zdroje stop selže nebo uzlech clusteru nemůže komunikovat které mezi sebou už.

![Pacemaker na SLES – přehled](./media/high-availability-guide-suse-pacemaker/pacemaker.png)

>[!IMPORTANT]
> Při plánování a nasazení Linuxu Pacemaker Clusterované uzly a SBD zařízení, je nezbytné pro celkovou spolehlivost úplná konfigurace clusteru, který součástí směrování mezi virtuálními počítači a virtuálních počítačů, který je hostitelem SBD zařízení neprochází přes jiná zařízení, jako jsou [síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/). V opačném případě problémy a události údržby pomocí síťového virtuálního zařízení může mít negativní dopad na stabilitu a spolehlivost na celkové konfiguraci clusteru. Abyste se vyhnuli takovým překážkám, nedefinujte pravidla směrování pro síťová virtuální zařízení nebo [uživatelsky definovaná pravidla směrování](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) , která směrují provoz mezi clusterovanými uzly a zařízeními SBD prostřednictvím síťová virtuální zařízení a podobných zařízení při plánování a nasazení clusterových uzlů pro Linux Pacemaker a SBD zařízení. 
>

## <a name="sbd-fencing"></a>Monitorování geografických zón SBD

Pokud chcete použít SBD zařízení pro monitorování geografických zón, postupujte podle těchto kroků.

### <a name="set-up-iscsi-target-servers"></a>Nastavení cílové servery iSCSI

Nejdřív je potřeba vytvořit iSCSI target virtuálních počítačů. cílový server iSCSI je sdílet s více clustery Pacemaker.

1. Nasaďte nový SLES 12 SP1 nebo virtuálním počítačům s vyšší a k nim připojit přes ssh. Počítače nemusí být velké. Velikost virtuálního počítače jako Standard_E2s_v3 nebo Standard_D2s_v3 je dostačující. Ujistěte se, že chcete používat Premium storage disk s operačním systémem.

Spusťte následující příkazy na všech **iSCSI target virtuálních počítačů**.

1. Aktualizace SLES

   <pre><code>sudo zypper update
   </code></pre>

1. Odebrání balíčků

   Aby se zabránilo známým problémem se systémem targetcli a SLES 12 SP3, odinstalujte následující balíčky. Můžete ignorovat chyby týkající se balíčky, které se nenašel

   <pre><code>sudo zypper remove lio-utils python-rtslib python-configshell targetcli
   </code></pre>

1. Instalace balíčků cíl iSCSI

   <pre><code>sudo zypper install targetcli-fb dbus-1-python
   </code></pre>

1. Zapněte službu iSCSI target

   <pre><code>sudo systemctl enable targetcli
   sudo systemctl start targetcli
   </code></pre>

### <a name="create-iscsi-device-on-iscsi-target-server"></a>Vytvoření zařízení iSCSI na cílovém serveru iSCSI

Spusťte následující příkazy na všech **iSCSI target virtuálních počítačů** vytvoření disků služby iSCSI pro clusterů, které jsou používány ve vašich systémů SAP. V následujícím příkladu se vytvoří SBD zařízení pro několik clusterů. To se dozvíte, jak byste použili jeden cílový server iSCSI pro několik clusterů. Zařízení SBD umísťují na disk s operačním systémem. Ujistěte se, že máte dostatek místa.

**`nfs`** slouží k identifikaci clusteru NFS, používá se **ascsnw1** k identifikaci clusteru ASCS **NW1**, **dbnw1** se používá k identifikaci databázového clusteru **NW1**, **NFS-0** a **NFS-1** jsou názvy hostitelů uzlů clusteru NFS, **NW1-Xscs-0** a **NW1-xscs-1** jsou názvy hostitelů hostitelských uzlů **NW1** ASCS a **NW1-DB-0** a **NW1-DB-1** jsou názvy hostitelů uzlů databázového clusteru. Je nahraďte názvy hostitelů uzlů clusteru a identifikátor SID systému SAP.

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

Můžete zkontrolovat, pokud všechno je správně nastavené pomocí

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

### <a name="set-up-sbd-device"></a>Nastavit SBD zařízení

Připojte se k zařízení iSCSI, který byl vytvořen v posledním kroku z clusteru.
Spuštěním následujících příkazů na uzlech nového clusteru, který chcete vytvořit.
Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Připojit k zařízení iSCSI

   Nejprve povolte iSCSI a SBD služby.

   <pre><code>sudo systemctl enable iscsid
   sudo systemctl enable iscsi
   sudo systemctl enable sbd
   </code></pre>

1. **[1]**  Změnit název iniciátoru do prvního uzlu

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změníte obsah souboru tak, aby odpovídaly seznamy ACL, které jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI, třeba pro server systému souborů NFS.

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-0.local:nfs-0</b>
   </code></pre>

1. **[2]**  Změnit název iniciátoru na druhém uzlu

   <pre><code>sudo vi /etc/iscsi/initiatorname.iscsi
   </code></pre>

   Změnit obsah souboru tak, aby odpovídaly seznamy ACL, které jste použili při vytváření zařízení iSCSI na cílovém serveru iSCSI

   <pre><code>InitiatorName=<b>iqn.2006-04.nfs-1.local:nfs-1</b>
   </code></pre>

1. **[A]**  Restartujte službu iSCSI

   Nyní restartujte službu iSCSI na použití změny

   <pre><code>sudo systemctl restart iscsid
   sudo systemctl restart iscsi
   </code></pre>

   Připojení zařízení iSCSI. V následujícím příkladu 10.0.0.17 je IP adresa cílového serveru iSCSI a 3260 je výchozím portem. <b>IQN.2006 04.nfs.local:nfs</b> je jedním z cílové názvy, které jsou uvedeny po spuštění prvního příkazu níže (zjišťování iscsiadm -m).

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

   Ujistěte se, že zařízení iSCSI, která jsou k dispozici a poznamenejte si název zařízení (v následujícím příkladu/dev/sde)

   <pre><code>lsscsi
   
   # [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
   # [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
   # [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
   # [5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
   # <b>[6:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdd</b>
   # <b>[7:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sde</b>
   # <b>[8:0:0:0]    disk    LIO-ORG  sbdnfs           4.0   /dev/sdf</b>
   </code></pre>

   Teď načtěte identifikátory zařízení iSCSI.

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

   Příkaz seznamu tři identifikátory zařízení pro každé zařízení SBD. Doporučujeme, abyste pomocí ID, který začíná scsi-3, v příkladu výše to je

   * **/dev/disk/by-ID/SCSI-36001405afb0ba8d3a3c413b8cc2cca03**
   * **/dev/disk/by-ID/SCSI-360014053fe4da371a5a4bb69a419a4df**
   * **/dev/disk/by-ID/SCSI-36001405f88f30e7c9684678bc87fe7bf**

1. **[1]**  Vytvořit SBD zařízení

   ID zařízení zařízení iSCSI použijte k vytvoření nových zařízení SBD na prvním uzlu clusteru.

   <pre><code>sudo sbd -d <b>/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03</b> -1 60 -4 120 create

   # Also create the second and third SBD devices if you want to use more than one.
   sudo sbd -d <b>/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df</b> -1 60 -4 120 create
   sudo sbd -d <b>/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf</b> -1 60 -4 120 create
   </code></pre>

1. **[A]**  Přizpůsobit SBD config

   Otevřete konfigurační soubor SBD

   <pre><code>sudo vi /etc/sysconfig/sbd
   </code></pre>

   Změňte vlastnost SBD zařízení, povolit integraci pacemaker a změnit režim spouštění SBD.

   <pre><code>[...]
   <b>SBD_DEVICE="/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-360014053fe4da371a5a4bb69a419a4df;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf"</b>
   [...]
   <b>SBD_PACEMAKER="yes"</b>
   [...]
   <b>SBD_STARTMODE="always"</b>
   [...]
   <b>SBD_WATCHDOG="yes"</b>
   </code></pre>

   Vytvoření konfiguračního souboru `softdog`

   <pre><code>echo softdog | sudo tee /etc/modules-load.d/softdog.conf
   </code></pre>

   Nyní načtení modulu

   <pre><code>sudo modprobe -v softdog
   </code></pre>

## <a name="cluster-installation"></a>Instalace clusteru

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Aktualizovat SLES

   <pre><code>sudo zypper update
   </code></pre>

1. **[A]** instalace komponenty potřebné pro prostředky clusteru

   <pre><code>sudo zypper in socat
   </code></pre>

1. **[A]** konfigurace operačního systému

   V některých případech Pacemaker vytvoří velký počet procesů a tím vyčerpá povolený počet procesů. V takovém případě prezenčního signálu mezi uzly clusteru může selhat a vést k převzetí služeb při selhání z vašich prostředků. Doporučujeme zvýšit maximální povolené procesy tak, že nastavíte následující parametr.

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

   Snížení velikosti mezipaměti změny. Další informace najdete v tématu [zápisu s nízkou výkonu na SLES 11/12 servery s velkou paměť RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** konfigurace Cloud-netconfig – Azure pro cluster ha

   Změňte konfigurační soubor pro síťové rozhraní, jak je vidět níže, aby modul plug-in cloudové sítě neodebral virtuální IP adresu (Pacemaker musí řídit přiřazení VIP). Další informace najdete v tématu [SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633). 

   <pre><code># Edit the configuration file
   sudo vi /etc/sysconfig/network/ifcfg-eth0 
   
   # Change CLOUD_NETCONFIG_MANAGE
   # CLOUD_NETCONFIG_MANAGE="yes"
   CLOUD_NETCONFIG_MANAGE="no"
   </code></pre>

1. **[1]**  Zpřístupnění ssh

   <pre><code>sudo ssh-keygen
   
   # Enter file in which to save the key (/root/.ssh/id_rsa): -> Press ENTER
   # Enter passphrase (empty for no passphrase): -> Press ENTER
   # Enter same passphrase again: -> Press ENTER
   
   # copy the public key
   sudo cat /root/.ssh/id_rsa.pub
   </code></pre>

1. **[2]**  Zpřístupnění ssh

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

1. **[1]**  Zpřístupnění ssh

   <pre><code># insert the public key you copied in the last step into the authorized keys file on the first server
   sudo vi /root/.ssh/authorized_keys
   </code></pre>

1. **[A]**  Ohrazení instalace agentů
   
   <pre><code>sudo zypper install fence-agents
   </code></pre>

   >[!IMPORTANT]
   > Pokud používáte systém SUSE Linux Enterprise Server pro SAP 15, nezapomeňte aktivovat další modul a nainstalovat další součást, která je předpokladem pro použití agenta Azure plot. Další informace o SUSE modulech a rozšířeních najdete v tématu [vysvětlené moduly a rozšíření](https://www.suse.com/documentation/sles-15/singlehtml/art_modules/art_modules.html). Podle pokynů níže nainstalujte sadu Azure Python SDK. 

   Následující pokyny, jak nainstalovat sadu Azure Python SDK, platí jenom pro SUSE Enterprise Server pro SAP **15**.  

    - Pokud používáte vlastní předplatné, postupujte podle těchto pokynů.  

    <pre><code>
    #Activate module PackageHub/15/x86_64
    sudo SUSEConnect -p PackageHub/15/x86_64
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

     - Pokud používáte předplatné s průběžnými platbami, postupujte podle těchto pokynů.  

    <pre><code>#Activate module PackageHub/15/x86_64
    zypper ar https://download.opensuse.org/repositories/openSUSE:/Backports:/SLE-15/standard/ SLE15-PackageHub
    #Install Azure Python SDK
    sudo zypper in python3-azure-sdk
    </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech. Výhodou použití/Etc/Hosts je, že cluster bude nezávisle na DNS, který může být kritickým prvkem způsobujícím selhání příliš.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí   

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[1]**  Instalaci clusteru

   <pre><code>sudo ha-cluster-init -u
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   # Address for ring0 [10.0.0.6] <b>Press ENTER</b>
   # Port for ring0 [5405] <b>Press ENTER</b>
   # SBD is already configured to use /dev/disk/by-id/scsi-36001405639245768818458b930abdf69;/dev/disk/by-id/scsi-36001405afb0ba8d3a3c413b8cc2cca03;/dev/disk/by-id/scsi-36001405f88f30e7c9684678bc87fe7bf - overwrite (y/n)? <b>n</b>
   # Do you wish to configure an administration IP (y/n)? <b>n</b>
   </code></pre>

1. **[2]**  Přidat uzel do clusteru

   <pre><code>sudo ha-cluster-join
   
   # ! NTP is not configured to start at system boot.
   # Do you want to continue anyway (y/n)? <b>y</b>
   # IP address or hostname of existing node (e.g.: 192.168.1.1) []<b>10.0.0.6</b>
   # /root/.ssh/id_rsa already exists - overwrite (y/n)? <b>n</b>
   </code></pre>

1. **[A]**  Hacluster Změna hesla na stejné heslo

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** upravit nastavení Corosync.  

   <pre><code>sudo vi /etc/corosync/corosync.conf
   </code></pre>

   Pokud hodnoty nejsou zde nebo jiné, přidejte do souboru následující tučného písma obsahu. Ujistěte se, že chcete-li změnit token 30000 umožňující Údržba pro zachování paměti. Další informace najdete v [tomto článku pro Linux][virtual-machines-linux-maintenance] nebo [Windows][virtual-machines-windows-maintenance].

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

## <a name="create-azure-fence-agent-stonith-device"></a>Vytvořit Azure ohrazení agenta využitím techniky STONITH zařízení

Využitím techniky STONITH zařízení využívá instanční objekt služby k autorizaci s Microsoft Azure. Postupujte podle těchto kroků můžete vytvořit instanční objekt služby.

1. Přejděte na <https://portal.azure.com>.
1. Otevře se okno Azure Active Directory  
   Přejděte do vlastností a poznamenejte si ID adresáře. Toto je **ID tenanta**.
1. Klikněte na možnost registrace aplikací
1. Klikněte na nová registrace.
1. Zadejte název, vyberte účty pouze v tomto adresáři organizace. 
2. Vyberte typ aplikace "Web", zadejte adresu URL pro přihlášení (například http:\//localhost) a klikněte na Přidat.  
   Adresa URL přihlašování se nepoužívá a může být jakákoliv platná adresa URL
1. Vyberte certifikáty a tajné klíče a pak klikněte na nový tajný klíč klienta.
1. Zadejte popis nového klíče, vyberte možnost "nikdy vyprší platnost" a klikněte na tlačítko Přidat.
1. Poznamenejte si hodnotu. Používá se jako **heslo** pro instanční objekt
1. Vyberte přehled. Poznamenejte si ID aplikace. Se používá jako uživatelské jméno (**přihlašovací ID** v následujících krocích) instanční objekt služby

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Vytvořit vlastní roli pro agent síťové izolace

Objekt služby nemá ve výchozím nastavení oprávnění pro přístup k prostředkům Azure. Je potřeba udělit oprávnění instančního objektu pro spuštění a zastavení (uvolníte) všechny virtuální počítače v clusteru. Pokud jste ještě nevytvořili vlastní roli, můžete vytvořit pomocí [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-powershell#create-a-custom-role) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-cli)

Použijte následující obsah vstupního souboru. Je potřeba upravit obsah, který je pro vaše předplatná, c276fc76-9cd4-44c9-99a7-4fd71546436e a e91d47c4-76f3-4271-a796-21b4ecfe3624 nahraďte ID vašeho předplatného. Pokud máte pouze jedno předplatné, odeberte v AssignableScopes druhou položku.

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

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** přiřazení vlastní role k instančnímu objektu

Přiřazení vlastní role "Linux ohrazení agenta roli", který byl vytvořen v kapitole poslední k Instančnímu objektu. Tuto roli vlastníka už nepoužívejte!

1. Přejít na [https://portal.azure.com](https://portal.azure.com)
1. Otevřete v okně všechny prostředky
1. Vyberte virtuální počítač na prvním uzlu clusteru
1. Klikněte na řízení přístupu (IAM)
1. Klikněte na tlačítko Přidat přiřazení role
1. Vyberte roli "Linux ohrazení agenta roli"
1. Zadejte název aplikace, kterou jste vytvořili výše
1. Klikněte na Uložit.

Opakujte předchozí kroky pro druhý uzel clusteru.

### <a name="1-create-the-stonith-devices"></a>**[1]**  Umožňuje vytvářet devices využitím techniky STONITH

Až budete upravovat oprávnění pro virtuální počítače, můžete nakonfigurovat zařízení využitím techniky STONITH v clusteru.

<pre><code># replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
sudo crm configure primitive rsc_st_azure stonith:fence_azure_arm \
   params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

sudo crm configure property stonith-timeout=900
sudo crm configure property stonith-enabled=true
</code></pre>

## <a name="default-pacemaker-configuration-for-sbd"></a>Výchozí konfigurace Pacemaker SBD

1. **[1]**  Povolit použití zařízení s využitím techniky STONITH a nastavit ohrazení zpoždění

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

## <a name="pacemaker-configuration-for-azure-scheduled-events"></a>Konfigurace Pacemaker pro plánované události Azure

Azure nabízí [naplánované události](https://docs.microsoft.com/azure/virtual-machines/linux/scheduled-events). Naplánované události se poskytují prostřednictvím služby meta-data Service a umožňují, aby se aplikace připravila na události, jako je třeba vypnutí virtuálního počítače, opětovné nasazení virtuálního počítače atd. Agenti prostředků **[Azure – monitorování událostí](https://github.com/ClusterLabs/resource-agents/pull/1161)** pro plánované události Azure Pokud se zjistí události, Agent se pokusí zastavit všechny prostředky na ovlivněném virtuálním počítači a přesunout je do jiného uzlu v clusteru. Aby bylo možné dosáhnout dalších prostředků Pacemaker, musí být nakonfigurovány. 

1. **[A]** nainstalujte agenta **Azure-Events** . 

<pre><code>sudo zypper install resource-agents
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
