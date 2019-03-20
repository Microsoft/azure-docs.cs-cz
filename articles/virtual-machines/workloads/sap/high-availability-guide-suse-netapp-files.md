---
title: Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na SUSE Linux Enterprise serveru s Azure NetApp Files | Dokumentace Microsoftu
description: Příručka pro vysokou dostupnost pro SAP NetWeaver na SUSE Linux Enterprise serveru s Azure Files NetApp pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/015/2019
ms.author: radeltch
ms.openlocfilehash: aca755ce0357887c4d6c27007342afe1fb7b0882
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182944"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise serveru s Azure Files NetApp pro aplikace SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Tento článek popisuje, jak nasadit virtuální počítače, konfigurace virtuálních počítačů, instalaci rozhraní clusteru a instalace s vysokou dostupností systému SAP NetWeaver 7.50, pomocí [souborů NetApp Azure](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-introduction/).
V ukázkové konfigurace, příkazy pro instalaci atd., ASCS instance je číslo 00, číslo instance Lajících 01, instance primární aplikace (Pa adresy) je 02 a instanci aplikace (AAS) je 03. Se používá QAS ID systému SAP. 

Tento článek vysvětluje, jak dosáhnout vysoké dostupnosti pro SAP NetWeaver aplikace s Azure NetApp Files. V tomto článku se podrobně databázové vrstvě.

Přečtěte si následující poznámky SAP a Paper nejprve:

