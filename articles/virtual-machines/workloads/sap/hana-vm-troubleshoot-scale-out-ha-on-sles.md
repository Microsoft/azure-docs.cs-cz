---
title: Řešení potíží s SLES HSR-Pacemaker na více instancí s na virtuálních počítačích Azure | SAP HANA Microsoft Docs
description: Průvodce pro kontrolu a odstraňování problémů komplexního SAP HANA konfigurace s vysokou dostupností škálované na základě SAP HANA systémové replikace (HSR) a Pacemaker v SLES 12 SP3 běžící na virtuálních počítačích Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/24/2018
ms.author: hermannd
ms.openlocfilehash: f4c1de484ce2659a7e84a1546a7c49c1d77a7d56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101674497"
---
# <a name="verify-and-troubleshoot-sap-hana-scale-out-high-availability-setup-on-sles-12-sp3"></a>Ověření a řešení potíží SAP HANA nastavení vysoké dostupnosti škálování na SLES 12 SP3 

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


Tento článek vám pomůže s kontrolou konfigurace clusteru Pacemaker pro SAP HANA škálování na více instancí, které běží na virtuálních počítačích Azure (VM). Instalace clusteru byla zajištěna v kombinaci s SAP HANA systémová replikace (HSR) a balíček SUSE pro SAPHanaSR – škálování. Všechny testy byly provedeny pouze v SUSE SLES 12 SP3. Oddíly článku zahrnují různé oblasti a obsahují vzorové příkazy a výňatky z konfiguračních souborů. Tyto ukázky doporučujeme jako metodu ověření a kontroly celého nastavení clusteru.



## <a name="important-notes"></a>Důležité poznámky

Všechny testy pro SAP HANA škálování v kombinaci s SAP HANA systémové replikace a Pacemaker byly provedeny pouze SAP HANA 2,0. Verze operačního systému byla SUSE Linux Enterprise Server 12 SP3 pro aplikace SAP. K nastavení clusteru Pacemaker se použil poslední balíček ot./min. SAPHanaSR-ScaleOut z SUSE.
SUSE publikoval [podrobný popis tohoto nastavení optimalizovaného pro výkon][sles-hana-scale-out-ha-paper].

U typů virtuálních počítačů, které jsou podporované pro SAP HANA škálování na více instancí, se podívejte na [SAP HANA certifikovaný adresář IaaS][sap-hana-iaas-list].

> [!NOTE]
> Tento článek obsahuje odkazy na *Hlavní* a *podřízené* výrazy, které Microsoft už nepoužívá. Po odebrání těchto podmínek ze softwaru je odebereme z tohoto článku.

V kombinaci s více podsítěmi a virtuální síťové adaptéry a nastavením HSR došlo k technickým potížím se škálováním na více instancí SAP HANA. Je nutné použít nejnovější opravy SAP HANA 2,0, kde byl tento problém vyřešen. Podporovány jsou následující verze SAP HANA: 

* REV 2.00.024.04 nebo vyšší 
* REV 2.00.032 nebo vyšší

Pokud potřebujete podporu od SUSE, postupujte podle pokynů v tomto [Průvodci][suse-pacemaker-support-log-files]. Shromážděte všechny informace o SAP HANA clusteru s vysokou dostupností (HA), jak je popsáno v článku. Podpora SUSE potřebuje tyto informace k další analýze.

