---
title: Virtuální počítače Azure s vysokou dostupností pro SAP NW v SLES s využitím Azure NetApp Files | Microsoft Docs
description: Průvodce vysokou dostupností pro SAP NetWeaver v SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: 7e42fb43fee4d3f8097b7ac530056d948e3f98c8
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486187"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7,50 pomocí [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
V příkladech konfigurací, instalačních příkazů atd. instance ASCS je číslo 00, olajících instance 01, primární instance aplikace (PAS) je 02 a instance aplikace (AAS) je 03. Používá se ID systému SAP QAS. 

Tento článek vysvětluje, jak dosáhnout vysoké dostupnosti pro aplikaci SAP NetWeaver pomocí Azure NetApp Files. Tato vrstva databáze není podrobněji popsána v tomto článku.

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* [Dokumentace k Azure NetApp Files][anf-azure-doc] 
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
* Komunitní WIKIWEB pro SAP] ( https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) obsahuje všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Průvodce osvědčenými postupy pro SUSE SAP ha][suse-ha-guide] Příručky obsahují všechny požadované informace pro nastavení NetWeaver HA a SAP HANA místní replikace systému. Použijte tyto příručky jako obecné standardní hodnoty. Poskytují mnohem podrobnější informace.
* [Zpráva k vydání verze SUSE High Availability Extension 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Vysoká dostupnost (HA) pro služby SAP NetWeaver Central Services vyžaduje sdílené úložiště.
Aby se zajistilo, že v SUSE Linux bylo ještě nutné sestavit samostatný cluster se systémem souborů NFS s vysokou dostupností. 

Nyní je možné dosáhnout dostupnosti SAP NetWeaver HA pomocí sdíleného úložiště nasazeného v Azure NetApp Files. Použití Azure NetApp Files pro sdílené úložiště eliminuje nutnost dalšího [clusteru NFS](./high-availability-guide-suse-nfs.md). Pacemaker je stále potřeba pro vysokou dostupnost centrálních služeb SAP NetWeaver (ASCS/SCS).


![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver OLAJÍCÍCH a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure se [Nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md) vyžaduje k použití virtuální IP adresy. Doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Následující seznam uvádí konfiguraci (A) SCS a nástroj pro vyrovnávání zatížení OLAJÍCÍCH.

### <a name="ascs"></a>Určitého SCS

* Konfigurace front-endu
  * 10.1.1.20 IP adres
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
  * 10.1.1.21 IP adres
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


## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>Nastavení infrastruktury Azure NetApp Files 

SAP NetWeaver vyžaduje pro přenos a profilový adresář sdílené úložiště.  Než budete pokračovat s nastavením infrastruktury souborů Azure NetApp, Seznamte se s [dokumentaci Azure NetApp Files][anf-azure-doc]. Ověřte, jestli vybraná oblast Azure nabízí Azure NetApp Files. Následující odkaz ukazuje dostupnost Azure NetApp Files podle oblasti Azure: [Azure NetApp Files dostupnost podle oblasti Azure][anf-avail-matrix].

Soubory Azure NetApp jsou k dispozici v několika [oblastech Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Před nasazením Azure NetApp Files požádat o registraci do Azure NetApp Files podle [pokynů pro soubory služby Azure NetApp][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Nasazení prostředků Azure NetApp Files  

Tento postup předpokládá, že jste už nasadili [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Prostředky Azure NetApp Files a virtuální počítače, kde budou připojené prostředky Azure NetApp Files, musí být nasazené ve stejné službě Azure Virtual Network nebo ve virtuálních sítích Azure s partnerským vztahem.  

1. Pokud jste to ještě neudělali, požádejte o [registraci Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Vytvořte účet NetApp ve vybrané oblasti Azure a postupujte podle [pokynů k vytvoření účtu NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md).  
3. Nastavte Azure NetApp Files fond kapacit podle [pokynů, jak nastavit fond kapacit Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
Architektura SAP NetWeaver uvedená v tomto článku používá fond kapacit s jednou Azure NetApp Files, SKU úrovně Premium. Pro úlohy aplikace SAP NetWeaver v Azure doporučujeme Azure NetApp Files Premium SKU.  

4. Přenesete podsíť do souborů Azure NetApp, jak je popsáno v [pokynech delegování podsítě na Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Nasaďte Azure NetApp Files svazky podle [pokynů pro vytvoření svazku pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Nasaďte svazky v určené Azure NetApp Files [podsíti](/rest/api/virtualnetwork/subnets). IP adresy svazků Azure NetApp se přiřazují automaticky. Mějte na paměti, že Azure NetApp Files prostředky a virtuální počítače Azure musí být ve stejném Virtual Network Azure nebo ve virtuálních sítích Azure s partnerským vztahem. V tomto příkladu používáme dva Azure NetApp Files svazky: SAP<b>QAS</b> a trans. Cesty k souborům, které jsou připojené k odpovídajícím přípojným bodům, jsou/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys atd.  

   1. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCS)
   3. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>olajících)
   5. Volume trans (nfs://10.1.0.4/trans)
   6. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. Volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)

   
V tomto příkladu jsme použili Azure NetApp Files pro všechny systémy souborů SAP NetWeaver k předvedení toho, jak se dá Azure NetApp Files použít. Systémy souborů SAP, které není nutné připojit přes systém souborů NFS, se dají nasadit taky jako [Azure Disk Storage](../../disks-types.md#premium-ssd) . V tomto příkladu musí být a <b>-e</b> Azure NetApp Files a <b>f-g</b> (tj./usr/SAP/<b>QAS</b>/d<b>02</b>,/usr/SAP/<b>QAS</b>/d<b>03</b>) se dají nasadit jako Azure Disk Storage. 

### <a name="important-considerations"></a>Důležité informace

Při zvažování Azure NetApp Files pro SAP NetWeaver v architektuře SUSE pro vysokou dostupnost mějte na paměti následující důležité informace:

- Minimální fond kapacit je 4 TiB. Velikost fondu kapacity se dá zvýšit v 1 přírůstcích TiB.
- Minimální objem je 100 GiB.
- Azure NetApp Files a všech virtuálních počítačů, kde se Azure NetApp Files svazky připojí, musí být ve stejné oblasti jako Azure Virtual Network nebo ve [virtuálních sítích s partnerským vztahem](../../../virtual-network/virtual-network-peering-overview.md) . V současné době se podporuje Azure NetApp Files přístup přes partnerský vztah virtuálních sítí ve stejné oblasti. Přístup k Azure NetApp přes globální partnerský vztah ještě není podporovaný.
- Vybraná virtuální síť musí mít podsíť, delegovanou na Azure NetApp Files.
- Azure NetApp Files nabízí [zásady exportu](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): můžete řídit povolené klienty, typ přístupu (čtení&zápisu, jen pro čtení atd.). 
- Azure NetApp Files funkce zatím nereaguje na zóny. Aktuálně Azure NetApp Files funkce není nasazená ve všech zónách dostupnosti v oblasti Azure. Mějte na paměti, že v některých oblastech Azure máte vliv na potenciální latenci. 
- Azure NetApp Files svazky lze nasadit jako svazky NFSv3 nebo NFSv 4.1. Pro aplikační vrstvu SAP (ASCS/OLAJÍCÍCH, aplikační servery SAP) se podporují oba protokoly. 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Ruční nasazení virtuálních počítačů se systémem Linux prostřednictvím Azure Portal

Nejprve je třeba vytvořit svazky Azure NetApp Files. Nasaďte virtuální počítače. Následně vytvoříte Nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondech back-endu.

1. Vytvoření skupiny prostředků
1. Vytvoření virtuální sítě
1. Vytvoření skupiny dostupnosti pro ASCS  
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 1  
   Použijte minimálně SLES4SAP 12 SP3. v tomto příkladu se používá bitová kopie SLES4SAP 12 SP3.  
   Vyberte skupinu dostupnosti vytvořenou dříve pro ASCS.  
1. Vytvořit virtuální počítač 2  
   Použijte minimálně SLES4SAP 12 SP3. v tomto příkladu se používá bitová kopie SLES4SAP 12 SP3.  
   Vyberte skupinu dostupnosti vytvořenou dříve pro ASCS.  
1. Vytvoření skupiny dostupnosti pro instance aplikace SAP (PAS, AAS)    
   Nastavit maximální aktualizační doménu
1. Vytvořit virtuální počítač 3  
   Použijte minimálně SLES4SAP 12 SP3. v tomto příkladu se používá bitová kopie SLES4SAP 12 SP3.  
   Vybrat skupinu dostupnosti vytvořenou dříve pro PAS/AAS   
1. Vytvořit virtuální počítač 4  
   Použijte minimálně SLES4SAP 12 SP3. v tomto příkladu se používá bitová kopie SLES4SAP 12 SP3.  
   Vybrat skupinu dostupnosti vytvořenou dříve pro PAS/AAS  

## <a name="disable-id-mapping-if-using-nfsv41"></a>Zakázat mapování ID (Pokud používáte NFSv 4.1)

Pokyny v této části se použijí jenom v případě, že používáte Azure NetApp Files svazky s protokolem NFSv 4.1. Proveďte konfiguraci na všech virtuálních počítačích, kde budou připojené svazky Azure NetApp Files NFSv 4.1.  

1. Ověřte nastavení domény systému souborů NFS. Ujistěte se, že je doména nakonfigurovaná jako výchozí doména Azure NetApp Files, tj. **`defaultv4iddomain.com`** a mapování je nastavené na **nikdo**.  

    > [!IMPORTANT]
    > Ujistěte se, že jste na virtuálním počítači nastavili doménu systému souborů NFS, `/etc/idmapd.conf` aby odpovídala výchozí konfiguraci domény v Azure NetApp Files: **`defaultv4iddomain.com`** . Pokud dojde k neshodě mezi konfigurací domény v klientovi NFS (tj. virtuálním počítačem) a serverem NFS, tj. konfigurací Azure NetApp, pak se budou zobrazovat oprávnění k souborům na svazcích Azure NetApp, které jsou připojené k virtuálním počítačům `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** ověřte `nfs4_disable_idmapping` . Měl by být nastaven na **Y**. Pokud chcete vytvořit adresářovou strukturu `nfs4_disable_idmapping` , kde se nachází, spusťte příkaz Mount. V/sys/modules nebudete moct ručně vytvořit adresář, protože přístup je vyhrazený pro jádro nebo ovladače.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Nastavení (A) SCS

V tomto příkladu byly prostředky nasazeny ručně prostřednictvím [Azure Portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Ruční nasazení Azure Load Balancer přes Azure Portal

Nejprve je třeba vytvořit svazky Azure NetApp Files. Nasaďte virtuální počítače. Následně vytvoříte Nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondu back-end.

1. Vytvořit nástroj pro vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 10.1.1.20 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **front-end. QAS. ASCS**)
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.1.1.20**).
         1. Klikněte na OK.
      1. 10.1.1.21 IP adres pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky v části a a vytvořte tak IP adresu pro OLAJÍCÍCH (například **10.1.1.21** a **front-end. QAS. OLAJÍCÍCH**)
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **back-end. QAS**)
      1. Klikněte na Přidat virtuální počítač.
      1. Vybrat virtuální počítač
      1. Vyberte virtuální počítače v clusteru (A) SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **stav). QAS. ASCS**)
         1. Vybrat TCP as Protocol, port 620**00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621**01** pro ASCS olajících
            * Opakujte výše uvedené kroky v části "c", chcete-li vytvořit sondu stavu pro OLAJÍCÍCH (například 621**01** a **stav). QAS. OLAJÍCÍCH**)
   1. Pravidla vyrovnávání zatížení
      1. Vytvoření fondu back-endu pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **kg. QAS. ASCS**)
         1. Vyberte front-end IP adresu pro ASCS, fond back-endu a test stavu, který jste vytvořili dříve (například **front-end. QAS. ASCS**, **back-end. QAS** a **stav. QAS. ASCS**)
         1. Vybrat **porty ha**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
         * Opakujte výše uvedené kroky a vytvořte tak pravidla vyrovnávání zatížení pro OLAJÍCÍCH (například **kg. QAS. OLAJÍCÍCH**)
1. Případně, pokud váš scénář vyžaduje základní nástroj pro vyrovnávání zatížení (interní), postupujte podle následujících kroků:  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 10.1.1.20 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **front-end. QAS. ASCS**)
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **10.1.1.20**).
         1. Klikněte na OK.
      1. 10.1.1.21 IP adres pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky v části a a vytvořte tak IP adresu pro OLAJÍCÍCH (například **10.1.1.21** a **front-end. QAS. OLAJÍCÍCH**)
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **back-end. QAS**)
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte skupinu dostupnosti, kterou jste vytvořili dříve pro ASCS. 
      1. Vyberte virtuální počítače v clusteru (A) SCS.
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620**00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **stav). QAS. ASCS**)
         1. Vybrat TCP as Protocol, port 620**00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621**01** pro ASCS olajících
            * Opakujte výše uvedené kroky v části "c", chcete-li vytvořit sondu stavu pro OLAJÍCÍCH (například 621**01** a **stav). QAS. OLAJÍCÍCH**)
   1. Pravidla vyrovnávání zatížení
      1. 32**00** TCP pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **kg. QAS. ASCS. 3200**)
         1. Vyberte front-end IP adresu pro ASCS, fond back-endu a test stavu, který jste vytvořili dříve (například **front-end. QAS. ASCS**)
         1. Zachovejte protokol **TCP**, zadejte port **3200**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky v části "d" pro porty**36 00**,**39 00**, 81**00**, 5**00**13, 5**00**14, 5**00**16 a TCP pro ASCS
      1. Další porty pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky v části "d" pro porty 32**01**, 33**01**, 5**01**13, 5**01**14, 5**01**16 a TCP pro ASCS olajících

      
      > [!IMPORTANT]
      > Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

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

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** aktualizace agentů prostředků SAP  
   
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

3. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.   

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

4. **[1]** vytvořte adresáře SAP ve svazku Azure NetApp Files.  
   Dočasně připojte Azure NetApp Files svazek na jeden z virtuálních počítačů a vytvořte adresáře SAP (cesty k souborům).  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntQAS
    sudo mkdir -p usrsapQASascs
    sudo mkdir -p usrsapQASers
    sudo mkdir -p usrsapQASsys
    sudo mkdir -p usrsapQASpas
    sudo mkdir -p usrsapQASaas
    # unmount the volume and delete the temporary directory
    sudo cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

## <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci SAP NetWeaver

1. **[A]** vytvoření sdílených adresářů

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

2. **[A]** konfigurace `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Pokud používáte NFSv3, vytvořte soubor pomocí:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   Pokud používáte NFSv 4.1, vytvořte soubor pomocí:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Při připojování svazků nezapomeňte odpovídat verzi protokolu NFS Azure NetApp Files svazků. Pokud se Azure NetApp Files svazky vytvoří jako svazky NFSv3, použijte odpovídající konfiguraci NFSv3. Pokud se Azure NetApp Files svazky vytvoří jako svazky NFSv 4.1, podle pokynů zakažte mapování ID a ujistěte se, že používáte odpovídající konfiguraci NFSv 4.1. V tomto příkladu se Azure NetApp Files svazky vytvořily jako NFSv3 svazky.  
   
   Restartováním `autofs` připojíte nové sdílené složky.
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** konfigurace odkládacího souboru

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

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** instalace SAP NetWeaver ASCS  

   Nainstalujte SAP NetWeaver ASCS jako kořen v prvním uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro ASCS, například <b>anftstsapvh</b>, <b>10.1.1.20</b> a číslo instance, kterou jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst. Pomocí parametru SAPINST_USE_HOSTNAME můžete nainstalovat SAP pomocí virtuálního hostitele.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**QAS**/ASCS**00**, zkuste nastavit vlastníka a skupinu složky ASCS**00**  a zkuste to znovu. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci olajících

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** instalace SAP NetWeaver olajících

   Nainstalujte SAP NetWeaver OLAJÍCÍCH jako kořenovou složku na druhém uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro OLAJÍCÍCH, například <b>anftstsapers</b>, <b>10.1.1.21</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>01</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst. Pomocí parametru SAPINST_USE_HOSTNAME můžete nainstalovat SAP pomocí virtuálního hostitele.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Použijte SWPM SP 20 PL 05 nebo vyšší. Nižší verze nenastaví správně oprávnění a instalace se nezdaří.

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**QAS**/ERS**01**, zkuste nastavit vlastníka a skupinu složky olajících**01** a zkuste to znovu.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** přizpůsobení profilů instancí ASCS/SCS a olajících
 
   * Profil ASCS/SCS

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
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

6. **[A]** konfigurace Keep Alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím nástroje pro vyrovnávání zatížení softwaru. Nástroj pro vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém limitu. Abyste tomu předešli, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS, pokud používáte ENSA1, a změnit nastavení systému Linux `keepalive` na všech serverech SAP pro ENSA1/ENSA2. Další informace najdete v tématu [SAP Note 1410736][1410736] .

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

7. **[A]** konfigurace uživatelů SAP po instalaci

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** přidejte do souboru služby SAP ASCS a olajících. `sapservice`

   Přidejte položku služby ASCS do druhého uzlu a zkopírujte položku služby OLAJÍCÍCH do prvního uzlu.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** vytvoření prostředků clusteru SAP

Pokud používáte architekturu serveru front Server 1 (ENSA1), definujte prostředky následujícím způsobem:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
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

   SAP představilo podporu pro front-Server 2, včetně replikace, od SAP NW 7,52. Počínaje platformou ABAP 1809 se ve výchozím nastavení nainstaluje služba fronty serveru 2. Podporu služby zařazení serveru 2 pro frontu najdete v tématu SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
Pokud používáte architekturu serveru fronty 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), definujte prostředky následujícím způsobem:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Pokud provádíte upgrade ze starší verze a přejdete na server fronty 2, přečtěte si článek SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver 

Některé databáze vyžadují, aby se instalace instance databáze spustila na aplikačním serveru. Příprava virtuálních počítačů aplikačního serveru, aby je bylo možné používat v těchto případech.

Postup níže předpokládá, že instalujete aplikační server na jiný server než servery ASCS/SCS a HANA. Jinak se některé z následujících kroků (třeba konfigurace překladu názvů hostitelů) nevyžadují.

Následující položky jsou s předponou buď **[A]** – platí pro pas i AAS, **[P]** – platí jenom pro pas nebo **[S]** – platí jenom pro AAS.


1. **[A]** konfigurace operačního systému

   Snižte velikost nečisté mezipaměti. Další informace najdete v tématu [nízký výkon zápisu na serverech SLES 11/12 s velkou pamětí RAM](https://www.suse.com/support/kb/doc/?id=7010287).

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

   ```bash
   sudo vi /etc/hosts
   ```

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** vytvoření adresáře sapmnt

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** vytvořit adresář pas

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** vytvořit adresář AAS

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** konfigurace `autofs` na pas

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Pokud používáte NFSv3, vytvořte nový soubor s:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Pokud používáte NFSv 4.1, vytvořte nový soubor s tímto:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Restartováním `autofs` připojíte nové sdílené složky.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** konfigurace `autofs` v AAS

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Pokud používáte NFSv3, vytvořte nový soubor s:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Pokud používáte NFSv 4.1, vytvořte nový soubor s tímto:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Restartováním `autofs` připojíte nové sdílené složky.

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** konfigurace odkládacího souboru

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

   Restartujte agenta, aby se změna aktivovala.

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalovaný na SAP HANA. Pro tuto instalaci můžete použít každou podporovanou databázi. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v tématu [Vysoká dostupnost SAP HANA na Azure Virtual Machines (virtuálních počítačích)][sap-hana-ha]. Seznam podporovaných databází najdete v tématu [SAP Note 1928533][1928533].

* Spusťte instalaci instance databáze SAP.

   Nainstalujte instanci databáze SAP NetWeaver jako kořenovou složku pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro databázi.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Pomocí těchto kroků nainstalujete aplikační Server SAP.

1. **[A]** Připravte aplikační server podle kroků v kapitole [SAP NetWeaver Application Server Preparation Preparation](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) (příprava aplikačního serveru).

2. **[A]** instalace aplikačního serveru SAP NetWeaver instalace primárního nebo dalšího serveru aplikace SAP NetWeaver.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** aktualizace SAP HANA zabezpečené úložiště

   Aktualizujte SAP HANA zabezpečené úložiště tak, aby odkazovalo na virtuální název nastavení replikace SAP HANA systému.

   Chcete-li zobrazit seznam položek, spusťte následující příkaz.
   <pre><code>
   hdbuserstore List
   </code></pre>

   Mělo by se zobrazit seznam všech položek, které by měly vypadat podobně jako
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   Výstup ukazuje, že IP adresa výchozí položky odkazuje na virtuální počítač, a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka musí být změněna tak, aby odkazovala na virtuální název hostitele nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše) a název databáze (**QAS** ve výstupu výše).

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

Následující testy jsou kopie testovacích případů v [SUSE průvodců osvědčenými postupy][suse-ha-guide]. Budou zkopírovány pro usnadnění práce. Vždy si přečtěte Příručky k osvědčeným postupům a proveďte všechny další testy, které mohou být přidány.

1. Test HAGetFailoverConfig, HACheckConfig a HACheckFailoverConfig

   \<sapsid>Na uzlu, kde je aktuálně spuštěná instance ASCS, spusťte následující příkazy jako ADM. Pokud se příkazy nezdaří s CHYBou: nedostatek paměti, může to být způsobeno pomlčkami ve vašem názvu hostitele. Jedná se o známý problém, který bude opraven nástrojem SUSE v balíčku SAP-SUSE-cluster-Connector.

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

2. Ruční migrace instance ASCS

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Pro migraci instance ASCS spusťte následující příkazy jako kořen.

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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. HAFailoverToNode testu

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   \<sapsid>Pro migraci instance ASCS spusťte následující příkazy jako ADM.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Simulace havárie uzlu 

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spusťte následující příkaz jako kořen v uzlu, ve kterém je spuštěná instance ASCS.

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Pokud používáte SBD, Pacemaker by se neměl automaticky spustit na ukončeném uzlu. Stav po spuštění uzlu by měl vypadat takto.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Pomocí následujících příkazů spusťte Pacemaker na ukončeném uzlu, vyčistěte zprávy SBD a vyčistěte prostředky, které selhaly.

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
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Test ručního restartování instance ASCS

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Vytvořte zámek zařazení do fronty, například upravit uživatele v su01 transakce. Spusťte následující příkazy jako <sapsid \> ADM na uzlu, ve kterém je spuštěná instance ASCS. Příkazy zazastaví instanci ASCS a znovu se spustí. Pokud používáte architekturu serveru fronty 1, očekává se, že se v tomto testu ztratí zámek fronty. Pokud používáte architekturu Server 2 pro zařazování do fronty, zachová se. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Instance ASCS by teď měla být v Pacemaker zakázaná.

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Znovu spusťte instanci ASCS na stejném uzlu.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   Zámek zařazení do fronty pro transakce su01 by měl být ztracen, pokud používáte architekturu replikace serveru fronty 1 a back-end by měl být obnoven. Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Ukončit proces serveru zprávy

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spuštěním následujících příkazů jako kořenového adresáře Identifikujte proces serveru a potom ho ukončete.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Pokud server pouze jednou zadáte, bude restartován nástrojem `sapstart` . Pokud jste ho ASCS dostatečně přesunuli, Pacemaker se nakonec přesune instance na jiný uzel. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Proces serveru dezaktivačního zařazení do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu, ve kterém je spuštěná instance ASCS, aby se mohl ukončit server fronty.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Instance ASCS by měla okamžitě převzít služby na jiný uzel. Instance OLAJÍCÍCH by také měla převzít služby při selhání po spuštění instance ASCS. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Proces replikace replikačního serveru do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spusťte následující příkaz jako kořenový adresář v uzlu, ve kterém je spuštěná instance OLAJÍCÍCH, čímž se ukončí proces serveru replikace ve frontě.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Pokud příkaz spouštíte pouze jednou, `sapstart` proces se restartuje. Pokud je spuštěno dostatečně často, `sapstart` proces nebude restartován a prostředek bude zastaven. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředku instance OLAJÍCÍCH po testu.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Ukončit proces sapstartsrv zařazování do fronty

   Stav prostředku před spuštěním testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Spusťte následující příkazy jako kořen v uzlu, kde je spuštěný ASCS.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Proces sapstartsrv by měl být vždy restartován agentem prostředků Pacemaker. Stav prostředku po testu:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Další kroky

* [HA pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP – příručka pro multi-SID](./high-availability-guide-suse-multi-sid.md)
* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
