---
title: Virtuální počítače Azure s vysokou dostupností pro SAP NW v RHEL s využitím Azure NetApp Files | Microsoft Docs
description: Navažte vysokou dostupnost pro SAP NW na virtuálních počítačích Azure (VM) RHEL s Azure NetApp Files.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: 8fa51bec1918b8dce99c80a61da0160c9650d5e4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116088"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

Tento článek popisuje, jak nasadit virtuální počítače, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7,50 pomocí [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
V ukázkových konfiguracích instalační příkazy atd. Instance ASCS je číslo 00, instance OLAJÍCÍCH je číslo 01, primární instance aplikace (PAS) je 02 a instance aplikace (AAS) je 03. Používá se ID systému SAP QAS. 

Tato vrstva databáze není podrobněji popsána v tomto článku.  

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* [Dokumentace k Azure NetApp Files][anf-azure-doc] 
* Poznámka [1928533]pro SAP obsahuje:
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure

* SAP Note [2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Poznámka SAP Poznámka [2002167] obsahuje doporučená nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP Poznámka [2009879] obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux
* Pro SAP Note [2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V případě SAP Poznámka [2191498] je požadovaná verze agenta hostitele SAP pro Linux v Azure.
* Poznámka SAP Poznámka [2243692] obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure.
* V části SAP Note [1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* [Komunitní komunita SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) má všechny požadované poznámky SAP pro Linux.
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [SAP NetWeaver v clusteru Pacemaker](https://access.redhat.com/articles/3150081)
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Konfigurace ASCS/OLAJÍCÍCH pro SAP NetWeaver se samostatnými prostředky v RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [Konfigurace SAP S/4HANA ASCS/OLAJÍCÍCH se samostatným serverem fronty 2 (ENSA2) v Pacemaker v RHEL ](https://access.redhat.com/articles/3974941)
* Dokumentace k RHEL specifické pro Azure:
  * [Zásady podpory pro RHEL clustery s vysokou dostupností – Microsoft Azure Virtual Machines jako členové clusteru](https://access.redhat.com/articles/3131341)
  * [Instalace a konfigurace Red Hat Enterprise Linux 7,4 (a novější) High-Availability clusteru v Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Vysoká dostupnost (HA) pro služby SAP NetWeaver Central Services vyžaduje sdílené úložiště.
Aby bylo možné v systému Red Hat Linux vytvořit samostatný cluster GlusterFS s vysokou dostupností, byl proto nezbytný. 

Nyní je možné dosáhnout dostupnosti SAP NetWeaver HA pomocí sdíleného úložiště nasazeného v Azure NetApp Files. Použití Azure NetApp Files pro sdílené úložiště eliminuje nutnost dalšího [clusteru GlusterFS](./high-availability-guide-rhel-glusterfs.md). Pacemaker je stále potřeba pro vysokou dostupnost centrálních služeb SAP NetWeaver (ASCS/SCS).

![Přehled vysoké dostupnosti SAP NetWeaver](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver OLAJÍCÍCH a databáze SAP HANA používají virtuální název hostitele a virtuální IP adresy. V Azure se nástroj pro vyrovnávání zatížení vyžaduje k použití virtuální IP adresy. Doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). Následující seznam uvádí konfiguraci nástroje pro vyrovnávání zatížení s oddělenými IP adresami front-endu pro (A) SCS a OLAJÍCÍCH.

### <a name="ascs"></a>Určitého SCS

* Konfigurace front-endu
  * 192.168.14.9 IP adres
* Port testu paměti
  * Port 620<strong> &lt; Nr &gt; </strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
  * 32<strong> &lt; Nr &gt; </strong> TCP
  * 36<strong> &lt; Nr &gt; </strong> TCP
  * 39<strong> &lt; Nr &gt; </strong> TCP
  * 81<strong> &lt; Nr &gt; </strong> TCP
  * 5<strong> &lt; Nr &gt; </strong>13 TCP
  * 5<strong> &lt; Nr &gt; </strong>14 TCP
  * 5<strong>. &lt; 16 TCP &gt; </strong>

### <a name="ers"></a>OLAJÍCÍCH

* Konfigurace front-endu
  * 192.168.14.10 IP adres
* Port testu paměti
  * Port 621<strong> &lt; Nr &gt; </strong>
* Pravidla vyrovnávání zatížení
  * Pokud používáte Standard Load Balancer, vyberte **porty ha** .
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

5. Nasaďte Azure NetApp Files svazky podle [pokynů pro vytvoření svazku pro Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Nasaďte svazky v určené Azure NetApp Files [podsíti](/rest/api/virtualnetwork/subnets). IP adresy svazků Azure NetApp se přiřazují automaticky. Mějte na paměti, že Azure NetApp Files prostředky a virtuální počítače Azure musí být ve stejném Virtual Network Azure nebo ve virtuálních sítích Azure s partnerským vztahem. V tomto příkladu používáme dva Azure NetApp Files svazky: SAP<b>QAS</b> a transSAP. Cesty k souborům, které jsou připojené k odpovídajícím přípojným bodům, jsou/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys atd.  

   1. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ASCS)
   3. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>olajících)
   5. Volume transSAP (nfs://192.168.24.4/transSAP)
   6. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. Volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>AAS)
  
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

## <a name="setting-up-ascs"></a>Nastavení (A) SCS

V tomto příkladu byly prostředky nasazeny ručně prostřednictvím [Azure Portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Ruční nasazení Linux pomocí Azure Portal

Nejprve je třeba vytvořit svazky Azure NetApp Files. Nasaďte virtuální počítače. Následně vytvoříte Nástroj pro vyrovnávání zatížení a použijete virtuální počítače ve fondu back-end.

1. Vytvořit nástroj pro vyrovnávání zatížení (interní, standardní):  
   1. Vytvoření IP adresy front-endu
      1. IP adresa 192.168.14.9 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **front-end. QAS. ASCS**)
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **192.168.14.9**).
         1. Klikněte na OK.
      1. 192.168.14.10 IP adres pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky v části a a vytvořte tak IP adresu pro OLAJÍCÍCH (například **192.168.14.10** a **front-end. QAS. OLAJÍCÍCH**)
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **back-end. QAS**)
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte Virtuální počítač. 
      1. Vyberte virtuální počítače v clusteru (A) SCS a jejich IP adresy.
      1. Klikněte na tlačítko Přidat.
   1. Vytvoření sond stavu
      1. Port 620 **00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **stav). QAS. ASCS**)
         1. Vybrat TCP as Protocol, port 620 **00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621 **01** pro ASCS olajících
            * Opakujte výše uvedené kroky v části "c", chcete-li vytvořit sondu stavu pro OLAJÍCÍCH (například 621 **01** a **stav). QAS. OLAJÍCÍCH**)
   1. Pravidla vyrovnávání zatížení
      1. Pravidla vyrovnávání zatížení pro ASCS
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
      1. IP adresa 192.168.14.9 pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte front-end IP fond a klikněte na Přidat.
         1. Zadejte název nového fondu IP adres front-endu (například **front-end. QAS. ASCS**)
         1. Nastavte přiřazení na statické a zadejte IP adresu (například **192.168.14.9**).
         1. Klikněte na OK.
      1. 192.168.14.10 IP adres pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky v části a a vytvořte tak IP adresu pro OLAJÍCÍCH (například **192.168.14.10** a **front-end. QAS. OLAJÍCÍCH**)
   1. Vytvoření back-endového fondu
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte fondy back-endu a klikněte na Přidat.
      1. Zadejte název nového back-end fondu (například **back-end. QAS**)
      1. Klikněte na Přidat virtuální počítač.
      1. Vyberte skupinu dostupnosti, kterou jste vytvořili dříve pro ASCS. 
      1. Vyberte virtuální počítače v clusteru (A) SCS.
      1. Klikněte na OK.
   1. Vytvoření sond stavu
      1. Port 620 **00** pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte sondy stavu a klikněte na Přidat.
         1. Zadejte název nového testu stavu (například **stav). QAS. ASCS**)
         1. Vybrat TCP as Protocol, port 620 **00**, zachovat interval 5 a špatný práh 2
         1. Klikněte na OK.
      1. Port 621 **01** pro ASCS olajících
            * Opakujte výše uvedené kroky v části "c", chcete-li vytvořit sondu stavu pro OLAJÍCÍCH (například 621 **01** a **stav). QAS. OLAJÍCÍCH**)
   1. Pravidla vyrovnávání zatížení
      1. 32 **00** TCP pro ASCS
         1. Otevřete nástroj pro vyrovnávání zatížení, vyberte pravidla vyrovnávání zatížení a klikněte na Přidat.
         1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **kg. QAS. ASCS. 3200**)
         1. Vyberte front-end IP adresu pro ASCS, fond back-endu a test stavu, který jste vytvořili dříve (například **front-end. QAS. ASCS**)
         1. Zachovejte protokol **TCP**, zadejte port **3200**
         1. Prodloužit časový limit nečinnosti na 30 minut
         1. **Ujistěte se, že jste povolili plovoucí IP adresu.**
         1. Klikněte na OK.
      1. Další porty pro ASCS
         * Opakujte výše uvedené kroky v části "d" pro porty **36 00**,**39 00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 a TCP pro ASCS
      1. Další porty pro ASCS OLAJÍCÍCH
         * Opakujte výše uvedené kroky v části "d" pro porty 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 a TCP pro ASCS olajících

      > [!IMPORTANT]
      > Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.  

      > [!Note]
      > Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  

      > [!IMPORTANT]
      > Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).

