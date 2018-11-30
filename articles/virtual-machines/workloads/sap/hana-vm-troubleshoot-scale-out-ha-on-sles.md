---
title: Řešení potíží s instalací na HSR Pacemaker škálování na více instancí SAP HANA 2.0 s aktualizací SP3 SLES 12 ve službě Azure virtual machines | Dokumentace Microsoftu
description: Příručka ke kontrole a řešení potíží komplexní SAP HANA horizontální navýšení kapacity konfigurace s vysokou dostupností na základě SAP HANA System Replication (HSR) a Pacemaker na SLES 12 SP3 spuštěný na Azure virtual machines
services: virtual-machines-linux
documentationcenter: ''
author: hermannd
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: f86107c5fcd4c0175d59689718dca15736aa3b17
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497364"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Ověřte a řešení potíží s nastavením vysoké dostupnosti SAP HANA horizontální navýšení kapacity na SLES 12 SP3 

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


Tento článek pomůže zkontrolovat konfiguraci clusteru Pacemaker pro SAP HANA horizontální navýšení kapacity, na kterém běží na Azure virtual machines (VM). Nastavení clusteru bylo dosaženo v kombinaci s SAP HANA System Replication (HSR) a balíček SUSE RPM SAPHanaSR horizontálním navýšením kapacity. Všechny testy byly provést pouze na SUSE SLES 12 SP3. Oddíly v článku zahrnují různé oblasti a zahrnout ukázkové příkazy a výňatky ze konfigurační soubory. Doporučujeme, abyste tyto ukázky jako metodu ověření a zkontrolujte nastavení celého clusteru.



## <a name="important-notes"></a>Důležité poznámky

Všechny testy pro úpravy rozsahu SAP HANA v kombinaci s systémové replikace SAP HANA a Pacemaker bylo provedeno pomocí SAP HANA 2.0 pouze. Verze operačního systému SUSE Linux Enterprise Server 12 SP3 byla pro aplikace SAP. Nejnovější balíček RPM SAPHanaSR škálování od společnosti SUSE, se použil k vytvoření clusteru Pacemaker.
SUSE publikovaná [podrobný popis tohoto instalačního programu optimalizováno pro výkon][sles-hana-scale-out-ha-paper].

Typy virtuálních počítačů, které jsou podporovány pro horizontální navýšení kapacity SAP HANA, zkontrolujte [IaaS directory s certifikací SAP HANA][sap-hana-iaas-list].

Došlo technického problému s Škálováním SAP HANA v kombinaci s více podsítí a virtuální síťové adaptéry a nastavení HSR. Je nutné použít nejnovější opravy SAP HANA 2.0, ve kterém se tento problém vyřešen. Jsou podporovány následující verze SAP HANA: 

* rev2.00.024.04 nebo vyšší 
* rev2.00.032 nebo vyšší

Pokud potřebujete podporu od společnosti SUSE, proveďte to [průvodce][suse-pacemaker-support-log-files]. Shromáždí všechny informace o SAP HANA vysokou dostupnost (HA) clusteru, jak je popsáno v článku. Podpora SUSE potřebuje tyto informace k další analýze.

