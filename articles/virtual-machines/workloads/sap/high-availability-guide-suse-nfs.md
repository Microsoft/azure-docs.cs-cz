---
title: Vysoká dostupnost pro NFS na virtuálních počítačích Azure v SLES | Microsoft Docs
description: Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server
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
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: a08d82103986435593cd73a0728e7426f205a90d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307533"
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
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný server NFS, který se dá použít k ukládání sdílených dat vysoce dostupného systému SAP.
Tato příručka popisuje, jak nastavit vysoce dostupný server NFS, který se používá dvěma systémy SAP, NW1 a NW2. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste použili [šablonu souborového serveru SAP][template-file-server] s předponou prostředků **výr**.


> [!NOTE]
> Tento článek obsahuje odkazy na *podřízené* a *hlavní* pravidla, které Microsoft už nepoužívá. Po odebrání podmínek ze softwaru je odebereme z tohoto článku.

Nejprve si přečtěte následující poznámky a dokumenty SAP

* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro aplikace SAP Poznámka [2205917] se doporučuje nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP.
* Poznámka SAP Poznámka [1944799] obsahuje pokyny pro SAP HANA SUSE Linux Enterprise Server pro aplikace SAP.
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* Poznámka SAP poznámky [1984787] obsahuje obecné informace o SUSE Linux Enterprise Server 12.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux (Tento článek)][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Příručky k osvědčeným postupům pro SUSE Linux Enterprise High Extension 12 SP3][sles-hae-guides]
  * Vysoce dostupné úložiště NFS s DRBD a Pacemaker
