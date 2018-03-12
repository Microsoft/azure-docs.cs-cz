---
title: "Nastavení replikace systému SAP HANA na virtuálních počítačích Azure (VM) | Microsoft Docs"
description: "Vytvořte vysokou dostupnost SAP HANA na virtuálních počítačích Azure (VM)."
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: sedusch
ms.openlocfilehash: 2bf9ed176f37c315aa4496894315f2318370ce7f
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="high-availability-of-sap-hana-on-azure-virtual-machines-vms"></a>Vysoká dostupnost SAP HANA na virtuálních počítačích Azure (VM)

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[hana-ha-guide-replication]:sap-hana-high-availability.md#14c19f65-b5aa-4856-9594-b81c7e4df73d
[hana-ha-guide-shared-storage]:sap-hana-high-availability.md#498de331-fa04-490b-997c-b078de457c9d

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged%2Fazuredeploy.json

Na místě, můžete použít buď replikaci HANA systému nebo používat sdílené úložiště, k vytvoření vysoké dostupnosti pro SAP HANA.
Na replikaci systému HANA Azure virtuální počítače v Azure je že jediný podporovaný funkce vysoké dostupnosti dosavadní práce. SAP HANA replikace se skládá z jedné primární uzel a alespoň jeden sekundární uzel. Změny dat na primárním uzlu, který se replikují na sekundární uzel synchronně nebo asynchronně.

Tento článek popisuje, jak k nasazení virtuálních počítačů, konfiguraci virtuálních počítačů, nainstalujte rozhraní framework clusteru, instalaci a konfiguraci replikace systému SAP HANA.
V konfiguraci příklad instalace příkazy čísla instance atd 03 a HDB ID HANA systému se používá.

Přečtěte si tyto poznámky k SAP a dokumenty Paper nejprve

