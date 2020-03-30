---
title: SAP HANA horizontální navýšení kapacity HSR-Pacemaker s SLES na virtuálních počítačích Azure řešení potíží| Dokumenty společnosti Microsoft
description: Průvodce kontrolou a odstraňováním problémů s komplexní konfigurací vysoké dostupnosti SAP HANA na základě replikace systému SAP HANA (HSR) a kardiostimulátoru na sles 12 SP3 běžící na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: e93b3412785817050ac53030be9ff2172a678c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617128"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Ověření a řešení potíží s nastavením sap HANA s vysokou dostupností na slezu 12 SP3 

[sles-pacemaker-ha-guide]:high-availability-guide-suse-pacemaker.md
[sles-hana-scale-out-ha-paper]:https://www.suse.com/documentation/suse-best-practices/singlehtml/SLES4SAP-hana-scaleOut-PerfOpt-12/SLES4SAP-hana-scaleOut-PerfOpt-12.html
[sap-hana-iaas-list]:https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html
[suse-pacemaker-support-log-files]:https://www.suse.com/support/kb/doc/?id=7022702
[azure-linux-multiple-nics]:https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics
[suse-cloud-netconfig]:https://www.suse.com/c/multi-nic-cloud-netconfig-ec2-azure/
[sap-list-port-numbers]:https://help.sap.com/viewer/ports
[sles-12-ha-paper]:https://www.suse.com/documentation/sle-ha-12/pdfdoc/book_sleha/book_sleha.pdf
[sles-zero-downtime-paper]:https://www.suse.com/media/presentation/TUT90846_towards_zero_downtime%20_how_to_maintain_sap_hana_system_replication_clusters.pdf
[sap-nw-ha-guide-sles]:high-availability-guide-suse.md
[sles-12-for-sap]:https://www.suse.com/media/white-paper/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf


Tento článek vám pomůže zkontrolovat konfiguraci clusteru Pacemaker pro SAP HANA horizontální navýšení kapacity, který běží na virtuálních počítačích Azure (VMs). Nastavení clusteru bylo provedeno v kombinaci s replikací systému SAP HANA (HSR) a balíčkem SUSE RPM SAPHanaSR-ScaleOut. Všechny testy byly provedeny pouze na SUSE SLES 12 SP3. Části článku pokrývají různé oblasti a obsahují ukázkové příkazy a výňatky z konfiguračních souborů. Tyto ukázky doporučujeme jako metodu ověření a kontroly celého nastavení clusteru.



## <a name="important-notes"></a>Důležité poznámky

Všechny testování sap hana horizontální navýšení kapacity v kombinaci s SAP HANA system replication a kardiostimulátor byl proveden pouze s SAP HANA 2.0. Verze operačního systému byla SUSE Linux Enterprise Server 12 SP3 pro aplikace SAP. Nejnovější balíček RPM, SAPHanaSR-ScaleOut od SUSE, byl použit k nastavení clusteru Pacemaker.
SUSE publikoval [podrobný popis tohoto nastavení optimalizovaného pro výkon][sles-hana-scale-out-ha-paper].

Pro typy virtuálních počítačů, které jsou podporovány pro sap hana horizontální navýšení kapacity, zkontrolujte [SAP HANA certifikovaný adresář IaaS][sap-hana-iaas-list].

Došlo k technickému problému s horizontálním navýšením kapacity SAP HANA v kombinaci s více podsítěmi a virtuálními sítěmi a nastavením HSR. Je nutné použít nejnovější opravy SAP HANA 2.0, kde byl tento problém opraven. Podporovány jsou následující verze SAP HANA: 

* rev2.00.024.04 nebo vyšší 
* rev2.00.032 nebo vyšší

Pokud potřebujete podporu od suse, postupujte podle této [příručky][suse-pacemaker-support-log-files]. Shromažďovat všechny informace o clusteru SAP HANA vysoké dostupnosti (HA), jak je popsáno v článku. Podpora SUSE potřebuje tyto informace pro další analýzu.