* [Příručky k osvědčeným postupům SUSE Linux Enterprise Server pro SAP Applications 12 SP3][sles-for-sap-bp]
* [Zpráva k vydání verze SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Přehled

Pro zajištění vysoké dostupnosti vyžaduje SAP NetWeaver Server NFS. Server NFS je nakonfigurovaný v samostatném clusteru a může ho používat víc systémů SAP.

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Server NFS používá vyhrazený virtuální název hostitele a virtuální IP adresy pro každý systém SAP, který používá tento server NFS. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Následující seznam uvádí konfiguraci nástroje pro vyrovnávání zatížení.        

* Konfigurace front-endu
  * IP adresa 10.0.0.4 pro NW1
  * IP adresa 10.0.0.5 pro NW2
* Konfigurace back-endu
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS
* Port testu paměti
  * Port 61000 pro NW1
  * Port 61001 pro NW2
* Pravidla vyrovnávání zatížení (Pokud se používá základní nástroj pro vyrovnávání zatížení)
  * 2049 TCP pro NW1
  * 2049 UDP pro NW1
  * 2049 TCP pro NW2
  * 2049 UDP pro NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Nastavení vysoce dostupného serveru NFS

K nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, skupiny dostupnosti a nástroje pro vyrovnávání zatížení, můžete použít buď šablonu Azure z GitHubu, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení systému Linux prostřednictvím šablony Azure

Azure Marketplace obsahuje obrázek pro SUSE Linux Enterprise Server aplikace SAP 12, které můžete použít k nasazení nových virtuálních počítačů.
K nasazení všech požadovaných prostředků můžete použít jednu z šablon pro rychlý Start na GitHubu. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti atd. Pomocí těchto kroků nasaďte šablonu:

1. Otevřete [šablonu souborového serveru SAP][template-file-server] v Azure Portal   
1. Zadejte následující parametry.
   1. Předpona prostředku  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro nasazené prostředky.
   2. Počet systémů SAP  
      Zadejte počet systémů SAP, které budou používat tento souborový server. Tím se nasadí požadované množství konfigurací front-endu, pravidel vyrovnávání zatížení, portů sondy, disků atd.
   3. Typ operačního systému  
      Vyberte jednu ze distribucí systému Linux. V tomto příkladu vyberte SLES 12.
   4. Uživatelské jméno správce a heslo správce  
      Vytvoří se nový uživatel, který se dá použít k přihlášení k počítači.
   5. ID podsítě  
      Pokud chcete virtuální počítač nasadit do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako/Subscriptions/**&lt; ID &gt; odběru**/ResourceGroups/název **&lt; &gt; skupiny prostředků****&lt; &gt;** /Providers/Microsoft.Network/virtualNetworks/název **&lt; virtuální sítě &gt;**/subnets/název podsítě

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linux pomocí Azure Portal

Nejprve je třeba vytvořit virtuální počítače pro tento cluster systému souborů NFS. Následně vytvoříte Nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondech back-endu.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvoření virtuálního počítače 1 použijte minimálně SLES4SAP 12 SP3. v tomto příkladu se používá SLES4SAP 12 SP3 BYOS image SLES for SAP Applications 12 SP3 (BYOS).  
   Vybrat skupinu dostupnosti vytvořenou dříve  
1. Vytvořte virtuální počítač 2 použijte minimálně SLES4SAP 12 SP3, v tomto příkladu se BYOS image SLES4SAP 12 SP3.  
   SLES for SAP Applications 12 SP3 (BYOS) se používá  
   Vybrat skupinu dostupnosti vytvořenou dříve  
1. Přidejte jeden datový disk pro každý systém SAP do obou virtuálních počítačů.
1. Vytvořte Load Balancer (interní). Doporučujeme [standardní nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md).  
   1. Při vytváření standardního nástroje pro vyrovnávání zatížení postupujte podle těchto pokynů:
      1. Vytvoření IP adresy front-endu
         1. IP adresa 10.0.0.4 pro NW1
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
            1. Zadejte název nového fondu IP adres front-endu (například **NW1-front-end**).
            1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.0.0.4**).
            1. Klikněte na OK.
         1. IP adresa 10.0.0.5 pro NW2
            * Opakujte výše uvedené kroky pro NW2.
      1. Vytvoření back-end fondů
         1. Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
            1. Zadejte název nového back-end fondu (například **NW-back-end**).
            1. Vyberte Virtual Network
            1. Klikněte na Přidat virtuální počítač.
            1. Vyberte virtuální počítače clusteru NFS a jejich IP adres.
            1. Klikněte na Přidat.
      1. Vytvoření sond stavu
         1. Port 61000 pro NW1
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
            1. Zadejte název nového testu stavu (například **NW1-HP**).
            1. Vybrat TCP as Protocol, port 610 **00**, zachovat interval 5 a špatný práh 2
            1. Klikněte na OK.
         1. Port 61001 pro NW2
            * Zopakováním výše uvedených kroků vytvořte sondu stavu pro NW2.
      1. Pravidla vyrovnávání zatížení
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **NW1-kg**).
         1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **NW1-front-endu**. **NW-back-end** a **NW1-HP**)
         1. Vyberte **porty ha**.
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
         * Opakujte výše uvedené kroky a vytvořte pravidlo vyrovnávání zatížení pro NW2.
   1. Případně, pokud váš scénář vyžaduje základní nástroj pro vyrovnávání zatížení, postupujte podle těchto pokynů:
      1. Vytvoření IP adresy front-endu
         1. IP adresa 10.0.0.4 pro NW1
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
            1. Zadejte název nového fondu IP adres front-endu (například **NW1-front-end**).
            1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.0.0.4**).
            1. Klikněte na OK.
         1. IP adresa 10.0.0.5 pro NW2
            * Opakujte výše uvedené kroky pro NW2.
      1. Vytvoření back-end fondů
         1. Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru systému souborů NFS
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
            1. Zadejte název nového back-end fondu (například **NW-back-end**).
            1. Klikněte na Přidat virtuální počítač.
            1. Vyberte skupinu dostupnosti, kterou jste vytvořili dříve.
            1. Vybrat virtuální počítače clusteru NFS
            1. Klikněte na OK.
      1. Vytvoření sond stavu
         1. Port 61000 pro NW1
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
            1. Zadejte název nového testu stavu (například **NW1-HP**).
            1. Vybrat TCP as Protocol, port 610 **00**, zachovat interval 5 a špatný práh 2
            1. Klikněte na OK.
         1. Port 61001 pro NW2
            * Zopakováním výše uvedených kroků vytvořte sondu stavu pro NW2.
      1. Pravidla vyrovnávání zatížení
         1. 2049 TCP pro NW1
            1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
            1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **NW1-kg-2049**).
            1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **NW1-front-endu**).
            1. Zachovejte protokol **TCP**, zadejte port **2049**
            1. Prodloužit časový limit nečinnosti na 30 minut
            1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
            1. Klikněte na OK.
         1. 2049 UDP pro NW1
            * Opakujte výše uvedené kroky pro port 2049 a UDP pro NW1
         1. 2049 TCP pro NW2
            * Opakujte výše uvedené kroky pro port 2049 a TCP pro NW2
         1. 2049 UDP pro NW2
            * Opakujte výše uvedené kroky pro port 2049 a UDP pro NW2

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte pro tento server NFS základní cluster Pacemaker.

### <a name="configure-nfs-server"></a>Konfigurace serveru NFS

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** povolit server NFS

   Vytvoření kořenové položky exportu systému souborů NFS

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** instalace součástí DRBD

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** vytvoření oddílu pro zařízení DRBD

   Zobrazit seznam všech dostupných datových disků

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

1. **[A]** vytvoření konfigurací LVM

   Vypsat všechny dostupné oddíly

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Příklad výstupu
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Vytvořit svazky LVM pro každý oddíl

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** konfigurace DRBD

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Ujistěte se, že soubor DRBD. conf obsahuje následující dva řádky:

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Změna globální konfigurace DRBD

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Do obslužné rutiny a oddílu NET přidejte následující položky.

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