Při interním testování nastavení clusteru máte nerozumíte normální řádné vypnutí virtuálního počítače prostřednictvím webu Azure portal. Proto doporučujeme, testování převzetí služeb při selhání clusteru pomocí jiných metod. Používá metody, například vynucení poplachu jádra, nebo vypnout sítě nebo migrovat **msl** prostředků. Podrobnosti najdete v následujících částech. Předpokladem je, že standardní vypnutí se stane s záměr. Nejlepším příkladem úmyslné vypnutí je kvůli údržbě. Podrobnosti najdete v [plánované údržby](#planned-maintenance).

Navíc při interním testování nastavení clusteru máte zaměňovat po ruční převzetí SAP HANA při clusteru do režimu údržby. Doporučujeme vám, že můžete přepnout zpět znovu ručně před ukončení režimu údržby clusteru. Další možností je k aktivaci převzetí služeb při selhání před převést cluster do režimu údržby. Další informace najdete v tématu [plánované údržby](#planned-maintenance). V dokumentaci od společnosti SUSE popisuje, jak cluster tímto způsobem můžete obnovit pomocí **crm** příkazu. Ale přístup uvedené dříve byla při interním testování robustní a nikdy jsme si ukázali, žádné neočekávané vedlejší účinky.

Při použití **crm migrovat** příkaz, ujistěte se, že pro vyčištění konfigurace clusteru. Přidá omezení umístění, které nemusíte být vědomi. Tato omezení ovlivnit chování clusteru. Další podrobnosti najdete v [plánované údržby](#planned-maintenance).



## <a name="test-system-description"></a>Popis systému testu.

 Pro ověření HA škálování na více instancí SAP HANA a certifikace se použil nastavení. To se skládal z obou systémů se třemi SAP HANA uzly každý: jeden hlavní server a dva pracovní procesy. Následující názvy virtuálních počítačů seznamy tabulek a interních IP adres. Na tyto virtuální počítače byly dokončeny všechny ukázky ověřování, které následují. Pomocí těchto názvy virtuálních počítačů a IP adresy v příkazu ukázky můžete lépe pochopili příkazy a jejich výstupy:


| Typ uzlu | název virtuálního počítače | IP adresa |
| --- | --- | --- |
| Hlavní uzel na webu 1 | hso-hana-vm-s1-0 | 10.0.0.30 |
| Pracovní uzel 1 na webu 1 | hso-hana-vm-s1-1 | 10.0.0.31 |
| Pracovní uzel 2 na webu 1 | hso-hana-vm-s1-2 | 10.0.0.32 |
| | | |
| Hlavní uzel na webu 2 | hso-hana-vm-s2-0 | 10.0.0.40 |
| Pracovní uzel 1 na webu 2 | hso-hana-vm-s2-1 | 10.0.0.41 |
| Pracovní uzel 2 na webu 2 | hso-hana-vm-s2-2  | 10.0.0.42 |
| | | |
| Majoritní maker | hso-hana-dm | 10.0.0.13 |
| Server SBD zařízení | hso-hana-sbd | 10.0.0.19 |
| | | |
| Server systému souborů NFS 1 | hso-systému souborů nfs-vm-0 | adresu 10.0.0.15 |
| Server systému souborů NFS 2 | hso-systému souborů nfs-vm-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Více podsítí a virtuální síťové adaptéry

Následující doporučení pro síť SAP HANA byly vytvořeny tři podsítě v rámci jedné virtuální sítě Azure. Úpravy rozsahu SAP HANA v Azure musí být nainstalovaná ve sdíleném režimu. To znamená, že každý uzel používá místní diskové svazky pro **/hana/data** a **/hana/log**. Vzhledem k tomu, že uzly používat pouze místní diskové svazky, není nutné definovat samostatnou podsíť pro úložiště:

- 10.0.2.0/24 pro komunikaci mezi uzly SAP HANA
- 10.0.1.0/24 pro SAP HANA System Replication (HSR)
- 10.0.0.0/24 pro všechno ostatní

Informace o SAP HANA konfigurace související s používáním více sítí najdete v tématu [SAP HANA global.ini](#sap-hana-globalini).

Každý virtuální počítač v clusteru má tři virtuální síťové adaptéry, které odpovídají počtu podsítí. [Jak vytvořit virtuální počítač s Linuxem v Azure s několika síťových karet] [ azure-linux-multiple-nics] popisuje potenciální problém směrování v Azure, při nasazování virtuálního počítače s Linuxem. Tento konkrétní směrování článek se týká pouze pro použití víc karet Vnic. Problém je vyřešen podle operačního systému SUSE na výchozím nastavení v SLES 12 SP3. Další informace najdete v tématu [s více síťovými Kartami s cloudu netconfig EC2 a Azure][suse-cloud-netconfig].


Pokud chcete ověřit, že SAP HANA je správně nakonfigurován na používání více sítí, spusťte následující příkazy. Nejprve zkontrolujte na úrovni operačního systému, že všechny tři interních IP adres pro všechny tři podsítě jsou aktivní. Pokud jste definovali podsítě pomocí různých rozsahů IP adres, je nutné přizpůsobit příkazy:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Následující ukázkový výstup je z druhého uzlu pracovního procesu na webu 2. Zobrazí se tří různých interních IP adres z eth0 eth1 a eth2:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


V dalším kroku ověření portů SAP HANA pro název serveru a funkce HSR. SAP HANA naslouchat požadavkům na odpovídající podsítě. V závislosti na číslo instance SAP HANA budete muset přizpůsobit příkazy. U testovacího systému číslo instance byla **00**. Existují různé způsoby, jak zjistit, jaké porty se používají. 

Následující příkaz SQL vrátí instance ID, číslo instance a další informace:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Najít správný port čísla, můžete se podívat, například, HANA Studio v části **konfigurace** nebo prostřednictvím příkazu SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Budete chtít najít každý port, který se používá v zásobníku softwaru SAP, včetně SAP HANA, vyhledat [portů TCP/IP, které všechny produkty SAP][sap-list-port-numbers].

Zadané číslo instance **00** v testovacím systému SAP HANA 2.0, je číslo portu pro názvový server **30001**. Číslo portu pro komunikaci metadat HSR **40002**. K přihlášení do pracovního uzlu a pak zkontrolujte služeb hlavního uzlu je jednou z možností. Pro účely tohoto článku jsme kontroluje pracovního uzlu 2 na webu 2 pokusu o připojení k hlavnímu uzlu na webu 2.

Zkontrolujte název serveru port:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

K prokázání, že komunikace mezi uzly používá podsíť **10.0.2.0/24**, výsledkem by měl vypadat jako následující ukázkový výstup.
Pouze připojení přes podsítě **10.0.2.0/24** uspěli:

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Teď zkontrolujte HSR port **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

K prokázání, že komunikace HSR používá podsíť **10.0.1.0/24**, výsledkem by měl vypadat jako následující ukázkový výstup.
Pouze připojení přes podsítě **10.0.1.0/24** uspěli:

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


**Corosync** konfigurační soubor musí být na všech uzlech v clusteru, včetně maker majoritní správné. Pokud připojení clusteru uzel nebude fungovat podle očekávání, vytvořit nebo zkopírovat **/etc/corosync/corosync.conf** ručně na všech uzlech a restartovat službu. 

Obsah **corosync.conf** z testovacího systému je příklad.

První část je **totem**, jak je popsáno v [clusteru instalace](https://review.docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#cluster-installation), kroku 11. Hodnota, můžete ignorovat **mcastaddr**. Právě ponechte existující položku. Položky pro **token** a **caiq** musí být nastavena podle [dokumentace ke službě Microsoft Azure SAP HANA][sles-pacemaker-ha-guide].

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

Druhá část **protokolování**, nebylo změněno z dané hodnoty výchozích hodnot:

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

Třetí část ukazuje **seznamu**. Všechny uzly clusteru mají zobrazení s jejich **nodeid**:

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

V poslední části **kvora**, je potřeba nastavit hodnotu pro **expected_votes** správně. Musí být počet uzlů, včetně maker majoritní. A hodnota **two_node** musí být **0**. Není úplně odeberte položku. Stačí nastavit hodnotu na **0**.

<pre><code>
quorum {
    # Enable and configure quorum subsystem (default: off)
    # see also corosync.conf.5 and votequorum.5
    provider: corosync_votequorum
    expected_votes: 7
    two_node: 0
}
</code></pre>


Restartujte službu prostřednictvím **systemctl**:

<pre><code>
systemctl restart corosync
</code></pre>




## <a name="sbd-device"></a>SBD zařízení

Jak nastavit zařízení s SBD na Virtuálním počítači Azure je popsán v [monitorování geografických zón SBD](https://review.docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing).

Nejdřív zkontrolujte na serveru SBD virtuálního počítače, pokud existují položky seznamů ACL pro každý uzel v clusteru. Na serveru SBD virtuálního počítače, spusťte následující příkaz:


<pre><code>
targetcli ls
</code></pre>


V testovacím systému výstup příkazu by měl vypadat jako v následující ukázce. Seznam ACL názvy jako **iqn.2006-04.hso-db-0.local:hso-db-0** musí být zadán jako iniciátor názvům na virtuálních počítačích. Každému virtuálnímu počítači potřebuje jinou.

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

Zkontrolujte, že názvy iniciátoru do všech virtuálních počítačů se liší a odpovídají dříve zobrazených položek. Tento příklad je z uzlu pracovního procesu 1 na webu 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Výstup bude vypadat takto:

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

Dále ověřte, že **zjišťování** funguje správně. Spusťte následující příkaz na všech uzlech clusteru s použitím IP adresy serveru SBD virtuálního počítače:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Výstup by měl vypadat jako v následující ukázce:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Další důkazu bod je k ověření, že se uzel zobrazí SDB zařízení. Na všech uzlech, včetně maker majoritní zkontrolujte ho:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Výstup by měl vypadat jako v následující ukázce. Názvy však mohou lišit. Po restartování virtuálního počítače může také změnit název zařízení:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

V závislosti na stavu systému někdy pomáhá restartujte služby iSCSI při řešení problémů. Potom spusťte následující příkazy:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Z libovolného uzlu, můžete zkontrolovat, jestli jsou všechny uzly **vymazat**. Ujistěte se, že používáte správný název zařízení v konkrétním uzlu:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Výstup by měl zobrazit **vymazat** pro každý uzel v clusteru:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Zkontrolujte další SBD **s výpisem paměti** možnost **sbd** příkazu. Tento ukázkový příkaz a výstup z uzlu majority maker, název zařízení jste **sdd**, nikoli **sdm**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Výstup, kromě název zařízení by měl vypadat stejné ve všech uzlech:

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

Jeden další kontrolu SBD je možné odeslat zprávu do jiného uzlu. Odeslat zprávu na pracovním uzlu 2 na webu 2, spusťte následující příkaz na pracovním uzlu 1 na webu 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Na cílové straně virtuálního počítače **hso-hana-vm-s2-2** v tomto příkladu můžete najít následující položku v **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Zkontrolujte, že položky v **/etc/sysconfig/sbd** odpovídají ve [nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](https://review.docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#sbd-fencing). Ověřte, že nastavení spuštění **/etc/iscsi/iscsid.conf** byla nastavena na automatické.

Následující položky jsou důležité pro **/etc/sysconfig/sbd**. Přizpůsobit **id** hodnota v případě potřeby:

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Zkontrolujte nastavení spouštění v **/etc/iscsi/iscsid.conf**. Povinné nastavení mělo stát u následujících **iscsiadm** popsaného v dokumentaci k příkazu. Ověřte a přizpůsobit jej ručně pomocí **vi** Pokud se liší.

Tento příkaz nastaví chování při spuštění:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Ujistěte se, tato položka se v **/etc/iscsi/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Během testování a ověřování po restartování virtuálního počítače se už v některých případech SBD zařízení. Došlo k neshodě mezi nastavení spouštění a co YaST2 jsme si ukázali. Pokud chcete zkontrolovat nastavení, proveďte tyto kroky:

1. Začněte YaST2.
2. Vyberte **síťové služby** na levé straně.
3. Posuňte se dolů na pravé straně **iniciátor iSCSI** a vyberte ji.
4. Na další obrazovce pod **služby** kartu, se zobrazí název jedinečný iniciátor pro uzel.
5. Nad název iniciátoru, ujistěte se, že **spustit službu** nastavena na hodnotu **při spouštění**.
6. Pokud není, nastavte ho na **při spouštění** místo **ručně**.
7. V dalším kroku přepnout na horní kartě **připojené cíle**.
8. Na **připojené cíle** obrazovky, měli byste vidět položku pro zařízení SBD podobně jako tento příklad: **10.0.0.19:3260 iqn.2006-04.dbhso.local:dbhso**.
9. Zkontrolujte, zda **spuštění** nastavena na hodnotu **při spuštění**.
10. Pokud ne, zvolte **upravit** a změňte ho.
11. Uložte změny a zavřete YaST2.



## <a name="pacemaker"></a>Pacemaker

Poté, co všechno je správně nastavené, můžete na všech uzlech, chcete-li zkontrolovat stav služby Pacemaker spusťte následující příkaz:

<pre><code>
systemctl status pacemaker
</code></pre>

Horní části výstup by měl vypadat jako v následující ukázce. Je důležité, stav po **aktivní** se zobrazuje jako **načíst** a **aktivní (spuštěno)**. Stav po **Loaded** musí zobrazit jako **povolené**.

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

Pokud je stále v nastavení **zakázané**, spusťte následující příkaz:

<pre><code>
systemctl enable pacemaker
</code></pre>

Pokud chcete zobrazit všechny nakonfigurované prostředky v Pacemaker, spusťte následující příkaz:

<pre><code>
crm status
</code></pre>

Výstup by měl vypadat jako v následující ukázce. Má doladit, který **cln** a **msl** prostředky jsou uvedeny jako zastavení Tvůrce virtuálního počítače, většinou **hso-hana-dm**. Neexistuje žádná instalace SAP HANA na uzel majority maker. Proto **cln** a **msl** prostředky jsou uvedeny jako zastavena. Je důležité, zobrazuje správné celkový počet virtuálních počítačů, **7**. Všechny virtuální počítače, které jsou součástí clusteru, musí být uvedené se stavem **Online**. Aktuální primární hlavní uzel musí správně rozpoznány. V tomto příkladu má **hso-hana-vm-s1-0**:

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

O důležitou funkci Pacemaker je režim údržby. V tomto režimu můžete provádět změny bez webinářem akce okamžité clusteru. Příkladem je virtuální počítač restartovat. Typické použití případů by plánované údržbě infrastruktury operačního systému nebo v Azure. Zobrazit [plánované údržby](#planned-maintenance). Pacemaker převést do režimu údržby, použijte následující příkaz:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Když zaškrtnete s **crm stav**, si všimnete ve výstupu, který všechny prostředky se označí jako **nespravované**. V tomto stavu není clusteru reagovat na změny, jako je spouštění nebo zastavování SAP HANA.
Následující příklad ukazuje výstup **crm stav** příkaz spuštěného clusteru v režimu údržby:

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


Tento ukázkový příkaz ukazuje, jak do konce režimu údržby clusteru:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Jiné **crm** příkazu je získán úplná konfigurace clusteru do editoru, takže ho můžete upravit. Uloží změny, cluster se spustí po příslušné akce:

<pre><code>
crm configure edit
</code></pre>

Podívejte se na úplná konfigurace clusteru, použijte **crm zobrazit** možnost:

<pre><code>
crm configure show
</code></pre>



Po selhání prostředky clusteru **crm stav** příkaz zobrazí seznam **akce se nezdařilo**. Viz následující ukázka tento výstup:


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

Je potřeba provést čištění cluster po selhání. Použít **crm** příkaz znovu a použijte příkaz Možnosti **vyčištění** zbavit těchto nepovedlo akce položky. Název odpovídající prostředku clusteru takto:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Příkaz by měl vrátit výstup jako v následující ukázce:

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

Jak je popsáno v [důležité poznámky](#important-notes), neměli byste používat standardní řádné vypnutí testování clusteru převzetí služeb při selhání nebo převzetí SAP HANA HSR. Namísto toho doporučujeme aktivovat poplachu jádra, vynutit migrace prostředků nebo může být vypnout všechny sítě na úrovni operačního systému virtuálního počítače. Další metodou, je **crm \<uzel\> pohotovostní** příkazu. Zobrazit [SUSE dokumentu][sles-12-ha-paper]. 

Následující tři ukázkové příkazy můžete vynutit převzetí služeb clusteru při:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Jak je popsáno v [plánované údržby](#planned-maintenance), je dobrým způsobem, jak monitorovat aktivity clusteru spustit **SAPHanaSR showAttr** s **watch** příkaz:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Pomáhá také podívat na stav prostředí SAP HANA pocházející ze skriptu Pythonu SAP. Nastavení clusteru hledá tuto hodnotu stavu. Jasně pochopí berete při rozhodování o selhání uzlu pracovního procesu. Pokud pracovní uzel ocitne mimo provoz, SAP HANA nevrací okamžitě chybu pro stav systému zcela horizontální navýšení kapacity. 

Existují některé opakování, aby se zabránilo zbytečným převzetí služeb při selhání. Cluster reaguje pouze v případě, že stav se změní z **Ok**, návratová hodnota **4**do **chyba**, návratová hodnota **1**. Tak, aby byl správné Pokud výstup z **SAPHanaSR showAttr** ukazuje virtuálního počítače se stavem **offline**. Ale neexistuje žádná aktivita ještě chcete přepnout primární a sekundární. Žádná aktivita clusteru získá aktivované tak dlouho, dokud nebude SAP HANA vrátí chybu.

Můžete monitorovat stav prostředí SAP HANA jako uživatel  **\<HANA SID\>adm** voláním skript Pythonu SAP. Možná budete muset přizpůsobit cesta:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Výstup tohoto příkazu by měl vypadat jako v následující ukázce. **Stav hostitele** sloupce a **celkový stav hostitele** jsou důležité. Aktuální výstup je širší s další sloupce.
Chcete-li výstupní tabulce lépe čitelný v tomto dokumentu, byly odebrány většina sloupce na pravé straně:

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


Existuje jiný příkaz a zkontrolujte aktuální aktivity clusteru. Viz následující příkaz a výstupní tail po hlavní uzel primárního webu byl ukončen. Zobrazí seznam přechod akce, jako je **zvyšuje se úroveň** bývalé sekundární hlavní uzel **hso-hana-vm-s2-0**, na nový primární hlavní server. Pokud všechno, co je v pořádku, a všechny aktivity jsou dokončené, to **přechod Souhrn** seznamu musí být prázdná.

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

Při rozhodování o plánované údržbě existují různé případy použití. Jedna otázka je, zda je právě údržbu infrastruktury jako změny na úrovni operačního systému a konfigurace disku nebo upgradu HANA.
Další informace najdete v dokumentech od společnosti SUSE jako [směrem k nule výpadek] [ sles-zero-downtime-paper] nebo [SAP HANA SR výkonu optimalizované scénář] [ sles-12-for-sap]. Tyto dokumenty také obsahovat ukázky, které ukazují, jak ručně provést migraci primární.

Velký interní testování bylo provedeno ověření případ použití údržbu infrastruktury. Aby se zabránilo veškeré problémy související s primární migrací, jsme se rozhodli vždy migrovat primární před přepnutím clusteru do režimu údržby. Díky tomu není nutné provádět clusteru, nemusíte zabývat bývalé situaci: které straně byl primární a která byla sekundární.

Existují dva různé situace v tomto ohledu:

- **Plánovaná údržba aktuální sekundární**. V takovém případě vám stačí převést cluster do režimu údržby a práci na sekundárním, aniž by to ovlivnilo clusteru.

- **Plánovaná údržba aktuální primární**. Tak, aby uživatelé můžou dál fungovat během údržby, budete muset vynutit převzetí služeb při selhání. S tímto přístupem musí spustit clusteru převzetí služeb při selhání Pacemaker a nikoli pouze na úrovni SAP HANA HSR. Pacemaker instalační program automaticky aktivuje převzetí SAP HANA. Také budete muset provést převzetí služeb při selhání před převést cluster do režimu údržby.

Postup údržby na aktuální sekundární lokalitě je následujícím způsobem:

1. Umístění clusteru do režimu údržby.
2. Provedení práce v sekundární lokalitě. 
3. Ukončit režim údržby clusteru.

Postup údržby na stávající primární lokality je složitější:

1. Ručně aktivujete převzetí služeb při selhání nebo převzetí SAP HANA přes Pacemaker migrace prostředků. Zobrazte podrobnosti, které následují.
2. SAP HANA v původní primární lokality získá vypnout pomocí nastavení clusteru.
3. Umístění clusteru do režimu údržby.
4. Po dokončení údržbových prací zaregistrujte původní primární jako nové sekundární lokality.
5. Vyčištění konfigurace clusteru. Zobrazte podrobnosti, které následují.
6. Ukončit režim údržby clusteru.


Migraci prostředku přidá položku do konfigurace clusteru. Příkladem je vynucení převzetí služeb při selhání. Budete muset před ukončení režimu údržby vyčistit tyto položky. Najdete v následující ukázce.

Nejprve vynutit převzetí služeb clusteru při migraci **msl** prostředků na aktuální sekundární hlavní uzel. Tento příkaz zobrazí upozornění, která **přesunout omezení** byl vytvořen:

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Zkontrolujte proces převzetí služeb při selhání prostřednictvím příkazu **SAPHanaSR showAttr**. Pokud chcete monitorovat stav clusteru, otevřete okno vyhrazené prostředí a spuštění příkazu s **watch**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Výstup by měl zobrazit ruční převzetí služeb při selhání. Máte bývalé sekundární hlavní uzel **povýšen**, v této ukázce **hso-hana-vm-s2-0**. Původní primární server byl zastaven, **lss** hodnotu **1** pro původní primární hlavní uzel **hso-hana-vm-s1-0**: 

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

Po převzetí služeb při selhání clusteru a SAP HANA převzetí, umístěte clusteru do režimu údržby a jak je popsáno v [Pacemaker](#pacemaker).

Příkazy **SAPHanaSR showAttr** a **crm stav** neindikují nic o omezení vytvořené migrace prostředků. Jednou z možností a aktivujte těchto omezení je zobrazíte úplná prostředek konfigurace clusteru pomocí následujícího příkazu:

<pre><code>
crm configure show
</code></pre>

V rámci konfigurace clusteru pro vás nová omezení umístění způsobené původní prostředek ruční migrace. Tento příklad položky začíná **umístění rozhraní příkazového řádku -**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Bohužel těchto omezení může mít vliv na celkové chování clusteru. Proto je nutné je před spuštěním zálohování v celém systému znovu odebrat. S **unmigrate** příkaz, je možné k vyčištění omezení umístění, které byly vytvořeny před. Pojmenování může být trochu matoucí. Není zkuste ho migrovat prostředek zpět do původního virtuálního počítače, ze kterého byla migrována. Pouze odstraní omezení, umístění a také vrátí hodnotu odpovídající informace, když spustíte příkaz:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Na konci údržbových prací, můžete zastavit režim údržby clusteru jak je znázorněno v [Pacemaker](#pacemaker).



## <a name="hbreport-to-collect-log-files"></a>hb_report shromažďovat soubory protokolů

Pokud chcete analyzovat problémy Pacemaker clusteru, je užitečné a také požadovaný podpora SUSE ke spuštění **hb_report** nástroj. Shromáždí všechny soubory důležité protokolů, které potřebujete k analýze, co se stalo. Toto volání Ukázka používá počáteční a koncový čas, kde došlo k konkrétním incidentu. Viz také [důležité poznámky](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Příkaz sděluje, kam umístit komprimované protokolové soubory:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Potom můžete extrahovat jednotlivé soubory pomocí standardu **cíl** příkaz:

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Při pohledu na extrahované soubory najít všechny soubory protokolů. Většina z nich byly umístit do samostatných adresáře pro každý uzel v clusteru:

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


V rámci časového rozsahu, který byl zadán, aktuální hlavní uzel **hso-hana-vm-s1-0** byl ukončen. Můžete najít položky související s touto událostí v **journal.log**:

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

Dalším příkladem je soubor protokolu Pacemaker na sekundární hlavní server, kde byl program nové primární hlavní server. Tento výpis ukazuje, že byl nastaven stav ukončil. primární hlavní uzel **offline**:

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





## <a name="sap-hana-globalini"></a>Global.ini SAP HANA


Následující ukázky jsou ze SAP HANA **global.ini** soubor na web clusteru 2. Tento příklad ukazuje záznamy překladů IP adres pro používání různých sítí pro komunikaci mezi uzly SAP HANA a funkce HSR název hostitele:

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



## <a name="hawk"></a>HAWK

Řešení clusteru poskytuje rozhraní prohlížeče, který nabízí grafické uživatelské rozhraní pro uživatele, kteří dávají přednost nabídky a grafiku s tím, že všechny příkazy na úrovni prostředí.
Abyste mohli použít rozhraní prohlížeče, nahraďte **\<uzel\>** skutečné uzlu SAP HANA v následující adrese URL. Pak zadejte přihlašovací údaje clusteru (uživatel **clusteru**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Tento snímek obrazovky znázorňuje řídicí panel clusteru:


![Řídicí panel clusteru HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Tento příklad ukazuje způsobené migrace prostředků clusteru, jak je vysvětleno v omezení umístění [plánované údržby](#planned-maintenance):


![HAWK seznamu omezení](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Můžete také nahrát **hb_report** výstup v Hawk pod **historie**, jak je znázorněno následujícím způsobem. Zobrazit [hb_report shromažďovat soubory protokolů](#hbreport-to-collect-log-files): 

![Výstup hb_report HAWK nahrávání](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

S **historie Explorer**, potom projděte všechny přechody clusteru součástí **hb_report** výstup:

![Přechody HAWK ve výstupu hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Tento poslední snímek obrazovky ukazuje **podrobnosti** část jeden přechod. Cluster reagovalo na selhání primární hlavní uzel uzlu **hso-hana-vm-s1-0**. Nyní zvýšení úrovně sekundárního uzlu na novou hlavní server **hso-hana-vm-s2-0**:

![Jeden přechod HAWK](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Další postup

Tento průvodce odstraňováním potíží popisuje vysokou dostupnost pro SAP HANA v konfiguraci horizontální navýšení kapacity. Kromě databázi je jiné důležité součásti v prostředí SAP SAP NetWeaver zásobníku. Další informace o [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích s Azure, které používají operačního systému SUSE Enterprise Linux Server][sap-nw-ha-guide-sles].