Během interního testování se nastavení clusteru zmátlo normálním bezproblémovým vypnutím virtuálního počítače prostřednictvím portálu Azure. Proto doporučujeme otestovat převzetí služeb při selhání clusteru jinými metodami. Používejte metody, jako je vynucení paniky jádra, nebo vypnutí sítí nebo migrace prostředku **msl.** Podrobnosti naleznete v následujících částech. Předpokládá se, že standardní vypnutí se děje s úmyslem. Nejlepším příkladem úmyslného vypnutí je údržba. Podrobnosti naleznete v [části Plánovaná údržba](#planned-maintenance).

Také během interního testování se nastavení clusteru zmátlo po ručním převzetí SAP HANA, zatímco cluster byl v režimu údržby. Před ukončením režimu údržby clusteru doporučujeme jej znovu přepnout zpět ručně. Další možností je spuštění převzetí služeb při selhání před přepnete cluster do režimu údržby. Další informace naleznete [v tématu Plánovaná údržba](#planned-maintenance). Dokumentace od SUSE popisuje, jak lze obnovit cluster tímto způsobem pomocí příkazu **CRM.** Ale výše uvedený přístup byl robustní během interního testování a nikdy nevykazovaly žádné neočekávané vedlejší účinky.

Při použití příkazu **crm migrate** nezapomeňte vyčistit konfiguraci clusteru. Přidá omezení umístění, které nemusí být vědomi. Tato omezení ovlivňují chování clusteru. Další podrobnosti naleznete v [tématu Plánovaná údržba](#planned-maintenance).



## <a name="test-system-description"></a>Popis zkušebního systému

 Pro sap hana horizontální navýšení kapacity HA ověření a certifikace, bylo použito nastavení. Skládá se ze dvou systémů se třemi uzly SAP HANA: jeden hlavní server a dva pracovníci. V následující tabulce jsou uvedeny názvy virtuálních počítačů a interní adresy IP. Všechny následující vzorky ověření byly provedeny na těchto virtuálních počítačích. Pomocí těchto názvů virtuálních počítačů a IP adres v ukázkách příkazů můžete lépe porozumět příkazům a jejich výstupům:


| Typ uzlu | název virtuálního počítače | IP adresa |
| --- | --- | --- |
| Hlavní uzel na webu 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Pracovní uzel 1 na staveništi 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Pracovní uzel 2 na staveništi 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Hlavní uzel na webu 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Pracovní uzel 1 na webu 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Pracovní uzel 2 na webu 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Uzel majoritního výrobce | hso-hana-dm | 10.0.0.13 |
| Server zařízení SBD | hso-hana-sbd | 10.0.0.19 |
| | | |
| NFS server 1 | hso-nfs-vm-0 | 10.0.0.15 |
| NFS server 2 | hso-nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Více podsítí a virtuálních síťových virtuálních sítí

Podle doporučení sítě SAP HANA byly vytvořeny tři podsítě v rámci jedné virtuální sítě Azure. Sap HANA horizontální navýšení kapacity v Azure musí být nainstalován v nesdíleném režimu. To znamená, že každý uzel používá místní diskové svazky pro **/hana/data** a **/hana/log**. Vzhledem k tomu, že uzly používají pouze místní diskové svazky, není nutné definovat samostatnou podsíť pro úložiště:

- 10.0.2.0/24 pro komunikaci SAP HANA internode
- 10.0.1.0/24 pro replikaci systému SAP HANA (HSR)
- 10.0.0.0/24 pro všechno ostatní

Informace o konfiguraci SAP HANA související s používáním více sítí naleznete v [tématu SAP HANA global.ini](#sap-hana-globalini).

Každý virtuální počítač v clusteru má tři virtuální sítě, které odpovídají počtu podsítí. [Jak vytvořit virtuální počítač Linuxu v Azure s více kartami síťového rozhraní][azure-linux-multiple-nics] popisuje potenciální problém směrování v Azure při nasazování virtuálního počítače s Linuxem. Tento konkrétní článek směrování platí pouze pro použití více virtuálních vNIC. Problém je vyřešen SUSE za výchozí v SLES 12 SP3. Další informace najdete [v tématu Multi-NIC s cloud-netconfig v EC2 a Azure][suse-cloud-netconfig].


Chcete-li ověřit, zda je SAP HANA správně nakonfigurován pro použití více sítí, spusťte následující příkazy. Nejprve zkontrolujte na úrovni operačního systému, že všechny tři interní IP adresy pro všechny tři podsítě jsou aktivní. Pokud jste definovali podsítě s různými rozsahy IP adres, je třeba upravit příkazy:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Následující ukázkový výstup je z druhého pracovního uzlu na webu 2. Můžete vidět tři různé interní IP adresy z eth0, eth1 a eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


Dále ověřte porty SAP HANA pro názvový server a HSR. SAP HANA by měl naslouchat na odpovídající podsítě. V závislosti na čísle instance SAP HANA je třeba upravit příkazy. Pro testovací systém bylo číslo instance **00**. Existují různé způsoby, jak zjistit, které porty se používají. 

Následující příkaz SQL vrátí ID instance, číslo instance a další informace:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Chcete-li najít správná čísla portů, můžete se podívat například v HANA Studio v části **Konfigurace** nebo pomocí příkazu SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Chcete-li najít každý port, který se používá v zásobníku softwaru SAP včetně SAP HANA, vyhledejte [porty TCP/IP všech produktů SAP][sap-list-port-numbers].

Vzhledem k tomu, že číslo instance **00** v testovacím systému SAP HANA 2.0, číslo portu pro názvový server je **30001**. Číslo portu pro komunikaci metadat HSR je **40002**. Jednou z možností je přihlásit se k pracovnímu uzlu a potom zkontrolovat služby hlavního uzlu. Pro tento článek jsme zkontrolovali pracovní uzel 2 na webu 2, který se pokouší připojit k hlavnímu uzlu na webu 2.

Zkontrolujte port názvového serveru:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Chcete-li prokázat, že komunikace internode používá podsíť **10.0.2.0/24**, výsledek by měl vypadat jako následující ukázkový výstup.
Úspěšné by mělo být pouze připojení prostřednictvím podsítě **10.0.2.0/24:**

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Nyní zkontrolujte hsr port **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Chcete-li prokázat, že komunikace HSR používá podsíť **10.0.1.0/24**, výsledek by měl vypadat jako následující ukázkový výstup.
Úspěšné by mělo být pouze připojení prostřednictvím podsítě **10.0.1.0/24:**

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync řekl:


Konfigurační soubor **corosync** musí být správný na každém uzlu v clusteru, včetně uzlu majoritního výrobce. Pokud spojení clusteru uzlu nefunguje podle očekávání, vytvořte nebo zkopírujte **/etc/corosync/corosync.conf** ručně do všech uzlů a restartujte službu. 

Obsah **corosync.conf** ze zkušebního systému je příkladem.

První část je **totem**, jak je popsáno v [instalaci clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), krok 11. Můžete ignorovat hodnotu pro **mcastaddr**. Jen si ponechte stávající položku. Položky pro **token** a **konsensus** musí být nastaveny podle [dokumentace Microsoft Azure SAP HANA][sles-pacemaker-ha-guide].

<pre><code>
totem {
    version:    2
    secauth:    on
    crypto_hash:    sha1
    crypto_cipher:  aes256
    cluster_name:   hacluster
    clear_node_high_bit: yes

    token:      30000
    token_retransmits_before_loss_const: 10
    join:       60
    consensus:  36000
    max_messages:   20

    interface {
        ringnumber:     0
        bindnetaddr: 10.0.0.0
        mcastaddr:  239.170.19.232
        mcastport:  5405

        ttl:        1
    }
    transport:      udpu

}
</code></pre>

Druhá část **protokolování**nebyla změněna z daných výchozích hodnot:

<pre><code>
logging {
    fileline:   off
    to_stderr:  no
    to_logfile:     no
    logfile:    /var/log/cluster/corosync.log
    to_syslog:  yes
    debug:      off
    timestamp:  on
    logger_subsys {
        subsys:     QUORUM
        debug:  off
    }
}
</code></pre>

Třetí část zobrazuje **nodelist**. Všechny uzly clusteru se musí zobrazit s **uzlem**:

<pre><code>
nodelist {
  node {
   ring0_addr:hso-hana-vm-s1-0
   nodeid: 1
   }
  node {
   ring0_addr:hso-hana-vm-s1-1
   nodeid: 2
   }
  node {
   ring0_addr:hso-hana-vm-s1-2
   nodeid: 3
   }
  node {
   ring0_addr:hso-hana-vm-s2-0
   nodeid: 4
   }
  node {
   ring0_addr:hso-hana-vm-s2-1
   nodeid: 5
   }
  node {
   ring0_addr:hso-hana-vm-s2-2
   nodeid: 6
   }
  node {
   ring0_addr:hso-hana-dm
   nodeid: 7
   }
}
</code></pre>

V poslední části **kvorum**je důležité správně nastavit hodnotu **pro expected_votes.** Musí se jednat o počet uzlů včetně uzlu majoritního výrobce. A hodnota pro **two_node** musí být **0**. Neodstraňujte položku úplně. Stačí nastavit hodnotu na **hodnotu 0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Restartujte službu pomocí **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>Zařízení SBD

Jak nastavit zařízení SBD na virtuálním počítači Azure je popsánv [oplocení SBD](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Nejprve zkontrolujte virtuální počítač serveru SBD, pokud existují položky ACL pro každý uzel v clusteru. Spusťte následující příkaz na virtuálním počítači serveru SBD:


<pre><code>
targetcli ls
</code></pre>


V testovacím systému výstup příkazu vypadá jako následující ukázka. Názvy ACL jako **iqn.2006-04.hso-db-0.local:hso-db-0** musí být zadány jako odpovídající názvy iniciátorů na virtuálních počítačích. Každý virtuální virtuální virtuální měnový virtuální ho potřebuje.

<pre><code>
 | | o- sbddbhso ................................................................... [/sbd/sbddbhso (50.0MiB) write-thru activated]
  | |   o- alua ................................................................................................... [ALUA Groups: 1]
  | |     o- default_tg_pt_gp ....................................................................... [ALUA state: Active/optimized]
  | o- pscsi .................................................................................................. [Storage Objects: 0]
  | o- ramdisk ................................................................................................ [Storage Objects: 0]
  o- iscsi ............................................................................................................ [Targets: 1]
  | o- iqn.2006-04.dbhso.local:dbhso ..................................................................................... [TPGs: 1]
  |   o- tpg1 ............................................................................................... [no-gen-acls, no-auth]
  |     o- acls .......................................................................................................... [ACLs: 7]
  |     | o- iqn.2006-04.hso-db-0.local:hso-db-0 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-1.local:hso-db-1 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-2.local:hso-db-2 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-3.local:hso-db-3 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-4.local:hso-db-4 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-5.local:hso-db-5 .................................................................. [Mapped LUNs: 1]
  |     | | o- mapped_lun0 ............................................................................. [lun0 fileio/sbddbhso (rw)]
  |     | o- iqn.2006-04.hso-db-6.local:hso-db-6 .................................................................. [Mapped LUNs: 1]
</code></pre>

Pak zkontrolujte, zda názvy iniciátorů na všech virtuálních počítačích se liší a odpovídají dříve zobrazené položky. Tento příklad je z pracovního uzlu 1 na webu 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Výstup vypadá jako následující ukázka:

<pre><code>
##
## /etc/iscsi/iscsi.initiatorname
##
## Default iSCSI Initiatorname.
##
## DO NOT EDIT OR REMOVE THIS FILE!
## If you remove this file, the iSCSI daemon will not start.
## If you change the InitiatorName, existing access control lists
## may reject this initiator.  The InitiatorName must be unique
## for each iSCSI initiator.  Do NOT duplicate iSCSI InitiatorNames.
InitiatorName=iqn.2006-04.hso-db-1.local:hso-db-1
</code></pre>

Dále ověřte, zda **zjišťování** funguje správně. Spusťte následující příkaz na každém uzlu clusteru pomocí IP adresy virtuálního počítače serveru SBD:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Výstup by měl vypadat jako následující ukázka:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Dalším bodem konce je ověřit, že uzel vidí zařízení SDB. Zkontrolujte, zda na každém uzlu, včetně uzlu majority maker:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Výstup by měl vypadat jako následující ukázka. Názvy se však mohou lišit. Název zařízení se může po restartování virtuálního počítače změnit také:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

V závislosti na stavu systému někdy pomáhá restartovat služby iSCSI k vyřešení problémů. Potom spusťte následující příkazy:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Z libovolného uzlu můžete zkontrolovat, zda jsou všechny uzly **vymazány**. Ujistěte se, že používáte správný název zařízení na konkrétním uzlu:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Výstup by měl být **jasný** pro každý uzel v clusteru:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Další kontrola SBD je **možnost výpisu** příkazu **sbd.** V tomto ukázkovém příkazu a výstupu z uzlu majoritního výrobce byl název zařízení **sdd**, nikoli **sdm**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Výstup, na rozdíl od názvu zařízení, by měl vypadat stejně na všech uzlech:

<pre><code>
==Dumping header on disk /dev/sdd
Header version     : 2.1
UUID               : 9fa6cc49-c294-4f1e-9527-c973f4d5a5b0
Number of slots    : 255
Sector size        : 512
Timeout (watchdog) : 60
Timeout (allocate) : 2
Timeout (loop)     : 1
Timeout (msgwait)  : 120
==Header on disk /dev/sdd is dumped
</code></pre>

Ještě jedna kontrola SBD je možnost odeslat zprávu do jiného uzlu. Chcete-li odeslat zprávu pracovnímu uzlu 2 na webu 2, spusťte následující příkaz na pracovní uzel 1 na webu 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Na straně cílového virtuálního počítače **hso-hana-vm-s2-2** v tomto příkladu najdete následující položku v **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Zkontrolujte, zda položky v **/etc/sysconfig/sbd** odpovídají popisu v [části Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Ověřte, zda je nastavení spuštění v **souboru /etc/iscsi/iscsid.conf** nastaveno na automatické.

Následující položky jsou důležité v **/etc/sysconfig/sbd**. V případě potřeby upravte hodnotu **id:**

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Zkontrolujte nastavení spuštění v **souboru /etc/iscsi/iscsid.conf**. Požadované nastavení mělo nastane pomocí následujícího příkazu **iscsiadm** popsaného v dokumentaci. Ověřte a přizpůsobte ručně s **vi,** pokud je to jiné.

Tento příkaz nastaví chování při spuštění:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Proveďte tuto položku v **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Během testování a ověřování po restartování virtuálního počítače, zařízení SBD nebyl viditelný už v některých případech. Tam byl rozpor mezi nastavením spuštění a co YaST2 ukázal. Chcete-li zkontrolovat nastavení, postupujte takto:

1. Spusťte YaST2.
2. Na levé straně vyberte **síťové služby.**
3. Posuňte se dolů na pravé straně k **iniciátoru iSCSI** a vyberte ho.
4. Na další obrazovce na kartě **Služba** se zobrazí jedinečný název iniciátoru uzlu.
5. Nad názvem iniciátoru zkontrolujte, zda je hodnota **Start služby** nastavena na **hodnotu Při spuštění**.
6. Pokud tomu tak není, nastavte ji na **Při zavádění** místo **ručně**.
7. Dále přepněte horní kartu na **Připojené cíle**.
8. Na obrazovce **Připojené cíle** byste měli vidět položku pro zařízení SBD, jako je tato ukázka: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Zkontrolujte, zda je při **spuštění**nastavena hodnota **Start-Up** .
10. Pokud ne, zvolte **Upravit** a změňte je.
11. Uložte změny a ukončete YaST2.



## <a name="pacemaker"></a>Kardiostimulátor

Po správném nastavení můžete spustit následující příkaz na každém uzlu a zkontrolovat stav služby Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

Horní část výstupu by měla vypadat jako následující ukázka. Je důležité, aby stav po **Active** je zobrazen jako **načtený** a **aktivní (spuštěný).** Stav po **načtené** musí být zobrazen jako **povolený**.

<pre><code>
  pacemaker.service - Pacemaker High Availability Cluster Manager
   Loaded: loaded (/usr/lib/systemd/system/pacemaker.service; enabled; vendor preset: disabled)
   Active: active (running) since Fri 2018-09-07 05:56:27 UTC; 4 days ago
     Docs: man:pacemakerd
           http://clusterlabs.org/doc/en-US/Pacemaker/1.1-pcs/html/Pacemaker_Explained/index.html
 Main PID: 4496 (pacemakerd)
    Tasks: 7 (limit: 4915)
   CGroup: /system.slice/pacemaker.service
           ├─4496 /usr/sbin/pacemakerd -f
           ├─4499 /usr/lib/pacemaker/cib
           ├─4500 /usr/lib/pacemaker/stonithd
           ├─4501 /usr/lib/pacemaker/lrmd
           ├─4502 /usr/lib/pacemaker/attrd
           ├─4503 /usr/lib/pacemaker/pengine
           └─4504 /usr/lib/pacemaker/crmd
</code></pre>

Pokud je nastavení stále **zakázáno**, spusťte následující příkaz:

<pre><code>
systemctl enable pacemaker
</code></pre>

Chcete-li zobrazit všechny nakonfigurované prostředky v kardiostimulátoru, spusťte následující příkaz:

<pre><code>
crm status
</code></pre>

Výstup by měl vypadat jako následující ukázka. Je v pořádku, že **cln** a **msl** zdroje jsou zobrazeny jako zastavena na majoritní výrobce VM, **hso-hana-dm**. Neexistuje žádná instalace SAP HANA na uzlu většinového výrobce. Takže **cln** a **msl** zdroje jsou zobrazeny jako zastaveno. Je důležité, aby se zobrazuje správný celkový počet virtuálních discích, **7**. Všechny virtuální společnosti, které jsou součástí clusteru, musí být uvedeny se stavem **Online**. Aktuální primární hlavní uzel musí být rozpoznán správně. V tomto příkladu je **hso-hana-vm-s1-0**:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Tue Sep 11 15:56:40 2018
Last change: Tue Sep 11 15:56:23 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0
</code></pre>

Důležitou vlastností kardiostimulátoru je režim údržby. V tomto režimu můžete provádět změny bez vyvolání okamžité akce clusteru. Příkladem je restartování virtuálního počítače. Typický případ použití by se plánované údržby operačního režimu nebo infrastruktury Azure. Viz [Plánovaná údržba](#planned-maintenance). Pomocí následujícího příkazu přepnete kardiostimulátor do režimu údržby:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Při kontrole **se stavem crm**zjistíte ve výstupu, že všechny prostředky jsou označeny jako **nespravované**. V tomto stavu clusteru nereaguje na žádné změny, jako je spuštění nebo zastavení SAP HANA.
Následující ukázka ukazuje výstup příkazu **stavu crm,** když je cluster v režimu údržby:

<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Wed Sep 12 07:48:10 2018
Last change: Wed Sep 12 07:46:54 2018 by root via cibadmin on hso-hana-vm-s2-1

7 nodes configured
17 resources configured

              *** Resource management is DISABLED ***
  The cluster will not attempt to start, stop or recover services

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm (unmanaged)
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00] (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-0 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaTop_HSO_HDB00   (ocf::suse:SAPHanaTopology):    Started hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00] (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s1-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-1 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Slave hso-hana-vm-s2-2 (unmanaged)
     rsc_SAPHanaCon_HSO_HDB00   (ocf::suse:SAPHanaController):  Master hso-hana-vm-s2-0 (unmanaged)
     Stopped: [ hso-hana-dm hso-hana-vm-s1-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s2-0 (unmanaged)
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s2-0 (unmanaged)
</code></pre>


Tato ukázka příkazů ukazuje, jak ukončit režim údržby clusteru:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Jiný příkaz **crm** získá úplnou konfiguraci clusteru do editoru, takže jej můžete upravit. Po uložení změn cluster spustí příslušné akce:

<pre><code>
crm configure edit
</code></pre>

Chcete-li se podívat na úplnou konfiguraci clusteru, použijte možnost **crm show:**

<pre><code>
crm configure show
</code></pre>



Po selhání prostředků clusteru zobrazí příkaz **stav CRM** seznam **neúspěšných akcí**. Viz následující ukázka tohoto výstupu:


<pre><code>
Stack: corosync
Current DC: hso-hana-dm (version 1.1.16-4.8-77ea74d) - partition with quorum
Last updated: Thu Sep 13 07:30:44 2018
Last change: Thu Sep 13 07:30:20 2018 by root via crm_attribute on hso-hana-vm-s1-0

7 nodes configured
17 resources configured

Online: [ hso-hana-dm hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started hso-hana-dm
 Clone Set: cln_SAPHanaTop_HSO_HDB00 [rsc_SAPHanaTop_HSO_HDB00]
     Started: [ hso-hana-vm-s1-0 hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-0 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm ]
 Master/Slave Set: msl_SAPHanaCon_HSO_HDB00 [rsc_SAPHanaCon_HSO_HDB00]
     Masters: [ hso-hana-vm-s1-0 ]
     Slaves: [ hso-hana-vm-s1-1 hso-hana-vm-s1-2 hso-hana-vm-s2-1 hso-hana-vm-s2-2 ]
     Stopped: [ hso-hana-dm hso-hana-vm-s2-0 ]
 Resource Group: g_ip_HSO_HDB00
     rsc_ip_HSO_HDB00   (ocf::heartbeat:IPaddr2):       Started hso-hana-vm-s1-0
     rsc_nc_HSO_HDB00   (ocf::heartbeat:anything):      Started hso-hana-vm-s1-0

Failed Actions:
* rsc_SAPHanaCon_HSO_HDB00_monitor_60000 on hso-hana-vm-s2-0 'unknown error' (1): call=86, status=complete, exitreason='none',
    last-rc-change='Wed Sep 12 17:01:28 2018', queued=0ms, exec=277663ms
</code></pre>

Je nutné provést vyčištění clusteru po selhání. Znovu použijte příkaz **crm** a pomocí **možnosti příkazu se** zbavte těchto neúspěšných položek akce. Pojmenujte odpovídající prostředek clusteru takto:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Příkaz by měl vrátit výstup jako následující ukázka:

<pre><code>
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-dm
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s1-2
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-0
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-1
Cleaned up rsc_SAPHanaCon_HSO_HDB00:0 on hso-hana-vm-s2-2
Waiting for 7 replies from the CRMd....... OK
</code></pre>



## <a name="failover-or-takeover"></a>Převzetí služeb při selhání nebo převzetí

Jak je popsáno v [důležité poznámky](#important-notes), neměli byste použít standardní řádné vypnutí k testování převzetí služeb při selhání clusteru nebo SAP HANA HSR převzetí. Místo toho doporučujeme spustit paniku jádra, vynutit migraci prostředků nebo případně vypnout všechny sítě na úrovni operačního modulu virtuálního počítači. Další metodou je ** \<\> příkaz pohotovostního** režimu uzlu CRM. Viz [dokument SUSE][sles-12-ha-paper]. 

Následující tři ukázkové příkazy mohou vynutit převzetí služeb při selhání clusteru:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Jak je popsáno v [plánované údržby](#planned-maintenance), dobrým způsobem, jak sledovat aktivity clusteru je spustit **SAPHanaSR-showAttr** s **příkazem watch:**

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Pomáhá také podívat se na stav prostředí SAP HANA pocházející ze skriptu SAP Python. Nastavení clusteru hledá tuto hodnotu stavu. Je jasné, když si myslíte o selhání pracovního uzlu. Pokud dojde k výpadku pracovního uzlu, SAP HANA okamžitě nevrátí chybu pro stav celého systému horizontálního navýšení kapacity. 

Existují některé pokusy o opakování, aby se zabránilo zbytečné převzetí služeb při selhání. Cluster reaguje pouze v případě, že se stav změní z **ok**, vrátí hodnotu **4**, **na chybu**, vrátí hodnotu **1**. Takže je správné, pokud výstup z **SAPHanaSR-showAttr** zobrazuje virtuální hod se stavem **offline**. Ale zatím není žádná aktivita, která by se změnila na primární a sekundární. Žádná aktivita clusteru se neaktivuje, pokud SAP HANA nevrátí chybu.

Stav prostředí SAP HANA můžete sledovat jako ** \<adm uživatele HANA\>SID voláním** skriptu SAP Python následujícím způsobem. Možná budete muset upravit cestu:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Výstup tohoto příkazu by měl vypadat jako následující ukázka. Sloupec **Stav hostitele** a celkový stav **hostitele** jsou důležité. Skutečný výstup je širší, s dalšími sloupci.
Aby byla výstupní tabulka čitelnější v tomto dokumentu, byla většina sloupců na pravé straně odstraněna:

<pre><code>
| Host             | Host   | Host   | Failover | Remove | 
|                  | Active | Status | Status   | Status | 
|                  |        |        |          |        | 
| ---------------- | ------ | ------ | -------- | ------ |    .......
| hso-hana-vm-s2-0 | yes    | ok     |          |        |        
| hso-hana-vm-s2-1 | yes    | ok     |          |        |         
| hso-hana-vm-s2-2 | yes    | ok     |          |        |        

overall host status: ok
</code></pre>


Existuje jiný příkaz pro kontrolu aktuálních aktivit clusteru. Podívejte se na následující příkaz a výstup ocaspo hlavní uzel primární lokality byla zabita. Jako nový primární hlavní server můžete zobrazit seznam akcí přechodu, jako je **povýšení** bývalého sekundárního hlavního uzlu **hso-hana-vm-s2-0.** Pokud je vše v pořádku a všechny aktivity jsou dokončeny, tento souhrn **přechodu** seznam musí být prázdný.

<pre><code>
 crm_simulate -Ls

...........

Transition Summary:
 * Fence hso-hana-vm-s1-0
 * Stop    rsc_SAPHanaTop_HSO_HDB00:1   (hso-hana-vm-s1-0)
 * Demote  rsc_SAPHanaCon_HSO_HDB00:1   (Master -> Stopped hso-hana-vm-s1-0)
 * Promote rsc_SAPHanaCon_HSO_HDB00:5   (Slave -> Master hso-hana-vm-s2-0)
 * Move    rsc_ip_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
 * Move    rsc_nc_HSO_HDB00     (Started hso-hana-vm-s1-0 -> hso-hana-vm-s2-0)
</code></pre>



## <a name="planned-maintenance"></a>Plánovaná údržba 

Pokud jde o plánovanou údržbu, existují různé případy použití. Jednou z otázek je, zda je to jen údržba infrastruktury, jako jsou změny na úrovni operačního systému a konfigurace disku nebo upgrade HANA.
Další informace naleznete v dokumentech od SUSE, [například Směrem k nulovým prostojům][sles-zero-downtime-paper] nebo [SCÉNÁŘ Optimalizovaný pro výkon SAP HANA SR][sles-12-for-sap]. Tyto dokumenty také obsahují ukázky, které ukazují, jak ručně migrovat primární.

Intenzivní interní testování bylo provedeno za účelem ověření případu použití údržby infrastruktury. Abychom se vyhnuli problémům souvisejícím s migrací primárních, rozhodli jsme se vždy migrovat primární před uvedením clusteru do režimu údržby. Tímto způsobem není nutné, aby cluster zapomněl na dřívější situaci: která strana byla primární a která byla sekundární.

V tomto ohledu existují dvě různé situace:

- **Plánovaná údržba aktuální sekundární**. V takovém případě můžete cluster uřadit do režimu údržby a provést práci na sekundárním zařízení bez ovlivnění clusteru.

- **Plánovaná údržba aktuální primární**. Aby uživatelé mohli pokračovat v práci během údržby, je třeba vynutit převzetí služeb při selhání. S tímto přístupem je nutné aktivovat převzetí služeb při selhání clusteru pomocí kardiostimulátoru a nikoli pouze na úrovni SAP HANA HSR. Nastavení kardiostimulátoru automaticky spustí převzetí SAP HANA. Před přepáním clusteru do režimu údržby je také nutné provést převzetí služeb při selhání.

Postup údržby v současném sekundárním místě je následující:

1. Přepne cluster do režimu údržby.
2. Dokončte práci na sekundárnílokalitě. 
3. Ukončite režim údržby clusteru.

Postup údržby v aktuální primární lokalitě je složitější:

1. Ručně aktivuj převzetí převzetí služeb při selhání nebo převzetí SAP HANA prostřednictvím migrace prostředků kardiostimulátoru. Podívejte se na následující podrobnosti.
2. SAP HANA v bývalé primární lokalitě se vypne nastavením clusteru.
3. Přepne cluster do režimu údržby.
4. Po dokončení údržby zaregistrujte dřívější primární jako nové sekundární pracoviště.
5. Vyčistěte konfiguraci clusteru. Podívejte se na následující podrobnosti.
6. Ukončite režim údržby clusteru.


Migrace prostředku přidá položku do konfigurace clusteru. Příkladem je vynucení převzetí služeb při selhání. Před ukončením režimu údržby je třeba tyto položky vyčistit. Viz následující ukázka.

Nejprve vynuťte převzetí služeb při selhání clusteru migrací prostředku **msl** do aktuálního sekundárního hlavního uzlu. Tento příkaz poskytuje upozornění, že byla vytvořena **vazba přesunutí:**

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Zkontrolujte proces převzetí služeb při selhání pomocí příkazu **SAPHanaSR-showAttr**. Chcete-li sledovat stav clusteru, otevřete vyhrazené okno prostředí a spusťte příkaz pomocí **hodinek**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Výstup by měl zobrazit ruční převzetí služeb při selhání. Bývalý sekundární hlavní uzel byl **povýšen**, v tomto vzorku, **hso-hana-vm-s2-0**. Původní primární lokalita byla zastavena, **hodnota lss** **1** pro bývalý primární hlavní uzel **hso-hana-vm-s1-0**: 

<pre><code>
Global cib-time                 prim  sec srHook sync_state
------------------------------------------------------------
global Wed Sep 12 07:40:02 2018 HSOS2 -   SFAIL  SFAIL


Sites lpt        lss mns              srr
------------------------------------------
HSOS1 10         1   hso-hana-vm-s1-0 P
HSOS2 1536738002 4   hso-hana-vm-s2-0 P


Hosts            clone_state node_state roles                        score  site
----------------------------------------------------------------------------------
hso-hana-dm                  online
hso-hana-vm-s1-0 UNDEFINED   online     master1::worker:             150    HSOS1
hso-hana-vm-s1-1 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s1-2 DEMOTED     online     slave::worker:               -10000 HSOS1
hso-hana-vm-s2-0 PROMOTED    online     master1:master:worker:master 150    HSOS2
hso-hana-vm-s2-1 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
hso-hana-vm-s2-2 DEMOTED     online     slave:slave:worker:slave     -10000 HSOS2
</code></pre>

Po převzetí služeb při selhání clusteru a převzetí SAP HANA přepnete cluster do režimu údržby, jak je popsáno v [pacemakeru](#pacemaker).

Příkazy **SAPHanaSR-showAttr** a **crm stav** neoznačují nic o omezení vytvořená migrací prostředků. Jednou z možností, jak tato omezení zviditelnit, je zobrazení úplné konfigurace prostředků clusteru pomocí následujícího příkazu:

<pre><code>
crm configure show
</code></pre>

V rámci konfigurace clusteru najdete nové omezení umístění způsobené předchozí ruční migrací prostředků. Tato ukázková položka začíná **cli- umístění:**

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Bohužel tato omezení mohou ovlivnit celkové chování clusteru. Takže je nutné je znovu odstranit, než znovu vynesete celý systém. Pomocí příkazu **unmigrate** je možné vyčistit omezení umístění, která byla vytvořena dříve. Pojmenování může být trochu matoucí. Nepokouší se migrovat prostředek zpět na původní virtuální ms, ze kterého byl migrován. Pouze odstraní omezení umístění a také vrátí odpovídající informace při spuštění příkazu:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Na konci údržby zastavíte režim údržby clusteru, jak je znázorněno v [pacemakeru](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report shromažďovat soubory protokolu

Chcete-li analyzovat problémy clusteru Pacemaker, je užitečné a také požadované suse podporu ke spuštění **nástroje hb_report.** Shromažďuje všechny důležité soubory protokolu, které potřebujete k analýze toho, co se stalo. Toto ukázkové volání používá počáteční a koncový čas, kde došlo k určitému incidentu. Viz také [důležité poznámky](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Příkaz vám řekne, kam umístit komprimované soubory protokolu:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Jednotlivé soubory pak můžete extrahovat pomocí standardního příkazu **tar:**

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Když se podíváte na extrahované soubory, najdete všechny soubory protokolu. Většina z nich byla vložena do samostatných adresářů pro každý uzel v clusteru:

<pre><code>
-rw-r--r-- 1 root root  13655 Sep 13 09:01 analysis.txt
-rw-r--r-- 1 root root  14422 Sep 13 09:01 description.txt
-rw-r--r-- 1 root root      0 Sep 13 09:01 events.txt
-rw-r--r-- 1 root root 275560 Sep 13 09:00 ha-log.txt
-rw-r--r-- 1 root root     26 Sep 13 09:00 ha-log.txt.info
drwxr-xr-x 4 root root   4096 Sep 13 09:01 hso-hana-dm
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s1-2
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-0
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-1
drwxr-xr-x 3 root root   4096 Sep 13 09:01 hso-hana-vm-s2-2
-rw-r--r-- 1 root root 264726 Sep 13 09:00 journal.log
</code></pre>


V zadaném časovém rozsahu byl aktuální hlavní uzel **hso-hana-vm-s1-0** zabit. Položky související s touto událostí naleznete v **deníku.log**:

<pre><code>
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: (to hsoadm) root on none
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session opened for user hsoadm by (uid=0)
2018-09-13T07:38:01+0000 hso-hana-vm-s2-1 systemd[1]: Started Session c44290 of user hsoadm.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] A new membership (10.0.0.13:120996) was formed. Members left: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [TOTEM ] Failed to receive the leave message. failed: 1
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Removing all hso-hana-vm-s1-0 attributes for peer loss
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 attrd[28313]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 stonith-ng[28311]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [QUORUM] Members[6]: 7 2 3 4 5 6
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 corosync[28302]:   [MAIN  ] Completed service synchronization, ready to provide service.
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 crmd[28315]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 pacemakerd[28308]:   notice: Node hso-hana-vm-s1-0 state is now lost
2018-09-13T07:38:02+0000 hso-hana-vm-s2-1 cib[28310]:   notice: Purged 1 peer with id=1 and/or uname=hso-hana-vm-s1-0 from the membership cache
2018-09-13T07:38:03+0000 hso-hana-vm-s2-1 su[93494]: pam_unix(su-l:session): session closed for user hsoadm
</code></pre>

Dalším příkladem je soubor protokolu Pacemaker na sekundární maškarní, který se stal novým primárním hlavním serverem. Tento výňatek ukazuje, že stav vyřazeného primárního hlavního uzlu byl nastaven na **offline**:

<pre><code>
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 3 still member of group stonith-ng (peer=hso-hana-vm-s1-2, counter=5.1)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 4 still member of group stonith-ng (peer=hso-hana-vm-s2-0, counter=5.2)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 5 still member of group stonith-ng (peer=hso-hana-vm-s2-1, counter=5.3)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 6 still member of group stonith-ng (peer=hso-hana-vm-s2-2, counter=5.4)
Sep 13 07:38:02 [4178] hso-hana-vm-s2-0 stonith-ng:     info: pcmk_cpg_membership:      Node 7 still member of group stonith-ng (peer=hso-hana-dm, counter=5.5)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 1 left group crmd (peer=hso-hana-vm-s1-0, counter=5.0)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: crm_update_peer_proc:     pcmk_cpg_membership: Node hso-hana-vm-s1-0[1] - corosync-cpg is now offline
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: peer_update_callback:     Client hso-hana-vm-s1-0/peer now has status [offline] (DC=hso-hana-dm, changed=4000000)
Sep 13 07:38:02 [4184] hso-hana-vm-s2-0       crmd:     info: pcmk_cpg_membership:      Node 2 still member of group crmd (peer=hso-hana-vm-s1-1, counter=5.0)
</code></pre>





## <a name="sap-hana-globalini"></a>SAP HANA global.ini


Následující výňatky jsou ze souboru SAP HANA **global.ini** v clusterové lokalitě 2. Tento příklad ukazuje položky řešení názvu hostitele pro použití různých sítí pro komunikaci internode SAP HANA a HSR:

<pre><code>
[communication]
tcp_keepalive_interval = 20
internal_network = 10.0.2/24
listeninterface = .internal
</code></pre>


<pre><code>
[internal_hostname_resolution]
10.0.2.40 = hso-hana-vm-s2-0
10.0.2.42 = hso-hana-vm-s2-2
10.0.2.41 = hso-hana-vm-s2-1
</code></pre>

<pre><code>
[ha_dr_provider_SAPHanaSR]
provider = SAPHanaSR
path = /hana/shared/myHooks
execution_order = 1
</code></pre>

<pre><code>
[system_replication_communication]
listeninterface = .internal

[system_replication_hostname_resolution]
10.0.1.30 = hso-hana-vm-s1-0
10.0.1.31 = hso-hana-vm-s1-1
10.0.1.32 = hso-hana-vm-s1-2
10.0.1.40 = hso-hana-vm-s2-0
10.0.1.41 = hso-hana-vm-s2-1
10.0.1.42 = hso-hana-vm-s2-2
</code></pre>



## <a name="hawk"></a>Hawk

Řešení clusteru poskytuje rozhraní prohlížeče, které nabízí grafické uživatelské rozhraní pro uživatele, kteří dávají přednost nabídkám a grafikám před všemi příkazy na úrovni prostředí.
Chcete-li použít rozhraní ** \<\> ** prohlížeče, nahraďte uzel skutečným uzlem SAP HANA v následující adrese URL. Pak zadejte pověření clusteru (uživatelský **cluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Tento snímek obrazovky ukazuje řídicí panel clusteru:


![Řídicí panel hawk clusteru](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Tento příklad ukazuje omezení umístění způsobená migrací prostředků clusteru, jak je vysvětleno v [části Plánovaná údržba](#planned-maintenance):


![Omezení hawk listu](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Můžete také nahrát **výstup hb_report** v Hawk v části **Historie**, zobrazené následovně. Informace o shromažďování souborů protokolu hb_report: 

![Hawk upload hb_report výstup](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Pomocí **Průzkumníka historie**pak můžete projít všechny přechody clusteru zahrnuté ve **výstupu hb_report:**

![Hawk přechody v hb_report výstupu](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Tento konečný snímek obrazovky ukazuje část **Podrobnosti** jednoho přechodu. Cluster reagoval na selhání primárního hlavního uzlu, uzel **hso-hana-vm-s1-0**. Nyní propaguje sekundární uzel jako nový master, **hso-hana-vm-s2-0**:

![Hawk jediný přechod](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Další kroky

Tento průvodce odstraňováním potíží popisuje vysokou dostupnost pro SAP HANA v konfiguraci horizontálního navýšení kapacity. Kromě databáze je další důležitou součástí v krajině SAP zásobník SAP NetWeaver. Přečtěte si o [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure, které používají SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].