1. **[A]** vytvoření zařízení DRBD se systémem souborů NFS

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Vloží konfiguraci nového zařízení DRBD a ukončí se.

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

   Vloží konfiguraci nového zařízení DRBD a ukončí se.

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

   Vytvoření zařízení DRBD a jeho spuštění

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** přeskočit počáteční synchronizaci

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** nastavit primární uzel

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** počkejte, dokud nebudou nová zařízení DRBD synchronizovaná.

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** vytvoření systémů souborů na zařízeních DRBD

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

1. **[A]** nastavení DRBD rozdělení – detekce mozku

   Při použití DRBD k synchronizaci dat z jednoho hostitele do druhého může dojít k tomu, že se nazývá rozdělit mozek. Dělená mozek je scénář, kdy oba uzly clusteru povýší zařízení DRBD jako primární a zastaralá se o synchronizaci. Může se jednat o vzácnou situaci, ale přesto budete chtít zpracovávat a řešit co nejrychleji rozdělený mozek. Je proto důležité upozornit, když došlo k rozdělení mozku.

   Přečtěte si [oficiální dokumentaci k DRBD](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) , jak nastavit oznámení o rozdělení mozku.

   Také je možné automaticky obnovit ze scénáře rozdělení mozku. Další informace najdete v tématu věnovaném [automatickým rozdělením zásad obnovení pro obnovení](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration) .
   
### <a name="configure-cluster-framework"></a>Konfigurovat architekturu clusteru

1. **[1]** přidání zařízení DRBD NFS pro SAP System NW1 do konfigurace clusteru

   > [!IMPORTANT]
   > Nedávné testování odhalilo situace, kde NetCat přestane reagovat na požadavky z důvodu nevyřízených položek a omezení zpracování pouze jednoho připojení. Prostředek NetCat přestane naslouchat požadavkům nástroje pro vyrovnávání zatížení Azure a plovoucí IP adresa přestane být k dispozici.  
   > Pro existující clustery Pacemaker doporučujeme v minulosti nahradit NetCat pomocí Socat. V současné době doporučujeme použít agenta prostředků Azure-, který je součástí prostředků balíčku – agenti s následujícími požadavky na verzi balíčku:
   > - Pro SLES 12 SP4/SP5 musí být ve verzi aspoň Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 musí být verze aspoň Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Všimněte si, že tato změna bude vyžadovat krátké výpadky.  
   > U existujících clusterů Pacemaker se v případě, že konfigurace již změnila tak, aby používala socat, jak je popsáno v tématu [posílení zabezpečení azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), neexistuje žádný požadavek na přepnutí přímo do agenta prostředků Azure-No.

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
   
   sudo crm configure primitive nc_<b>NW1</b>_nfs azure-lb port=<b>61000</b>
   
   sudo crm configure group g-<b>NW1</b>_nfs \
     fs_<b>NW1</b>_sapmnt exportfs_<b>NW1</b> nc_<b>NW1</b>_nfs vip_<b>NW1</b>_nfs
   
   sudo crm configure order o-<b>NW1</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW1</b>_nfs:promote g-<b>NW1</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW1</b>_nfs_on_drbd inf: \
     g-<b>NW1</b>_nfs ms-drbd_<b>NW1</b>_nfs:Master
   </code></pre>

1. **[1]** přidání zařízení DRBD NFS pro SAP System NW2 do konfigurace clusteru

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
     options="rw,no_root_squash,crossmnt" clientspec="*" fsid=2 wait_for_leasetime_on_stop=true op monitor interval="30s"
   
   sudo crm configure primitive vip_<b>NW2</b>_nfs \
     IPaddr2 \
     params ip=<b>10.0.0.5</b> cidr_netmask=<b>24</b> op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW2</b>_nfs azure-lb port=<b>61001</b>
   
   sudo crm configure group g-<b>NW2</b>_nfs \
     fs_<b>NW2</b>_sapmnt exportfs_<b>NW2</b> nc_<b>NW2</b>_nfs vip_<b>NW2</b>_nfs
   
   sudo crm configure order o-<b>NW2</b>_drbd_before_nfs inf: \
     ms-drbd_<b>NW2</b>_nfs:promote g-<b>NW2</b>_nfs:start
   
   sudo crm configure colocation col-<b>NW2</b>_nfs_on_drbd inf: \
     g-<b>NW2</b>_nfs ms-drbd_<b>NW2</b>_nfs:Master
   </code></pre>

   `crossmnt`Možnost v `exportfs` prostředcích clusteru je k dispozici v naší dokumentaci kvůli zpětné kompatibilitě se staršími verzemi SLES.  

1. **[1]** zakázat režim údržby
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Instalace SAP ASCS a databáze](high-availability-guide-suse.md)
* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