Během interního testování bylo instalace clusteru zaměňována z normálního bezproblémového vypínání virtuálního počítače prostřednictvím Azure Portal. Proto doporučujeme, abyste provedli testování převzetí služeb při selhání clusteru jinými metodami. Použijte metody, jako je vynucení nouzového jádra nebo vypnutí sítí nebo migrace prostředku **MSL** . Podívejte se na podrobnosti v následujících oddílech. Předpokladem je, že standardní vypínání proběhne s úmyslem. Nejlepším příkladem úmyslného vypnutí je údržba. Podrobnosti najdete v části [plánovaná údržba](#planned-maintenance).

Během interního testování se instalace clusteru po manuální SAP HANA převzetí změnila, ale cluster byl v režimu údržby. Doporučujeme, abyste ho znovu přepnuli předtím, než ukončíte režim údržby clusteru. Další možností je aktivovat převzetí služeb při selhání předtím, než přepnete cluster do režimu údržby. Další informace najdete v tématu [plánovaná údržba](#planned-maintenance). Dokumentace z SUSE popisuje, jak můžete cluster obnovit tímto způsobem pomocí příkazu **CRM** . Výše zmíněný postup byl ale robustní během interního testování a nikdy nevykazoval žádné neočekávané vedlejší účinky.

Když použijete příkaz pro **migraci CRM** , nezapomeňte vyčistit konfiguraci clusteru. Přidává omezení umístění, o kterých nemusíte vědět. Tato omezení ovlivňují chování clusteru. Další podrobnosti najdete v části [plánovaná údržba](#planned-maintenance).



## <a name="test-system-description"></a>Popis testovacího systému

 Pro SAP HANA ověřování a certifikace HA se škálováním na více instancí se použila instalace. Skládá se ze dvou systémů se třemi SAP HANA uzly: jeden hlavní a dva pracovní procesy. V následující tabulce jsou uvedeny názvy virtuálních počítačů a interní IP adresy. Všechny kontrolní vzory, které následují, byly na těchto virtuálních počítačích provedeny. Pomocí těchto názvů virtuálních počítačů a IP adres v ukázkách příkazů můžete lépe pochopit příkazy a jejich výstupy:


| Typ uzlu | název virtuálního počítače | IP adresa |
| --- | --- | --- |
| Hlavní uzel v lokalitě 1 | hSo-Hana-VM-S1-0 | 10.0.0.30 |
| Pracovní uzel 1 v lokalitě 1 | hSo-Hana-VM-S1-1 | 10.0.0.31 |
| Pracovní uzel 2 v lokalitě 1 | hSo-Hana-VM-S1-2 | 10.0.0.32 |
| | | |
| Hlavní uzel v lokalitě 2 | hSo-Hana-VM-S2-0 | 10.0.0.40 |
| Pracovní uzel 1 v lokalitě 2 | hSo-Hana-VM-S2-1 | 10.0.0.41 |
| Pracovní uzel 2 v lokalitě 2 | hSo-Hana-VM-S2-2  | 10.0.0.42 |
| | | |
| Majoritní uzel maker | hSo-Hana-DM | 10.0.0.13 |
| Server zařízení SBD | hSo – Hana – SBD | 10.0.0.19 |
| | | |
| Server NFS 1 | hSo-NFS-VM-0 | 10.0.0.15 |
| Server NFS 2 | hSo-NFS-VM-1 | 10.0.0.14 |



## <a name="multiple-subnets-and-vnics"></a>Několik podsítí a virtuální síťové adaptéry

Po SAP HANA doporučení k síti se v jedné virtuální síti Azure vytvořily tři podsítě. SAP HANA horizontálního navýšení kapacity v Azure musí být nainstalované v nesdíleném režimu. To znamená, že každý uzel používá pro **/Hana/data** a **/Hana/log** svazky na místních discích. Vzhledem k tomu, že uzly používají pouze svazky na místních discích, není nutné definovat samostatnou podsíť pro úložiště:

- 10.0.2.0/24 pro SAP HANA komunikaci mezi uzly
- 10.0.1.0/24 pro replikaci systému SAP HANA (HSR)
- 10.0.0.0/24 pro všechno ostatní

Informace o konfiguraci SAP HANA souvisejících s používáním více sítí najdete v tématu [SAP HANA global.ini](#sap-hana-globalini).

Každý virtuální počítač v clusteru má tři virtuální síťové AdaptéryY, které odpovídají počtu podsítí. [Postup vytvoření virtuálního počítače se systémem Linux v Azure s několika síťovými kartami][azure-linux-multiple-nics] popisuje potenciální problém s směrováním v Azure při nasazení virtuálního počítače se systémem Linux. Tento konkrétní článek směrování platí jenom pro použití víc virtuální síťové adaptéry. Problém je vyřešen SUSE na výchozí hodnotu v SLES 12 SP3. Další informace najdete v tématu [s více síťovými kartami s cloudovým netconfig v EC2 a Azure][suse-cloud-netconfig].


Pokud chcete ověřit, jestli je SAP HANA správně nakonfigurovaná tak, aby používala více sítí, spusťte následující příkazy. Nejdřív na úrovni operačního systému ověřte, že všechny tři interní IP adresy pro všechny tři podsítě jsou aktivní. Pokud jste definovali podsítě s různými rozsahy IP adres, je nutné upravit příkazy:

<pre><code>
ifconfig | grep "inet addr:10\."
</code></pre>

Následující vzorový výstup pochází z druhého pracovního uzlu v lokalitě 2. V eth0, eth1 a ETH2 se můžete podívat na tři různé interní IP adresy:

<pre><code>
inet addr:10.0.0.42  Bcast:10.0.0.255  Mask:255.255.255.0
inet addr:10.0.1.42  Bcast:10.0.1.255  Mask:255.255.255.0
inet addr:10.0.2.42  Bcast:10.0.2.255  Mask:255.255.255.0
</code></pre>


V dalším kroku ověřte SAP HANA porty názvového serveru a HSR. SAP HANA by měl naslouchat na odpovídajících podsítích. V závislosti na čísle instance SAP HANA je třeba upravit příkazy. V testovacím systému byl číslo instance **00**. Existují různé způsoby, jak zjistit, jaké porty se používají. 

Následující příkaz jazyka SQL vrátí ID instance, číslo instance a další informace:

<pre><code>
select * from "SYS"."M_SYSTEM_OVERVIEW"
</code></pre>

Správná čísla portů najdete například v HANA studiu v části **Konfigurace** nebo pomocí příkazu SQL:

<pre><code>
select * from M_INIFILE_CONTENTS WHERE KEY LIKE 'listen%'
</code></pre>

Pokud chcete najít každý port, který se používá v softwarovém zásobníku SAP, včetně SAP HANA, prohledejte [porty TCP/IP všech produktů SAP][sap-list-port-numbers].

Pro číslo instance **00** v testovacím systému SAP HANA 2,0 je číslo portu pro názvový server **30001**. Číslo portu pro komunikaci s HSR metadaty je **40002**. Jednou z možností je přihlásit se k pracovnímu uzlu a potom kontrolovat služby hlavního uzlu. V tomto článku jsme kontrolovali pracovní uzel 2 v lokalitě 2, který se pokouší připojit k hlavnímu uzlu v lokalitě 2.

Ověřte port názvového serveru:

<pre><code>
nc -vz 10.0.0.40 30001
nc -vz 10.0.1.40 30001
nc -vz 10.0.2.40 30001
</code></pre>

Aby bylo možné prokázat, že komunikace mezi uzly používá podsíť **10.0.2.0/24**, výsledek by měl vypadat jako následující vzorový výstup.
Úspěšné je jenom připojení přes podsíť **10.0.2.0/24** :

<pre><code>
nc: connect to 10.0.0.40 port 30001 (tcp) failed: Connection refused
nc: connect to 10.0.1.40 port 30001 (tcp) failed: Connection refused
Connection to 10.0.2.40 30001 port [tcp/pago-services1] succeeded!
</code></pre>

Teď vyhledejte port HSR **40002**:

<pre><code>
nc -vz 10.0.0.40 40002
nc -vz 10.0.1.40 40002
nc -vz 10.0.2.40 40002
</code></pre>

Aby HSR komunikace používala podsíť **10.0.1.0/24**, výsledek by měl vypadat jako následující vzorový výstup.
Úspěšné je jenom připojení přes podsíť **10.0.1.0/24** :

<pre><code>
nc: connect to 10.0.0.40 port 40002 (tcp) failed: Connection refused
Connection to 10.0.1.40 40002 port [tcp/*] succeeded!
nc: connect to 10.0.2.40 port 40002 (tcp) failed: Connection refused
</code></pre>



## <a name="corosync"></a>Corosync


Konfigurační soubor **Corosync** musí být správný na každém uzlu v clusteru, včetně uzlu většina maker. Pokud připojení clusteru uzlu nefunguje podle očekávání, vytvořte nebo zkopírujte **/etc/Corosync/Corosync.conf** ručně na všechny uzly a restartujte službu. 

Příkladem je obsah **Corosync. conf** z testovacího systému.

První část je **Totem**, jak je popsáno v tématu [instalace clusteru](./high-availability-guide-suse-pacemaker.md#cluster-installation), krok 11. Hodnotu **mcastaddr** můžete ignorovat. Stačí zachovat existující položku. Položky pro **token** a **konsensu** musí být nastaveny podle [Microsoft Azure SAP HANA dokumentaci][sles-pacemaker-ha-guide].

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

Druhá část, **protokolování** se nezměnila z daných výchozích hodnot:

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

Třetí část ukazuje **seznam uzlů.**. Všechny uzly clusteru se musí zobrazit s jejich **NodeId**:

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

V poslední části **kvora** je důležité nastavit hodnotu pro **expected_votes** správně. Musí to být počet uzlů, včetně uzlu většina maker. A hodnota **two_node** musí být **0**. Položku úplně neodstraňujte. Stačí nastavit hodnotu na **0**.

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




## <a name="sbd-device"></a>Zařízení SBD

Postup nastavení zařízení SBD na virtuálním počítači Azure je popsané v tématu [SBDing (oplocening](./high-availability-guide-suse-pacemaker.md#sbd-fencing)).

Nejdřív na virtuálním počítači SBD serveru ověřte, jestli existují položky seznamu ACL pro každý uzel v clusteru. Na virtuálním počítači s SBD serverem spusťte následující příkaz:


<pre><code>
targetcli ls
</code></pre>


V testovacím systému výstup příkazu vypadá jako v následujícím příkladu. Názvy ACL, jako je například **IQN. 2006-04. hSo-DB-0. local: hSo-DB-0** , musí být zadány jako odpovídající názvy iniciátorů na virtuálních počítačích. Každý virtuální počítač potřebuje nějaký jiný.

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

Potom zkontrolujte, že se názvy iniciátorů na všech virtuálních počítačích liší a odpovídají dříve zobrazeným položkám. Tento příklad je z pracovního uzlu 1 v lokalitě 1:

<pre><code>
cat /etc/iscsi/initiatorname.iscsi
</code></pre>

Výstup vypadá jako v následujícím příkladu:

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

Pak ověřte, že **zjišťování** funguje správně. Spusťte následující příkaz na každém uzlu clusteru s použitím IP adresy virtuálního počítače SBD serveru:

<pre><code>
iscsiadm -m discovery --type=st --portal=10.0.0.19:3260
</code></pre>

Výstup by měl vypadat jako v následující ukázce:

<pre><code>
10.0.0.19:3260,1 iqn.2006-04.dbhso.local:dbhso
</code></pre>

Dalším bodem ověření je ověřit, že uzel vidí zařízení SDB. Podívejte se na všechny uzly, včetně uzlu většina maker:

<pre><code>
lsscsi | grep dbhso
</code></pre>

Výstup by měl vypadat jako v následujícím příkladu. Názvy se ale můžou lišit. Název zařízení se může také po restartování virtuálního počítače změnit:

<pre><code>
[6:0:0:0]    disk    LIO-ORG  sbddbhso         4.0   /dev/sdm
</code></pre>

V závislosti na stavu systému se někdy pomůže restartovat služby iSCSI a vyřešit problémy. Potom spusťte následující příkazy:

<pre><code>
systemctl restart iscsi
systemctl restart iscsid
</code></pre>


Z libovolného uzlu můžete zjistit, zda jsou všechny uzly **jasné**. Ujistěte se, že používáte správný název zařízení v určitém uzlu:

<pre><code>
sbd -d /dev/sdm list
</code></pre>

Výstup by měl pro každý uzel v clusteru zobrazovat **nejasný** název:

<pre><code>
0       hso-hana-vm-s1-0        clear
1       hso-hana-vm-s2-2        clear
2       hso-hana-vm-s2-1        clear
3       hso-hana-dm     clear
4       hso-hana-vm-s1-1        clear
5       hso-hana-vm-s2-0        clear
6       hso-hana-vm-s1-2        clear
</code></pre>


Další kontrolou SBD je možnost **výpisu** z příkazu **SBD** . V tomto ukázkovém příkazu a výstupu z uzlu většina maker byl název zařízení **SDD**, nikoli **SDM**:

<pre><code>
sbd -d /dev/sdd dump
</code></pre>

Výstup od názvu zařízení by měl vypadat stejně jako na všech uzlech:

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

Jedna další kontrolu pro SBD je možnost odeslání zprávy do jiného uzlu. Chcete-li odeslat zprávu do pracovního uzlu 2 v lokalitě 2, spusťte následující příkaz v pracovním uzlu 1 v lokalitě 2:

<pre><code>
sbd -d /dev/sdm message hso-hana-vm-s2-2 test
</code></pre>

Na straně cílového virtuálního počítače **hSo-Hana-VM-S2-2** v tomto příkladu můžete najít následující položku v **/var/log/messages**:

<pre><code>
/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68:   notice: servant: Received command test from hso-hana-vm-s2-1 on disk /dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68
</code></pre>

Ověřte, že položky v **/etc/sysconfig/SBD** odpovídají popisu v části [nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md#sbd-fencing). Ověřte, zda je nastavení spouštění v **/etc/iSCSI/iscsid.conf** nastaveno na hodnotu automaticky.

Následující položky jsou důležité v **/etc/sysconfig/SBD**. V případě potřeby upravit hodnotu **ID** :

<pre><code>
SBD_DEVICE="/dev/disk/by-id/scsi-36001405e614138d4ec64da09e91aea68;"
SBD_PACEMAKER=yes
SBD_STARTMODE=always
SBD_WATCHDOG=yes
</code></pre>


Ověřte nastavení spuštění v **/etc/iSCSI/iscsid.conf**. K požadovanému nastavení by došlo s následujícím příkazem **iscsiadm** , který je popsaný v dokumentaci. Pokud se liší, ověřte je a přizpůsobte ji ručně pomocí **VI** .

Tento příkaz nastaví chování při spuštění:

<pre><code>
iscsiadm -m node --op=update --name=node.startup --value=automatic
</code></pre>

Tuto položku nastavte v **/etc/iSCSI/iscsid.conf**:

<pre><code>
node.startup = automatic
</code></pre>

Během testování a ověřování se po restartování virtuálního počítače už v některých případech nezobrazuje zařízení SBD. Mezi nastavením spuštění a obsahem zobrazeným v YaST2 došlo k nesouladu. Chcete-li zjistit nastavení, proveďte následující kroky:

1. Spusťte YaST2.
2. Na levé straně vyberte **Network Services** .
3. Posuňte se na pravé straně k **iniciátoru iSCSI** a vyberte ho.
4. Na další obrazovce na kartě **Služba** vidíte jedinečný název iniciátoru pro uzel.
5. Nad názvem iniciátoru zajistěte, aby byla **počáteční hodnota služby** nastavena **při spuštění**.
6. Pokud není, nastavte ji na, pokud se **spouští** místo **ručního** spuštění.
7. Dále přepněte horní kartu na **propojené cíle**.
8. Na obrazovce **připojené cíle** by se měla zobrazit položka pro zařízení SBD, jako je tato ukázka: **10.0.0.19:3260 IQN. 2006-04. dbhso. local: dbhso**.
9. Ověřte, zda je **počáteční** hodnota nastavena **na hodnotu při spuštění**.
10. Pokud ne, klikněte na **Upravit** a změňte ho.
11. Uložte změny a ukončete YaST2.



## <a name="pacemaker"></a>Pacemaker

Až se všechno nastaví správně, můžete na každém uzlu spustit následující příkaz, abyste zkontrolovali stav služby Pacemaker:

<pre><code>
systemctl status pacemaker
</code></pre>

Horní část výstupu by měla vypadat jako v následujícím příkladu. Je důležité, aby byl stav po **aktivním** stavu zobrazen jako **načtený** a **aktivní (spuštěný)**. Po **načtení** musí být stav zobrazený jako **povolený**.

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

Pokud je nastavení stále **zakázané**, spusťte následující příkaz:

<pre><code>
systemctl enable pacemaker
</code></pre>

Pokud chcete zobrazit všechny nakonfigurované prostředky v Pacemaker, spusťte následující příkaz:

<pre><code>
crm status
</code></pre>

Výstup by měl vypadat jako v následujícím příkladu. Je dobré, aby se prostředky **CLN** a **MSL** zobrazovaly jako zastavené na virtuálním počítači většiny maker, **hSo-Hana-DM**. V uzlu většina Maker není SAP HANA instalace. Prostředky **CLN** a **MSL** se tak zobrazují jako zastavené. Je důležité, aby se zobrazil správný celkový počet virtuálních počítačů, **7**. Všechny virtuální počítače, které jsou součástí clusteru, musí být uvedené s stavem **online**. Aktuální primární hlavní uzel musí být správně rozpoznán. V tomto příkladu je to **hSo-Hana-VM-S1-0**:

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

Důležitou funkcí Pacemaker je režim údržby. V tomto režimu můžete provádět úpravy bez provoking okamžité akce clusteru. Příkladem je restartování virtuálního počítače. Typický případ použití by byl plánovaným operačním systémem nebo údržbou infrastruktury Azure. Viz [plánovaná údržba](#planned-maintenance). Pomocí následujícího příkazu vložte Pacemaker do režimu údržby:

<pre><code>
crm configure property maintenance-mode=true
</code></pre>

Když zkontrolujete **stav CRM**, všimnete si, že se zobrazí výstup všech prostředků, které jsou označené jako **nespravované**. V tomto stavu cluster nereaguje na žádné změny, jako je spuštění nebo zastavení SAP HANA.
Následující příklad ukazuje výstup příkazu pro **stav CRM** , když je cluster v režimu údržby:

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


Tato ukázka příkazu ukazuje, jak ukončit režim údržby clusteru:

<pre><code>
crm configure property maintenance-mode=false
</code></pre>


Jiný příkaz **CRM** načte úplnou konfiguraci clusteru do editoru, takže ho můžete upravit. Po uložení změn cluster spustí příslušné akce:

<pre><code>
crm configure edit
</code></pre>

Pokud se chcete podívat na úplnou konfiguraci clusteru, použijte možnost **Zobrazit v aplikaci CRM** :

<pre><code>
crm configure show
</code></pre>



Po selhání prostředků clusteru se v příkazu pro **stav CRM** zobrazuje seznam **neúspěšných akcí**. Podívejte se na následující ukázku tohoto výstupu:


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

Po selhání je nutné provést vyčištění clusteru. Znovu použijte příkaz **CRM** a k odstranění těchto položek neúspěšných akcí použijte příkaz **Vyčištění** . Pojmenujte odpovídající prostředek clusteru následujícím způsobem:

<pre><code>
crm resource cleanup rsc_SAPHanaCon_HSO_HDB00
</code></pre>

Příkaz by měl vracet výstup podobný následující ukázce:

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

Jak je popsáno v části [Důležité poznámky](#important-notes), neměli byste používat standardní bezproblémové vypnutí k otestování převzetí služeb při selhání clusteru nebo převzetí SAP HANA HSR. Místo toho doporučujeme, abyste aktivovali nouzové jádro, vynutili migraci prostředků nebo případně vypnuli všechny sítě na úrovni operačního systému virtuálního počítače. Další metodou je příkaz **CRM \<node\> Standby** . Podívejte se na [dokument SUSE][sles-12-ha-paper]. 

Následující tři ukázkové příkazy můžou vynutit převzetí služeb při selhání clusteru:

<pre><code>
echo c &gt /proc/sysrq-trigger

crm resource migrate msl_SAPHanaCon_HSO_HDB00 hso-hana-vm-s2-0 force

wicked ifdown eth0
wicked ifdown eth1
wicked ifdown eth2
......
wicked ifdown eth&ltn&gt
</code></pre>

Jak je popsáno v části [plánovaná údržba](#planned-maintenance), dobrým způsobem, jak monitorovat aktivity clusteru, je spuštění **SAPHanaSR-showAttr** pomocí příkazu **Watch** :

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Také pomáhá se zobrazením stavu SAP HANA na šířku ze skriptu SAP Python. Instalační program clusteru hledá tuto hodnotu stavu. Pokud se domníváte, že dojde k selhání pracovního uzlu, je to jasné. Pokud dojde k výpadku pracovního uzlu, SAP HANA okamžitě nevrátí chybu pro stav celého systému s možností horizontálního rozšíření kapacity. 

Dojde k několika opakovaným pokusům, aby nedocházelo k nepotřebným selháním. Cluster se chová jenom v případě, že se stav změní z **OK**, vrátí hodnotu **4** na **chybu** a vrátí hodnotu **1**. Takže je správné, pokud výstup z **SAPHanaSR-showAttr** zobrazuje virtuální počítač se stavem **offline**. Zatím ale neexistuje žádná aktivita pro přepnutí primárního a sekundárního. Žádná aktivita clusteru se neaktivuje, dokud SAP HANA nevrátí chybu.

Můžete monitorovat stav SAP HANA krajiny na šířku jako text **\<HANA SID\> ADM** , a to tak, že zavoláte skript SAP Python. Možná budete muset přizpůsobit cestu:

<pre><code>
watch python /hana/shared/HSO/exe/linuxx86_64/HDB_2.00.032.00.1533114046_eeaf4723ec52ed3935ae0dc9769c9411ed73fec5/python_support/landscapeHostConfiguration.py
</code></pre>

Výstup tohoto příkazu by měl vypadat jako v následujícím příkladu. Sloupec **stav hostitele** a **Celkový stav hostitele** jsou důležité. Skutečný výstup je širší a další sloupce.
Aby byla v tomto dokumentu čitelnější výstupní tabulka, většina sloupců na pravé straně byla odstraněna:

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


K dispozici je jiný příkaz pro kontrolu aktuálních aktivit clusteru. Podívejte se na následující příkaz a výstup dolů po ukončení hlavního uzlu primární lokality. Můžete se podívat na seznam akcí přechodu, jako je například **zvýšení úrovně** bývalého sekundárního hlavního uzlu, **hSo-Hana-VM-S2-0**, jako nového primárního hlavního serveru. Pokud je vše v pořádku a všechny aktivity jsou dokončeny, musí být tento **souhrnný seznam přechodů** prázdný.

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

Existují různé případy použití při plánované údržbě. Jednou otázkou je to, jestli jde jenom o údržbu infrastruktury, jako je třeba změna na úrovni operačního systému a disku nebo upgradu na HANA.
Další informace můžete najít v dokumentech z SUSE, jako je například [Bezvýpadek][sles-zero-downtime-paper] nebo [SAP HANA scénář optimalizovaného pro výkon SR][sles-12-for-sap]. Tyto dokumenty obsahují také ukázky, které ukazují, jak ručně migrovat primární.

Při ověřování případu použití údržby infrastruktury bylo provedeno výrazné interní testování. Abyste se vyhnuli jakýmkoli problémům souvisejícím s migrací primární služby, rozhodli jsme se nejprve migrovat primární, než do režimu údržby vložíte cluster. Tímto způsobem není nutné cluster zapomenout na předchozí situaci: která strana byla primární a která byla sekundární.

V tomto ohledu existují dvě různé situace:

- **Plánovaná údržba aktuálního sekundárního**. V takovém případě můžete cluster jednoduše vložit do režimu údržby a provést práci na sekundárním počítači, aniž by to ovlivnilo cluster.

- **Plánovaná údržba aktuálně primárního**. Aby uživatelé mohli i nadále pracovat během údržby, je nutné vynutit převzetí služeb při selhání. S tímto přístupem musíte aktivovat převzetí služeb při selhání clusteru Pacemaker, a ne jenom na úrovni SAP HANA HSR. Instalační program Pacemaker automaticky aktivuje převzetí SAP HANA. Je také nutné provést převzetí služeb při selhání, než přepnete cluster do režimu údržby.

Postup údržby aktuální sekundární lokality je následující:

1. Přepněte cluster do režimu údržby.
2. Proveďte práci na sekundární lokalitě. 
3. Ukončete režim údržby clusteru.

Postup údržby aktuální primární lokality je složitější:

1. Ručně Aktivujte převzetí služeb při selhání nebo SAP HANA převzetí prostřednictvím migrace prostředků Pacemaker. Podívejte se na podrobnosti, které následují.
2. Instalační program clusteru ukončí činnost SAP HANA v bývalé primární lokalitě.
3. Přepněte cluster do režimu údržby.
4. Po dokončení práce s údržbou Zaregistrujte předchozí primární lokalitu jako novou sekundární lokalitu.
5. Vyčistěte konfiguraci clusteru. Podívejte se na podrobnosti, které následují.
6. Ukončete režim údržby clusteru.


Migrace prostředku přidá položku do konfigurace clusteru. Příkladem je vynucené převzetí služeb při selhání. Před ukončením režimu údržby musíte tyto položky vyčistit. Podívejte se na následující ukázku.

Nejdřív vynuťte převzetí služeb při selhání clusteru migrací prostředku **MSL** do aktuálního sekundárního hlavního uzlu. Tento příkaz zobrazí upozornění, že bylo vytvořeno **omezení přesunu** :

<pre><code>
crm resource migrate msl_SAPHanaCon_HSO_HDB00 force

INFO: Move constraint created for msl_SAPHanaCon_HSO_HDB00
</code></pre>


Projděte si proces převzetí služeb při selhání pomocí příkazu **SAPHanaSR-showAttr**. Pokud chcete monitorovat stav clusteru, otevřete vyhrazené okno prostředí a spusťte příkaz s **kukátkem**:

<pre><code>
watch SAPHanaSR-showAttr
</code></pre>

Výstup by měl ukazovat na ruční převzetí služeb při selhání. Byl **povýšen** předchozí sekundární hlavní uzel v této ukázce, **hSo-Hana-VM-S2-0**. Bývalá primární lokalita se zastavila, **LSS** hodnota **1** pro bývalé primární hlavní uzel **hSo-Hana-VM-S1-0**: 

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

Po převzetí služeb při selhání clusteru a převzetí SAP HANA vložte cluster do režimu údržby, jak je popsáno v tématu [Pacemaker](#pacemaker).

V příkazech **SAPHanaSR-showAttr** a **CRM** se neuvádějí žádné informace o omezeních vytvořených migrací prostředků. Jednu z možností, jak tato omezení zobrazit, je zobrazit úplnou konfiguraci prostředků clusteru pomocí následujícího příkazu:

<pre><code>
crm configure show
</code></pre>

V konfiguraci clusteru najdete nové omezení umístění způsobené předchozí ruční migrací prostředků. Tato ukázková položka začíná v **umístění CLI –**:

<pre><code>
location cli-ban-msl_SAPHanaCon_HSO_HDB00-on-hso-hana-vm-s1-0 msl_SAPHanaCon_HSO_HDB00 role=Started -inf: hso-hana-vm-s1-0
</code></pre>

Tato omezení bohužel můžou ovlivnit celkové chování clusteru. Proto je nutné je znovu odebrat předtím, než přepnete celý systém. Pomocí příkazu **unmigrovat** je možné vyčistit omezení umístění, která byla vytvořena před. Pojmenování může být trochu matoucí. Nepokouší se migrovat prostředek zpátky na původní virtuální počítač, ze kterého se migruje. Pouze odebere omezení umístění a také vrátí odpovídající informace při spuštění příkazu:


<pre><code>
crm resource unmigrate msl_SAPHanaCon_HSO_HDB00

INFO: Removed migration constraints for msl_SAPHanaCon_HSO_HDB00
</code></pre>

Na konci práce údržby zastavíte režim údržby clusteru, jak je znázorněno v [Pacemaker](#pacemaker).



## <a name="hb_report-to-collect-log-files"></a>hb_report shromažďování souborů protokolu

Aby bylo možné analyzovat problémy clusteru Pacemaker, je užitečné a také si ji vyžádala podpora SUSE pro spuštění nástroje **hb_report** . Shromažďuje všechny důležité soubory protokolů, které potřebujete k analýze toho, co se stalo. Toto ukázkové volání používá počáteční a koncový čas, kdy došlo k určitému incidentu. Viz také [Důležité poznámky](#important-notes):

<pre><code>
hb_report -f "2018/09/13 07:36" -t "2018/09/13 08:00" /tmp/hb_report_log
</code></pre>

Příkaz oznamuje, kam umístí komprimované soubory protokolu:

<pre><code>
The report is saved in /tmp/hb_report_log.tar.bz2
Report timespan: 09/13/18 07:36:00 - 09/13/18 08:00:00
</code></pre>

Jednotlivé soubory pak můžete extrahovat pomocí standardního příkazu **tar** :

<pre><code>
tar -xvf hb_report_log.tar.bz2
</code></pre>

Když se podíváte na extrahované soubory, najdete všechny soubory protokolů. Většina z nich byla vložena do samostatných adresářů pro každý uzel v clusteru:

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


V zadaném časovém rozsahu byl ukončen aktuální hlavní uzel **hSo-Hana-VM-S1-0** . Položky související s touto událostí najdete v **deníku Journal. log**:

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

Dalším příkladem je soubor protokolu Pacemaker v sekundární hlavní databázi, který se stal novým primárním hlavním serverem. V tomto výpisu se dozvíte, že stav ukončeného primárního hlavního uzlu byl nastaven na hodnotu **offline**:

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


Následující výňatky jsou ze souboru SAP HANA **global.ini** v clusteru Server 2. Tento příklad ukazuje položky překladu názvů hostitelů pro používání různých sítí pro SAP HANA komunikaci mezi uzly a HSR:

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

Řešení clusteru poskytuje rozhraní prohlížeče, které nabízí uživatelské rozhraní pro uživatele, kteří upřednostňují nabídky a grafiku pro všechny příkazy na úrovni prostředí.
Chcete-li použít rozhraní prohlížeče, nahraďte **\<node\>** skutečným SAP HANAm uzlem v následující adrese URL. Pak zadejte přihlašovací údaje clusteru (uživatelský **cluster**):

<pre><code>
https://&ltnode&gt:7630
</code></pre>

Na tomto snímku obrazovky se zobrazuje řídicí panel clusteru:


![Řídicí panel clusteru Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-1.png)


Tento příklad ukazuje omezení umístění způsobená migrací prostředků clusteru, jak je vysvětleno v části [plánovaná údržba](#planned-maintenance):


![Omezení seznamu Hawk](media/hana-vm-scale-out-HA-troubleshooting/hawk-2.png)


Výstup **hb_report** můžete také nahrát do Hawk v části **Historie**, jak je znázorněno níže. Soubory protokolu se shromažďují v hb_report. 

![Hawk výstup nahrávání hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-3.png)

Pomocí **Průzkumníka historie** pak můžete projít všechny přechody clusteru zahrnuté do výstupního **hb_report** :

![Hawk přechody ve výstupu hb_report](media/hana-vm-scale-out-HA-troubleshooting/hawk-4.png)

Tento konečný snímek obrazovky ukazuje část s **podrobnostmi** o jednom přechodu. Cluster se reagoval v primárním selhání hlavního uzlu, node **hSo-Hana-VM-S1-0**. Teď podporuje sekundární uzel jako nový hlavní server **hSo-Hana-VM-S2-0**:

![Hawk jeden přechod](media/hana-vm-scale-out-HA-troubleshooting/hawk-5.png)


## <a name="next-steps"></a>Další kroky

Tato příručka pro řešení potíží popisuje vysokou dostupnost pro SAP HANA v konfiguraci s možností horizontálního rozšíření kapacity. Kromě databáze je další důležitou součástí systému SAP NetWeaver Stack. Přečtěte si o [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure, které používají server SUSE Enterprise Linux][sap-nw-ha-guide-sles].
