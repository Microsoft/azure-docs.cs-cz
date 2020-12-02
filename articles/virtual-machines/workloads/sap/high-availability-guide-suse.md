---
title: Virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver v SLES | Microsoft Docs
description: Průvodce vysokou dostupností pro SAP NetWeaver v SUSE Linux Enterprise Server pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: ac70833d10c7cbcf5c4dc0fe709e2a6a58a30f26
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484205"
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
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7,50.
V ukázkových konfiguracích instalační příkazy atd. Je použito číslo instance ASCS 00, OLAJÍCÍCH instance 2 a SAP System ID NW1. Názvy prostředků (například virtuální počítače, virtuální sítě) v příkladu předpokládají, že jste k vytváření prostředků použili [šablonu][template-converged] s ID systému SAP NW1.

Nejprve si přečtěte následující poznámky a dokumenty SAP

* Poznámka [1928533][1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553][2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro aplikace SAP Poznámka [2205917][2205917] se doporučuje nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP.
* Poznámka SAP Poznámka [1944799][1944799] obsahuje pokyny pro SAP HANA SUSE Linux Enterprise Server pro aplikace SAP.
* Pro SAP Note [2178632][2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498][2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692][2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* Poznámka SAP poznámky [1984787][1984787] obsahuje obecné informace o SUSE Linux Enterprise Server 12.
* V části SAP Note [1999351][1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Průvodce osvědčenými postupy pro SUSE SAP ha][suse-ha-guide] Příručky obsahují všechny požadované informace pro nastavení NetWeaver HA a SAP HANA místní replikace systému. Použijte tyto příručky jako obecné standardní hodnoty. Poskytují mnohem podrobnější informace.
* [Zpráva k vydání verze SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]

## <a name="overview"></a>Přehled

Pro zajištění vysoké dostupnosti vyžaduje SAP NetWeaver Server NFS. Server NFS je nakonfigurovaný v samostatném clusteru a může ho používat víc systémů SAP.

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-suse/ha-suse.png)

Server NFS, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver OLAJÍCÍCH a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Následující seznam uvádí konfiguraci (A) SCS a nástroj pro vyrovnávání zatížení OLAJÍCÍCH.

### <a name="ascs"></a>Určitého SCS

* Konfigurace front-endu
  * 10.0.0.7 IP adres
* Port testu paměti
  * Port 620<strong> &lt; Nr &gt; </strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
  * Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 36<strong> &lt; Nr &gt; </strong> TCP
    * 39<strong> &lt; Nr &gt; </strong> TCP
    * 81<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong>. &lt; 16 TCP &gt; </strong>

### <a name="ers"></a>OLAJÍCÍCH

* Konfigurace front-endu
  * 10.0.0.8 IP adres
* Port testu paměti
  * Port 621<strong> &lt; Nr &gt; </strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
  * Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>13 TCP
    * 5<strong> &lt; Nr &gt; </strong>14 TCP
    * 5<strong>. &lt; 16 TCP &gt; </strong>

* Konfigurace back-endu
  * Připojeno k primárním síťovým rozhraním všech virtuálních počítačů, které by měly být součástí clusteru (A) SCS/OLAJÍCÍCH


## <a name="setting-up-a-highly-available-nfs-server"></a>Nastavení vysoce dostupného serveru NFS

SAP NetWeaver vyžaduje pro přenos a profilový adresář sdílené úložiště. Přečtěte si [vysokou dostupnost pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server][nfs-ha] o tom, jak nastavit server NFS pro SAP NetWeaver.

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

K nasazení všech požadovaných prostředků Azure, včetně virtuálních počítačů, skupiny dostupnosti a nástroje pro vyrovnávání zatížení, můžete použít buď šablonu Azure z GitHubu, nebo můžete prostředky nasadit ručně.

### <a name="deploy-linux-via-azure-template"></a>Nasazení systému Linux prostřednictvím šablony Azure

Azure Marketplace obsahuje obrázek pro SUSE Linux Enterprise Server aplikace SAP 12, které můžete použít k nasazení nových virtuálních počítačů. Image Marketplace obsahuje agenta prostředků pro SAP NetWeaver.

K nasazení všech požadovaných prostředků můžete použít jednu z šablon pro rychlý Start na GitHubu. Šablona nasadí virtuální počítače, nástroj pro vyrovnávání zatížení, skupinu dostupnosti atd. Pomocí těchto kroků nasaďte šablonu:

1. Otevřete [šablonu ASCS/SCS s více SID][template-multisid-xscs] nebo [sblíženou šablonu][template-converged] na Azure Portal. 
   Šablona ASCS/SCS pouze vytvoří pravidla vyrovnávání zatížení pro instance SAP NetWeaver ASCS/SCS a OLAJÍCÍCH (pouze Linux), zatímco sblížená šablona také vytvoří pravidla vyrovnávání zatížení pro databázi (například Microsoft SQL Server nebo SAP HANA). Pokud plánujete instalaci systému založeného na SAP NetWeaver a chcete také nainstalovat databázi do stejného počítače, použijte [sblíženou šablonu][template-converged].
1. Zadejte následující parametry.
   1. Předpona prostředků (jenom šablona ASCS/SCS multi SID)  
      Zadejte předponu, kterou chcete použít. Hodnota se používá jako předpona pro nasazené prostředky.
   3. ID systému SAP (jenom sblížená šablona)  
      Zadejte ID systému SAP pro systém SAP, který chcete nainstalovat. ID se používá jako předpona pro nasazené prostředky.
   4. Typ zásobníku  
      Vyberte typ zásobníku SAP NetWeaver.
   5. Typ operačního systému  
      Vyberte jednu ze distribucí systému Linux. V tomto příkladu vyberte SLES 12 BYOS.
   6. Typ databáze  
      Vybrat HANA
   7. Velikost systému SAP.  
      Množství SAP, které nový systém poskytuje. Pokud si nejste jistí, kolik SAP systém vyžaduje, požádejte svého partnera technologie SAP nebo systémový integrátor.
   8. Dostupnost systému  
      Vybrat HA
   9. Uživatelské jméno správce a heslo správce  
      Vytvoří se nový uživatel, který se dá použít k přihlášení k počítači.
   10. ID podsítě  
   Pokud chcete virtuální počítač nasadit do existující virtuální sítě, kde máte definovanou podsíť, ke které je potřeba přiřadit virtuální počítač, pojmenujte ID této konkrétní podsítě. ID obvykle vypadá jako/Subscriptions/**&lt; ID &gt; odběru**/ResourceGroups/název **&lt; &gt; skupiny prostředků****&lt; &gt;** /Providers/Microsoft.Network/virtualNetworks/název **&lt; virtuální sítě &gt;**/subnets/název podsítě

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linux pomocí Azure Portal

Nejprve je třeba vytvořit virtuální počítače pro tento cluster systému souborů NFS. Následně vytvoříte Nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondu back-end.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1  
   Použijte minimálně SLES4SAP 12 SP1, v tomto příkladu obrázek SLES4SAP 12 SP1. https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES for SAP Applications 12 SP1 se používá  
   Vybrat skupinu dostupnosti vytvořenou dříve  
1. Vytvořit virtuální počítač 2  
   Použijte minimálně SLES4SAP 12 SP1, v tomto příkladu obrázek SLES4SAP 12 SP1. https://portal.azure.com/#create/SUSE.SUSELinuxEnterpriseServerforSAPApplications12SP1PremiumImage-ARM  
   SLES for SAP Applications 12 SP1 se používá  
   Vybrat skupinu dostupnosti vytvořenou dříve  
1. Přidejte alespoň jeden datový disk do obou virtuálních počítačů.  
   Datové disky se používají pro `<SAPSID`> adresář/usr/SAP/
1. Vytvořit nástroj pro vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **NW1-ASCS-front-end**).
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.0.0.7**).
         1. Klikněte na OK.
      1. 10.0.0.8 IP adres pro ASCS OLAJÍCÍCH
         * Zopakováním výše uvedených kroků vytvořte IP adresu pro OLAJÍCÍCH (například **10.0.0.8** a **NW1-aers-back-end**).
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **NW1-back-end**).
      1. Klikněte na Přidat virtuální počítač.
      1. Vybrat virtuální počítač
      1. Vyberte virtuální počítače v clusteru (A) SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620 **00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **NW1-ASCS-HP**).
         1. Vybrat TCP as Protocol, port 620 **00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621 **02** pro ASCS olajících
         * Zopakováním výše uvedených kroků vytvořte sondu stavu pro OLAJÍCÍCH (například 621 **02** a **NW1-aers-HP**)
   1. Pravidla vyrovnávání zatížení
      1. Pravidla vyrovnávání zatížení pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **NW1-9,1-ASCS**).
         1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, které jste vytvořili dříve (například **NW1-ASCS-front**-end, **NW1-back-end** a **NW1-ASCS-HP**).
         1. Vybrat **porty ha**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
         * Zopakováním výše uvedených kroků vytvořte pravidla vyrovnávání zatížení pro OLAJÍCÍCH (například **NW1-9,1-olajících**).