* [Dokumentace ke službě Azure souborů NetApp][anf-azure-doc] 
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
* [Nasazení virtuálních počítačů Azure pro SAP na platformě Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP na platformě Linux][dbms-guide]
* [SUSE SAP HA příručky s osvědčenými postupy] [ suse-ha-guide] vodítka obsahovat všechny požadované informace k nastavení Netweaver HA a SAP HANA System Replication místní. Pomocí těchto návodů jako obecné směrného plánu. Poskytují mnohem podrobnější informace.
* [Zpráva k vydání verze s aktualizací SP3 SUSE vysokou dostupnost rozšíření 12][suse-ha-12sp3-relnotes]
* [NetApp aplikace SAP v Microsoft Azure pomocí služby soubory Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Vysoká availability(HA) pro centrální služby SAP Netweaver vyžaduje sdílené úložiště.
Abychom toho dosáhli v SUSE Linuxu zatím bylo nutné k vytvoření samostatného clusteru s vysokou dostupností systému souborů NFS. 

Nyní je možné dosáhnout SAP Netweaver HA pomocí sdíleného úložiště, které jsou nasazené v Azure NetApp soubory. Použití služby soubory Azure NetApp pro sdílené úložiště se eliminují potřebu další [clusteru systému souborů NFS](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs). Pacemaker stále se vyžaduje pro vysokou dostupnost z centrální services(ASCS/SCS) SAP Netweaveru.


![Přehled SAP NetWeaver vysoké dostupnosti](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver Lajících a databáze SAP HANA pomocí virtuální název hostitele a virtuální IP adresy. V Azure [nástroj pro vyrovnávání zatížení](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-overview) je povinná používat virtuální IP adresu. Následující seznam obsahuje konfiguraci (A) SCS a Lajících nástroj pro vyrovnávání zatížení.

### <a name="ascs"></a>(A)SCS

* Konfiguraci front-endu
  * IP adresa 10.1.1.20
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 620**&lt;nr&gt;**
* Pravidla vyrovnávání zatížení
  * 32**&lt;nr&gt;** TCP
  * 36**&lt;nr&gt;** TCP
  * 39**&lt;nr&gt;** TCP
  * 81**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

### <a name="ers"></a>ERS

* Konfiguraci front-endu
  * IP adresa 10.1.1.21
* Konfigurace back-endu
  * Připojení k primární síťová rozhraní všech virtuálních počítačů, které by měla být součástí (A) SCS/Lajících clusteru
* Port testu
  * Port 621**&lt;nr&gt;**
* Pravidla vyrovnávání zatížení
  * 33**&lt;nr&gt;** TCP
  * 5**&lt;nr&gt;** 13 TCP
  * 5**&lt;nr&gt;** 14 TCP
  * 5**&lt;nr&gt;** 16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Azure souborů NetApp 

SAP NetWeaver vyžaduje sdílené úložiště pro přenos a profil adresář.  Než budete pokračovat v instalaci pro infrastrukturu souborů Azure NetApp, seznamte se s [dokumentace ke službě soubory Azure NetApp][anf-azure-doc]. Zaškrtněte, pokud vybrané oblasti Azure nabízí NetApp soubory Azure. Následující odkaz zobrazí dostupnost souborů NetApp Azure podle oblasti Azure: [Soubory Azure NetApp dostupnost podle oblasti Azure][anf-avail-matrix].

Funkce souborů Azure NetApp je ve verzi public preview v několika oblastech Azure. Před nasazením NetApp soubory Azure, zaregistrujte si souborů NetApp Azure ve verzi preview, po [registrace pro Azure NetApp soubory pokynů][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure souborů NetApp  

V krocích se předpokládá, že jste už nasadili [Azure Virtual Network](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview). Uvědomte si, že Azure NetApp soubory prostředků a virtuální počítače, kde se prostředky Azure NetApp Files připojí musí nasadit do stejné virtuální síti Azure.  

1. Pokud jste dosud neučinili, který, požádat o [registraci ve verzi preview Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-register).  

2. Ve vybrané oblasti Azure, po vytvoření účtu NetApp [pokyny k vytvoření účtu NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-netapp-account).  
3. Nastavení kapacity fondu souborů NetApp Azure, po [pokyny, jak nastavit službu Azure NetApp Files kapacity fondu](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
Architektura SAP Netweaver uvedené v tomto článku využívá jeden souborů NetApp Azure kapacitu fondu, SKU úrovně Premium. Doporučujeme, abyste SKU úrovně Premium souborů NetApp Azure pro úlohy aplikací SAP Netweaver v Azure.  

4. Podsíť, která se soubory Azure NetApp delegovat, jak je popsáno v [pokyny delegovat podsítě do služby soubory Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Svazky souborů Azure NetApp, následující nasadit [pokyny pro vytvoření svazku pro soubory Azure NetApp](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-create-volumes). Svazky v určené soubory NetApp Azure nasadit [podsítě](https://docs.microsoft.com/en-us/rest/api/virtualnetwork/subnets). Mějte na paměti, že Azure NetApp soubory prostředků a virtuální počítače Azure musí být ve stejné virtuální síti Azure. Například sapmnt<b>QAS</b>, usrsap<b>QAS</b>atd jsou názvy svazků a sapmnt<b>qas</b>, usrsap<b>qas</b>, jsou filepaths pro Azure Soubory NetApp svazky.  

   1. svazek sapmnt<b>QAS</b> (nfs://10.1.0.4/sapmnt<b>qas</b>)
   2. svazek usrsap<b>QAS</b> (nfs://10.1.0.4/usrsap<b>qas</b>)
   3. svazek usrsap<b>QAS</b>sys (nfs://10.1.0.5/usrsap<b>qas</b>sys)
   4. svazek usrsap<b>QAS</b>lajících (nfs://10.1.0.4/usrsap<b>qas</b>lajících)
   5. svazek trans (nfs://10.1.0.4/trans)
   6. svazek usrsap<b>QAS</b>pas (nfs://10.1.0.5/usrsap<b>qas</b>Pa adresy)
   7. svazek usrsap<b>QAS</b>aas (nfs://10.1.0.4/usrsap<b>qas</b>aas)
   
V tomto příkladu jsme použili souborů NetApp Azure pro všechny systémy souborů SAP Netweaver ukazují, jak je možné NetApp soubory Azure. Je také možné nasadit systémy SAP souborů, které nemusíte připojit prostřednictvím systému souborů NFS jako [Azure disk storage](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#premium-ssd) . V tomto příkladu <b>-e</b> musí být souborů NetApp Azure a <b>f-g</b> (to znamená, / USR/sap/<b>QAS</b>/D<b>02</b>, /USR/sap/<b>QAS </b>/D<b>03</b>) může být nasazený jako Azure disk storage. 

### <a name="important-considerations"></a>Důležité informace

Při zvažování souborů NetApp Azure pro SAP Netweaver na architektuře operačního systému SUSE vysokou dostupnost, mějte na paměti následující důležité aspekty:

- Minimální kapacitu fondu je 4 TB. Velikost kapacity fondu musí být v násobcích po 4 TB.
- Minimální objem je 100 GB
- Služba soubory Azure NetApp a všechny virtuální počítače, ve kterém se svazky souborů Azure NetApp připojí musí být ve stejné virtuální síti Azure. [Partnerský vztah virtuální sítě](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview) není zatím podporována NetApp soubory Azure.
- Vybranou virtuální síť musí mít podsíť delegovat do služby soubory Azure NetApp.
- Služba soubory Azure NetApp v současné době podporuje pouze NFSv3 
- Služba soubory Azure NetApp nabízí [exportovat zásady](https://docs.microsoft.com/en-gb/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): povolených klientů, můžete řídit přístup typu (čtení a zápis, jen pro čtení, atd.). 
- Funkce Azure souborů NetApp ještě není zóny. Funkce souborů NetApp Azure není momentálně ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti možné důsledků latence v některých oblastech Azure. 

   > [!NOTE]
   > Mějte na paměti, že soubory NetApp Azure nepodporuje ještě partnerský vztah virtuální sítě. Nasazení virtuálních počítačů a svazky souborů NetApp Azure ve stejné virtuální síti.

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Ruční nasazení virtuálních počítačů s Linuxem pomocí webu Azure portal

Nejprve musíte vytvořit svazky NetApp soubory Azure. Nasazení virtuálních počítačů. Potom vytvořte nástroj pro vyrovnávání zatížení a použijte virtuální počítače v back-endové fondy.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvořit skupinu dostupnosti pro ASC  
   Nastavení maximální aktualizační doména
1. Vytvoření virtuálního počítače 1  
   Použijte alespoň 12 SP3 SLES4SAP, v tomto příkladu se používá image SLES4SAP 12 SP3  
   Vyberte skupinu dostupnosti pro ASCS vytvořili dříve  
1. Vytvoření virtuálního počítače 2  
   Použijte alespoň 12 SP3 SLES4SAP, v tomto příkladu se používá image SLES4SAP 12 SP3  
   Vyberte skupinu dostupnosti pro ASCS vytvořili dříve  
1. Vytvořit skupinu dostupnosti pro SAP instancí aplikace (Pa adresy, AAS)    
   Nastavení maximální aktualizační doména
1. Vytvoření virtuálního počítače 3  
   Použijte alespoň 12 SP3 SLES4SAP, v tomto příkladu se používá image SLES4SAP 12 SP3  
   Vyberte dříve vytvořili pro Pa adresy/AAS skupiny dostupnosti   
1. Vytvoření virtuálního počítače 4  
   Použijte alespoň 12 SP3 SLES4SAP, v tomto příkladu se používá image SLES4SAP 12 SP3  
   Vyberte dříve vytvořili pro Pa adresy/AAS skupiny dostupnosti  

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

V tomto příkladu byly ručně nasadili prostředky prostřednictvím [webu Azure portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Ruční nasazení nástroje pro vyrovnávání zatížení Azure prostřednictvím portálu Azure portal

Nejprve musíte vytvořit svazky NetApp soubory Azure. Nasazení virtuálních počítačů. Potom vytvořte nástroj pro vyrovnávání zatížení a použijte virtuální počítače v back-endové fondy.

1. Vytvořte nástroj pro vyrovnávání zatížení (interní)  
   1. Vytvoření front-endové IP adresy
      1. IP adresa 10.1.1.20 ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-endový fond IP adres a klikněte na tlačítko Přidat
         1. Zadejte název nové front-endový fond IP (například **front-endu. QAS. ASCS**)
         1. Nastavení přiřazení do statické a zadejte IP adresu (například **10.1.1.20**)
         1. Klikněte na tlačítko OK
      1. IP adresa 10.1.1.21 ASCS Lajících
         * Opakujte předchozí kroky v části "a" k vytvoření IP adresy pro Lajících (například **10.1.1.21** a **front-endu. QAS. Lajících**)
   1. Vytvoření back-endové fondy
      1. Vytvoření back-endový fond pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte back-endové fondy a klikněte na tlačítko Přidat
         1. Zadejte název nového back-endového fondu (například **back-endu. QAS**)
         1. Klikněte na tlačítko Přidat virtuální počítač.
         1. Vyberte skupinu dostupnosti jste vytvořili dříve pro ASC 
         1. Vyberte virtuální počítače (A) SCS clusteru
         1. Klikněte na tlačítko OK
   1. Vytvoření sondy stavu
      1. Port 620**00** pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení vyberte testy stavu a klikněte na tlačítko Přidat
         1. Zadejte název nového stavu testu (například **stavu. QAS. ASCS**)
         1. Jako protokol, port 620 vyberte TCP**00**, Interval 5 a prahová hodnota špatného stavu 2
         1. Klikněte na tlačítko OK
      1. Port 621**01** pro ASCS Lajících
            * Opakujte předchozí kroky v části "c" k vytvoření sondy stavu pro Lajících (například 621**01** a **stavu. QAS. Lajících**)
   1. Pravidla vyrovnávání zatížení
      1. 32**00** TCP pro ASC
         1. Otevřete nástroj pro vyrovnávání zatížení, pravidel Vyrovnávání zatížení vyberte a klikněte na tlačítko Přidat
         1. Zadejte název nového pravidla služby load balancer (například **lb. QAS. ASCS.3200**)
         1. Vyberte IP adresu front-endu pro ASCS, back-endového fondu a sondu stavu, které jste vytvořili dříve (například **front-endu. QAS. ASCS**)
         1. Zachovat protokol **TCP**, zadejte port **3200**
         1. Zvyšte časový limit nečinnosti až 30 minut.
         1. **Ujistěte se, že chcete povolit plovoucí IP adresy**
         1. Klikněte na tlačítko OK
      1. Další porty pro ASC
         * Opakujte předchozí kroky v části "d" pro porty 36**00**, 39**00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASC
      1. Další porty pro ASCS Lajících
         * Opakujte předchozí kroky v části "d" pro porty 33**01**, 5**01**13, 5**01**14, 5**01**16 a TCP pro ASCS Lajících

> [!IMPORTANT]
> Nepovolujte TCP časová razítka na virtuálních počítačích Azure umístěných za nástrojem pro vyrovnávání zatížení Azure. Povolení protokolu TCP časová razítka způsobí, že sond stavu selhání. Nastavte parametr **net.ipv4.tcp_timestamps** k **0**. Podrobnosti najdete v tématu [sondy stavu nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-custom-probe-overview).

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v [nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) pro vytvoření základní Pacemaker clusteru pro tento server (A) SCS.

### <a name="installation"></a>Instalace

Následující položky jsou s předponou buď **[A]** – platí pro všechny uzly, **[1]** – platí jenom pro uzel 1 nebo **[2]** – platí jenom pro uzel 2.

1. **[A]**  Nainstalovat konektor SUSE

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Nepoužívejte pomlčky v názvy hostitelů uzlů clusteru. V opačném případě nebude fungovat vašeho clusteru. Jedná se o známé omezení a SUSE pracuje na opravě. Oprava budou vydané jako oprava balíčku sap suse cloudový konektor.

   Ujistěte se, že jste nainstalovali novou verzi konektoru SAP SUSE clusteru. Starý byla volána sap_suse_cluster_connector a novým nazývá **sap suse clusteru konektor**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
      Information for package sap-suse-cluster-connector:
   ---------------------------------------------------
   Repository     : SLE-12-SP3-SAP-Updates
   Name           : sap-suse-cluster-connector
   Version        : 3.1.0-8.1
   Arch           : noarch
   Vendor         : SUSE LLC <https://www.suse.com/>
   Support Level  : Level 3
   Installed Size : 45.6 KiB
   Installed      : Yes
   Status         : up-to-date
   Source package : sap-suse-cluster-connector-3.1.0-8.1.src
   Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]**  Agentů SAP aktualizace prostředku  
   
   Oprava pro balíček prostředků agenty je potřeba použít novou konfiguraci, která je popsána v tomto článku. Můžete zkontrolovat, pokud oprava je již nainstalována pomocí následujícího příkazu

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   Výstup by měl vypadat přibližně

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Pokud příkazu grep nenajde IS_ERS parametr, musíte nainstalovat opravu na [stránce pro stažení SUSE](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>Příprava instalace SAP NetWeaver

1. **[A]**  Vytvořit sdílené složky

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]**  Konfigurace autofs

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Vytvořte soubor s

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > Soubory NetApp Azure v současné době podporuje pouze NFSv3. Nevynechávejte nfsvers = 3 přepínače.
   
   Restartujte autofs připojit nové sdílené složky
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]**  Nakonfigurovat ODKLÁDACÍHO souboru

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   Restartujte agenta k aktivaci změny

   <pre><code>sudo service waagent restart
   </code></pre>


### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/Lajících

1. **[1]**  Vytvoření virtuální IP prostředku a sondu stavu pro instance ASC

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/nc" cmdline_options="-l -k 620<b>00</b>" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #     rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** Install SAP NetWeaver ASCS  

   Instalace SAP NetWeaver ASCS jako uživatel root na prvním uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro ASCS, například <b>anftstsapvh</b>, <b>10.1.1.20</b> a číslo instance, který jste použili pro test paměti nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER. Parametr SAPINST_USE_HOSTNAME slouží k instalaci SAP pomocí virtuální název hostitele.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Pokud se instalace nezdaří vytvořit podsložku v umístění/USR/sap/**QAS**/ASCS**00**, zkuste nastavit vlastníka a skupiny ASCS**00** složky a zkuste to znovu. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]**  Vytvoření virtuální IP prostředku a sondu stavu pro instance Lajících

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/nc" cmdline_options="-l -k 621<b>01</b>" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/nc"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** Install SAP NetWeaver ERS

   Instalace SAP NetWeaver Lajících jako uživatel root na druhém uzlu pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro Lajících, například <b>anftstsapers</b>, <b>10.1.1.21</b> a číslo instance, který jste použili pro test paměti nástroje pro vyrovnávání zatížení, například <b>01</b>.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER. Parametr SAPINST_USE_HOSTNAME slouží k instalaci SAP pomocí virtuální název hostitele.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastavujte oprávnění správně a instalace se nezdaří.

   Pokud se instalace nezdaří vytvořit podsložku v umístění/USR/sap/**QAS**/ERS**01**, zkuste nastavit vlastníka a skupiny Lajících**01** složky a zkuste to znovu.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]**  Adapt ASCS/SCS a Lajících instance profily
 
   * Profil ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
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
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]**  Konfigurace zachování

   Komunikace mezi aplikační server SAP NetWeaver a ASCS/SCS je směrován přes nástroj pro vyrovnávání zatížení softwaru. Nástroje pro vyrovnávání zatížení odpojí neaktivní připojení po Konfigurovatelný časový limit. Chcete-li tomu zabránit, budete muset nastavit parametr v SAP NetWeaver ASCS/SCS profilu a změnit nastavení systému Linux. Čtení [1410736 Poznámka SAP] [ 1410736] Další informace.

   ASCS/SCS profilu parametr modul/encni/set_so_keepalive byl již přidán v posledním kroku.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. **[A]**  Po instalaci nastavit uživatelů SAPU

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]**  Přidejte do souboru sapservice services ASCS a SAP Lajících

   Přidáte ASCS služby položku na druhém uzlu a zkopírovat položku služby Lajících do prvního uzlu.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]**  Vytvořit prostředky clusteru SAP

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Ujistěte se, že stav clusteru je ok a zda jsou spuštěny všechny prostředky. Není důležité na uzlu, které jsou spuštěné prostředky.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver aplikace server přípravy 

Některé databáze vyžadovat, aby instalaci instance databáze spuštěné na aplikační server. Příprava virtuálních počítačů pro server aplikace bude moct používat v těchto případech.

Kroky zmeškáte Předpokládejme, že nainstalujete aplikační server na serveru se liší od ASC/SCS a HANA servery. V opačném případě nejsou potřeba některé z kroků (např. konfigurace rozlišení názvu hostitele).

Následující položky jsou s předponou buď **[A]** – lze použít na Pa adresy a AAS, **[P]** – platí jenom pro Pa adresy nebo **[S]** – platí jenom pro AAS.


1. **[A]**  Konfigurace operačního systému

   Snížení velikosti mezipaměti změny. Další informace najdete v tématu [zápisu s nízkou výkonu na SLES 11/12 servery s velkou paměť RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]**  Nastavit rozlišení názvu hostitele

   Můžete buď použít DNS server nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   Nahraďte IP adresu a název hostitele v následujících příkazech

   ```bash
   sudo vi /etc/hosts
   ```

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele, aby odpovídaly vašemu prostředí

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]**  Vytvořit adresář sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]**  Vytvořit adresář Pa adresy

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]**  Vytvořit adresář jako

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]**  Konfigurace autofs na Pa adresy

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Vytvořte nový soubor s

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/ursap<b>qas</b>pas
   </code></pre>

   Restartujte autofs připojit nové sdílené složky

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]**  Konfigurace autofs na AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Vytvořte nový soubor s

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
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

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je nainstalován SAP NetWeaver na systému SAP HANA. Všechny podporované databáze můžete použít pro tuto instalaci. Další informace o tom, jak instalace SAP HANA v Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]. Seznam podporovaných databází najdete v tématu [SAP 1928533 Poznámka][1928533].

* Spuštění instalace instancí databáze SAP

   Nainstalujte instanci databáze SAP NetWeaver jako uživatel root pomocí virtuální název hostitele, který se mapuje na adresu IP konfigurace front-endu nástroje pro vyrovnávání zatížení pro databázi.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace serveru aplikace SAP NetWeaver

Postupujte podle těchto kroků nainstalujte aplikační server SAP.

1. **[A]**  Připravit aplikačního serveru postupujte podle kroků v kapitole [Příprava serveru aplikace SAP NetWeaver](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) výše a připravte je aplikační server.

2. **[A]**  Aplikační server SAP NetWeaver nainstalovat instalace serveru primární nebo další aplikace SAP NetWeaver.

   Pokud chcete, aby uživatel nekořenovými pro připojení k sapinst můžete použít parametr sapinst SAPINST_REMOTE_ACCESS_USER.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]**  Zabezpečené úložiště pověření aktualizace SAP HANA

   Aktualizujte SAP HANA zabezpečeného úložiště tak, aby odkazoval na virtuální název tohoto nastavení systémové replikace SAP HANA.

   Spusťte následující příkaz k výpisu záznamů
   <pre><code>
   hdbuserstore List
   </code></pre>

   To by měla všechny položky seznamu a by měl vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položku odkazuje k virtuálnímu počítači a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka je potřeba změnit tak, aby odkazoval na virtuální název hostitele z nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše) a název databáze (**QAS** ve výstupu výše).

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Test nastavení clusteru

Následující testy jsou kopie testovacích případů v [osvědčené postupy vodítka SUSE][suse-ha-guide]. Zkopírují se pro usnadnění práce. Vždy také přečtěte si osvědčené postupy vodítka a provádět všechny další testy, které byly přidány.

1. Test HAGetFailoverConfig HACheckConfig a HACheckFailoverConfig

   Spuštěním následujících příkazů jako \<sapsid > adm na uzlu, kde je aktuálně spuštěna instance ASC. Pokud se převzetí služeb při selhání příkazy: Nedostatek paměti, je možné příčiny pomlčky v názvu vašeho hostitele. Jedná se o známý problém a bude opraven v balíčku suse clusteru konektoru sap SUSE.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. ASCS instance přenášet ručně

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Spuštěním následujících příkazů jako uživatel root migrovat instance ASC.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. Test HAFailoverToNode

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Spuštěním následujících příkazů jako <sapsid>adm migrovat instance ASC.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulace selhání uzlu 

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spuštěním následujícího příkazu jako uživatel root v uzlu, kde je spuštěná instance ASC

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pokud používáte SBD, Pacemaker nesmí začínat automaticky ukončil uzlu. Stav po spuštění uzlu znovu by měl vypadat takto.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Pomocí následujících příkazů start Pacemaker na ukončil uzlu, vyčistit SBD zprávy a vyčistit nevydařené zdroje.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Ruční restartování testu ASCS instance

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Vytvoření zámku zařadit do fronty, pro příklad úpravy uživatele v su01 transakce. Spuštěním následujících příkazů jako < sapsid\>adm na uzlu, kde je spuštěná instance ASC. Příkazy se zastavit instanci ASCS a spusťte jej znovu. Zařazování zámek má dojít ke ztrátě v tomto testu.

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS instance by teď mělo být zakázané v Pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Znovu spusťte ASCS instance na stejném uzlu.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Uzamčení zařazování transakce su01 by měl být ztraceny a back endu by byla obnovena. Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Ukončit proces serveru zpráv

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře Identifikujte proces serveru zpráv a ukončete ji.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Pokud jste pouze kill server zpráv jednou, se restartuje tak sapstart. Pokud je často dostatečné Pacemaker budou nakonec kill přesunout do jiného uzlu ASCS instance. Spuštěním následujících příkazů jako kořenový adresář pro vyčištění prostředků stavu instance ASCS a Lajících po testu.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Ukončení procesu zařazení do fronty serveru

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Spuštěním následujících příkazů jako uživatel root v uzlu, kde je spuštěná ASCS instance k ukončení serveru zařadit do fronty.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS instance by měl bezprostředně převzetí služeb při selhání do jiného uzlu. Instance Lajících by měl také převzetí služeb při selhání po spuštění instance ASC. Spuštěním následujících příkazů jako kořenový adresář pro vyčištění prostředků stavu instance ASCS a Lajících po testu.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Ukončení procesu zařazení do fronty replikace serveru

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spusťte následující příkaz jako uživatel root v uzlu, kde je spuštěná instance Lajících k ukončení procesu zařazení do fronty replikace serveru.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Pokud spustíte pouze příkaz jednou, restartuje sapstart procesu. Při spuštění často dostatečné, bude sapstart restart procesu a prostředek bude v zastaveném stavu. Spuštěním následujících příkazů jako kořenový adresář pro vyčištění prostředků stavu instance Lajících po testu.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Ukončení procesu sapstartsrv zařadit do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře na uzlu se spuštěným ASC.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Proces sapstartsrv by měl být restartován vždy agentem Pacemaker prostředků. Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Další postup

* [Azure Virtual Machines, plánování a implementace SAP][planning-guide]
* [Nasazení virtuálních počítačů pro SAP v Azure][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP 
* HANA v Azure (velké instance), najdete v článku [SAP HANA (velké instance) vysokou dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o vytvoření vysoké dostupnosti a plánování zotavení po havárii SAP Hana na virtuálních počítačích Azure najdete v tématu [vysoké dostupnosti systému SAP HANA v Azure Virtual Machines (VM)][sap-hana-ha]
