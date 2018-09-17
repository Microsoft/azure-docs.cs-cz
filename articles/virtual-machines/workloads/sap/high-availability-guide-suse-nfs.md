---
title: Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server | Dokumentace Microsoftu
description: Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server
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
ms.date: 08/16/2018
ms.author: sedusch
ms.openlocfilehash: 99b7b83ca2d7f6f19df137e6ecf5deaf411e9a5e
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634740"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server

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

[sles-hae-guides]:https://www.suse.com/documentation/sle-ha-12/
[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Tento článek popisuje, jak nasadit virtuální počítače, konfigurace virtuálních počítačů, nainstalujte rozhraní clusteru a nainstalujte systém souborů NFS server s vysokou dostupností, který slouží k ukládání sdílených dat s vysokou dostupností systému SAP.
Tato příručka popisuje, jak vytvořit vysoce dostupný server systému souborů NFS, který se používá dva systémy SAP NW1 a NW2. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu se předpokládá, že jste použili [šablony serveru SAP souboru] [ template-file-server] s předponou prostředků **produkční**.

Přečtěte si následující poznámky SAP a Paper nejprve

* Poznámka SAP [1928533], který obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporovány pro nasazení softwaru SAP
  * Informace o kapacitě důležité pro velikosti virtuálních počítačů Azure
  * Podporované SAP software a operační systém (OS) a kombinace databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* Poznámka SAP [2015553] uvádí předpoklady pro nasazení softwaru SAP nepodporuje SAP v Azure.
* Poznámka SAP [2205917] se doporučuje nastavení operačního systému SUSE Linux Enterprise Server pro aplikace SAP
* Poznámka SAP [1944799] obsahuje pokyny pro SAP HANA pro SUSE Linux Enterprise Server pro aplikace SAP
* Poznámka SAP [2178632] podrobné informace o všech monitorování metrik pro SAP v Azure.
* Poznámka SAP [2191498] má požadovaná verze SAP hostitele agenta pro Linux v Azure.
* Poznámka SAP [2243692] obsahuje informace o SAP programech v Linuxu v Azure.
* Poznámka SAP [1984787] obsahuje obecné informace o operačním systémem SUSE Linux Enterprise Server 12.
* Poznámka SAP [1999351] obsahuje další informace o odstraňování potíží pro rozšířené monitorování rozšíření Azure pro SAP.
* [WIKI komunity SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Azure Virtual Machines, plánování a implementace SAP na platformě Linux][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [SUSE Linux Enterprise vysokou dostupnost rozšíření 12 SP3 osvědčené postupy vodítka][sles-hae-guides]
  * Úložiště s vysokou dostupností systému souborů NFS se DRBD a Pacemaker
* [SUSE Linux Enterprise Server pro SAP aplikace 12 SP3 osvědčené postupy vodítka][sles-for-sap-bp]

## <a name="overview"></a>Přehled

Abyste dosáhli vysoké dostupnosti, SAP NetWeaver vyžaduje serverem NFS. Server systému souborů NFS je nakonfigurované na jiném clusteru a mohou být využívána více systémů SAP.

![Přehled SAP NetWeaver vysoké dostupnosti](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Server systému souborů NFS používá vyhrazené virtuální název hostitele a virtuální IP adresy pro každý systém SAP, který používá tento server systému souborů NFS. V Azure je potřeba nástroj pro vyrovnávání zatížení pomocí virtuální IP adresu. Následující seznam obsahuje konfiguraci nástroje pro vyrovnávání zatížení.        

* Konfiguraci front-endu
  * Adresa IP adresou 10.0.0.4 NW1
  * IP adresa 10.0.0.5 NW2
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS
* Port testu
  * Port 61000 NW1
  * Port 61001 NW2
* Pravidla Vyrovnávání zatížení
  * 2049 TCP pro NW1
  * UDP 2049 pro NW1
  * 2049 TCP pro NW2
  * UDP 2049 pro NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Nastavení vysoce dostupný server pro systém souborů NFS

Nasadit všechny požadované prostředky Azure, včetně virtuálních počítačů můžete použít šablony Azure z Githubu, dostupnost nastavit a nástroj pro vyrovnávání zatížení nebo můžete ručně nasadit prostředky.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu pomocí šablony Azure

Na webu Azure Marketplace obsahuje bitovou kopii operačního systému SUSE Linux Enterprise Server pro 12 aplikací SAP, který můžete použít k nasazení nových virtuálních počítačů.
Můžete některou ze šablon quickstart na Githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti, atd. Postupujte podle těchto kroků a nasaďte šablonu:

1. Otevřít [šablony serveru SAP souboru] [ template-file-server] na webu Azure Portal   
1. Zadejte následující parametry
   1. Předpona prostředků  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazené.
   2. Počet systému SAP  
      Zadejte počet systémů SAP, které budou používat tento souborový server. To nasadí požadované konfigurace front-endu, Vyrovnávání zatížení a pravidla test porty, disky atd.
   3. Typ operačního systému  
      Vyberte jednu z Linuxových distribucí. V tomto příkladu vyberte SLES 12
   4. Uživatelské jméno Admin a heslo správce  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   5. ID podsítě  
      Pokud chcete nasadit virtuální počítač do existující virtuální síť ve kterých máte definované podsíti virtuálního počítače by se měla přiřadit k pojmenování ID tuto konkrétní podsíť. ID obvykle vypadá /subscriptions/**&lt;ID předplatného&gt;**/resourceGroups/**&lt;název skupiny prostředků&gt;**/poskytovatelé/ Microsoft.Network/virtualNetworks/**&lt;název virtuální sítě&gt;**/subnets/**&lt;název podsítě&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu prostřednictvím webu Azure portal

Nejprve musíte vytvořit virtuální počítače pro tento cluster systému souborů NFS. Potom vytvořte nástroj pro vyrovnávání zatížení a použijte virtuální počítače v back-endové fondy.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti  
   Nastavení maximální aktualizační doména
1. Vytvořit virtuální počítač 1 použijte alespoň SLES4SAP 12 SP3, v tomto příkladu SLES4SAP 12 SP3 BYOS image SLES pro SAP aplikace 12 SP3 (BYOS) se používá  
   Vyberte dříve vytvořenou skupinu dostupnosti  
1. Vytvořit virtuální počítač 2 použití alespoň 12 SP3 SLES4SAP, v tomto příkladu SLES4SAP 12 SP3 BYOS image  
   SLES pro SAP aplikace 12 SP3 (BYOS) se používá.  
   Vyberte dříve vytvořenou skupinu dostupnosti  
1. Přidejte jeden datový disk pro každý systém SAP do obou virtuálních počítačů.
1. Vytvořte nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvoření front-endové IP adresy
      1. Adresa IP adresou 10.0.0.4 NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-endový fond IP adres a klikněte na tlačítko Přidat
         1. Zadejte název nové front-endový fond IP (například **nw1 front-endu**)
         1. Nastavení přiřazení do statické a zadejte IP adresu (například **10.0.0.4**)
         1. Klikněte na tlačítko OK
      1. IP adresa 10.0.0.5 NW2
         * Opakujte předchozí kroky pro NW2
   1. Vytvoření back-endové fondy
      1. Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS pro NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového back-endového fondu (například **nw1 back-endu**)
         1. Klikněte na tlačítko Přidat virtuální počítač
         1. Vyberte skupinu dostupnosti jste vytvořili dříve
         1. Vyberte virtuální počítače clusteru systému souborů NFS
         1. Klikněte na tlačítko OK
      1. Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS pro NW2
         * Zopakujte výše uvedené kroky a vytvořte fond back-endu pro NW2
   1. Vytvoření sondy stavu
      1. Port 61000 NW1
         1. Otevřete nástroj pro vyrovnávání zatížení vyberte testy stavu a klikněte na tlačítko Přidat
         1. Zadejte název nového stavu testu (například **nw1 hp**)
         1. Jako protokol, port 610 vyberte TCP**00**, Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 61001 NW2
         * Zopakujte výše uvedené kroky a vytvořte sondu stavu pro NW2
   1. Pravidla Vyrovnávání zatížení
      1. 2049 TCP pro NW1
         1. Otevřete nástroj pro vyrovnávání zatížení, pravidel Vyrovnávání zatížení vyberte a klikněte na tlačítko Přidat
         1. Zadejte název nového pravidla služby load balancer (například **nw1-lb-2049**)
         1. Vyberte IP adresu front-endu, back-endového fondu a sondu stavu, které jste vytvořili dříve (například **nw1 front-endu**)
         1. Zachovat protokol **TCP**, zadejte port **2049**
         1. Zvyšte časový limit nečinnosti až 30 minut.
         1. **Ujistěte se, že chcete povolit plovoucí IP adresy**
         1. Klikněte na tlačítko OK
      1. UDP 2049 pro NW1
         * Opakujte předchozí kroky pro port 2049 a UDP pro NW1
      1. 2049 TCP pro NW2
         * Opakujte předchozí kroky pro 2049 portu a protokolu TCP pro NW2
      1. UDP 2049 pro NW2
         * Opakujte předchozí kroky pro port 2049 a UDP pro NW2

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) vytvořit cluster základní Pacemaker pro tento server systému souborů NFS.

### <a name="configure-nfs-server"></a>Konfigurace serveru NFS

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]**  Serveru povolit systému souborů NFS

   Vytvoření kořenového záznamu export systému souborů NFS

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]**  Nainstalovat komponenty drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]**  Vytvořte oddíl pro drbd zařízení

   Seznam všech dostupných datových disků

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Příklad výstupu
   
   ```
   lun0  lun1
   ```

   Vytvořit oddíly pro každý datový disk

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]**  Vytvořit LVM konfigurace

   Vypsat všechny dostupné oddíly.

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Příklad výstupu
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Vytvořte svazky LVM pro každý oddíl

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]**  Nakonfigurovat drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Ujistěte se, že soubor drbd.conf obsahuje následující dva řádky

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Změna konfigurace globálních drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Přidejte následující položky do obslužné rutiny a net oddílu.

   <pre><code>global {
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
             md-flushes yes;
             disk-flushes yes;
             c-plan-ahead 1;
             c-min-rate 100M;
             c-fill-target 20M;
             c-max-rate 4G;
        }
        net {
             after-sb-0pri discard-younger-primary;
             after-sb-1pri discard-secondary;
             after-sb-2pri call-pri-lost-after-sb;
             protocol     C;
             tcp-cork yes;
             max-buffers 20000;
             max-epoch-size 20000;
             sndbuf-size 0;
             rcvbuf-size 0;
        }
   }
   </code></pre>

1. **[A]**  Umožňuje vytvářet devices drbd systému souborů NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Vložit konfigurace pro nové zařízení drbd a ukončení

   <pre><code>resource <b>NW1</b>-nfs {
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

   <pre><code>sudo vi /etc/drbd.d/<b>NW2</b>-nfs.res
   </code></pre>

   Vložit konfigurace pro nové zařízení drbd a ukončení

   <pre><code>resource <b>NW2</b>-nfs {
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

   Vytvoření zařízení drbd a spusťte ji

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Přeskočit počáteční synchronizaci.

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Nastavení primárního uzlu

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]**  Počkejte, dokud se synchronizují nová zařízení drbd

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]**  Vytvořit systémy souborů na zařízeních drbd

   <pre><code>sudo mkfs.xfs /dev/drbd0
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

1. **[A]**  Nastavení drbd "schizofrenní" zjišťování

   Při použití drbd k synchronizaci dat z jednoho hostitele do druhého, může dojít, proto volaná schizofrenní. Rozdělení brain je scénář, ve kterém obou uzlů clusteru povýšen primární zařízení drbd a přešel synchronizovaný. Je možné výjimečné situace, ale přesto chtějí zpracování a vyřešit schizofrenní co nejrychleji. Proto je důležité, která vás upozorní, když se stalo schizofrenní.

   Čtení [drbd oficiální dokumentaci](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-split-brain-notification) o tom, jak nastavit oznámení brain rozdělení.

   Je také možné automaticky zotavit po scénář brain rozdělení. Další informace najdete v článku [automatické rozdělení brain obnovení zásad](http://docs.linbit.com/doc/users-guide-83/s-configure-split-brain-behavior/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurace architektury clusteru

1. **[1]**  Přidat zařízení drbd systému souborů NFS pro systém SAP NW1 do konfigurace clusteru

   <pre><code>sudo crm configure rsc_defaults resource-stickiness="200"

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
   
   sudo crm configure primitive nfsserver systemd:nfs-server \
     op monitor interval="30s"
   sudo crm configure clone cl-nfsserver nfsserver

   sudo crm configure primitive exportfs_<b>NW1</b> \
     ocf:heartbeat:exportfs \
     params directory="/srv/nfs/<b>NW1</b>" \
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=1 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW1</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.4</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61000</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]**  Přidat zařízení drbd systému souborů NFS pro systém SAP NW2 do konfigurace clusteru

   <pre><code># Enable maintenance mode
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
     options="rw,no_root_squash" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs \
     anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k <b>61001</b>" op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

1. **[1]**  Zakázat režim údržby
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Další postup

* [Instalace SAP ASCS a databáze](high-availability-guide-suse.md)
* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