1. Případně, pokud váš scénář vyžaduje základní nástroj pro vyrovnávání zatížení (interní), postupujte podle následujících kroků:  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 10.0.0.7 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **NW1-ASCS-front-end**).
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.0.0.7**).
         1. Klikněte na OK.
      1. 10.0.0.8 IP adres pro ASCS OLAJÍCÍCH
         * Zopakováním výše uvedených kroků vytvořte IP adresu pro OLAJÍCÍCH (například **10.0.0.8** a **NW1-aers-front-end**).
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **NW1-back-end**).
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte skupinu dostupnosti, kterou jste vytvořili dříve.
      1. Vyberte virtuální počítače v clusteru (A) SCS.
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620 **00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **NW1-ASCS-HP**).
         1. Vybrat TCP as Protocol, port 620 **00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621 **02** pro ASCS olajících
         * Zopakováním výše uvedených kroků vytvořte sondu stavu pro OLAJÍCÍCH (například 621 **02** a **NW1-aers-HP**)
   1. Pravidla vyrovnávání zatížení
      1. 32 **00** TCP pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **NW1-kg-3200**).
         1. Vyberte front-end IP adresu, fond back-endu a sondu stavu, který jste vytvořili dříve (například **NW1-ASCS-front-end**).
         1. Zachovejte protokol **TCP**, zadejte port **3200**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky pro porty 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 a TCP pro ASCS
      1. Další porty pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky pro porty 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 a TCP pro ASCS olajících

> [!IMPORTANT]
> Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

> [!Note]
> Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

> [!IMPORTANT]
> Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte pro tento server (a) SCS základní cluster Pacemaker.

### <a name="installation"></a>Instalace

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** instalace KONEKTORu SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Známý problém s používáním pomlčky v názvech hostitelů je opravený s verzí **3.1.1** balíčku **SAP-SUSE-cluster-Connector**. Pokud používáte uzly clusteru s pomlčkou v názvu hostitele, ujistěte se, že používáte aspoň verzi 3.1.1 balíčku SAP-SUSE-cluster-Connector. V opačném případě nebude cluster fungovat. 

   Ujistěte se, že jste nainstalovali novou verzi konektoru SAP SUSE cluster. Stará se o to, že se navolala sap_suse_cluster_connector a ta nového se nazývá **SAP-SUSE-cluster-Connector**.

   ```
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
   ```

1. **[A]** aktualizace agentů prostředků SAP  
   
   Pro použití nové konfigurace, která je popsaná v tomto článku, je nutná oprava balíčku Resource-Agents. Můžete ověřit, jestli je oprava už nainstalovaná, a to pomocí následujícího příkazu.

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Výstup by měl být podobný

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Pokud příkaz grep nenajde parametr IS_ERS, je nutné nainstalovat opravu uvedenou na [stránce pro stažení SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents) .

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.   

   <pre><code># IP address of the load balancer frontend configuration for NFS
   <b>10.0.0.4 nw1-nfs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci SAP NetWeaver

1. **[A]** vytvoření sdílených adresářů

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

1. **[A]** konfigurace AutoFS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Vytvořit soubor s

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   /usr/sap/<b>NW1</b>/SYS -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sidsys
   </code></pre>

   Pokud chcete připojit nové sdílené složky, restartujte autofs.

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** konfigurace odkládacího souboru

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Restartujte agenta, aby se změna aktivovala.

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/OLAJÍCÍCH

1. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci ASCS

   > [!IMPORTANT]
   > Nedávné testování odhalilo situace, kde NetCat přestane reagovat na požadavky z důvodu nevyřízených položek a omezení zpracování pouze jednoho připojení. Prostředek NetCat přestane naslouchat požadavkům nástroje pro vyrovnávání zatížení Azure a plovoucí IP adresa přestane být k dispozici.  
   > Pro existující clustery Pacemaker doporučujeme v minulosti nahradit NetCat pomocí Socat. V současné době doporučujeme použít agenta prostředků Azure-, který je součástí prostředků balíčku – agenti s následujícími požadavky na verzi balíčku:
   > - Pro SLES 12 SP4/SP5 musí být ve verzi aspoň Resource-Agents-4.3.018. a7fb5035-3.30.1.  
   > - Pro SLES 15/15 SP1 musí být verze aspoň Resource-Agents-4.3.0184.6 ee15eb2-4.13.1.  
   >
   > Všimněte si, že tato změna bude vyžadovat krátké výpadky.  
   > U existujících clusterů Pacemaker se v případě, že konfigurace již změnila tak, aby používala socat, jak je popsáno v tématu [posílení zabezpečení azure Load-Balancer](https://www.suse.com/support/kb/doc/?id=7024128), neexistuje žádný požadavek na přepnutí přímo do agenta prostředků Azure-No.

   <pre><code>sudo crm node standby <b>nw1-cl-1</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ASCS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCS' directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ASCS IPaddr2 \
     params ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>NW1</b>_ASCS fs_<b>NW1</b>_ASCS nc_<b>NW1</b>_ASCS vip_<b>NW1</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo crm_mon -r
   
   # Node nw1-cl-1: standby
   # <b>Online: [ nw1-cl-0 ]</b>
   # 
   # Full list of resources:
   # 
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-0</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   </code></pre>

1. **[1]** instalace SAP NetWeaver ASCS  

   Nainstalujte SAP NetWeaver ASCS jako kořen v prvním uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS, například <b>NW1-ASCS</b>, <b>10.0.0.7</b> a číslo instance, kterou jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**NW1**/ASCS **00**, zkuste nastavit vlastníka a skupinu složky ASCS **00** a zkuste to znovu.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci olajících

   <pre><code>sudo crm node online <b>nw1-cl-1</b>
   sudo crm node standby <b>nw1-cl-0</b>
   
   sudo crm configure primitive fs_<b>NW1</b>_ERS Filesystem device='<b>nw1-nfs</b>:/<b>NW1</b>/ASCSERS' directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='nfs4' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>NW1</b>_ERS IPaddr2 \
     params ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>NW1</b>_ERS azure-lb port=621<b>02</b>
   
   sudo crm configure group g-<b>NW1</b>_ERS fs_<b>NW1</b>_ERS nc_<b>NW1</b>_ERS vip_<b>NW1</b>_ERS
   </code></pre>

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo crm_mon -r
   
   # Node <b>nw1-cl-0: standby</b>
   # <b>Online: [ nw1-cl-1 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   </code></pre>

1. **[2]** instalace SAP NetWeaver olajících

   Nainstalujte SAP NetWeaver OLAJÍCÍCH jako kořenovou složku na druhém uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro OLAJÍCÍCH, například <b>NW1-aers</b>, <b>10.0.0.8</b> a číslo instance, kterou jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>02</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastaví správně oprávnění a instalace se nezdaří.

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**NW1**/ERS **02**, zkuste nastavit vlastníka a skupinu ve složce olajících **02** a zkuste to znovu.

   <pre><code>chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>


1. **[1]** přizpůsobení profilů instancí ASCS/SCS a olajících
 
   * Profil ASCS/SCS

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Pro ENSA1 i ENSA2 se ujistěte, že `keepalive` jsou nastavené parametry operačního systému, jak je popsáno v tématu SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * Profil OLAJÍCÍCH

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

1. **[A]** konfigurace Keep Alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím nástroje pro vyrovnávání zatížení softwaru. Nástroj pro vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém limitu. Abyste tomu předešli, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS, pokud používáte ENSA1, a změnit nastavení systému Linux `keepalive` na všech serverech SAP pro ENSA1/ENSA2. Další informace najdete v tématu [SAP Note 1410736][1410736] .

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** konfigurace uživatelů SAP po instalaci

   <pre><code># Add sidadm to the haclient group
   sudo usermod -aG haclient <b>nw1</b>adm
   </code></pre>

1. **[1]** do souboru sapservice přidejte služby SAP ASCS a olajících.

   Přidejte položku služby ASCS do druhého uzlu a zkopírujte položku služby OLAJÍCÍCH do prvního uzlu.

   <pre><code>cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>nw1-cl-1</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>nw1-cl-1</b> "cat /usr/sap/sapservices" | grep ERS<b>02</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

1. **[1]** vytvoření prostředků clusteru SAP

Pokud používáte architekturu serveru front Server 1 (ENSA1), definujte prostředky následujícím způsobem:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
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

  SAP představilo podporu pro front-Server 2, včetně replikace, od SAP NW 7,52. Počínaje platformou ABAP 1809 se ve výchozím nastavení nainstaluje služba fronty serveru 2. Podporu služby zařazení serveru 2 pro frontu najdete v tématu SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
Pokud používáte architekturu serveru fronty 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definujte prostředky následujícím způsobem:

<pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    operations \$id=rsc_sap_<b>NW1</b>_ERS<b>02</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>" AUTOMATIC_RECOVER=false IS_ERS=true 
   
   sudo crm configure modgroup g-<b>NW1</b>_ASCS add rsc_sap_<b>NW1</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>NW1</b>_ERS add rsc_sap_<b>NW1</b>_ERS<b>02</b>
   
   sudo crm configure colocation col_sap_<b>NW1</b>_no_both -5000: g-<b>NW1</b>_ERS g-<b>NW1</b>_ASCS
   sudo crm configure order ord_sap_<b>NW1</b>_first_start_ascs Optional: rsc_sap_<b>NW1</b>_ASCS<b>00</b>:start rsc_sap_<b>NW1</b>_ERS<b>02</b>:stop symmetrical=false
   
   sudo crm node online <b>nw1-cl-0</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

  Pokud provádíte upgrade ze starší verze a přejdete na server fronty 2, přečtěte si článek SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.


   <pre><code>sudo crm_mon -r
   
   # Online: <b>[ nw1-cl-0 nw1-cl-1 ]</b>
   #
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ASCS
   #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-1</b>
   #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-1</b>
   #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-1</b>
   #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-1</b>
   #  Resource Group: g-NW1_ERS
   #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    <b>Started nw1-cl-0</b>
   #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      <b>Started nw1-cl-0</b>
   #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       <b>Started nw1-cl-0</b>
   #      rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   <b>Started nw1-cl-0</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver

Některé databáze vyžadují, aby se instalace instance databáze spustila na aplikačním serveru. Příprava virtuálních počítačů aplikačního serveru, aby je bylo možné používat v těchto případech.

Postup níže předpokládá, že instalujete aplikační server na jiný server než servery ASCS/SCS a HANA. Jinak se některé z následujících kroků (třeba konfigurace překladu názvů hostitelů) nevyžadují.

1. Konfigurace operačního systému

   Snižte velikost nečisté mezipaměti. Další informace najdete v tématu [nízký výkon zápisu na serverech SLES 11/12 s velkou pamětí RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>sudo vi /etc/sysctl.conf

   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. Nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   ```bash
   sudo vi /etc/hosts
   ```

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   <pre><code># IP address of the load balancer frontend configuration for NFS
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

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. Konfigurace AutoFS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   +auto.master
   /- /etc/auto.direct
   </code></pre>

   Vytvořit nový soubor s

   <pre><code>sudo vi /etc/auto.direct
   
   # Add the following lines to the file, save and exit
   /sapmnt/<b>NW1</b> -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/sapmntsid
   /usr/sap/trans -nfsvers=4,nosymlink,sync <b>nw1-nfs</b>:/<b>NW1</b>/trans
   </code></pre>

   Pokud chcete připojit nové sdílené složky, restartujte autofs.

   <pre><code>sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. Nakonfigurovat odkládací soubor

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Restartujte agenta, aby se změna aktivovala.

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalovaný na SAP HANA. Pro tuto instalaci můžete použít každou podporovanou databázi. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v tématu [Vysoká dostupnost SAP HANA na Azure Virtual Machines (virtuálních počítačích)][sap-hana-ha]. Seznam podporovaných databází najdete v tématu [SAP Note 1928533][1928533].

1. Spusťte instalaci instance databáze SAP.

   K instalaci instance databáze SAP NetWeaver jako kořenového adresáře použijte virtuální název hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro databázi, například <b>NW1-DB</b> a <b>10.0.0.13</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Pomocí těchto kroků nainstalujete aplikační Server SAP.

1. Příprava aplikačního serveru

   K přípravě aplikačního serveru použijte postup v části [Příprava aplikačního serveru SAP NetWeaver](high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) .

1. Instalace aplikačního serveru SAP NetWeaver

   Nainstalujte primární nebo další server aplikace SAP NetWeaver.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. Aktualizace zabezpečeného úložiště SAP HANA

   Aktualizujte SAP HANA zabezpečené úložiště tak, aby odkazovalo na virtuální název nastavení replikace SAP HANA systému.

   Chcete-li zobrazit seznam položek, spusťte následující příkaz.
   <pre><code>hdbuserstore List
   </code></pre>

   Mělo by se zobrazit seznam všech položek, které by měly vypadat podobně jako
   <pre><code>DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>HN1</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položky odkazuje na virtuální počítač, a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka musí být změněna tak, aby odkazovala na virtuální název hostitele nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše) a název databáze (**HN1** ve výstupu výše).

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db:30313@HN1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Následující testy jsou kopie testovacích případů v SUSE průvodců osvědčenými postupy. Budou zkopírovány pro usnadnění práce. Vždy si přečtěte Příručky k osvědčeným postupům a proveďte všechny další testy, které mohou být přidány.

1. HAGetFailoverConfig testů, HACheckConfig a HACheckFailoverConfig

   \<sapsid>Na uzlu, kde je aktuálně spuštěná instance ASCS, spusťte následující příkazy jako ADM. Pokud se příkazy nezdaří s CHYBou: nedostatek paměti, může to být způsobeno pomlčkami ve vašem názvu hostitele. Jedná se o známý problém, který bude opraven nástrojem SUSE v balíčku SAP-SUSE-cluster-Connector.

   <pre><code>nw1-cl-0:nw1adm 54> sapcontrol -nr <b>00</b> -function HAGetFailoverConfig
   
   # 15.08.2018 13:50:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: Toolchain Module
   # HASAPInterfaceVersion: Toolchain Module (sap_suse_cluster_connector 3.0.1)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode:
   # HANodes: nw1-cl-0, nw1-cl-1
   
   nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -function HACheckConfig
   
   # 15.08.2018 14:00:04
   # HACheckConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   # SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   # SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   # SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   # SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   # SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   # SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   # SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   # SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   # SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   # SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   # SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (nw1-ascs_NW1_00), SAPInstance includes is-ers patch
   # SUCCESS, SAP CONFIGURATION, Enqueue replication (nw1-ascs_NW1_00), Enqueue replication enabled
   # SUCCESS, SAP STATE, Enqueue replication state (nw1-ascs_NW1_00), Enqueue replication active
   
   nw1-cl-0:nw1adm 56> sapcontrol -nr 00 -function HACheckFailoverConfig
   
   # 15.08.2018 14:04:08
   # HACheckFailoverConfig
   # OK
   # state, category, description, comment
   # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

1. Ruční migrace instance ASCS

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Pro migraci instance ASCS spusťte následující příkazy jako kořen.

   <pre><code>nw1-cl-0:~ # crm resource migrate rsc_sap_NW1_ASCS00 force
   # INFO: Move constraint created for rsc_sap_NW1_ASCS00
   
   nw1-cl-0:~ # crm resource unmigrate rsc_sap_NW1_ASCS00
   # INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. HAFailoverToNode testu

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   \<sapsid>Pro migraci instance ASCS spusťte následující příkazy jako ADM.

   <pre><code>nw1-cl-0:nw1adm 55> sapcontrol -nr 00 -host nw1-ascs -user nw1adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   # Remove migration constraints
   nw1-cl-0:~ # crm resource clear rsc_sap_NW1_ASCS00
   #INFO: Removed migration constraints for rsc_sap_NW1_ASCS00
   </code></pre>

   Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Simulace havárie uzlu

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkaz jako kořen v uzlu, ve kterém je spuštěná instance ASCS.

   <pre><code>nw1-cl-0:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pokud používáte SBD, Pacemaker by se neměl automaticky spustit na ukončeném uzlu. Stav po spuštění uzlu by měl vypadat takto.

   <pre><code>Online: [ nw1-cl-1 ]
   OFFLINE: [ nw1-cl-0 ]
   
   Full list of resources:
   
   stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-1 'not running' (7): call=219, status=complete, exitreason='none',
       last-rc-change='Wed Aug 15 14:38:38 2018', queued=0ms, exec=0ms
   </code></pre>

   Pomocí následujících příkazů spusťte Pacemaker na ukončeném uzlu, vyčistěte zprávy SBD a vyčistěte prostředky, které selhaly.

   <pre><code># run as root
   # list the SBD device(s)
   nw1-cl-0:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   nw1-cl-0:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message nw1-cl-0 clear
   
   nw1-cl-0:~ # systemctl start pacemaker
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Test ručního restartování instance ASCS

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Vytvořte zámek zařazení do fronty, například upravit uživatele v su01 transakce. \<sapsid>Na uzlu, kde je spuštěná instance ASCS, spusťte následující příkazy jako ADM. Příkazy zazastaví instanci ASCS a znovu se spustí. Pokud používáte architekturu serveru fronty 1, očekává se, že se v tomto testu ztratí zámek fronty. Pokud používáte architekturu Server 2 pro zařazování do fronty, zachová se. 

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Instance ASCS by teď měla být v Pacemaker zakázaná.

   <pre><code>rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Znovu spusťte instanci ASCS na stejném uzlu.

   <pre><code>nw1-cl-1:nw1adm 54> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Zámek fronty transakcí su01 by měl být ztracený a back-end by měl být resetován. Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Ukončit proces serveru zprávy

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře Identifikujte proces serveru a potom ho ukončete.

   <pre><code>nw1-cl-1:~ # pgrep ms.sapNW1 | xargs kill -9
   </code></pre>

   Pokud server pouze jednou zastavíte, bude restartován pomocí sapstart. Pokud jste ho ASCS dostatečně přesunuli, Pacemaker se nakonec přesune instance na jiný uzel. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Proces serveru dezaktivačního zařazení do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu, ve kterém je spuštěná instance ASCS, aby se mohl ukončit server fronty.

   <pre><code>nw1-cl-0:~ # pgrep en.sapNW1 | xargs kill -9
   </code></pre>

   Instance ASCS by měla okamžitě převzít služby na jiný uzel. Instance OLAJÍCÍCH by také měla převzít služby při selhání po spuštění instance ASCS. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ASCS00
   nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Proces replikace replikačního serveru do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkaz jako kořenový adresář v uzlu, ve kterém je spuštěná instance OLAJÍCÍCH, čímž se ukončí proces serveru replikace ve frontě.

   <pre><code>nw1-cl-0:~ # pgrep er.sapNW1 | xargs kill -9
   </code></pre>

   Pokud příkaz spustíte pouze jednou, sapstart proces restartuje. Pokud je spuštěno dostatečně často, sapstart proces nerestartuje a prostředek bude v zastaveném stavu. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředku instance OLAJÍCÍCH po testu.

   <pre><code>nw1-cl-0:~ # crm resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Ukončit proces sapstartsrv zařazování do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu, kde je spuštěný ASCS.

   <pre><code>nw1-cl-1:~ # pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   nw1-cl-1:~ # kill -9 59545
   </code></pre>

   Proces sapstartsrv by měl být vždy restartován agentem prostředků Pacemaker. Stav prostředku po testu:

   <pre><code>stonith-sbd     (stonith:external/sbd): Started nw1-cl-1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_ERS
        fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [HA pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP – příručka pro multi-SID](./high-availability-guide-suse-multi-sid.md)
* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .