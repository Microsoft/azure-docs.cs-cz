---
title: Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server | Microsoft Docs
description: Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: sedusch
ms.openlocfilehash: 004baee6f3b1ed016ee0336a86d5ea3909d8f255
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656223"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server

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
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Tento článek popisuje, jak k nasazení virtuálních počítačů, konfiguraci virtuálních počítačů, nainstalujte rozhraní framework clusteru a instalaci server s vysokou dostupností systému souborů NFS, který slouží k uložení sdílená data vysokou dostupností systému SAP.
Tato příručka popisuje, jak nastavit server s vysokou dostupností systému souborů NFS, který je používán dvěma systémy SAP, NW1 a NW2. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládá, že jste použili [šablony serveru SAP souboru] [ template-file-server] s předponou prostředků **produkčního**.

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
* [SAP HANA SR výkonu optimalizované scénář][suse-hana-ha-guide]  
  V Průvodci obsahuje všechny požadované informace pro nastavení replikace systému SAP HANA na místě. Tohoto průvodce použijte jako Směrný plán.
* [Vysoce dostupné systému souborů NFS úložiště s DRBD a kardiostimulátor] [ suse-drbd-guide] obsahuje všechny požadované informace k nastavení systému souborů NFS server s vysokou dostupností. Tohoto průvodce použijte jako Směrný plán.


## <a name="overview"></a>Přehled

K dosažení vysoké dostupnosti, vyžaduje SAP NetWeaver serverem NFS. Server systému souborů NFS je nakonfigurován v samostatném clusteru a mohou být využívána na více systémů SAP.

