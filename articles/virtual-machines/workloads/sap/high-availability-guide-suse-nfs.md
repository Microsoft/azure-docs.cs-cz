---
title: Vysoká dostupnost pro nfs na virtuálních počítačích Azure na SLES | Dokumenty společnosti Microsoft
description: Vysoká dostupnost pro systém windows na virtuálních počítačích Azure na SUSE Linux Enterprise Server
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
ms.date: 03/26/2020
ms.author: radeltch
ms.openlocfilehash: 4dce0a675f5841591da00a322b72718964d382ac
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348875"
---
# <a name="high-availability-for-nfs-on-azure-vms-on-suse-linux-enterprise-server"></a>Vysoká dostupnost pro systém windows na virtuálních počítačích Azure na SUSE Linux Enterprise Server

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

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný server systému souborů NFS, který lze použít k ukládání sdílených dat vysoce dostupného systému SAP.
Tato příručka popisuje, jak nastavit vysoce dostupný server nfs, který je používán dvěma systémy SAP, NW1 a NW2. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste použili [šablonu souborového serveru SAP][template-file-server] s předponou **prostředků prod**.

Nejprve si přečtěte následující poznámky a dokumenty SAP

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2205917] doporučil nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [1944799] má SAP HANA pokyny pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1984787] má obecné informace o SUSE Linux Enterprise Server 12.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu (tento článek)][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [SUSE Linux Enterprise Rozšíření pro vysokou dostupnost 12 SP3 průvodce doporučenými postupy][sles-hae-guides]
  * Vysoce dostupné úložiště NFS s DRBD a kardiostimulátorem
* [Příručky osvědčených postupů SUSE Linux Enterprise Server pro SAP Aplikace 12 SP3][sles-for-sap-bp]
* [SUSE Rozšíření o vysokou dostupnost 12 SP3 Poznámky k verzi][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Přehled

Pro dosažení vysoké dostupnosti vyžaduje sap netweaver server nfs. Server systému systému zabezpečení systému zabezpečení je nakonfigurován v samostatném clusteru a může být používán více systémy SAP.

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-nfs/ha-suse-nfs.png)

Server systému souborů NFS používá vyhrazený virtuální název hostitele a virtuální IP adresy pro každý systém SAP, který používá tento server nfs. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení.        

* Front-endová konfigurace
  * IP adresa 10.0.0.4 pro NW1
  * IP adresa 10.0.0.5 pro NW2
* Back-endová konfigurace
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru systému nfs
* Port sondy
  * Port 61000 pro NW1
  * Port 61001 pro NW2
* Pravidla vyrovnávání zatížení (pokud používáte základní systém vyrovnávání zatížení)
  * 2049 TCP pro NW1
  * 2049 UDP pro NW1
  * 2049 TCP pro NW2
  * 2049 UDP pro NW2

## <a name="set-up-a-highly-available-nfs-server"></a>Nastavení vysoce dostupného serveru systému windows

Buď můžete použít šablonu Azure z GitHubu k nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, sady dostupnosti a nástroje pro vyrovnávání zatížení, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu přes šablonu Azure

Azure Marketplace obsahuje image pro SUSE Linux Enterprise Server pro SAP Aplikace 12, které můžete použít k nasazení nových virtuálních počítačů.
Můžete použít jednu ze šablon rychlého startu na GitHubu k nasazení všech požadovaných prostředků. Šablona nasazuje virtuální počítače, nástroj pro vyrovnávání zatížení, sadu dostupnosti atd. Chcete-li šablonu nasadit, postupujte takto:

1. Otevření [šablony souborového serveru SAP][template-file-server] na webu Azure Portal   
1. Zadejte následující parametry
   1. Předpona zdroje  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazeny.
   2. Počet systémů SAP  
      Zadejte počet systémů SAP, které budou používat tento souborový server. Tím se nasadí požadované množství front-endkonfigurace, pravidla vyrovnávání zatížení, sondy porty, disky atd.
   3. Typ operačního příkazu  
      Vyberte jednu z distribucí Linuxu. V tomto příkladu vyberte SLES 12
   4. Admin uživatelské jméno a admin heslo  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   5. ID podsítě  
      Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k čemuž by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako /subscriptions/**&lt;&gt;ID předplatného**/resourceGroups/**&lt;název&gt;skupiny prostředků**/providers/Microsoft.Network/virtualNetworks/**&lt;název&gt;virtuální sítě**/podsítě/**&lt;název&gt; podsítě**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu přes portál Azure

Nejprve je třeba vytvořit virtuální počítače pro tento cluster systému připojení k systému sítě. Poté vytvoříte nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondech back-end.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření sady dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1 Použijte alespoň SLES4SAP 12 SP3, v tomto příkladu sles4SAP 12 SP3 BYOS obraz SLES Pro SAP Aplikace 12 SP3 (BYOS) se používá  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Vytvořit virtuální počítač 2 Použijte alespoň SLES4SAP 12 SP3, v tomto příkladu SLES4SAP 12 SP3 BYOS image  
   SLES Pro SAP Aplikace 12 SP3 (BYOS) se používá  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Přidejte jeden datový disk pro každý systém SAP do obou virtuálních počítačů.
1. Vytvořte balancer zatížení (interní). Doporučujeme [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview).  
   1. Podle těchto pokynů vytvořte standardní vytáčíč zatížení:
      1. Vytvoření front-endových IP adres
         1. IP adresa 10.0.0.4 pro NW1
            1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
            1. Zadejte název nového fondu ip adres front-endu (například **nw1-frontend)**
            1. Nastavte přiřazení na statickou a zadejte adresu IP (například **10.0.0.4**)
            1. Klikněte na OK.
         1. IP adresa 10.0.0.5 pro NW2
            * Opakujte výše uvedené kroky pro NW2
      1. Vytvoření back-endových fondů
         1. Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru systému nfs
            1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
            1. Zadejte název nového back-endového fondu (například **nw-backend)**
            1. Vybrat virtuální síť
            1. Klikněte na Přidat virtuální počítač.
            1. Vyberte virtuální počítače clusteru systému eFS a jejich IP adresy.
            1. Klikněte na Přidat.
      1. Vytvoření sond stavu
         1. Port 61000 pro NW1
            1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
            1. Zadejte název nové zdravotní sondy (například **nw1-hp)**
            1. Vyberte protokol TCP, port 610**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
            1. Klikněte na OK.
         1. Port 61001 pro NW2
            * Opakováním výše uvedených kroků vytvořte sondu stavu pro NW2.
      1. Pravidla vyrovnávání zatížení
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **nw1-lb**)
         1. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **nw1-frontend**. **nw-backend** a **nw1-hp**)
         1. Vyberte **porty HA**.
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
         * Opakováním výše uvedených kroků vytvořte pravidlo vyrovnávání zatížení pro NW2.
   1. Případně pokud váš scénář vyžaduje základní vykladač zatížení, postupujte podle následujících pokynů:
      1. Vytvoření front-endových IP adres
         1. IP adresa 10.0.0.4 pro NW1
            1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
            1. Zadejte název nového fondu ip adres front-endu (například **nw1-frontend)**
            1. Nastavte přiřazení na statickou a zadejte adresu IP (například **10.0.0.4**)
            1. Klikněte na OK.
         1. IP adresa 10.0.0.5 pro NW2
            * Opakujte výše uvedené kroky pro NW2
      1. Vytvoření back-endových fondů
         1. Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru systému nfs
            1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
            1. Zadejte název nového back-endového fondu (například **nw-backend)**
            1. Klikněte na Přidat virtuální počítač.
            1. Vyberte dříve vytvořenou sadu dostupností.
            1. Výběr virtuálních počítačů clusteru nfs
            1. Klikněte na OK.
      1. Vytvoření sond stavu
         1. Port 61000 pro NW1
            1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
            1. Zadejte název nové zdravotní sondy (například **nw1-hp)**
            1. Vyberte protokol TCP, port 610**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
            1. Klikněte na OK.
         1. Port 61001 pro NW2
            * Opakováním výše uvedených kroků vytvořte sondu stavu pro NW2.
      1. Pravidla vyrovnávání zatížení
         1. 2049 TCP pro NW1
            1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
            1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **nw1-lb-2049**)
            1. Vyberte adresu IP front-endu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **nw1-frontend)**
            1. Zachovat protokol **TCP**, zadejte port **2049**
            1. Zvýšení časového limitu nečinnosti na 30 minut
            1. **Ujistěte se, že povolit plovoucí IP**
            1. Klikněte na OK.
         1. 2049 UDP pro NW1
            * Opakujte výše uvedené kroky pro port 2049 a UDP pro NW1
         1. 2049 TCP pro NW2
            * Opakujte výše uvedené kroky pro port 2049 a TCP pro NW2
         1. 2049 UDP pro NW2
            * Opakujte výše uvedené kroky pro port 2049 a UDP pro NW2

> [!Note]
> Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů. Podrobnosti o tom, jak dosáhnout odchozí připojení viz [veřejné připojení koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích sap vysoké dostupnosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Nástroj pro vyrovnávání zatížení Azure. Povolení časových razítek TCP způsobí selhání sond y stavu. Nastavte parametr **net.ipv4.tcp_timestamps** na **0**. Podrobnosti viz [Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Vytvořit cluster kardiostimulátoru

Postupujte podle kroků v [části Nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte základní cluster Pacemakerpro tento server nfs.

### <a name="configure-nfs-server"></a>Konfigurace serveru nfs

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí
   
   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   <b>10.0.0.5 nw2-nfs</b>
   </code></pre>

1. **[A]** Povolit server nfs

   Vytvoření kořenové položky exportu nfs

   <pre><code>sudo sh -c 'echo /srv/nfs/ *\(rw,no_root_squash,fsid=0\)>/etc/exports'
   
   sudo mkdir /srv/nfs/
   </code></pre>

1. **[A]** Instalace součástí drbd

   <pre><code>sudo zypper install drbd drbd-kmp-default drbd-utils
   </code></pre>

1. **[A]** Vytvoření oddílu pro zařízení drbd

   Vypsat všechny dostupné datové disky

   <pre><code>sudo ls /dev/disk/azure/scsi1/
   </code></pre>

   Příklad výstupu
   
   ```
   lun0  lun1
   ```

   Vytvoření oddílů pro každý datový disk

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun1'
   </code></pre>

1. **[A]** Vytvoření konfigurací LVM

   Seznam všech dostupných oddílů

   <pre><code>ls /dev/disk/azure/scsi1/lun*-part*
   </code></pre>

   Příklad výstupu
   
   ```
   /dev/disk/azure/scsi1/lun0-part1  /dev/disk/azure/scsi1/lun1-part1
   ```

   Vytvoření svazků LVM pro každý oddíl

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0-part1  
   sudo vgcreate vg-<b>NW1</b>-NFS /dev/disk/azure/scsi1/lun0-part1
   sudo lvcreate -l 100%FREE -n <b>NW1</b> vg-<b>NW1</b>-NFS

   sudo pvcreate /dev/disk/azure/scsi1/lun1-part1
   sudo vgcreate vg-<b>NW2</b>-NFS /dev/disk/azure/scsi1/lun1-part1
   sudo lvcreate -l 100%FREE -n <b>NW2</b> vg-<b>NW2</b>-NFS
   </code></pre>

1. **[A]** Konfigurace drbd

   <pre><code>sudo vi /etc/drbd.conf
   </code></pre>

   Ujistěte se, že soubor drbd.conf obsahuje následující dva řádky

   <pre><code>include "drbd.d/global_common.conf";
   include "drbd.d/*.res";
   </code></pre>

   Změna globální konfigurace drbd

   <pre><code>sudo vi /etc/drbd.d/global_common.conf
   </code></pre>

   Přidejte následující položky do obslužné rutiny a části sítě.

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

1. **[A]** Vytvoření zařízení NFS drbd

   <pre><code>sudo vi /etc/drbd.d/<b>NW1</b>-nfs.res
   </code></pre>

   Vložte konfiguraci nového zařízení drbd a ukončete

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

   Vložte konfiguraci nového zařízení drbd a ukončete

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

   Vytvořte zařízení drbd a spusťte jej

   <pre><code>sudo drbdadm create-md <b>NW1</b>-nfs
   sudo drbdadm create-md <b>NW2</b>-nfs
   sudo drbdadm up <b>NW1</b>-nfs
   sudo drbdadm up <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Přeskočit počáteční synchronizaci

   <pre><code>sudo drbdadm new-current-uuid --clear-bitmap <b>NW1</b>-nfs
   sudo drbdadm new-current-uuid --clear-bitmap <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Nastavení primárního uzlu

   <pre><code>sudo drbdadm primary --force <b>NW1</b>-nfs
   sudo drbdadm primary --force <b>NW2</b>-nfs
   </code></pre>

1. **[1]** Počkejte, až budou nová zařízení drbd synchronizována

   <pre><code>sudo drbdsetup wait-sync-resource NW1-nfs
   sudo drbdsetup wait-sync-resource NW2-nfs
   </code></pre>

1. **[1]** Vytvoření souborových systémů na zařízeních drbd

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

1. **[A]** Nastavení drbd split-detekce mozku

   Při použití drbd k synchronizaci dat z jednoho hostitele do druhého, může dojít k takzvanému rozděleného mozku. Rozdělený mozek je scénář, kde oba uzly clusteru propagovaly zařízení drbd jako primární a byly synchronizovány. Může to být vzácná situace, ale stále chcete zvládnout a vyřešit rozdělený mozek co nejrychleji. Je proto důležité být upozorněni, když došlo k rozdělení mozku.

   Přečtěte si [oficiální dokumentaci drbd](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-split-brain-notification) o tom, jak nastavit oznámení o rozdělení mozku.

   Je také možné automaticky zotavit ze scénáře rozděleného mozku. Další informace naleznete v části [Automatické zásady obnovení mozku](https://www.linbit.com/drbd-user-guide/users-guide-drbd-8-4/#s-automatic-split-brain-recovery-configuration)
   
### <a name="configure-cluster-framework"></a>Konfigurace architektury clusteru

1. **[1]** Přidání zařízení NFS drbd pro systém SAP NW1 do konfigurace clusteru

   > [!IMPORTANT]
   > Nedávné testování odhalilo situace, kdy netcat přestane reagovat na požadavky z důvodu nevyřízených položek a jeho omezení zpracování pouze jednoho připojení. Prostředek netcat přestane naslouchat požadavkům azure balancer a plovoucí IP přestane být k dispozici.  
   > Pro stávající pacemaker clustery, doporučujeme v minulosti nahradit netcat socat. V současné době doporučujeme používat agenta prostředků azure-lb, který je součástí agenty prostředků balíčku, s následujícími požadavky na verzi balíčku:
   > - Pro SLES 12 SP4/SP5 verze musí být alespoň agenty prostředků-4.3.018.a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 verze musí být alespoň agenty prostředků-4.3.0184.6ee15eb2-4.13.1.  
   >
   > Všimněte si, že změna bude vyžadovat krátké prostoje.  
   > Pro existující clustery Pacemaker, pokud konfigurace již byla změněna na použití socat, jak je popsáno v [Azure Load Balancer detekce posílení ,](https://www.suse.com/support/kb/doc/?id=7024128)není nutné okamžitě přepnout na agenta prostředků azure-lb.

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

1. **[1]** Přidání zařízení NFS drbd pro systém SAP NW2 do konfigurace clusteru

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

   Možnost `crossmnt` v `exportfs` prostředcích clusteru je k dispozici v naší dokumentaci pro zpětnou kompatibilitu se staršími verzemi SLES.  

1. **[1]** Zakázání režimu údržby
   
   <pre><code>sudo crm configure property maintenance-mode=false
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Instalace SAP ASCS a databáze](high-availability-guide-suse.md)
* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
