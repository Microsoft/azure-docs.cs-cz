---
title: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na SUSE Linux Enterprise Server pro aplikace SAP | Dokumentace Microsoftu
description: Příručka pro vysokou dostupnost pro SAP NetWeaver na SUSE Linux Enterprise Server pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: sedusch
ms.openlocfilehash: 935b501964435e80172ef3e147f777bf47119b48
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285118"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP

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

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Tento článek popisuje, jak nasadit virtuální počítače, konfigurace virtuálních počítačů, instalaci rozhraní clusteru a instalace s vysokou dostupností systému SAP NetWeaver 7.50.
V ukázkové konfigurace příkazy instalace atd. Číslo instance ASCS 00 Lajících instance číslo 02 a používá se ID NW1 systému SAP. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu se předpokládá, že jste použili [konvergované šablony] [ template-converged] systému SAP ID NW1 vytvoříte prostředky.

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
* [SUSE SAP HA příručky s osvědčenými postupy] [ suse-ha-guide] příručky obsahuje všechny požadované informace k nastavení Netweaver HA a SAP HANA System Replication místní. Prosím použijte to provede jako obecné směrného plánu. Poskytují mnohem podrobnější informace.
* [Vysoce dostupné úložiště NFS DRBD a Pacemaker] [ suse-drbd-guide] Průvodce obsahuje všechny nezbytné informace k nastavení vysoce dostupný server pro systém souborů NFS. Tento průvodce použijte jako základ.


## <a name="overview"></a>Přehled

Abyste dosáhli vysoké dostupnosti, SAP NetWeaver vyžaduje serverem NFS. Server systému souborů NFS je nakonfigurované na jiném clusteru a mohou být využívána více systémů SAP.

![Přehled SAP NetWeaver vysoké dostupnosti](./media/high-availability-guide-suse/img_001.png)

Server systému souborů NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Lajících a databáze SAP HANA pomocí virtuální název hostitele a virtuální IP adresy. V Azure je potřeba nástroj pro vyrovnávání zatížení pomocí virtuální IP adresu. Následující seznam obsahuje konfiguraci (A) SCS a Lajících nástroj pro vyrovnávání zatížení.

### <a name="ascs"></a>(A)SCS

* Konfiguraci front-endu
  * IP adresa 10.0.0.7
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 620**&lt;nr&gt;**
* Pravidla Vyrovnávání zatížení
  * 32**&lt;nr&gt;**  TCP
  * 36**&lt;nr&gt;**  TCP
  * 39**&lt;nr&gt;**  TCP
  * 81**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>LAJÍCÍCH

* Konfiguraci front-endu
  * IP adresa 10.0.0.8
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 621**&lt;nr&gt;**
* Pravidla Vyrovnávání zatížení
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Nastavení vysoce dostupný server pro systém souborů NFS

SAP NetWeaver vyžaduje sdílené úložiště pro přenos a profil adresář. Čtení [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server] [ nfs-ha] o tom, jak nastavit serverem NFS pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

Můžete použít buď Azure šablony z githubu nasadit všechny požadované prostředky Azure, včetně virtual machines, dostupnosti, nastavte a nástroj pro vyrovnávání zatížení nebo ručně nasadit prostředky.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linuxu pomocí šablony Azure

Na webu Azure Marketplace obsahuje bitovou kopii operačního systému SUSE Linux Enterprise Server pro 12 aplikací SAP, který můžete použít k nasazení nových virtuálních počítačů. Marketplace image obsahuje agenta prostředků pro SAP NetWeaver.

Můžete některou ze šablon quickstart na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti, atd. Postupujte podle těchto kroků a nasaďte šablonu:

1. Otevřít [šablony ASCS/SCS více SID] [ template-multisid-xscs] nebo [konvergované šablony] [ template-converged] na portálu Azure portal, pouze vytvoří šablonu The ASCS/SCS pravidla pro vyrovnávání zatížení pro SAP NetWeaver ASCS/SCS a Lajících instance (pouze Linux) že sblížené Šablona také vytvoří pravidla Vyrovnávání zatížení pro databázi (třeba Microsoft SQL Server nebo SAP HANA). Pokud máte v plánu pro instalaci systému SAP NetWeaver na základě a také chtít nainstalovat databázi na stejných počítačů, použijte [konvergované šablony][template-converged].
1. Zadejte následující parametry
   1. Předpona prostředků (pouze šablona ASCS/SCS více SID)  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazené.
   3. ID systému SAP (pouze sblížené šablona)  
      Zadejte ID systému SAP systému SAP, ve kterém chcete nainstalovat. ID se používá jako předpona pro prostředky, které jsou nasazené.
   4. Typ zásobníku  
      Vyberte typ zásobníku SAP NetWeaver
   5. Typ operačního systému  
      Vyberte jednu z Linuxových distribucí. V tomto příkladu vyberte SLES 12 BYOS
   6. Typ databáze  
      Vyberte HANA
   7. Velikost systému SAP  
      Množství protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP bude systém vyžadovat, požádejte SAP technologické partnery nebo systémový integrátor
   8. Dostupnost systému  
      Vyberte HA
   9. Uživatelské jméno Admin a heslo správce  
      Je vytvořen nový uživatel, který lze použít k přihlášení k počítači.
   10. ID podsítě  
   ID podsítě, ke kterému musí být připojené k virtuálním počítačům.  Ponechte prázdné, pokud chcete vytvořit novou virtuální síť nebo vyberte stejné podsíti, který používá nebo vytvořen jako součást nasazení serveru systému souborů NFS. ID obvykle vypadá /subscriptions/**&lt;ID předplatného&gt;**/resourceGroups/**&lt;název skupiny prostředků&gt;**/poskytovatelé/ Microsoft.Network/virtualNetworks/**&lt;název virtuální sítě&gt;**/subnets/**&lt;název podsítě&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linuxu prostřednictvím webu Azure portal

Nejprve musíte vytvořit virtuální počítače pro tento cluster systému souborů NFS. Potom vytvořte nástroj pro vyrovnávání zatížení a použijte virtuální počítače v back-endové fondy.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti  
   Nastavení maximální aktualizační doména
1. Vytvoření virtuálního počítače 1   
   Použijte alespoň 12 SP1 SLES4SAP, v tomto příkladu SLES4SAP 12 SP1 obrazu https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES pro SAP aplikace 12 SP1 se používá.  
   Vyberte dříve vytvořenou skupinu dostupnosti  
1. Vytvoření virtuálního počítače 2   
   Použijte alespoň 12 SP1 SLES4SAP, v tomto příkladu SLES4SAP 12 SP1 obrazu https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES pro SAP aplikace 12 SP1 se používá.  
   Vyberte dříve vytvořenou skupinu dostupnosti  
1. Přidejte alespoň jeden datový disk pro oba virtuální počítače  
   Datové disky se používajípro/USR/sap/`<SAPSID`> adresáře
1. Vytvořte nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvoření front-endové IP adresy
      1. IP adresa 10.0.0.7 ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-endový fond IP adres a klikněte na tlačítko Přidat
         1. Zadejte název nové front-endový fond IP (například **nw1. ascs front-end**)
         1. Nastavení přiřazení do statické a zadejte IP adresu (například **10.0.0.7**)
         1. Klikněte na tlačítko OK
      1. IP adresa 10.0.0.8 ASCS Lajících
         * Zopakujte výše uvedené kroky a vytvořte IP adresu pro Lajících (například **10.0.0.8** a **nw1-aers-backend**)
   1. Vytvoření back-endové fondy
      1. Vytvoření back-endový fond pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového back-endového fondu (například **nw1-ascs-backend**)
         1. Klikněte na tlačítko Přidat virtuální počítač.
         1. Vyberte skupinu dostupnosti jste vytvořili dříve
         1. Vyberte virtuální počítače (A) SCS clusteru
         1. Klikněte na tlačítko OK
      1. Vytvoření back-endový fond pro ASCS Lajících
         * Zopakujte výše uvedené kroky a vytvořte back-endový fond pro Lajících (například **nw1-aers-backend**)
   1. Vytvoření sondy stavu
      1. Port 620**00** pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení vyberte testy stavu a klikněte na tlačítko Přidat
         1. Zadejte název nového stavu testu (například **nw1. ascs hp**)
         1. Jako protokol, port 620 vyberte TCP**00**, Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 621**02** pro ASCS Lajících
         * Zopakujte výše uvedené kroky a vytvořte sondu stavu pro Lajících (například 621**02** a **nw1. aers hp**)
   1. Pravidla Vyrovnávání zatížení
      1. 32**00** TCP pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, pravidel Vyrovnávání zatížení vyberte a klikněte na tlačítko Přidat
         1. Zadejte název nového pravidla služby load balancer (například **nw1-lb-3200**)
         1. Vyberte IP adresu front-endu, back-endového fondu a sondu stavu, které jste vytvořili dříve (například **nw1. ascs front-end**)
         1. Zachovat protokol **TCP**, zadejte port **3200**
         1. Zvyšte časový limit nečinnosti až 30 minut.
         1. **Ujistěte se, že chcete povolit plovoucí IP adresy**
         1. Klikněte na tlačítko OK    
      1. Další porty pro ASC
         * Opakujte předchozí kroky pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASC
      1. Další porty pro ASCS Lajících
         * Opakujte předchozí kroky pro porty 33**02**, 5**02**13, 5**02**14, 5**02**16 a TCP pro ASCS Lajících

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) pro vytvoření základní Pacemaker clusteru pro tento server (A) SCS.

### <a name="installation"></a>Instalace

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nainstalovat konektor SUSE
   
   <pre><code>
   sudo zypper install sap-suse-cluster-connector
   </code></pre>

   Ujistěte se, že jste nainstalovali novou verzi konektoru SAP SUSE clusteru. Starý byla volána sap_suse_cluster_connector a novým nazývá **sap suse clusteru konektor**.

   <pre><code>
   sudo zypper info sap-suse-cluster-connector
   
   Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   <b>Version        : 3.0.0-2.2</b>
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 41.6 KiB
   <b>Installed      : Yes</b>
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.0.0-2.2.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

1. **[A]**  Agentů SAP aktualizace prostředku  
   
   Oprava pro balíček prostředků agenty je potřeba použít novou konfiguraci, která je popsána v tomto článku. Můžete zkontrolovat, pokud oprava je již nainstalována pomocí následujícího příkazu

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Výstup by měl vypadat přibližně

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Pokud příkazu grep nenajde IS_ERS parametr, musíte nainstalovat opravu na [stránce pro stažení SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele   

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   <pre><code>
   sudo vi /etc/hosts
   </code></pre>
   
   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí   
   
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Příprava instalace SAP NetWeaver

1. **[A]**  Vytvořit sdílené složky

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
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

1. **[A]**  Konfigurace autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Vytvořte soubor s

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   /usr/sap/<b>NW1</b>/ASCS<b>00</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCS
   /usr/sap/<b>NW1</b>/ERS<b>02</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS
   </code></pre>

   Restartujte autofs připojit nové sdílené složky

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]**  Nakonfigurovat ODKLÁDACÍHO souboru

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

   Restartujte agenta k aktivaci změny

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/Lajících

1. **[1]**  Vytvoření virtuální IP prostředku a sondu stavu pro instance ASC

   <pre><code>
   sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

   <pre><code>
   sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]**  Instalace SAP NetWeaver ASCS  

   Instalace SAP NetWeaver ASCS jako uživatel root na prvním uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS, například <b>nw1 ascs</b>, <b>10.0.0.7</b> a instance číslo, které jste použili pro test paměti nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalace nezdaří vytvořit podsložku v umístění/USR/sap/**NW1**/ASCS**00**, zkuste nastavit vlastníka a skupiny ASCS**00** složky a zkuste to znovu.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Vytvoření virtuální IP prostředku a sondu stavu pro instance Lajících

   <pre><code>
   sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>02</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_NW1_ASCS,nc_NW1_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>
 
   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

   <pre><code>
   sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]**  Instalace SAP NetWeaver Lajících  

   Instalace SAP NetWeaver Lajících jako uživatel root na druhém uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro Lajících, například <b>nw1 aers</b>, <b>10.0.0.8</b> a instance číslo, které jste použili pro test paměti nástroje pro vyrovnávání zatížení, například <b>02</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastavujte oprávnění správně a instalace se nezdaří.

   Pokud se instalace nezdaří vytvořit podsložku v umístění/USR/sap/**NW1**/ERS**02**, zkuste nastavit vlastníka a skupiny Lajících**02** složky a zkuste to znovu.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adapt ASCS/SCS a Lajících instance profily
 
   * Profil ASCS/SCS

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * Profil Lajících

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Konfigurace zachování

   Komunikace mezi aplikační server SAP NetWeaver a ASCS/SCS je směrován přes nástroj pro vyrovnávání zatížení softwaru. Nástroje pro vyrovnávání zatížení odpojí neaktivní připojení po Konfigurovatelný časový limit. Chcete-li tomu zabránit, budete muset nastavit parametr v SAP NetWeaver ASCS/SCS profilu a změnit nastavení systému Linux. Čtení [1410736 Poznámka SAP] [ 1410736] Další informace.
   
   ASCS/SCS profilu parametr modul/encni/set_so_keepalive byl již přidán v posledním kroku.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Po instalaci nastavit uživatelů SAPU
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  Přidejte do souboru sapservice services ASCS a SAP Lajících

   Přidáte ASCS služby položku na druhém uzlu a zkopírovat položku služby Lajících do prvního uzlu.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]**  Vytvořit prostředky clusteru SAP

   <pre><code>
   sudo crm configure property maintenance-mode="true"   
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure location loc_sap_<b>NW1</b>_failover_to_ers rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>NW1</b> eq 1
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

   <pre><code>
   sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   # rsc_st_azure    (stonith:fence_azure_arm):      <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      nc_NW1_ASCS        (ocf::heartbeat:anything):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      nc_NW1_ERS (ocf::heartbeat:anything):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver aplikace server přípravy

Některé databáze vyžadovat, aby instalaci instance databáze spuštěné na aplikační server. Příprava virtuálních počítačů pro server aplikace bude moct používat v těchto případech.

Kroky zmeškáte Předpokládejme, že nainstalujete aplikační server na serveru se liší od ASC/SCS a HANA servery. V opačném případě nejsou potřeba některé z kroků (např. konfigurace rozlišení názvu hostitele).

1. Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech
   ```bash
   sudo vi /etc/hosts
   ```
   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí    
    
   <pre><code>
   # IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   # IP address of all application servers
   <b>10.0.0.20 nw1-di-0</b>
   <b>10.0.0.21 nw1-di-1</b>
   </code></pre>

1. Vytvořit adresář sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurace autofs
 
   <pre><code>
   sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Vytvořte nový soubor s

   <pre><code>
   sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Restartujte autofs připojit nové sdílené složky

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Konfigurace STRÁNKOVACÍHO souboru
 
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

   Restartujte agenta k aktivaci změny

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je nainstalován SAP NetWeaver na systému SAP HANA. Všechny podporované databáze můžete použít pro tuto instalaci. Další informace o tom, jak instalace SAP HANA v Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]. Seznam podporovaných databází najdete v tématu [SAP 1928533 Poznámka][1928533].

1. Spuštění instalace instancí databáze SAP

   Nainstalujte instanci databáze SAP NetWeaver jako kořen virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro databázi například pomocí <b>nw1 db</b> a <b>10.0.0.13</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace serveru aplikace SAP NetWeaver

Postupujte podle těchto kroků nainstalujte aplikační server SAP. 

1. Příprava aplikace serveru

Postupujte podle kroků v kapitole [Příprava serveru aplikace SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) výše a připravte je aplikační server.

1. Instalace aplikační server SAP NetWeaver

   Instalace serveru primární nebo dalších aplikací SAP NetWeaver.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Zabezpečené úložiště pověření aktualizace SAP HANA

   Aktualizujte SAP HANA zabezpečeného úložiště tak, aby odkazoval na virtuální název tohoto nastavení systémové replikace SAP HANA.

   Spusťte následující příkaz k výpisu záznamů
   <pre><code>
   hdbuserstore List
   </code></pre>

   To by měla všechny položky seznamu a by měl vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položku odkazuje k virtuálnímu počítači a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka je potřeba změnit tak, aby odkazoval na virtuální název hostitele z nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše).

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Další postup
* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