![Přehled SAP NetWeaver vysokou dostupnost](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Pro každý systému SAP, který používá tento server systému souborů NFS používá NFS server vyhrazené virtuální název hostitele a virtuální IP adresy. K použití virtuální IP adresy se v Azure, vyžaduje nástroj pro vyrovnávání zatížení. Následující seznam obsahuje konfiguraci služby Vyrovnávání zatížení.        

* Front-endovou konfiguraci
  * IP adresa 10.0.0.4 pro NW1
  * IP adresa 10.0.0.5 pro NW2
* Konfigurace back-end
  * Připojené k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS
* Port testu
  * Port 61000 pro NW1
  * Port 61001 pro NW2
* Pravidla Vyrovnávání zatížení
  * 2049 TCP pro NW1
  * UDP 2049 pro NW1
  * 2049 TCP pro NW2
  * UDP 2049 pro NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Nastavení serveru s vysokou dostupností systému souborů NFS

Můžete použít buď Azure šablony z githubu nasadit všechny požadované prostředky Azure, včetně virtuálních počítačů, dostupnost nastavit a nástroj pro vyrovnávání zatížení nebo prostředky můžete nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linux pomocí šablony Azure

Azure Marketplace obsahuje bitovou kopii pro SUSE Linux Enterprise Server pro 12 aplikace SAP, který můžete použít k nasazení nových virtuálních počítačů.
Můžete jeden z šablony rychlý start na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti apod. Postupujte podle těchto kroků nasadíte šablony:

1. Otevřete [šablony serveru SAP souboru] [ template-file-server] na portálu Azure   
1. Zadejte následující parametry
   1. Předpona prostředků  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazeny.
   2. Zadejte počet systému SAP počet SAP systémy, které budou používat tento souborový server. To nasadí požadované konfigurace front-endu, pravidla, Vyrovnávání zatížení testu porty, disky atd.
   3. Typ operačního systému  
      Vyberte jednu z distribucích systému Linux. V tomto příkladu vyberte SLES 12
   4. Uživatelské jméno správce a heslo správce  
      Po vytvoření nového uživatele, který lze použít pro přihlášení k počítači.
   5. ID podsítě  
      ID podsítě, ke které by měl být připojený virtuální počítače. Ponechte prázdné, pokud chcete vytvořit novou virtuální síť, nebo vyberte podsíť virtuální sítě VPN nebo Expressroute připojit virtuální počítač k síti na pracovišti. ID obvykle vypadá /subscriptions/**&lt;ID předplatného&gt;**/resourceGroups/**&lt;název skupiny prostředků&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;název virtuální sítě&gt;**/subnets/**&lt;název podsítě.&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční instalace Linux prostřednictvím portálu Azure

Nejprve musíte vytvořit virtuální počítače pro tento cluster systému souborů NFS. Později můžete vytvořit nástroj pro vyrovnávání zatížení a používat virtuální počítače v back-endové fondy.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvořit skupinu dostupnosti  
   Sada maximální aktualizace domény
1. Vytvoření virtuálního počítače 1   
   Použijte alespoň SLES4SAP 12 SP3, v tomto příkladu SLES4SAP 12 SP3 BYOS bitové kopie SLES pro SAP aplikace 12 SP3 (BYOS) se používá  
   Vyberte dříve vytvořenou sadu dostupnosti  
1. Vytvoření virtuálního počítače 2   
   Použijte alespoň SLES4SAP 12 SP3, v tomto příkladu bitovou kopii SLES4SAP 12 SP3 BYOS  
   SLES pro SAP aplikace 12 SP3 (BYOS) se používá.  
   Vyberte dříve vytvořenou sadu dostupnosti  
1. Přidejte jeden datový disk pro každý systém SAP do obou virtuálních počítačů.
1. Vytvořit nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvořte front-end IP adresy
      1. IP adresa 10.0.0.4 pro NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fond IP front-endu a klikněte na tlačítko Přidat
         1. Zadejte název nového fondu IP front-endu (například **nw1 front-endu**)
         1. Nastavit statickou přiřazení a zadejte IP adresu (například **10.0.0.4**)
         1. Klikněte na tlačítko OK         
      1. IP adresa 10.0.0.5 pro NW2
         * Opakujte předchozí kroky pro NW2
   1. Vytvoření fondu back-end
      1. Připojené k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS pro NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, zvolte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového fondu back-end (například **nw1 back-end**)
         1. Klikněte na tlačítko Přidat virtuální počítač
         1. Vyberte jste dříve vytvořili sadu dostupnosti
         1. Vyberte virtuální počítače clusteru systému souborů NFS
         1. Klikněte na tlačítko OK
      1. Připojené k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS pro NW2
         * Opakujte tento postup vytvoření fondu back-end pro NW2
   1. Vytvoření sondy stavu
      1. Port 61000 pro NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, zvolte sondy stavu služby a klikněte na tlačítko Přidat
         1. Zadejte název nové kontroly stavu (například **nw1 hp**)
         1. Vyberte TCP jako protokol, port 610**00**, zachovat Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 61001 pro NW2
         * Opakujte tento postup k vytvoření test stavu pro NW2
   1. Pravidla Vyrovnávání zatížení
      1. 2049 TCP pro NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, zvolte pravidla Vyrovnávání zatížení a klikněte na tlačítko Přidat
         1. Zadejte název nové pravidlo Vyrovnávání zatížení (například **nw1-lb 2049**)
         1. Vyberte front-endovou IP adresu, fond back-end a test stavu, které jste vytvořili dříve (například **nw1 front-endu**)
         1. Zachovat protokol **TCP**, zadejte port **2049**
         1. Časový limit nečinnosti zvýšení do 30 minut
         1. **Nezapomeňte povolit plovoucí IP adresa**
         1. Klikněte na tlačítko OK    
      1. UDP 2049 pro NW1
         * Opakujte předchozí kroky pro port 2049 a UDP pro NW1
      1. 2049 TCP pro NW2
         * Opakujte předchozí kroky pro port 2049 a TCP pro NW2
      1. UDP 2049 pro NW2
         * Opakujte předchozí kroky pro port 2049 a UDP pro NW2

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru kardiostimulátor

Postupujte podle kroků v [nastavení kardiostimulátor na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) vytvořit cluster základní kardiostimulátor pro tento server systému souborů NFS.

### <a name="configure-nfs-server"></a>Konfigurace serveru NFS

Následující položky jsou předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nastavit rozlišení názvu hostitele   

   Můžete buď použít DNS server, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak chcete použít soubor/etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky, které se/etc/hosts. Změnit IP adresu a název hostitele tak, aby odpovídaly prostředí   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Serveru povolit systému souborů NFS

   Vytvořit kořenový záznam exportu, spusťte server systému souborů NFS a povolit automatické spuštění

   <pre><code>
   sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/

   sudo systemctl enable nfsserver
   sudo service nfsserver restart
   </code></pre>

1. **[A]**  Nainstalovat komponenty drbd

   <pre><code>
   sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Vytvořit oddíl pro drbd zařízení

   Vypsat všechny disky dostupných dat

   <pre><code>
   sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Příklad výstupu
   
   ```
   lun0  lun1
   ```

   Vytvoření oddílů pro každý datový disk

   <pre><code>
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Vytvořit LVM konfigurace

   Zobrazí seznam všech dostupných oddílů

   <pre><code>
   ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Příklad výstupu
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Vytvářet svazky LVM pro každý oddíl

   <pre><code>
   sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Konfigurace drbd

   <pre><code>
   sudo vi /etc/drbd.conf
   </code></pre>

   Ujistěte se, že soubor drbd.conf obsahuje následující dva řádky

   <pre><code>
   include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Změnit konfiguraci globální drbd

   <pre><code>
   sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Přidejte následující položky do obslužné rutiny a net části.

   <pre><code>
   global {
        usage-count no;
   }
   common {
        handlers {
             fence-peer "/usr/lib/drbd/crm-fence-peer.sh";
             after-resync-target "/usr/lib/drbd/crm-unfence-peer.sh";
             split-brain "/usr/lib/drbd/notify-split-brain.sh root";
             pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
        }
        startup {
             wfc-timeout 0;
        }
        options {
        }
        disk {
             resync-rate 50M;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
        }
   }
   </code></pre>

1. **[A]**  Vytvořit zařízení drbd systému souborů NFS

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Vložit konfigurace pro nové zařízení drbd a ukončení

   <pre><code>
   resource <b>NW1</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7790</b>;
             device    /dev/drbd<b>0</b>;
             disk      /dev/<b>vg-NW1-NFS</b>/<b>NW1</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   <pre><code>
   sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Vložit konfigurace pro nové zařízení drbd a ukončení

   <pre><code>
   resource <b>NW2</b>-nfs {
        protocol     C;
        disk {
             on-io-error       detach;
        }
        on <b>prod-nfs-0</b> {
             address   <b>10.0.0.6:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
        on <b>prod-nfs-1</b> {
             address   <b>10.0.0.7:7791</b>;
             device    /dev/drbd<b>1</b>;
             disk      /dev/<b>vg-NW2-NFS</b>/<b>NW2</b>;
             meta-disk internal;
        }
   }
   </code></pre>

   Vytvořte drbd zařízení a spusťte ji

   <pre><code>
   sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Přeskočit počáteční synchronizaci.

   <pre><code>
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Nastavte primární uzel

   <pre><code>
   sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Počkejte, dokud se synchronizují nová zařízení drbd

   <pre><code>
   sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Vytvořit systémy souborů na zařízeních drbd

   <pre><code>
   sudo mkfs.xfs /dev/drbd0
   sudo mkdir /srv/nfs/NW1
   sudo chattr +i /srv/nfs/NW1
   sudo mount -t xfs /dev/drbd0 /srv/nfs/NW1
   sudo mkdir /srv/nfs/NW1/sidsys
   sudo mkdir /srv/nfs/NW1/sapmntsid
   sudo mkdir /srv/nfs/NW1/trans
   sudo mkdir /srv/nfs/NW1/ASCS
   sudo mkdir /srv/nfs/NW1/ASCSERS
   sudo mkdir /srv/nfs/NW1/SCS
   sudo mkdir /srv/nfs/NW1/SCSERS
   sudo umount /srv/nfs/NW1

   sudo mkfs.xfs /dev/drbd1
   sudo mkdir /srv/nfs/NW2
   sudo chattr +i /srv/nfs/NW2
   sudo mount -t xfs /dev/drbd1 /srv/nfs/NW2
   sudo mkdir /srv/nfs/NW2/sidsys
   sudo mkdir /srv/nfs/NW2/sapmntsid
   sudo mkdir /srv/nfs/NW2/trans
   sudo mkdir /srv/nfs/NW2/ASCS
   sudo mkdir /srv/nfs/NW2/ASCSERS
   sudo mkdir /srv/nfs/NW2/SCS
   sudo mkdir /srv/nfs/NW2/SCSERS
   sudo umount /srv/nfs/NW2
   </code></pre>

1. **[A]**  Instalace drbd "schizofrenní" detekce

   Při použití drbd k synchronizaci dat z jednoho hostitele na druhého, může dojít, takže názvem schizofrenní. Schizofrenní je scénář, kde obou uzlů clusteru povýší drbd zařízení tak, aby primární a přešel synchronizován. Může být velmi výjimečných situaci, ale přesto chcete zpracovat a schizofrenní tak rychlý jako možné vyřešit. Proto je důležité být upozorněni, když se stalo schizofrenní.

   Čtení [drbd oficiální dokumentaci](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) o tom, jak nastavit oznámení mozku rozdělení.

   Také je možné automaticky zotavit scénáři mozku rozdělení. Další informace najdete v tématu [automatické rozdělení mozku obnovení zásad](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurace architektury clusteru

1. **[1]**  Přidat zařízení drbd systému souborů NFS systému SAP NW1 konfigurace clusteru

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true
   
   sudo crm configure primitive drbd_<b>NW1</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW1</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW1</b>_nfs drbd_<b>NW1</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW1</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd0 \
     directory=/srv/nfs/<b>NW1</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=3 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=4 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=5 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=6 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=7 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW1</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=8 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> exportfs_<b>NW1</b>_sidsys \
     exportfs_<b>NW1</b>_sapmntsid exportfs_<b>NW1</b>_trans exportfs_<b>NW1</b>_ASCS \
     exportfs_<b>NW1</b>_ASCSERS exportfs_<b>NW1</b>_SCS exportfs_<b>NW1</b>_SCSERS \
     nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Přidat zařízení drbd systému souborů NFS systému SAP NW2 konfigurace clusteru

   <pre><code>
   # Enable maintenance mode
   sudo crm configure property maintenance-mode=true   
   
   sudo crm configure primitive drbd_<b>NW2</b>_nfs \
     ocf:linbit:drbd \
     params drbd_resource="<b>NW2</b>-nfs" \
     op monitor interval="15" role="Master" \
     op monitor interval="30" role="Slave"
   
   sudo crm configure ms ms-drbd_<b>NW2</b>_nfs drbd_<b>NW2</b>_nfs \
     meta master-max="1" master-node-max="1" clone-max="2" \
     clone-node-max="1" notify="true" interleave="true"
   
   sudo crm configure primitive fs_<b>NW2</b>_sapmnt \
     ocf:heartbeat:Filesystem \
     params device=/dev/drbd1 \
     directory=/srv/nfs/<b>NW2</b>  \
     fstype=xfs \
     op monitor interval="10s"
   
   sudo crm configure primitive exportfs_<b>NW2</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>" \
     options="rw,no_root_squash" clientspec="*" fsid=9 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sidsys \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sidsys" \
     options="rw,no_root_squash" clientspec="*" fsid=10 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_sapmntsid \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/sapmntsid" \
     options="rw,no_root_squash" clientspec="*" fsid=11 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_trans \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/trans" \
     options="rw,no_root_squash" clientspec="*" fsid=12 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCS" \
     options="rw,no_root_squash" clientspec="*" fsid=13 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_ASCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/ASCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=14 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCS" \
     options="rw,no_root_squash" clientspec="*" fsid=15 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive exportfs_<b>NW2</b>_SCSERS \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW2</b>/SCSERS" \
     options="rw,no_root_squash" clientspec="*" fsid=16 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> exportfs_<b>NW2</b>_sidsys \
     exportfs_<b>NW2</b>_sapmntsid exportfs_<b>NW2</b>_trans exportfs_<b>NW2</b>_ASCS \
     exportfs_<b>NW2</b>_ASCSERS exportfs_<b>NW2</b>_SCS exportfs_<b>NW2</b>_SCSERS \
     nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Zakázat režim údržby
   
   <pre><code>
   sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Další postup
* [Nainstalujte SAP ASC a databáze](high-availability-guide-suse.md)
* [Azure virtuálních počítačů, plánování a implementace pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP][dbms-guide]
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure naleznete v tématu [vysokou dostupnost z SAP HANA ve virtuálních počítačích Azure (VM)][sap-hana-ha]
