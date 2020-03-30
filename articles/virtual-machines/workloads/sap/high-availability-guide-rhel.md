---
title: Vysoká dostupnost virtuálních počítačích Azure pro SAP NW v RHEL | Dokumenty společnosti Microsoft
description: Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux
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
ms.openlocfilehash: 73b958149d9d6d907785fe1c2c56b8198bb91f70
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351093"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7.50.
V ukázkových konfiguracích, instalačních příkazech atd. Používá se instance ASCS číslo 00, číslo ers instance 02 a SAP System ID NW1. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste k vytvoření prostředků použili [šablonu ASCS/SCS][template-multisid-xscs] s předponou prostředků NW1.

Nejprve si přečtěte následující poznámky a dokumenty SAP

* SAP Poznámka [1928533], která má:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2002167] doporučil nastavení operačního systému pro Red Hat Enterprise Linux
* SAP Note [2009879] má SAP HANA pokyny pro Red Hat Enterprise Linux
* SAP Note [2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498] má požadovanou verzi SAP Host Agent pro Linux v Azure.
* SAP Note [2243692] má informace o licencování SAP na Linuxu v Azure.
* SAP Note [1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované SAP Notes pro Linux.
* [Plánování a implementace virtuálních počítačů Azure pro SAP na Linuxu][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP na Linuxu][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP na Linuxu][dbms-guide]
* [Dokumentace k produktu pro red hat gluster skladování](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP Netweaver v clusteru kardiostimulátorů](https://access.redhat.com/articles/3150081)
* Obecná dokumentace RHEL
  * [Přehled doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa doplňků s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Odkaz na doplněk s vysokou dostupností](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/ERS pro SAP Netweaver se samostatnými prostředky v RHEL 7.5](https://access.redhat.com/articles/3569681)
  * [Konfigurace SAP S/4HANA ASCS/ERS pomocí samostatného enqueue serveru 2 (ENSA2) v kardiostimulátoru na RHEL](https://access.redhat.com/articles/3974941)
* Dokumentace RHEL specifické pro Azure:
  * [Zásady podpory pro clustery s vysokou dostupností RHEL – virtuální počítače Microsoft Azure jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace vysoce dostupnosti red hatového linuxového linuxu 7.4 (a novějšího) v Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Přehled

Pro dosažení vysoké dostupnosti vyžaduje SAP NetWeaver sdílené úložiště. GlusterFS je konfigurován v samostatném clusteru a může být používán více systémy SAP.

![Přehled vysoké dostupnosti sap netweaveru](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure je nástroj pro vyrovnávání zatížení nutný k použití virtuální IP adresy. Doporučujeme používat [standardní vyvažovač zatížení](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal). V následujícím seznamu je uvedena konfigurace nástroje pro vyrovnávání zatížení (A)SCS a ERS.

### <a name="ascs"></a>(A) Scs

* Front-endová konfigurace
  * IP adresa 10.0.0.7
* Port sondy
  * Port 620<strong>&lt;nr&gt;</strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * Pokud používáte základní vytápěč zatížení, vytvořte pravidla vyrovnávání zatížení pro následující porty
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 36<strong>&lt;&gt; nr</strong> TCP
    * 39<strong>&lt;&gt; nr</strong> TCP
    * 81<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

### <a name="ers"></a>Ers

* Front-endová konfigurace
  * IP adresa 10.0.0.8
* Port sondy
  * Port 621<strong>&lt;nr&gt;</strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte standardní vykladač zatížení, vyberte **porty HA**
  * Pokud používáte základní vytápěč zatížení, vytvořte pravidla vyrovnávání zatížení pro následující porty
    * 32<strong>&lt;&gt; nr</strong> TCP
    * 33<strong>&lt;&gt; nr</strong> TCP
    * 5<strong>&lt;&gt;nr</strong>13 TCP
    * 5<strong>&lt;&gt;nr</strong>14 TCP
    * 5<strong>&lt;&gt;nr</strong>16 TCP

* Back-endová konfigurace
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A)SCS/ERS

## <a name="setting-up-glusterfs"></a>Nastavení GlusterFS

SAP NetWeaver vyžaduje sdílené úložiště pro adresář přenosu a profilu. Přečtěte si [glusterfs na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver][glusterfs-ha] o tom, jak nastavit GlusterFS pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A)SCS

Buď můžete použít šablonu Azure z GitHubu k nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, sady dostupnosti a nástroje pro vyrovnávání zatížení, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu přes šablonu Azure

Azure Marketplace obsahuje image pro Red Hat Enterprise Linux, kterou můžete použít k nasazení nových virtuálních počítačů. Můžete použít jednu ze šablon rychlého startu na GitHubu k nasazení všech požadovaných prostředků. Šablona nasazuje virtuální počítače, nástroj pro vyrovnávání zatížení, sadu dostupnosti atd. Chcete-li šablonu nasadit, postupujte takto:

1. Otevření [šablony ASCS/SCS][template-multisid-xscs] na webu Azure Portal  
1. Zadejte následující parametry
   1. Předpona zdroje  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazeny.
   1. Typ zásobníku  
      Výběr typu zásobníku SAP NetWeaver
   1. Typ operačního příkazu  
      Vyberte jednu z distribucí Linuxu. V tomto příkladu vyberte RHEL 7
   1. Typ db  
      Vybrat HANA
   1. Počet systému Sap  
      Počet systému SAP, které běží v tomto clusteru. Vyberte 1.
   1. Dostupnost systému  
      Vybrat HA
   1. Uživatelské jméno správce, heslo správce nebo klíč SSH  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   1. ID podsítě  
   Pokud chcete nasadit virtuální ho do existující virtuální sítě, kde máte podsíť definovanou, k čemuž by měl být virtuální hotel přiřazen, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako /subscriptions/**&lt;&gt;ID předplatného**/resourceGroups/**&lt;název&gt;skupiny prostředků**/providers/Microsoft.Network/virtualNetworks/**&lt;název&gt;virtuální sítě**/podsítě/**&lt;název&gt; podsítě**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu přes portál Azure

Nejprve je třeba vytvořit virtuální počítače pro tento cluster. Poté vytvoříte nástroj pro vyrovnávání zatížení a použijete virtuální počítače v back-endovém fondu.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření sady dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1  
   Použijte alespoň RHEL 7, v tomto příkladu Red Hat Enterprise Linux 7.4 image<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Vytvořit virtuální počítač 2  
   Použijte alespoň RHEL 7, v tomto příkladu Red Hat Enterprise Linux 7.4 image<https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Vybrat dříve vytvořenou sadu dostupnosti.  
1. Přidání alespoň jednoho datového disku do obou virtuálních počítačů  
   Datové disky se používají pro adresář /usr/sap/`<SAPSID`>
1. Vytvoření systému vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření front-endových IP adres
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
         1. Zadejte název nového fondu ip adres front-endu (například **nw1-ascs-frontend)**
         1. Nastavte přiřazení na statickou a zadejte adresu IP (například **10.0.0.7**)
         1. Klikněte na OK.
      1. IP adresa 10.0.0.8 pro ASCS ERS
         * Chcete-li vytvořit IP adresu pro ERS (například **10.0.0.8** a **nw1-aers-frontend),** opakujte výše uvedené kroky( například 10.0.0.8 a nw1-aers-frontend)
   1. Vytvoření back-endového fondu
      1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
      1. Zadejte název nového back-endového fondu (například **nw1-backend)**
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte Virtuální počítač.
      1. Vyberte virtuální počítače clusteru (A)SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
         1. Zadejte název nové sondy stavu (například **nw1-ascs-hp)**
         1. Vyberte protokol TCP, port 620**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
         1. Klikněte na OK.
      1. Port 621**02** pro ASCS ERS
         * Opakujte výše uvedené kroky a vytvořte sondu stavu pro ERS (například 621**02** a **nw1-aers-hp)**
   1. Pravidla vyrovnávání zatížení
      1. Pravidla vyrovnávání zatížení pro ASCS
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **nw1-lb-ascs)**
         1. Vyberte front-endovou IP adresu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **nw1-ascs-frontend**, **nw1-backend** a **nw1-ascs-hp)**
         1. Vybrat **porty HA**
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
         * Opakováním výše uvedených kroků vytvořte pravidla vyrovnávání zatížení pro ERS (například **nw1-lb-ers)**
1. Případně pokud váš scénář vyžaduje základní vyrovnávání zatížení (interní), postupujte takto:  
   1. Vytvoření front-endových IP adres
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete balancer, vyberte front-endový fond IP adres a klikněte na Přidat
         1. Zadejte název nového fondu ip adres front-endu (například **nw1-ascs-frontend)**
         1. Nastavte přiřazení na statickou a zadejte adresu IP (například **10.0.0.7**)
         1. Klikněte na OK.
      1. IP adresa 10.0.0.8 pro ASCS ERS
         * Chcete-li vytvořit IP adresu pro ERS (například **10.0.0.8** a **nw1-aers-frontend),** opakujte výše uvedené kroky( například 10.0.0.8 a nw1-aers-frontend)
   1. Vytvoření back-endového fondu
      1. Otevřete balancer, vyberte fondy back-endu a klikněte na Přidat
      1. Zadejte název nového back-endového fondu (například **nw1-backend)**
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte dříve vytvořenou sadu dostupností.
      1. Výběr virtuálních počítačů clusteru (A)SCS
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete systém vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat
         1. Zadejte název nové sondy stavu (například **nw1-ascs-hp)**
         1. Vyberte protokol TCP, port 620**00**, zachovat prahovou hodnotu Interval 5 a Není v pořádku 2.
         1. Klikněte na OK.
      1. Port 621**02** pro ASCS ERS
         * Opakujte výše uvedené kroky a vytvořte sondu stavu pro ERS (například 621**02** a **nw1-aers-hp)**
   1. Pravidla vyrovnávání zatížení
      1. 32**00** TCP pro ASCS
         1. Otevřete správce zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **nw1-lb-3200**)
         1. Vyberte front-endovou IP adresu, back-endový fond a sondu stavu, kterou jste vytvořili dříve (například **nw1-ascs-frontend)**
         1. Zachovat protokol **TCP**, zadat port **3200**
         1. Zvýšení časového limitu nečinnosti na 30 minut
         1. **Ujistěte se, že povolit plovoucí IP**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASCS
      1. Další porty pro ASCS ERS
         * Opakujte výše uvedené kroky pro porty 33**02**, 5**02**13, 5**02**14, 5**02**16 a TCP pro ASCS ERS

> [!Note]
> Když virtuální počítače bez veřejných IP adres jsou umístěny v back-endovém fondu interní (žádná veřejná IP adresa) standardní azure vyrovnávání zatížení, bude existovat žádné odchozí připojení k internetu, pokud se provádí další konfigurace umožňující směrování do veřejných koncových bodů. Podrobnosti o tom, jak dosáhnout odchozí připojení viz [veřejné připojení koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích sap vysoké dostupnosti](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections).  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Nástroj pro vyrovnávání zatížení Azure. Povolení časových razítek TCP způsobí selhání sond y stavu. Nastavte parametr **net.ipv4.tcp_timestamps** na **0**. Podrobnosti viz [Sondy stavu vykladače zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Vytvořit cluster kardiostimulátoru

Postupujte podle kroků v [části Nastavení kardiostimulátoru na Red Hat Enterprise Linux u Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte pro tento (A)SCS server základní cluster kardiostimulátoru.

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci sap netweaveru

Následující položky jsou předponou **buď [A]** - použitelné pro všechny uzly, **[1]** - platí pouze pro uzel 1 nebo **[2]** - platí pouze pro uzel 2.

1. **[A]** Překlad názvů hostitele nastavení

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** Vytvoření sdílených adresářů

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** Instalace klienta GlusterFS a další požadavky

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A]** Zkontrolujte verzi resource-agents-sap

   Ujistěte se, že verze nainstalovaného balíčku resource-agents-sap je alespoň 3.9.5-124.el7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** Přidání položek připojení

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Připojení nových sdílených složek

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** Konfigurace souboru SWAP

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Chcete-li změnu aktivovat, restartujte agenta.

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** Konfigurace RHEL

   Konfigurace rhel, jak je popsáno v poznámce SAP [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/ERS

1. **[1]** Vytvoření virtuálního prostředku IP a sondy stavu pro instanci ASCS

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** Instalace systému ASCS aplikace SAP NetWeaver  

   Nainstalujte SAP NetWeaver ASCS jako root na první uzel pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro ASCS, například <b>nw1-ascs</b>, <b>10.0.0.7</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**NW1**/ASCS**00**, zkuste nastavit vlastníka a skupinu složky ASCS**00** a opakujte akci.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Vytvoření virtuálního IP prostředku a sondy stavu pro instanci ERS

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** Instalace SAP NetWeaver ERS  

   Nainstalujte SAP NetWeaver ERS jako root na druhý uzel pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endové konfigurace nástroje pro vyrovnávání zatížení pro ERS, například <b>nw1-aers</b>, <b>10.0.0.8</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>02</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalaci nepodaří vytvořit podsložku v /usr/sap/**NW1**/ERS**02**, zkuste nastavit vlastníka a skupinu složky ERS**02** a opakujte akci.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** Přizpůsobení profilů instancí ASCS/SCS a ERS

   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * PROFIL ERS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** Konfigurace keep alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím softwarového nástroje pro vyrovnávání zatížení. Systém vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém uzláčase. Chcete-li tomu zabránit, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS a změnit nastavení systému Linux. Další informace [načtete v poznámce SAP 1410736.][1410736]

   Parametr profilu ASCS/SCS enque/encni/set_so_keepalive byl již přidán v posledním kroku.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]** Aktualizace souboru /usr/sap/sapservices

   Chcete-li zabránit spuštění instancí skriptem sapinit spuštění, všechny instance spravované Pacemaker musí být komentoval ze souboru /usr/sap/sapservices. Nezakomentujte instanci SAP HANA, pokud bude použita s HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** Vytvoření prostředků clusteru SAP

  Pokud používáte architekturu enqueue server 1 (ENSA1), definujte prostředky takto:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP zavedl podporu pro enqueue server 2, včetně replikace, od SAP NW 7.52. Počínaje platformou ABAP 1809 je ve výchozím nastavení nainstalován server 2 fronty. Viz SAP poznámka [2630416](https://launchpad.support.sap.com/#/notes/2630416) pro zařazení do fronty server 2 podporu.
   Pokud používáte architekturu enqueue server 2[(ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), nainstalujte agenta prostředků prostředku-agent-sap-4.1.1-12.el7.x86_64 nebo novější a definujte prostředky takto:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Pokud upgradujete ze starší verze a přepájíte na server fronty 2, přečtěte si poznámku SAP [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Časové topoukázky ve výše uvedené konfiguraci jsou pouze příklady a může být nutné přizpůsobit konkrétní nastavení SAP. 

   Ujistěte se, že stav clusteru je v pořádku a že jsou spuštěny všechny prostředky. Není důležité, na kterém uzlu jsou spuštěny prostředky.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** Přidání pravidel brány firewall pro ASCS a ERS na obou uzlech

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver

Některé databáze vyžadují, aby byla instalace instance databáze spuštěna na aplikačním serveru. Připravte virtuální počítače aplikačního serveru, aby je bylo možné v těchto případech používat.

Níže uvedené kroky předpokládají, že nainstalujete aplikační server na server odlišný od serverů ASCS/SCS a HANA. V opačném případě nejsou některé z následujících kroků (například konfigurace překladu názvů hostitele) potřeba.

1. Nastavení překladu názvů hostitele

   Můžete použít server DNS nebo upravit /etc/hosts ve všech uzlech. Tento příklad ukazuje, jak používat soubor /etc/hosts.
   Nahrazení adresy IP a názvu hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do /etc/hosts. Změna IP adresy a názvu hostitele tak, aby odpovídaly vašemu prostředí

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. Vytvoření adresáře sapmnt

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Instalace klienta GlusterFS a dalších požadavků

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Přidání položek připojení

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   Připojení nových sdílených složek

   <pre><code>sudo mount -a
   </code></pre>

1. Konfigurace souboru SWAP
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Chcete-li změnu aktivovat, restartujte agenta.

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalován na SAP HANA. Pro tuto instalaci můžete použít všechny podporované databáze. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v [tématu Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Spuštění instalace instance databáze SAP

   Nainstalujte instanci databáze SAP NetWeaver jako root pomocí virtuálního názvu hostitele, který se mapuje na IP adresu front-endkonfigurace nástroje pro vyrovnávání zatížení pro databázi například <b>nw1-db</b> a <b>10.0.0.13</b>.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Chcete-li nainstalovat aplikační server SAP, postupujte takto.

1. Příprava aplikačního serveru

   Postupujte podle výše uvedených kroků v kapitole [Příprava aplikačního serveru SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) a připravte si aplikační server.

1. Instalace aplikačního serveru SAP NetWeaver

   Nainstalujte primární nebo další aplikační server SAP NetWeaver.

   Můžete použít sapinst parametr SAPINST_REMOTE_ACCESS_USER povolit nekořenového uživatele pro připojení k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizace zabezpečeného úložiště SAP HANA

   Aktualizujte zabezpečené úložiště SAP HANA tak, aby ukazovalo na virtuální název nastavení systémové replikace SAP HANA.

   Spuštěním následujícího příkazu \<zobrazíte seznam položek jako sapsid>adm

   <pre><code>hdbuserstore List
   </code></pre>

   To by mělo vypsat všechny položky a mělo by vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položky směřuje na virtuální počítač a ne na IP adresu nástroje pro vyrovnávání zatížení. Tuto položku je třeba změnit tak, aby přecóklála na virtuální název hostitele nástroje pro vyrovnávání zatížení. Ujistěte se, že používáte stejný port (**30313** ve výstupu výše) a název databáze (**HN1** ve výstupu výše)!

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Testování nastavení clusteru

1. Ruční migrace instance ASCS

   Stav prostředku před zahájením testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako root pro migraci instance ASCS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Simulovat selhání uzlu

   Stav prostředku před zahájením testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spuštění následujícího příkazu jako kořenového adresáře v uzlu, ve kterém je spuštěna instance ASCS

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   Stav po spuštění uzlu by měl vypadat takto.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   K vyčištění neúspěšných prostředků použijte následující příkaz.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Zastavte proces serveru zpráv

   Stav prostředku před zahájením testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako root k identifikaci procesu serveru zpráv a jeho usmrcení.

   <pre><code>[root@nw1-cl-0 ~]# pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Pokud server zpráv zabijete pouze jednou, bude `sapstart`restartován společností . Pokud jej zabijete dostatečně často, Pacemaker nakonec přesune instanci ASCS do jiného uzlu. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředků instance ASCS a ERS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Proces serveru zastavování zařazení do fronty

   Stav prostředku před zahájením testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkazy jako root v uzlu, kde je spuštěna instance ASCS, aby se zničil server fronty.

   <pre><code>[root@nw1-cl-1 ~]# pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Instance ASCS by měla okamžitě přepojit převzetí služeb při selhání do jiného uzlu. Instance ERS by také měla po spuštění instance ASCS převezměte služby při selhání. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředků instance ASCS a ERS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Proces serveru replikace zařazení do fronty

   Stav prostředku před zahájením testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkaz jako root v uzlu, kde je spuštěna instance ERS, abyste zničili proces replikačního serveru fronty.

   <pre><code>[root@nw1-cl-1 ~]# pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Pokud spustíte příkaz pouze `sapstart` jednou, restartuje proces. Pokud jej spustíte `sapstart` dostatečně často, proces nerestartujete a prostředek bude v zastaveném stavu. Spusťte následující příkazy jako root, abyste po testu vyčistili stav prostředku instance ERS.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav zdroje po zkoušce:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Zabít proces sapstartsrv do fronty

   Stav prostředku před zahájením testu:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako root v uzlu, kde je spuštěnascs.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Sapstartsrv proces by měl být vždy restartován agentem prostředků Pacemaker jako součást monitorování. Stav zdroje po zkoušce:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [Ha pro SAP NW na virtuálních počítačích Azure na RHEL pro sap aplikace multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid)
* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA v Azure (velké instance), najdete v [tématu SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