## <a name="disable-id-mapping-if-using-nfsv41"></a>Zakázat mapování ID (Pokud používáte NFSv 4.1)

Pokyny v této části se použijí jenom v případě, že používáte Azure NetApp Files svazky s protokolem NFSv 4.1. Proveďte konfiguraci na všech virtuálních počítačích, kde budou připojené svazky Azure NetApp Files NFSv 4.1.  

1. Ověřte nastavení domény systému souborů NFS. Ujistěte se, že je doména nakonfigurovaná jako výchozí doména Azure NetApp Files, tj. **`defaultv4iddomain.com`** a mapování je nastavené na **nikdo**.  

    > [!IMPORTANT]
    > Ujistěte se, že jste na virtuálním počítači nastavili doménu systému souborů NFS, `/etc/idmapd.conf` aby odpovídala výchozí konfiguraci domény v Azure NetApp Files: **`defaultv4iddomain.com`** . Pokud dojde k neshodě mezi konfigurací domény v klientovi NFS (tj. virtuálním počítačem) a serverem NFS, tj. konfigurací Azure NetApp, pak se budou zobrazovat oprávnění k souborům na svazcích Azure NetApp, které jsou připojené k virtuálním počítačům `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
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
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Další podrobnosti o tom, jak změnit `nfs4_disable_idmapping` parametr, naleznete v tématu https://access.redhat.com/solutions/1749883 .

### <a name="create-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte pro tento server (a) SCS základní cluster Pacemaker.

### <a name="prepare-for-sap-netweaver-installation"></a>Příprava na instalaci SAP NetWeaver

Následující položky jsou předpony buď **[A]** – platí pro všechny uzly, **[1]** – platí pouze pro uzel 1 nebo **[2]** – platí pouze pro uzel 2.

1. **[A]** nastavení rozlišení názvu hostitele

   Můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele.

    ```
    sudo vi /etc/hosts
    ```

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** vytvořte adresáře SAP ve svazku Azure NetApp Files.  
   Dočasně připojte Azure NetApp Files svazek na jeden z virtuálních počítačů a vytvořte adresáře SAP (cesty k souborům).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
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

1. **[A]** vytvoření sdílených adresářů

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** nainstalovat klienta systému souborů NFS a další požadavky

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Podívejte se na verzi prostředků-Agents-SAP

   Zajistěte, aby byla verze nainstalovaného balíčku Resource-Agents-SAP aspoň 3.9.5 -124. el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** přidat položky připojení

   Pokud používáte NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Pokud používáte NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Při připojování svazků nezapomeňte odpovídat verzi protokolu NFS Azure NetApp Files svazků. Pokud se Azure NetApp Files svazky vytvoří jako svazky NFSv3, použijte odpovídající konfiguraci NFSv3. Pokud se Azure NetApp Files svazky vytvoří jako svazky NFSv 4.1, podle pokynů zakažte mapování ID a ujistěte se, že používáte odpovídající konfiguraci NFSv 4.1. V tomto příkladu se Azure NetApp Files svazky vytvořily jako NFSv3 svazky.  

   Připojit nové sdílené složky

   ```
   sudo mount -a  
   ```

1. **[A]** konfigurace odkládacího souboru

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restartujte agenta, aby se změna aktivovala.

   ```
   sudo service waagent restart
   ```

1. **[A]** konfigurace RHEL

   Nakonfigurujte RHEL podle popisu v tématu SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>Instalace SAP NetWeaver ASCS/OLAJÍCÍCH

1. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci ASCS

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** instalace SAP NetWeaver ASCS  

   Nainstalujte SAP NetWeaver ASCS jako kořen v prvním uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro ASCS, například <b>anftstsapvh</b>, <b>192.168.14.9</b> a číslo instance, kterou jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>00</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**QAS**/ASCS **00**, zkuste nastavit vlastníka a skupinu složky ASCS **00** a zkuste to znovu.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** vytvoření prostředku virtuální IP adresy a stavu – sonda pro instanci olajících

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** instalace SAP NetWeaver olajících  

   Nainstalujte SAP NetWeaver OLAJÍCÍCH jako kořenovou složku na druhém uzlu pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu služby Vyrovnávání zatížení pro OLAJÍCÍCH, například <b>anftstsapers</b>, <b>192.168.14.10</b> a číslo instance, které jste použili pro sondu nástroje pro vyrovnávání zatížení, například <b>01</b>.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Pokud se při instalaci nepovede vytvořit podsložku v/usr/SAP/**QAS**/ERS **01**, zkuste nastavit vlastníka a skupinu složky olajících **01** a zkuste to znovu.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** přizpůsobení profilů instancí ASCS/SCS a olajících

   * Profil ASCS/SCS

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Pro ENSA1 i ENSA2 se ujistěte, že `keepalive` jsou nastavené parametry operačního systému, jak je popsáno v tématu SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * Profil OLAJÍCÍCH

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** konfigurace Keep Alive

   Komunikace mezi aplikačním serverem SAP NetWeaver a ASCS/SCS je směrována prostřednictvím nástroje pro vyrovnávání zatížení softwaru. Nástroj pro vyrovnávání zatížení odpojí neaktivní připojení po konfigurovatelném časovém limitu. Abyste tomu předešli, musíte nastavit parametr v profilu SAP NetWeaver ASCS/SCS, pokud používáte ENSA1, a změnit nastavení systému Linux `keepalive` na všech serverech SAP pro ENSA1/ENSA2. Další informace najdete v tématu [SAP Note 1410736][1410736] .

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** aktualizace souboru/usr/SAP/sapservices

   Aby se zabránilo spuštění instancí pomocí spouštěcího skriptu sapinit, musí být všechny instance spravované pomocí Pacemaker odkomentovatelné ze souboru/usr/SAP/sapservices. Nekomentovat instanci SAP HANA, pokud se použije s HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** vytvoření prostředků clusteru SAP

   Pokud používáte architekturu serveru front Server 1 (ENSA1), definujte prostředky následujícím způsobem:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP představilo podporu pro front-Server 2, včetně replikace, od SAP NW 7,52. Počínaje platformou ABAP 1809 se ve výchozím nastavení nainstaluje služba fronty serveru 2. Podporu služby zařazení serveru 2 pro frontu najdete v tématu SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) .
   Pokud používáte architekturu Server 2 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), nainstalujte agenta prostředků – agenti-SAP-4.1.1-12.el7.x86_64 nebo novější a definujte prostředky následujícím způsobem:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Pokud provádíte upgrade ze starší verze a přejdete na server fronty 2, přečtěte si článek SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > Časové limity ve výše uvedené konfiguraci jsou jenom příklady a možná je budete muset přizpůsobit konkrétnímu nastavení SAP. 

   Ujistěte se, že stav clusteru je OK a že všechny prostředky jsou spuštěné. Není důležité, na kterém uzlu jsou prostředky spuštěné.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** přidejte pravidla brány firewall pro ASCS a olajících v obou uzlech přidejte pravidla brány firewall pro ASCS a olajících na obou uzlech.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3201/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Příprava aplikačního serveru SAP NetWeaver

   Některé databáze vyžadují, aby se instalace instance databáze spustila na aplikačním serveru. Příprava virtuálních počítačů aplikačního serveru, aby je bylo možné používat v těchto případech.  

   Postup níže předpokládá, že instalujete aplikační server na jiný server než servery ASCS/SCS a HANA. Jinak se některé z následujících kroků (třeba konfigurace překladu názvů hostitelů) nevyžadují.  

   Následující položky jsou s předponou buď **[A]** – platí pro pas i AAS, **[P]** – platí jenom pro pas nebo **[S]** – platí jenom pro AAS.  

1. **[A]** nastavení rozlišení názvu hostitele můžete buď použít server DNS, nebo upravit/etc/hosts na všech uzlech. Tento příklad ukazuje, jak použít soubor/etc/hosts.
   V následujících příkazech nahraďte IP adresu a název hostitele:  

   ```
   sudo vi /etc/hosts
   ```

   Vložte následující řádky do/etc/hosts. Změňte IP adresu a název hostitele tak, aby odpovídaly vašemu prostředí.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** vytvoření adresáře sapmnt vytvořte adresář sapmnt.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** nainstalovat klienta systému souborů NFS a další požadavky  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** přidat položky připojení  
   Pokud používáte NFSv3:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Pokud používáte NFSv 4.1:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   Připojit nové sdílené složky

   ```
   sudo mount -a
   ```

1. **[P]** vytvoření a připojení adresáře pas  
   Pokud používáte NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Pokud používáte NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** vytvořte a připojte adresář AAS  
   Pokud používáte NFSv3:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Pokud používáte NFSv 4.1:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** konfigurace odkládacího souboru
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   Restartujte agenta, aby se změna aktivovala.

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Instalace databáze

V tomto příkladu je SAP NetWeaver nainstalovaný na SAP HANA. Pro tuto instalaci můžete použít každou podporovanou databázi. Další informace o tom, jak nainstalovat SAP HANA v Azure, najdete v tématu [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. Spusťte instalaci instance databáze SAP.

   Nainstalujte instanci databáze SAP NetWeaver jako kořenovou složku pomocí virtuálního hostitele, který se mapuje na IP adresu konfigurace front-endu nástroje pro vyrovnávání zatížení pro databázi.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Instalace aplikačního serveru SAP NetWeaver

Pomocí těchto kroků nainstalujete aplikační Server SAP.

1. Příprava aplikačního serveru

   K přípravě aplikačního serveru použijte postup v části [Příprava aplikačního serveru SAP NetWeaver](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) .

1. Instalace aplikačního serveru SAP NetWeaver

   Nainstalujte primární nebo další server aplikace SAP NetWeaver.

   Pomocí parametru sapinst SAPINST_REMOTE_ACCESS_USER můžete pro uživatele, který není rootem, připojovat se k sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Aktualizace zabezpečeného úložiště SAP HANA

   Aktualizujte SAP HANA zabezpečené úložiště tak, aby odkazovalo na virtuální název nastavení replikace SAP HANA systému.

   Spusťte následující příkaz, který vypíše položky jako \<sapsid> ADM.

   ```
   hdbuserstore List
   ```

   Mělo by se zobrazit seznam všech položek, které by měly vypadat podobně jako
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   Výstup ukazuje, že IP adresa výchozí položky odkazuje na virtuální počítač, a ne na IP adresu nástroje pro vyrovnávání zatížení. Tato položka musí být změněna tak, aby odkazovala na virtuální název hostitele nástroje pro vyrovnávání zatížení. Nezapomeňte použít stejný port (**30313** ve výstupu výše) a název databáze (**QAS** ve výstupu výše).

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>Otestování instalace clusteru

1. Ruční migrace instance ASCS

   Stav prostředku před spuštěním testu:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Pro migraci instance ASCS spusťte následující příkazy jako kořen.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav prostředku po testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Simulace havárie uzlu

   Stav prostředku před spuštěním testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Spusťte následující příkaz jako kořen v uzlu, ve kterém je spuštěná instance ASCS.

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   Stav po spuštění uzlu by měl vypadat takto.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   K vyčištění neúspěšných prostředků použijte následující příkaz.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav prostředku po testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Ukončit proces serveru zprávy

   Stav prostředku před spuštěním testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Spuštěním následujících příkazů jako kořenového adresáře Identifikujte proces serveru a potom ho ukončete.

   ```
   [root@anftstsapcl1 ~]# pgrep -f ms.sapQAS | xargs kill -9
   ```

   Pokud server pouze jednou zadáte, bude restartován nástrojem `sapstart` . Pokud jste ho ASCS dostatečně přesunuli, Pacemaker se nakonec přesune instance na jiný uzel. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav prostředku po testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Proces serveru dezaktivačního zařazení do fronty

   Stav prostředku před spuštěním testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Spusťte následující příkazy jako kořen v uzlu, ve kterém je spuštěná instance ASCS, aby se mohl ukončit server fronty.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f en.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enq.sapQAS | xargs kill -9
   ```

   Instance ASCS by měla okamžitě převzít služby na jiný uzel. Instance OLAJÍCÍCH by také měla převzít služby při selhání po spuštění instance ASCS. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředků instance ASCS a OLAJÍCÍCH po testu.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav prostředku po testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Proces replikace replikačního serveru do fronty

   Stav prostředku před spuštěním testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Spusťte následující příkaz jako kořenový adresář v uzlu, ve kterém je spuštěná instance OLAJÍCÍCH, čímž se ukončí proces serveru replikace ve frontě.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f er.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enqr.sapQAS | xargs kill -9
   ```

   Pokud příkaz spouštíte pouze jednou, `sapstart` proces se restartuje. Pokud je spuštěno dostatečně často, `sapstart` proces nebude restartován a prostředek bude zastaven. Spusťte následující příkazy jako kořen pro vyčištění stavu prostředku instance OLAJÍCÍCH po testu.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Stav prostředku po testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Ukončit proces sapstartsrv zařazování do fronty

   Stav prostředku před spuštěním testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Spusťte následující příkazy jako kořen v uzlu, kde je spuštěný ASCS.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Proces sapstartsrv by měl být v rámci monitorování vždy restartován agentem prostředků Pacemaker. Stav prostředku po testu:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Další kroky

* [HA pro SAP NW na virtuálních počítačích Azure v RHEL pro aplikace SAP – příručka pro multi-SID](./high-availability-guide-rhel-multi-sid.md)
* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA v Azure (velké instance), najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .