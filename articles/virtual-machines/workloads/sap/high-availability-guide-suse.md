---
title: Azure virtuální počítače vysoká dostupnost pro SAP NetWeaver na SUSE Linux Enterprise Server pro aplikace SAP | Microsoft Docs
description: Vysoká dostupnost příručce pro SAP NetWeaver na SUSE Linux Enterprise Server pro aplikace SAP
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
ms.openlocfilehash: 12efeba68f30aa8723acc32449ae05ffac4c1ac4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658753"
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

[suse-hana-ha-guide]:https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Tento článek popisuje postup nasazení virtuálních počítačů, konfiguraci virtuálních počítačů, nainstalujte rozhraní framework clusteru a nainstalujte systému SAP NetWeaver 7.50 vysoce dostupný.
V konfiguraci příklad příkazy instalace atd. Čísla instance ASC 00 YBRAT instance číslo 02 a používá se ID NW1 systému SAP. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládá, že jste použili [konvergované šablony] [ template-converged] systémem SAP ID NW1 pro vytvoření prostředků.

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

![Přehled SAP NetWeaver vysokou dostupnost](./media/high-availability-guide-suse/img_001.png)

Na serveru NFS, SAP NetWeaver ASC, SAP NetWeaver SCS, SAP NetWeaver YBRAT a databázi SAP HANA použijte virtuální název hostitele a virtuální IP adresy. K použití virtuální IP adresy se v Azure, vyžaduje nástroj pro vyrovnávání zatížení. V následujícím seznamu jsou konfigurace (A) SCS a YBRAT nástroj pro vyrovnávání zatížení.

### <a name="ascs"></a>(A)SCS

* Front-endovou konfiguraci
  * IP adresa 10.0.0.7
* Konfigurace back-end
  * Připojené k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí (A) SCS/YBRAT clusteru
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

### <a name="ers"></a>YBRAT

* Front-endovou konfiguraci
  * IP adresa 10.0.0.8
* Konfigurace back-end
  * Připojené k primární síťová rozhraní všech virtuálních počítačů, které by měly být součástí (A) SCS/YBRAT clusteru
* Port testu
  * Port 621**&lt;nr&gt;**
* Pravidla Vyrovnávání zatížení
  * 33**&lt;nr&gt;**  TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-a-highly-available-nfs-server"></a>Nastavení serveru s vysokou dostupností systému souborů NFS

SAP NetWeaver vyžaduje sdílené úložiště pro přenos a profil adresář. Čtení [vysokou dostupnost pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server] [ nfs-ha] o tom, jak nastavení serveru NFS pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

Můžete použít buď Azure šablony z githubu nasadit všechny požadované prostředky Azure, včetně virtuálních počítačů, dostupnost nastavit a nástroj pro vyrovnávání zatížení nebo prostředky můžete nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení Linux pomocí šablony Azure

Azure Marketplace obsahuje bitovou kopii pro SUSE Linux Enterprise Server pro 12 aplikace SAP, který můžete použít k nasazení nových virtuálních počítačů. Bitová kopie marketplace obsahuje agenta prostředků pro SAP NetWeaver.

Můžete jeden z šablony rychlý start na githubu nasadit všechny požadované prostředky. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, dostupnosti apod. Postupujte podle těchto kroků nasadíte šablony:

1. Otevřete [ASC nebo SCS více SID šablony] [ template-multisid-xscs] nebo [konvergované šablony] [ template-converged] na Azure portálu ASC nebo SCS pouze vytvoří šablona pravidla Vyrovnávání zatížení pro SAP NetWeaver ASC nebo SCS a instance YBRAT (pouze Linux) zatímco sblížené Šablona také vytváří pravidla Vyrovnávání zatížení pro databázi (například Microsoft SQL Server nebo SAP HANA). Pokud máte v plánu pro instalaci systému SAP NetWeaver na základě a také chcete databázi nainstalovat na stejný počítače, použijte [konvergované šablony][template-converged].
1. Zadejte následující parametry
   1. Předpona prostředků (pouze šablony SID více ASC nebo SCS)  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro prostředky, které jsou nasazeny.
   3. ID systému SAP (pouze sblížené šablony)  
      Zadejte ID systému SAP systému SAP, který chcete nainstalovat. Identifikátor se používá jako předpona pro prostředky, které jsou nasazeny.
   4. Typ zásobníku  
      Vyberte typ SAP NetWeaver zásobníku
   5. Typ operačního systému  
      Vyberte jednu z distribucích systému Linux. V tomto příkladu vyberte SLES 12 BYOS
   6. Typ databázového  
      Vyberte HANA
   7. Velikost systému SAP  
      Množství protokoly SAP poskytuje nový systém. Pokud si nejste jisti kolik protokoly SAP vyžaduje systém, obraťte se na partnera technologie SAP nebo systémový integrátor
   8. Dostupnost systému  
      Vyberte HA
   9. Uživatelské jméno správce a heslo správce  
      Po vytvoření nového uživatele, který lze použít pro přihlášení k počítači.
   10. ID podsítě  
   ID podsítě, ke které by měl být připojený virtuální počítače.  Ponechte prázdné, pokud chcete vytvořit novou virtuální síť, nebo vyberte stejné podsíti, který můžete použít nebo vytvořit jako součást nasazení serveru NFS. ID obvykle vypadá /subscriptions/**&lt;ID předplatného&gt;**/resourceGroups/**&lt;název skupiny prostředků&gt;**/providers/ Microsoft.Network/virtualNetworks/**&lt;název virtuální sítě&gt;**/subnets/**&lt;název podsítě.&gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční instalace Linux prostřednictvím portálu Azure

Nejprve musíte vytvořit virtuální počítače pro tento cluster systému souborů NFS. Později můžete vytvořit nástroj pro vyrovnávání zatížení a používat virtuální počítače v back-endové fondy.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvořit skupinu dostupnosti  
   Sada maximální aktualizace domény
1. Vytvoření virtuálního počítače 1   
   Použijte alespoň SLES4SAP 12 SP1 na tomto příkladu SLES4SAP 12 SP1 obrázku https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES pro SAP aplikace 12 SP1 se používá.  
   Vyberte dříve vytvořenou sadu dostupnosti  
1. Vytvoření virtuálního počítače 2   
   Použijte alespoň SLES4SAP 12 SP1 na tomto příkladu SLES4SAP 12 SP1 obrázku https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES pro SAP aplikace 12 SP1 se používá.  
   Vyberte dříve vytvořenou sadu dostupnosti  
1. Přidejte alespoň jeden datový disk pro virtuální počítače  
   Datové disky se používají proUSR/sap/`<SAPSID`> adresář
1. Vytvořit nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvořte front-end IP adresy
      1. IP adresu 10.0.0.7 ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fond IP front-endu a klikněte na tlačítko Přidat
         1. Zadejte název nového fondu IP front-endu (například **nw1. ASC front-end**)
         1. Nastavit statickou přiřazení a zadejte IP adresu (například **10.0.0.7**)
         1. Klikněte na tlačítko OK
      1. IP adresa 10.0.0.8 pro YBRAT ASC
         * Opakujte tento postup vytvoření IP adresy pro YBRAT (například **10.0.0.8** a **nw1. aers backend**)
   1. Vytvoření fondu back-end
      1. Vytvořte fond back-end pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, zvolte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového fondu back-end (například **nw1. ASC backend**)
         1. Klikněte na tlačítko Přidat virtuální počítač.
         1. Vyberte jste dříve vytvořili sadu dostupnosti
         1. Vyberte virtuální počítače (a) SCS clusteru
         1. Klikněte na tlačítko OK
      1. Vytvořte fond back-end pro YBRAT ASC
         * Opakujte tento postup vytvoření fondu back-end pro YBRAT (například **nw1. aers backend**)
   1. Vytvoření sondy stavu
      1. Port 620**00** pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, zvolte sondy stavu služby a klikněte na tlačítko Přidat
         1. Zadejte název nové kontroly stavu (například **nw1. ASC hp**)
         1. Vyberte TCP jako protokol, port 620**00**, zachovat Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 621**02** pro YBRAT ASC
         * Opakujte tento postup k vytvoření test stavu pro YBRAT (například 621**02** a **nw1. aers hp**)
   1. Pravidla Vyrovnávání zatížení
      1. 32**00** TCP pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, zvolte pravidla Vyrovnávání zatížení a klikněte na tlačítko Přidat
         1. Zadejte název nové pravidlo Vyrovnávání zatížení (například **nw1-lb-3200**)
         1. Vyberte front-endovou IP adresu, fond back-end a test stavu, které jste vytvořili dříve (například **nw1. ASC front-end**)
         1. Zachovat protokol **TCP**, zadejte port **3200**
         1. Časový limit nečinnosti zvýšení do 30 minut
         1. **Nezapomeňte povolit plovoucí IP adresa**
         1. Klikněte na tlačítko OK    
      1. Další porty pro ASC
         * Opakujte předchozí kroky pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASC
      1. Další porty pro YBRAT ASC
         * Opakujte předchozí kroky pro porty 33**02**, 5**02**13, 5**02**14, 5**02**16 a TCP pro YBRAT ASC

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru kardiostimulátor

Postupujte podle kroků v [nastavení kardiostimulátor na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) kardiostimulátor pro vytvoření základní clusteru pro tento server (A) SCS.

### <a name="installation"></a>Instalace

Následující položky jsou předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nainstalovat konektor SUSE
   
   <pre><code>
   sudo zypper install sap_suse_cluster_connector
   </code></pre>

1. **[A]**  Aktualizace SAP prostředků agentů  
   
   Oprava pro balíček prostředků agentů je potřeba použít novou konfiguraci, který je popsaný v tomto článku. Můžete zkontrolovat, pokud je oprava již nainstalována pomocí následujícího příkazu

   <pre><code>
   sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Výstup by měl vypadat přibližně

   <pre><code>
   &lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Pokud příkaz grep nenajde parametr IS_ERS, budete muset nainstalovat opravu uvedené na [stránce pro stažení SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code>
   # example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

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
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Příprava pro SAP NetWeaver instalace

1. **[A]**  Vytvoření sdíleného adresáře

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

1. **[A]**  Konfigurace ODKLÁDACÍHO souboru

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

   Restartujte agenta aktivovat změn

   <pre><code>
   sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASC nebo YBRAT

1. **[1]**  Vytvoření virtuální IP prostředků a test stavu pro instanci ASC

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

   Ujistěte se, zda je stav clusteru ok a zda jsou spuštěny všechny prostředky. Není důležité, na který uzel prostředky jsou spuštěné.

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

1. **[1]**  Nainstalovat SAP NetWeaver ASC  

   Instalace SAP NetWeaver ASC jako kořenového na prvním uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP front-endové konfigurace služby Vyrovnávání zatížení pro ASC, například <b>nw1 Asc</b>, <b>10.0.0.7</b> a instance číslo, které jste použili pro kontrolu služby Vyrovnávání zatížení, například <b>00</b>.

   Parametr sapinst SAPINST_REMOTE_ACCESS_USER můžete povolit uživateli nekořenovými pro připojení k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se instalace nezdaří vytvořit podsložku v usr/sap/**NW1**/ASCS**00**, zkuste nastavit vlastníka a skupiny Asc**00** složku a zkuste to znovu.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]**  Vytvoření virtuální IP prostředků a test stavu pro instanci YBRAT

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
 
   Ujistěte se, zda je stav clusteru ok a zda jsou spuštěny všechny prostředky. Není důležité, na který uzel prostředky jsou spuštěné.

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

