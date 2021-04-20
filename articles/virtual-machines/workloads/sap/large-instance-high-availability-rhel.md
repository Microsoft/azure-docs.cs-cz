---
title: Vysoká dostupnost Azure velkých instancí pro SAP v RHEL
description: Naučte se automatizovat převzetí služeb při selhání databáze SAP HANA pomocí clusteru Pacemaker v Red Hat Enterprise Linux.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: f7b6e6efbbd17655b4f68d79ac26ee34ae754a3b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728441"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>Vysoká dostupnost Azure velkých instancí pro SAP v RHEL

> [!NOTE]
> Tento článek obsahuje odkazy na termín nesouhlasný – *termín, který* společnost Microsoft už nepoužívá. Když se tento termín odebere ze softwaru, odebereme ho z tohoto článku.

V tomto článku se dozvíte, jak nakonfigurovat cluster Pacemaker v RHEL 7,6 pro automatizaci převzetí služeb při selhání databáze SAP HANA. K dokončení kroků v této příručce potřebujete mít dobrý přehled o Linux, SAP HANA a Pacemaker.

Následující tabulka obsahuje názvy hostitelů, které se používají v celém rámci tohoto článku. Bloky kódu v článku zobrazují příkazy, které je třeba spustit, a výstup těchto příkazů. Věnujte velkou pozornost, na který uzel je odkazován v jednotlivých příkazech.

| Typ | Název hostitele | Uzel|
|-------|-------------|------|
|Primární hostitel|`sollabdsm35`|uzel 1|
|Sekundární hostitel|`sollabdsm36`|uzel 2|

## <a name="configure-your-pacemaker-cluster"></a>Konfigurace clusteru Pacemaker


Než začnete s konfigurací clusteru, nastavte výměnu klíčů SSH, aby bylo možné navázat vztah důvěryhodnosti mezi uzly.

1. Pomocí následujících příkazů vytvořte identické `/etc/hosts` na obou uzlech.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  Vytvoření a výměna klíčů SSH.
    1. Generování klíčů ssh.

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. Zkopírujte klíče do ostatních hostitelů pro nepřístupný protokol SSH.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  Zakáže SELinux na obou uzlech.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. Restartujte servery a pak pomocí následujícího příkazu ověřte stav SELinux.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. Nakonfigurujte protokol NTP (Network Time Protocol). Časová a časová pásma obou uzlů clusteru se musí shodovat. K otevření `chrony.conf` a ověření obsahu souboru použijte následující příkaz.
    1. Následující obsah by měl být přidán do konfiguračního souboru. Změňte skutečné hodnoty na základě vašeho prostředí.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. Povolte službu Chrony. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. Aktualizace systému
    1. Nejdřív nainstalujte nejnovější aktualizace do systému před tím, než začnete instalovat zařízení SBD.
    1. Pokud nechcete úplnou aktualizaci systému, a to i v případě, že se doporučuje, aktualizujte minimálně následující balíčky.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. Nainstalujte úložiště SAP HANA a RHEL-HA.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. Na všechny uzly nainstalujte nástroje Pacemaker, SBD, OpenIPMI, ipmitool a fencing_sbd.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>Konfigurace sledovacího zařízení

V této části se dozvíte, jak nakonfigurovat sledovací zařízení. Tato část používá stejné dva hostitele, `sollabdsm35` a `sollabdsm36` odkazuje na začátek tohoto článku.