* Poznámka SAP [1928533], na kterém je:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Kapacita důležité informace o velikosti virtuálního počítače Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze SAP jádra pro Windows a Linux v Microsoft Azure
* Poznámka SAP [2015553] uvádí požadavky pro nasazení softwaru podporovaných SAP SAP v Azure.
* Poznámka SAP [2205917] se doporučuje nastavení operačního systému SUSE Linux Enterprise Server pro aplikace SAP
* Poznámka SAP [1944799] má SAP HANA pokyny pro SUSE Linux Enterprise Server pro aplikace SAP
* Poznámka SAP [2178632] obsahuje podrobné informace o veškeré monitorování metriky pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o licencích SAP v systému Linux v Azure.
* Poznámka SAP [1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* Poznámka SAP [1999351] Další informace o řešení problémů s Azure rozšířené monitorování rozšíření pro SAP.
* [SAP komunity WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP poznámky pro Linux.
* [Azure virtuálních počítačů, plánování a implementace pro SAP v systému Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP v systému Linux (v tomto článku)][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP v systému Linux][dbms-guide]
* [SAP HANA SR výkonu optimalizované scénář] [ suse-hana-ha-guide] průvodci obsahuje všechny požadované informace pro nastavení replikace systému SAP HANA na místě. Tohoto průvodce použijte jako Směrný plán.

## <a name="deploying-linux"></a>Nasazení Linux

Agent prostředků pro SAP HANA je součástí systému SUSE Linux Enterprise Server pro aplikace SAP.
Azure Marketplace obsahuje bitovou kopii pro SUSE Linux Enterprise Server pro SAP 12 aplikace s BYOS (přineste si vlastní předplatné), který můžete použít k nasazení nových virtuálních počítačů.

### <a name="manual-deployment"></a>Ruční nasazení

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvořte dva účty úložiště
1. Vytvořit skupinu dostupnosti  
   Sada maximální aktualizace domény
1. Vytvořit nástroj pro vyrovnávání zatížení (interní)  
   Vyberte virtuální síť vytvořili v druhém kroku
1. Vytvoření virtuálního počítače 1   
   Použijte alespoň SLES4SAP 12 SP1 v tomto příkladu https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1 image SLES4SAP 12 SP1 BYOS  
   SLES pro SAP aplikace 12 SP1 (BYOS) se používá.  
   Vyberte účet úložiště 1   
   Vyberte sady dostupnosti.  
1. Vytvoření virtuálního počítače 2   
   Použijte alespoň SLES4SAP 12 SP1 v tomto příkladu https://portal.azure.com/#create/suse-byos.sles-for-sap-byos12-sp1 image SLES4SAP 12 SP1 BYOS  
   SLES pro SAP aplikace 12 SP1 (BYOS) se používá.  
   Vyberte účet úložiště 2    
   Vyberte sady dostupnosti.  
1. Přidat datových disků
1. Konfigurace pro vyrovnávání zatížení
    1. Vytvořte fond IP front-endu
        1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fond IP front-endu a klikněte na tlačítko Přidat
        1. Zadejte název nového fondu IP front-endu (například hana-front-endu)
        1. Klikněte na tlačítko OK
        1. Po vytvoření nového fondu IP front-endu, zapište si jeho IP adresu
    1. Vytvořte fond back-end
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte back-endové fondy a klikněte na tlačítko Přidat
        1. Zadejte název nového fondu back-end (například hana-back-end)
        1. Klikněte na tlačítko Přidat virtuální počítač
        1. Vyberte jste dříve vytvořili sadu dostupnosti
        1. Vyberte virtuální počítače clusteru SAP HANA
        1. Klikněte na tlačítko OK
    1. Vytvoření sondy stavu
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte sondy stavu služby a klikněte na tlačítko Přidat
        1. Zadejte název nové kontroly stavu (například hana-hp)
        1. Vyberte TCP jako protokol, port 625**03**, zachovat Interval 5 a prahová hodnota špatného stavu 2
        1. Klikněte na tlačítko OK
    1. Vytvoření pravidel vyrovnávání zatížení
        1. Otevřete nástroj pro vyrovnávání zatížení, zvolte pravidla Vyrovnávání zatížení a klikněte na tlačítko Přidat
        1. Zadejte název nové pravidlo Vyrovnávání zatížení (například hana-lb-3**03**15)
        1. Vyberte front-endovou IP adresu, fond back-end a test stavu, který jste vytvořili dříve (pro front-endu – příklad hana)
        1. Zachovat protokol TCP, zadejte port 3**03**15
        1. Časový limit nečinnosti zvýšení do 30 minut
        1. **Nezapomeňte povolit plovoucí IP adresa**
        1. Klikněte na tlačítko OK
        1. Opakujte předchozí kroky pro port 3**03**17

### <a name="deploy-with-template"></a>Nasazení pomocí šablony
Můžete jeden z šablony rychlý start na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti apod. Pokud chcete nasadit šablonu, postupujte takto:

1. Otevřete [databáze šablony] [ template-multisid-db] nebo [konvergované šablony] [ template-converged] na portálu Azure. 
   Zatímco sblížené Šablona také vytváří pravidla Vyrovnávání zatížení ASC nebo SCS a instance YBRAT (pouze Linux), vytvoří šablona databáze pouze pravidla Vyrovnávání zatížení pro databázi. Pokud máte v plánu pro instalaci systému SAP NetWeaver na základě a také chcete nainstalovat instanci ASC nebo SCS stejné počítače, použijte [konvergované šablony][template-converged].
1. Zadejte následující parametry
    1. ID systému SAP  
       Zadejte ID systému SAP systému SAP, který chcete nainstalovat. ID se bude používat jako předpona pro prostředky, které jsou nasazeny.
    1. Typ zásobníku (platí pouze pokud použijete šablonu sblížené)   
       Vyberte typ SAP NetWeaver zásobníku
    1. Typ operačního systému  
       Vyberte jednu z distribucích systému Linux. V tomto příkladu vyberte SLES 12 BYOS
    1. Typ databáze  
       Vyberte HANA
    1. Velikost systému SAP  
       Množství nový systém bude poskytovat protokoly SAP. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor
    1. Dostupnost systému  
       Vyberte HA
    1. Uživatelské jméno správce a heslo správce  
       Po vytvoření nového uživatele, který lze použít pro přihlášení k počítači.
    1. Nový nebo existující podsíť  
       Určuje, zda mají být vytvořeny nové virtuální sítě a podsítě nebo by měl použít existující podsítí. Pokud již máte virtuální síť, která je připojen k síti na pracovišti, vyberte existující.
    1. ID podsítě  
    ID podsítě, ke které by měl být připojený virtuální počítače. Chcete-li připojit virtuální počítač k síti na pracovišti, vyberte podsíť virtuální sítě VPN nebo Express Route. ID obvykle vypadá /subscriptions/`<subscription ID`> /resourceGroups/`<resource group name`> /providers/Microsoft.Network/virtualNetworks/`<virtual network name`> /subnets/`<subnet name`>

## <a name="setting-up-linux-ha"></a>Nastavení Linux HA

Následující položky jsou s předponou buď [A] - platí pro všechny uzly [1] - platí jenom pro uzel 1 nebo [2] - platí jenom pro uzel 2.

1. [A] SLES pro SAP BYOS jenom - SLES zaregistrovat, abyste mohli použít úložiště
1. [A] SLES pro SAP BYOS pouze – přidání modulu veřejného cloudu
1. [A] aktualizace SLES
    ```bash
    sudo zypper update

    ```

1. [1] přístupu ssh
    ```bash
    sudo ssh-keygen -tdsa
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key
    sudo cat /root/.ssh/id_dsa.pub
    ```

2. [2] přístupu ssh
    ```bash
    sudo ssh-keygen -tdsa

    # insert the public key you copied in the last step into the authorized keys file on the second server
    sudo vi /root/.ssh/authorized_keys
    
    # Enter file in which to save the key (/root/.ssh/id_dsa): -> ENTER
    # Enter passphrase (empty for no passphrase): -> ENTER
    # Enter same passphrase again: -> ENTER
    
    # copy the public key    
    sudo cat /root/.ssh/id_dsa.pub
    ```

1. [1] přístupu ssh
    ```bash
    # insert the public key you copied in the last step into the authorized keys file on the first server
    sudo vi /root/.ssh/authorized_keys
    
    ```

1. [A] nainstalovat rozšíření HA
    ```bash
    sudo zypper install sle-ha-release fence-agents
    
    ```

1. [A] rozložení disku instalační program
    1. LVM  
    Obecně doporučujeme používat LVM pro svazky, které ukládají data a soubory protokolu. Následující příklad předpokládá, že máte virtuální počítače čtyři datových disků připojených, které se mají použít k vytvoření dva svazky.
        * Vytvořte fyzických svazků pro všechny disky, které chcete použít.
    <pre><code>
    sudo pvcreate /dev/sdc
    sudo pvcreate /dev/sdd
    sudo pvcreate /dev/sde
    sudo pvcreate /dev/sdf
    </code></pre>
        * Vytvoření skupiny svazku pro datové soubory, jedna skupina svazku pro soubory protokolů a jeden pro do sdíleného adresáře SAP HANA
    <pre><code>
    sudo vgcreate vg_hana_data /dev/sdc /dev/sdd
    sudo vgcreate vg_hana_log /dev/sde
    sudo vgcreate vg_hana_shared /dev/sdf
    </code></pre>
        * Vytvoření logické svazky
    <pre><code>
    sudo lvcreate -l 100%FREE -n hana_data vg_hana_data
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log
    sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared
    sudo mkfs.xfs /dev/vg_hana_data/hana_data
    sudo mkfs.xfs /dev/vg_hana_log/hana_log
    sudo mkfs.xfs /dev/vg_hana_shared/hana_shared
    </code></pre>
        * Vytvořte připojení adresáře a zkopírujte identifikátor UUID všechny logické svazky
    <pre><code>
    sudo mkdir -p /hana/data
    sudo mkdir -p /hana/log
    sudo mkdir -p /hana/shared
    # write down the ID of /dev/vg_hana_data/hana_data, /dev/vg_hana_log/hana_log and /dev/vg_hana_shared/hana_shared
    sudo blkid
    </code></pre>
        * Vytvoření položky fstab pro tři logické svazky
    <pre><code>
    sudo vi /etc/fstab
    </code></pre>
    Vložení tohoto řádku /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_data/hana_data&gt;</b> /hana/data xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_log/hana_log&gt;</b> /hana/log xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/<b>&lt;UUID of /dev/vg_hana_shared/hana_shared&gt;</b> /hana/shared xfs  defaults,nofail  0  2
    </code></pre>
        * Připojit nové svazky
    <pre><code>
    sudo mount -a
    </code></pre>
    1. Nešifrovaná disky  
       Pro malé nebo ukázku systémů, můžete umístit HANA soubory protokolu a data na jeden disk. Následující příkazy na /dev/sdc vytvořit oddíl a naformátovat s xfs.
    ```bash
    sudo fdisk /dev/sdc
    sudo mkfs.xfs /dev/sdc1
    
    # <a name="write-down-the-id-of-devsdc1"></a>Poznamenejte si ID /dev/sdc1
    sudo/sbin/blkid sudo vi/etc/fstab
    ```

    Insert this line to /etc/fstab
    <pre><code>
    /dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
    </code></pre>

    Create the target directory and mount the disk.

    ```bash
    sudo mkdir /hana
    sudo mount -a
    ```

1. [A] instalační program rozlišení názvu hostitele pro všechny hostitele  
    Můžete buď použít DNS server, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak chcete použít soubor/etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech
    ```bash
    sudo vi /etc/hosts
    ```
    Vložte následující řádky, které se/etc/hosts. Změnit IP adresu a název hostitele tak, aby odpovídaly prostředí    
    
    <pre><code>
    <b>&lt;IP address of host 1&gt; &lt;hostname of host 1&gt;</b>
    <b>&lt;IP address of host 2&gt; &lt;hostname of host 2&gt;</b>
    </code></pre>

1. [1] instalaci clusteru
    ```bash
    sudo ha-cluster-init
    
    # Do you want to continue anyway? [y/N] -> y
    # Network address to bind to (e.g.: 192.168.1.0) [10.79.227.0] -> ENTER
    # Multicast address (e.g.: 239.x.x.x) [239.174.218.125] -> ENTER
    # Multicast port [5405] -> ENTER
    # Do you wish to use SBD? [y/N] -> N
    # Do you wish to configure an administration IP? [y/N] -> N
    ```
        
1. [2] Přidání uzlu do clusteru
    ```bash
    sudo ha-cluster-join
        
    # WARNING: NTP is not configured to start at system boot.
    # WARNING: No watchdog device found. If SBD is used, the cluster will be unable to start without a watchdog.
    # Do you want to continue anyway? [y/N] -> y
    # IP address or hostname of existing node (e.g.: 192.168.1.1) [] -> IP address of node 1 e.g. 10.0.0.5
    # /root/.ssh/id_dsa already exists - overwrite? [y/N] N
    ```

1. [A] změnit heslo hacluster na stejné heslo
    ```bash
    sudo passwd hacluster
    
    ```

1. [A] nakonfigurujte corosync používají jiné přenos a přidání seznamu. V opačném případě clusteru není budete pracovat. 
    ```bash
    sudo vi /etc/corosync/corosync.conf    
    
    ```

    Do souboru přidejte následující obsah tučně.
    
    <pre><code> 
    [...]
      interface { 
          [...] 
      }
      <b>transport:      udpu</b>
    } 
    <b>nodelist {
      node {
        ring0_addr:     < ip address of node 1 >
      }
      node {
        ring0_addr:     < ip address of node 2 > 
      } 
    }</b>
    logging {
      [...]
    </code></pre>

    Potom restartujte službu corosync

    ```bash
    sudo service corosync restart
    
    ```

1. [A] instalovat balíčky HANA HA  
    ```bash
    sudo zypper install SAPHanaSR
    
    ```

## <a name="installing-sap-hana"></a>Instalace SAP HANA

K instalaci replikaci systému SAP HANA, postupujte podle kapitoly 4 z [SAP HANA SR výkonu optimalizované scénář průvodce][suse-hana-ha-guide].

1. [A] spusťte hdblcm z disku DVD HANA
    * Zvolte instalace-1 >
    * Vyberte další součásti k instalaci -> 1
    * Zadejte instalační cestu [/ hana/sdílené]: -> zadejte
    * Zadejte název místního hostitele [.]: -> zadejte
    * Opravdu chcete přidat další hostitele do systému? (y/n) [n]: -> ENTER
    * Zadejte ID systému SAP HANA: <SID of HANA e.g. HDB>
    * Zadejte čísla Instance [00]:   
  Čísla HANA Instance. Pokud používá šablony Azure nebo Ruční nasazení a potom použít 03
    * Vyberte režim databáze / zadejte Index [1]: -> zadejte
    * Vyberte použití systému / zadejte Index [4]:  
  Vyberte systém využití
    * Zadejte umístění datových svazků [/ hana/data/HDB]: -> zadejte
    * Zadejte umístění protokolu svazků [/ hana/log/HDB]: -> zadejte
    * Omezení přidělení paměti maximální? [n]: -> ENTER
    * Zadejte název hostitele certifikát pro hostitele,..." [...]: -> Zadejte
    * Zadejte SAP hostitele agenta uživatele (sapadm) heslo:
    * Potvrďte SAP hostitele agenta uživatele (sapadm) heslo:
    * Zadejte správce systému (hdbadm) heslo:
    * Potvrzení správce systému (hdbadm) heslo:
    * Zadejte domovský adresář správce systému [/ usr/sap nebo HDB/domovské]: -> zadejte
    * Zadejte prostředí přihlášení správce systému [/ bin/dílet]: -> zadejte
    * Zadejte ID uživatele správce systému [1001]: -> zadejte
    * Zadejte ID ze skupiny uživatelů (sapsys) [79]: -> zadejte
    * Zadejte heslo k databázi uživatelů (systém):
    * Potvrďte heslo k databázi uživatelů (systém):
    * Restartování systému po restartování počítače? [n]: -> ENTER
    * Chcete pokračovat? (Ano/Ne):   
  Ověřit, souhrn a zadejte y můžete pokračovat
1. [A] Agent hostitele upgradu SAP  
  Stáhněte si nejnovější archivu SAP Agent hostitele z [SAP Softwarecenter] [ sap-swcenter] a spusťte následující příkaz k aktualizaci agenta. Nahraďte cestu do archivu tak, aby odkazoval na soubor, který jste stáhli.
    ```bash
    sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
    ```

1. [1] vytvoření HANA replikace (jako uživatel root)  
    Spusťte následující příkaz. Nezapomeňte nahradit tučné řetězce (HANA systému ID HDB a číslo instance 03) s hodnotami instalace SAP HANA.
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"' 
    hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync' 
    hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME' 
    </code></pre>

1. [A] vytvoření položky úložiště klíčů (jako uživatel root)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
    </code></pre>
1. [1] zálohy databáze (jako uživatel root)
    <pre><code>
    PATH="$PATH:/usr/sap/<b>HDB</b>/HDB<b>03</b>/exe"
    hdbsql -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')" 
    </code></pre>
1. [1] přepnout na sapsid uživatele (například hdbadm) a vytvořte primární lokality.
    <pre><code>
    su - <b>hdb</b>adm
    hdbnsutil -sr_enable –-name=<b>SITE1</b>
    </code></pre>
1. [2] přepnout na sapsid uživatele (například hdbadm) a vytvořit sekundární lokalitu.
    <pre><code>
    su - <b>hdb</b>adm
    sapcontrol -nr <b>03</b> -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=<b>saphanavm1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b> 
    </code></pre>

## <a name="configure-cluster-framework"></a>Konfigurace architektury clusteru

Změňte výchozí nastavení

<pre>
sudo vi crm-defaults.txt
# enter the following to crm-defaults.txt
<code>
property $id="cib-bootstrap-options" \
  no-quorum-policy="ignore" \
  stonith-enabled="true" \
  stonith-action="reboot" \
  stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
  resource-stickiness="1000" \
  migration-threshold="5000"
op_defaults $id="op-options" \
  timeout="600"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Nyní se nám načíst soubor do clusteru
sudo crm nakonfigurovat aktualizace zatížení crm-defaults.txt
</pre>

### <a name="create-stonith-device"></a>Vytvoření STONITH zařízení

STONITH zařízení používá objekt služby k autorizaci s Microsoft Azure. Pokud chcete vytvořit objekt služby, postupujte podle těchto kroků.

1. Přejděte na <https://portal.azure.com>.
1. Otevřete okno Azure Active Directory  
   Přejděte k vlastnostem a poznamenejte si ID adresáře. Toto ID je **ID klienta**.
1. Klikněte na možnost registrace aplikace
1. Klikněte na tlačítko Přidat.
1. Zadejte název, vyberte typ aplikace "Aplikace webového rozhraní API", zadejte přihlašovací adresu URL (například http://localhost) a klikněte na možnost vytvořit
1. Adresa URL přihlašování se nepoužívá a může být libovolná platná adresa URL
1. Vyberte nové aplikace a na kartě nastavení klikněte na klíče
1. Zadejte popis pro nový klíč, vyberte "Je platné stále" a klikněte na Uložit
1. Poznamenejte si hodnotu. Použije se jako **heslo** pro objekt služby
1. Poznamenejte si ID aplikace. Se používá jako uživatelské jméno (**přihlašovacího ID** v následujících krocích) instančního objektu

Objekt služby nemá oprávnění pro přístup k prostředkům Azure ve výchozím nastavení. Přidělení oprávnění objektu služby ke spuštění a zastavení (zrušit přidělení) všechny virtuální počítače v clusteru.

1. Go to https://portal.azure.com
1. Otevře se okno všechny prostředky
1. Vyberte virtuální počítač
1. Klikněte na řízení přístupu (IAM)
1. Klikněte na tlačítko Přidat.
1. Vyberte roli vlastníka
1. Zadejte název aplikace, kterou jste vytvořili v dřívějších krocích
1. Klikněte na tlačítko OK

Poté, co jste upravili oprávnění pro virtuální počítače, můžete nakonfigurovat zařízení STONITH v clusteru.

<pre>
sudo vi crm-fencing.txt
# enter the following to crm-fencing.txt
# replace the bold string with your subscription ID, resource group, tenant ID, service principal ID and password
<code>
primitive rsc_st_azure_1 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

primitive rsc_st_azure_2 stonith:fence_azure_arm \
    params subscriptionId="<b>subscription ID</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" login="<b>login ID</b>" passwd="<b>password</b>"

colocation col_st_azure -2000: rsc_st_azure_1:Started rsc_st_azure_2:Started
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Nyní se nám načíst soubor do clusteru
sudo crm nakonfigurovat aktualizace zatížení crm-fencing.txt
</pre>

### <a name="create-sap-hana-resources"></a>Vytvořit prostředky SAP HANA

<pre>
sudo vi crm-saphanatop.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number and HANA system ID
<code>
primitive rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHanaTopology \
    operations $id="rsc_sap2_<b>HDB</b>_HDB<b>03</b>-operations" \
    op monitor interval="10" timeout="600" \
    op start interval="0" timeout="600" \
    op stop interval="0" timeout="300" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>"

clone cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> rsc_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" clone-node-max="1" target-role="Started" interleave="true"
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Nyní se nám načíst soubor do clusteru
sudo crm nakonfigurovat aktualizace zatížení crm-saphanatop.txt
</pre>

<pre>
sudo vi crm-saphana.txt
# enter the following to crm-saphana.txt
# replace the bold string with your instance number, HANA system ID and the frontend IP address of the Azure load balancer. 
<code>
primitive rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> ocf:suse:SAPHana \
    operations $id="rsc_sap_<b>HDB</b>_HDB<b>03</b>-operations" \
    op start interval="0" timeout="3600" \
    op stop interval="0" timeout="3600" \
    op promote interval="0" timeout="3600" \
    op monitor interval="60" role="Master" timeout="700" \
    op monitor interval="61" role="Slave" timeout="700" \
    params SID="<b>HDB</b>" InstanceNumber="<b>03</b>" PREFER_SITE_TAKEOVER="true" \
    DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

ms msl_SAPHana_<b>HDB</b>_HDB<b>03</b> rsc_SAPHana_<b>HDB</b>_HDB<b>03</b> \
    meta is-managed="true" notify="true" clone-max="2" clone-node-max="1" \
    target-role="Started" interleave="true"

primitive rsc_ip_<b>HDB</b>_HDB<b>03</b> ocf:heartbeat:IPaddr2 \ 
    meta target-role="Started" is-managed="true" \ 
    operations $id="rsc_ip_<b>HDB</b>_HDB<b>03</b>-operations" \ 
    op monitor interval="10s" timeout="20s" \ 
    params ip="<b>10.0.0.21</b>" 
primitive rsc_nc_<b>HDB</b>_HDB<b>03</b> anything \ 
    params binfile="/usr/bin/nc" cmdline_options="-l -k 625<b>03</b>" \ 
    op monitor timeout=20s interval=10 depth=0 
group g_ip_<b>HDB</b>_HDB<b>03</b> rsc_ip_<b>HDB</b>_HDB<b>03</b> rsc_nc_<b>HDB</b>_HDB<b>03</b>
 
colocation col_saphana_ip_<b>HDB</b>_HDB<b>03</b> 2000: g_ip_<b>HDB</b>_HDB<b>03</b>:Started \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>:Master  
order ord_SAPHana_<b>HDB</b>_HDB<b>03</b> 2000: cln_SAPHanaTopology_<b>HDB</b>_HDB<b>03</b> \ 
    msl_SAPHana_<b>HDB</b>_HDB<b>03</b>
</code>

# <a name="now-we-load-the-file-to-the-cluster"></a>Nyní se nám načíst soubor do clusteru
sudo crm nakonfigurovat aktualizace zatížení crm-saphana.txt
</pre>

### <a name="test-cluster-setup"></a>Nastavení clusteru s podporou testu
Tato kapitola popisuje, jak můžete otestovat vašeho nastavení. Každý test předpokládá, že jsou kořenové a hlavním serveru SAP HANA běží na saphanavm1 virtuálního počítače.

#### <a name="fencing-test"></a>Vymezení testu

Instalační program agenta vymezení můžete otestovat zakázáním síťové rozhraní na uzlu saphanavm1.

<pre><code>
sudo ifdown eth0
</code></pre>

Virtuální počítač by měl nyní restartovat, nebo byla zastavena v závislosti na konfiguraci clusteru.
Pokud jste nastavili stonith akce, která bude vypnuto, virtuální počítač bude zastavena a prostředky se migrují do spuštěného virtuálního počítače.

Po spuštění virtuálního počítače, prostředků SAP HANA nepodaří spustit jako sekundární Pokud nastavíte AUTOMATED_REGISTER = "false". V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-manual-failover"></a>Testování ruční převzetí služeb při selhání

Zastavování služby kardiostimulátor na uzlu saphanavm1, můžete otestovat ruční převzetí služeb při selhání.
<pre><code>
service pacemaker stop
</code></pre>

Po převzetí služeb při selhání můžete spustit službu znovu. Pokud nastavíte AUTOMATED_REGISTER = "false", SAP HANA prostředku saphanavm1 nepodaří spustit jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>
service pacemaker start
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 


# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

#### <a name="testing-a-migration"></a>Testování migrace

Spuštěním následujícího příkazu můžete migrovat hlavní uzel SAP HANA
<pre><code>
crm resource migrate msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
crm resource migrate g_ip_<b>HDB</b>_HDB<b>03</b> <b>saphanavm2</b>
</code></pre>

Pokud nastavíte AUTOMATED_REGISTER = "false", tato sekvence příkazů by migrovat SAP HANA hlavní uzel a skupiny, která obsahuje virtuální IP adresu, kterou saphanavm2.
SAP HANA prostředku saphanavm1 se nepodaří spustit jako sekundární. V takovém případě nakonfigurujte instanci HANA jako sekundární spuštěním tohoto příkazu:

<pre><code>
su - <b>hdb</b>adm

# Stop the HANA instance just in case it is running
sapcontrol -nr <b>03</b> -function StopWait 600 10
hdbnsutil -sr_register --remoteHost=<b>saphanavm2</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b> 
</code></pre>

Migrace vytvoří omezení umístění, které je potřeba znovu odstranit.

<pre><code>
crm configure edited

# Delete location constraints that are named like the following contraint. You should have two constraints, one for the SAP HANA resource and one for the IP address group.
location cli-prefer-g_ip_<b>HDB</b>_HDB<b>03</b> g_ip_<b>HDB</b>_HDB<b>03</b> role=Started inf: <b>saphanavm2</b>
</code></pre>

Musíte také vyčištění stav prostředku sekundárního uzlu

<pre><code>
# Switch back to root and cleanup the failed state
exit
crm resource cleanup msl_SAPHana_<b>HDB</b>_HDB<b>03</b> <b>saphanavm1</b>
</code></pre>

## <a name="next-steps"></a>Další postup
* [Azure virtuálních počítačů, plánování a implementace pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP][dbms-guide]
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md). 