1. **[2]**  Nainstalovat SAP NetWeaver YBRAT  

   Instalace SAP NetWeaver YBRAT jako kořenového na druhém uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP front-endové konfigurace služby Vyrovnávání zatížení pro YBRAT, například <b>nw1 aers</b>, <b>10.0.0.8</b> a instance číslo, které jste použili pro kontrolu služby Vyrovnávání zatížení, například <b>02</b>.

   Parametr sapinst SAPINST_REMOTE_ACCESS_USER můžete povolit uživateli nekořenovými pro připojení k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastavujte oprávnění správně a instalace se nezdaří.

   Pokud se instalace nezdaří vytvořit podsložku v usr/sap/**NW1**/ERS**02**, zkuste nastavit vlastníka a skupiny YBRAT**02** složku a zkuste to znovu.

   <pre><code>
   chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>
   > 

1. **[1]**  Adapt ASC nebo SCS a YBRAT instance profily
 
   * ASC nebo SCS profilu

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

   * YBRAT profilu

   <pre><code> 
   sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   </code></pre>


1. **[A]**  Konfigurace zachování

   Komunikace mezi serverem aplikace SAP NetWeaver a ASC nebo SCS směrován přes softwarovému Vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení odpojí neaktivní připojení po vypršení časového limitu se dají konfigurovat. K tomu potřebujete nastavení parametru v profilu SAP NetWeaver ASC nebo SCS a změnit nastavení systému Linux. Čtení [1410736 Poznámka SAP] [ 1410736] Další informace.
   
   Již byl přidán ASC nebo SCS profil parametr enque/encni/set_so_keepalive v posledním kroku.

   <pre><code> 
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

1. **[A]**  Nakonfigurujte uživatele, SAP po instalaci
 
   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm   
   </code></pre>

1. **[1]**  Přidejte do souboru sapservice služby ASC a YBRAT SAP

   Přidáte ASC služby položku na druhém uzlu a zkopírujte položku služby YBRAT do prvního uzlu.

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

   Ujistěte se, zda je stav clusteru ok a zda jsou spuštěny všechny prostředky. Není důležité, na který uzel prostředky jsou spuštěné.

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

Některé databáze vyžadují, aby instalaci instance databáze se spouští na aplikační server. Připravte virtuální počítače serveru aplikace mohli používat v těchto případech.

Mocí následujících kroky předpokládají, nainstalovat aplikační server na server, která se liší od serverů ASC nebo SCS a HANA. V opačném případě některé z následujících kroků (např. konfigurace rozlišení názvu hostitele) nejsou potřeba.

1. Instalační program rozlišení názvu hostitele

   Můžete buď použít DNS server, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak chcete použít soubor/etc/hosts.
   Nahraďte adresu IP a název hostitele v následujících příkazech
   ```bash
   sudo vi /etc/hosts
   ```
   Vložte následující řádky, které se/etc/hosts. Změnit IP adresu a název hostitele tak, aby odpovídaly prostředí    
    
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

1. Vytvoření adresáře sapmnt

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

1. Konfigurace ODKLÁDACÍ soubor
 
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

   Restartujte agenta aktivovat změn

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalován na SAP HANA. Všechny podporované databázové můžete použít pro tuto instalaci. Další informace o tom, jak nainstalovat SAP HANA v Azure najdete v tématu [vysokou dostupnost z SAP HANA ve virtuálních počítačích Azure (VM)][sap-hana-ha]. Seznam podporovaných databází, naleznete v části [1928533 Poznámka SAP][1928533].

1. Spusťte instalaci instance databáze SAP

   Instalovat instanci databáze SAP NetWeaver jako kořenové pomocí virtuální název hostitele, který se mapuje na adresu IP front-endové konfigurace služby Vyrovnávání zatížení pro databázi, například <b>nw1-db</b> a <b>10.0.0.13</b>.

   Parametr sapinst SAPINST_REMOTE_ACCESS_USER můžete povolit uživateli nekořenovými pro připojení k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace serveru aplikace SAP NetWeaver

Postupujte podle těchto kroků nainstalujte SAP aplikační server. 

1. Příprava aplikace serveru

Postupujte podle kroků v kapitole [přípravy serveru SAP NetWeaver aplikace](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) výše a připravte je aplikační server.

1. Instalace serveru SAP NetWeaver aplikace

   Instalace serveru primární nebo další aplikace SAP NetWeaver.

   Parametr sapinst SAPINST_REMOTE_ACCESS_USER můžete povolit uživateli nekořenovými pro připojení k sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Zabezpečené úložiště pověření aktualizace SAP HANA

   Aktualizujte zabezpečené úložiště SAP HANA tak, aby odkazoval na virtuální název tohoto nastavení replikace systému SAP HANA.

   Spusťte následující příkaz k zobrazení seznamu položek
   <pre><code>
   hdbuserstore List
   </code></pre>

   To by měl zobrazit seznam všech položek a by měl vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: HN1
   </code></pre>

   Výstup ukazuje, že virtuální počítač a nikoli IP adresa služby Vyrovnávání zatížení ukazovat na IP adresu výchozí položku. Tato položka je potřeba změnit tak, aby odkazoval na virtuální název hostitele pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše)!

   <pre><code>
   su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="next-steps"></a>Další postup
* [Azure virtuálních počítačů, plánování a implementace pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení virtuálních počítačů databázového systému Azure pro SAP][dbms-guide]
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA v Azure (velké instance) naleznete v tématu [SAP HANA (velké instance) vysoké dostupnosti a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Další informace o vytvoření vysoké dostupnosti a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure naleznete v tématu [vysokou dostupnost z SAP HANA ve virtuálních počítačích Azure (VM)][sap-hana-ha]