1. Ujistěte se, že démon procesu sledovacích procesů není v žádném systému spuštěn.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. Výchozí sledovací zařízení Linux, které bude nainstalováno během instalace, je sledovací zařízení iTCO, které není podporováno systémy SDFlex UCS a HPE. Proto musí být toto sledovací zařízení zakázané.
    1. V systému je nainstalovaná a načtená nesprávná sledovací zařízení:
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. Uvolněte nesprávný ovladač z prostředí:
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. Abyste se ujistili, že ovladač není načtený během příštího spuštění systému, musí být ovladač blocklisted. Chcete-li seznamu blokovaných moduly iTCO, přidejte na konec `50-blacklist.conf` souboru následující:
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. Zkopírujte soubor do sekundárního hostitele.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. Otestujte, jestli je spuštěná služba IPMI. Je důležité, aby časovač IPMI neběžel. Správa časovače se provede ze služby SBD Pacemaker.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. Ve výchozím nastavení se požadované zařízení/dev/Watchdog nevytvoří.

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. Nakonfigurujte sledovací zařízení IPMI.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. Zkopírujte konfigurační soubor sledovacích zařízení do sekundárního souboru.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  Povolte a spusťte službu IPMI.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     Teď je spuštěná služba IPMI a vytvoří se/dev/Watchdog zařízení, ale časovač se pořád zastaví. Později bude SBD spravovat nastavení sledovacího zařízení a povolí časovač IPMI.
7.  Ověřte, že/dev/Watchdog existuje, ale není používán.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>Konfigurace SBD
V této části se dozvíte, jak nakonfigurovat SBD. Tato část používá stejné dva hostitele, `sollabdsm35` a `sollabdsm36` odkazuje na začátek tohoto článku.

1.  Ujistěte se, že je na obou uzlech viditelný disk iSCSI nebo FC. V tomto příkladu se používá zařízení SBD založené na FC. Další informace o zásadách škálování na SBD najdete v tématu [pokyny k návrhu pro clustery s vysokou dostupností RHEL – SBD předpoklady](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Faccess.redhat.com%2Farticles%2F2941601&data=04%7C01%7Cralf.klahr%40microsoft.com%7Cd49d7a3e3871449cdecc08d8c77341f1%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637478645171139432%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=c%2BUAC5gmgpFNWZCQFfiqcik8CH%2BmhH2ly5DsOV1%2FE5M%3D&reserved=0).
2.  Identifikátor logické jednotky (LUN) musí být identický na všech uzlech.
  
3.  U zařízení SBD se podívejte na stav Multipath.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  Vytváření SBD disků a nastavení primitivních zón clusteru. Tento krok se musí provést na prvním uzlu.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  Zkopírujte SBD config přes do Uzel2.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  Ověřte, že je disk SBD viditelný z obou uzlů.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  Přidejte zařízení SBD do konfiguračního souboru SBD.

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>Inicializace clusteru
V této části inicializujete cluster. Tato část používá stejné dva hostitele, `sollabdsm35` a `sollabdsm36` odkazuje na začátek tohoto článku.

1.  Nastavte heslo uživatele clusteru (všechny uzly).
    ```
    passwd hacluster
    ```
2.  Spustí počítače ve všech systémech.
    ```
    systemctl enable pcsd
    ```
  

3.  Zastavte bránu firewall a zakažte ji na (všech uzlech).
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  Spusťte službu pcsd.
    ```
    systemctl start pcsd
    ```

5.  Ověřování clusteru spouštějte jenom z Uzel1.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  Vytvořte cluster.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  Ověřte stav clusteru.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. Pokud se jeden uzel nepřipojuje ke clusteru, kontrolujte, jestli je brána firewall pořád spuštěná.

9. Vytvoření a povolení zařízení SBD
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. Zastavte cluster, restartujte Clusterové služby (na všech uzlech).

    ```
    pcs cluster stop --all
    ```

11. Restartujte Clusterové služby (na všech uzlech).

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. Corosync musí spustit službu SBD.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. Restartujte cluster (Pokud se nespustí automaticky z pcsd).

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Povolit nastavení Stonith
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. Ověřte stav nového clusteru teď s jedním prostředkem.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. Nyní musí být spuštěn časovač IPMI a zařízení/dev/Watchdog musí být otevřeno pomocí SBD.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. Ověřte stav SBD.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. Otestujte SBDy při selhání jádra.

    * Aktivujte chybu jádra.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * Druhý test ke spuštění je použít k plotu uzel pomocí příkazů PC.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. Pro zbytek SAP HANA clusteringu můžete zakázat STONITH nastavením:

   * sada vlastností počítačů `stonith-enabled=false`
   * Někdy je snazší STONITH deaktivovat během instalace clusteru, protože se vyhnete neočekávaným restartováním systému.
   * Tento parametr musí být nastaven na hodnotu true pro účely produktivního využívání. Pokud tento parametr není nastaven na hodnotu true, cluster se nepodporuje.
   * sada vlastností počítačů `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>Integrace HANA do clusteru

V této části integrujete HANA do clusteru. Tato část používá stejné dva hostitele, `sollabdsm35` a `sollabdsm36` odkazuje na začátek tohoto článku.

Pro integraci HANA jsou k dispozici dvě možnosti. První možností je řešení optimalizované pro náklady, ve kterém můžete použít sekundární systém ke spuštění systému QAS. Tuto metodu nedoporučujeme, protože neopouští žádný systém k testování aktualizací v softwaru clusteru, operačním systému nebo HANA a aktualizace konfigurace může vést k neplánovaným výpadkům PRD systému. Kromě toho, pokud je potřeba v sekundárním systému aktivovat systém PRD, musí být na sekundárním uzlu vypnutý QAS. Druhou možností je nainstalovat systém QAS do jednoho clusteru a použít druhý cluster pro PRD. Tato možnost také umožňuje testovat všechny součásti před jejich přepnutím do produkčního prostředí. V tomto článku se dozvíte, jak nakonfigurovat druhou možnost.


* Tento proces je sestavením popisu RHEL na stránce:

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>Postup pro konfiguraci HSR

1.  Jedná se o akce, které je potřeba provést na Uzel1 (primární).
    1. Ujistěte se, že je režim protokolu databáze nastaven na hodnotu normální.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. Replikace SAP HANA systému bude fungovat až po provedení prvotního zálohování. Následující příkaz vytvoří počáteční zálohu v `/tmp/` adresáři. Vyberte pro databázi správný systém souborů zálohy. 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. Zálohujte všechny kontejnery databáze této databáze.
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. Povolte proces HSR ve zdrojovém systému.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. Ověřte stav primárního systému.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. Jedná se o akce, které je potřeba provést v Uzel2 (sekundární).
     1. Zastavte databázi.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. Pouze pro SAP HANA 2.0 zkopírujte do `PKI SSFS_HR2.KEY` `SSFS_HR2.DAT` sekundárního uzlu SAP HANA systém a soubory z primárního uzlu.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. Jako lokalitu replikace povolte sekundární.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. Spusťte databázi.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. Ověřte stav databáze.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. Je také možné získat další informace o stavu replikace:
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>Popis režimu replikace protokolů

Další informace o režimu replikace protokolů najdete v [oficiální dokumentaci ke službě SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html).
  

#### <a name="network-setup-for-hana-system-replication"></a>Nastavení sítě pro replikaci systému HANA


Chcete-li zajistit, aby provoz replikace používal správnou síť VLAN pro replikaci, musí být správně nakonfigurován v nástroji `global.ini` . Pokud tento krok přeskočíte, HANA bude pro replikaci používat síť VLAN pro přístup, která může být nežádoucí.


Následující příklady znázorňují konfiguraci překladu názvů hostitelů pro replikaci systému do sekundární lokality. Je možné identifikovat tři odlišné sítě:

* Veřejná síť s adresami v rozsahu 10.0.1. *

* Síť pro interní SAP HANA komunikaci mezi hostiteli v každé lokalitě: 192.168.1. *

* Vyhrazená síť pro replikaci systému: 10.5.1. *

V prvním příkladu `[system_replication_communication]listeninterface` byl parametr nastaven na `.global` a jsou zadány pouze hostitelé replikačního webu.

V následujícím příkladu `[system_replication_communication]listeninterface` byl parametr nastaven na `.internal` a jsou zadáni všichni hostitelé obou lokalit.

  

Další informace najdete v tématu [Konfigurace sítě pro replikaci SAP HANA systému](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html).

  

Pro replikaci systému není nutné `/etc/hosts` soubor upravovat, interní (' virtuální ') názvy hostitelů musí být namapovány na IP adresy v souboru, aby bylo možné `global.ini` vytvořit vyhrazenou síť pro replikaci systému. Tato syntaxe je následující:

global.ini

[system_replication_hostname_resolution]

<IP-address_site>=<Internal-Host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Konfigurace SAP HANA v clusteru Pacemaker
V této části se dozvíte, jak nakonfigurovat SAP HANA v clusteru Pacemaker. Tato část používá stejné dva hostitele, `sollabdsm35` a `sollabdsm36` odkazuje na začátek tohoto článku.

Ujistěte se, že splňujete následující požadavky:  

* Cluster Pacemaker je nakonfigurovaný podle dokumentace a má správnou a funkční oplocení.

* SAP HANA spouštění při spuštění je na všech uzlech clusteru zakázané, protože spuštění a zastavení se bude spravovat clusterem.

* Replikace a převzetí SAP HANA systému pomocí nástrojů z SAP fungují správně mezi uzly clusteru.

* SAP HANA obsahuje účet monitorování, který může cluster používat z obou uzlů clusteru.

* Oba uzly se přihlásí k odběru kanálů s vysokou dostupností a RHEL for SAP HANA (RHEL 6, RHEL 7).

  

* V části Obecné spusťte všechny příkazy počítačů jenom z uzlu, protože CIB se automaticky aktualizuje z prostředí počítačů.

* [Další informace o zásadách kvora](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>Postup konfigurace 
1. Konfigurace počítačů.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  Nakonfigurujte Corosync.
    Další informace najdete v tématu [Jak můžu nakonfigurovat RHEL 7 cluster s vysokou dostupností s využitím Pacemaker a Corosync](https://access.redhat.com/solutions/1293523).
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  Vytvořte Klonovaný prostředek SAPHanaTopology.
    Prostředek SAPHanaTopology shromažďuje stav a konfiguraci replikace systému SAP HANA na všech uzlech. SAPHanaTopology vyžaduje, aby byly nakonfigurovány následující atributy.
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 InstanceNumber=00 --clone clone-max=2 clone-node-max=1    interleave=true
       ```

    | Název atributu | Description  |
    |---|---|
    | SID | Identifikátor systému SAP (SID) SAP HANA instalace. Musí být stejné pro všechny uzly. |
    | Číslo instance | 2 číslice instance SAP Idntifier.|

    * Stav prostředku
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       InstanceNumber 2-digit SAP Instance identifier.
       pcs resource show SAPHanaTopology_HR2_00-clone
   
       Clone: SAPHanaTopology_HR2_00-clone
   
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
   
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat
       type=SAPHanaTopology)
   
        Attributes: InstanceNumber=00 SID=HR2
   
        Operations: monitor interval=60 timeout=60
       (SAPHanaTopology_HR2_00-monitor-interval-60)
   
        start interval=0s timeout=180
       (SAPHanaTopology_HR2_00-start-interval-0s)
   
        stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
   
       ```

4.  Vytvořte primární nebo sekundární prostředek SAPHana.
    * Prostředek SAPHana zodpovídá za spuštění, zastavení a přemístění databáze SAP HANA. Tento prostředek musí být spuštěn jako prostředek primárního a sekundárního clusteru. Prostředek má následující atributy.

| Název atributu            | Povinné? | Výchozí hodnota | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | Yes       | Žádné          | Identifikátor systému SAP (SID) SAP HANA instalace. Musí být stejné pro všechny uzly.                                                                                                                                                                                                                                                                                                                                                                                       |
| Číslo instance            | Yes       | žádné          | identifikátor instance SAP 2 číslice.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | ne        | ano           | Má cluster upřednostňovat přepnutí na sekundární instanci namísto místního restartování? ("ne": preferovat restartování místně; "Ano": preferovat převzetí na vzdálený web.)                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | ne        | FALSE         | Po převzetí a DUPLICATE_PRIMARY_TIMEOUT by měl být předchozí SAP HANA registrována jako sekundární? ("NEPRAVDA": Ne, ruční zásah bude potřeba; "true": Ano, předchozí primární primární bude registrován agentem prostředků jako sekundární)                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | ne        | 7200          | Časový rozdíl (v sekundách) potřebný mezi primárními časovými razítky, pokud dojde k Dual-primární situaci. Pokud je časový rozdíl menší než časová mezera, cluster bude obsahovat jednu nebo obě instance ve stavu "čekání". Díky tomu může mít správce možnost reagovat na převzetí služeb při selhání. Po uplynutí časového rozdílu se zaregistruje původní primární primární disk. Po této registraci do nové primární lokality budou systémová replikace přepsat všechna data. |

5.  Vytvořte prostředek HANA.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200   AUTOMATED_REGISTER=true primary notify=true clone-max=2 clone-node-max=1 interleave=true

    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary

        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true

        Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)

        Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200
    InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2

        Operations: demote interval=0s timeout=320
    (SAPHana_HR2_00-demote-interval-0s)

        monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)

        monitor interval=121 role=Secondary timeout=60
    (SAPHana_HR2_00-monitor-

        interval-121)

        monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-

        interval-119)

        promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)

        start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)

        stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  Vytvořte prostředek virtuální IP adresy.
    Cluster bude obsahovat virtuální IP adresu, aby bylo možné dosáhnout primární instance SAP HANA. Níže je příklad příkazu k vytvoření prostředku IPaddr2 s protokolem IP 10.7.0.84/24.
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  Vytvořte omezení.
    * Pro správnou operaci musíme před spuštěním prostředků SAPHana zajistit spuštění prostředků SAPHanaTopology a taky to, že virtuální IP adresa je k dispozici na uzlu, ve kterém je spuštěný primární prostředek SAPHana. Chcete-li toho dosáhnout, je třeba vytvořit následující 2 omezení.
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>Testování ručního přesunu prostředku SAPHana do jiného uzlu

#### <a name="sap-hana-takeover-by-cluster"></a>(Převzetí SAP HANA clusterem)


Chcete-li otestovat přesunutí prostředku SAPHana z jednoho uzlu na jiný, použijte následující příkaz. Všimněte si, že možnost `--primary` by se neměla používat při spuštění následujícího příkazu kvůli internímu fungování prostředku SAPHana.
```pcs resource move SAPHana_HR2_00-primary```

Poté, co všechny počítače přesunují příkaz, vytvoří cluster omezení umístění k dosažení přesunutí prostředku. Tato omezení je nutné odebrat, aby v budoucnu bylo možné automatické převzetí služeb při selhání.
Pokud je chcete odebrat, můžete použít následující příkaz.
```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* Přihlaste se k HANA jako ověření.

  * Hostitel se sníženou úrovní:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * Povýšený hostitel:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

S možností `AUTOMATED_REGISTER=false` , nemůžete přepnout zpátky a zpátky.

Pokud je tato možnost nastavená na false, musíte uzel znovu zaregistrovat:
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

Nyní Uzel2, což byl primární, funguje jako sekundární hostitel.

Zvažte nastavení této možnosti na hodnotu true pro automatizaci registrace hostitele se sníženou úrovní.
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

Bez ohledu na to, jestli dáváte přednost automatické registraci, záleží na scénáři zákazníka. Po převzetí bude pro provozní tým snazší automaticky znovu registrovat uzel. Můžete ale chtít uzel zaregistrovat ručně, abyste mohli nejdřív spustit další testy, abyste se ujistili, že vše funguje podle očekávání.
