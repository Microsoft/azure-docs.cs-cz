---
title: Virtuální počítače Azure HA pro SAP NW ve Windows s protokolem SMB (Azure NetApp Files) | Microsoft Docs
description: Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure ve Windows pomocí protokolu SMB (Azure NetApp Files) pro aplikace SAP
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
ms.date: 08/12/2020
ms.author: radeltch
ms.openlocfilehash: 90b0ab4fdabd40e803d1f85a640e4cb387e40c44
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94958944"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure ve Windows pomocí protokolu SMB (Azure NetApp Files) pro aplikace SAP

[dbms-guide]:dbms-guide.md
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

Tento článek popisuje, jak nasadit, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7,50 na virtuální počítače s Windows pomocí [protokolu SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) v [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

Tato vrstva databáze není podrobněji popsána v tomto článku. Předpokládáme, že už je vytvořená [virtuální síť](../../../virtual-network/virtual-networks-overview.md) Azure.  

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* [Dokumentace k Azure NetApp Files][anf-azure-doc] 
* Poznámka [1928533][1928533]pro SAP obsahující:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows v Microsoft Azure
* SAP Note [2015553][2015553] uvádí požadavky na nasazení softwaru SAP podporovaná službou SAP v Azure.
* Pro SAP Note [2178632][2178632] najdete podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* V části SAP Note [1999351][1999351] najdete další informace o odstraňování potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140) uvádí předpoklady pro funkci certifikační autority podporované SAP protokolu SMB 3. x.
* Poznámka SAP Poznámka [2802770](https://launchpad.support.sap.com/#/notes/2802770) obsahuje informace o řešení potíží s pomalým spuštěním transakcí SAP AL11 ve Windows 2012 a 2016.
* V části SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507) najdete informace o transparentní funkci převzetí služeb při selhání pro sdílenou složku na Windows serveru s protokolem SMB 3,0.
* Poznámka [662452](https://launchpad.support.sap.com/#/notes/662452) pro SAP má doporučení (deaktivace generování názvu 8,3), aby se vyřešil špatný výkon systému souborů nebo chyby během přístupu k datům.
* [Instalace vysoké dostupnosti SAP NetWeaver v clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složce pro instance SAP ASCS/SCS v Azure](./sap-high-availability-installation-wsfc-file-share.md) 
* [Architektura a scénáře s vysokou dostupností pro Azure Virtual Machines pro SAP NetWeaver](./sap-high-availability-architecture-scenarios.md)
* [Přidat port sondy v konfiguraci clusteru ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalace instance SCS (A) do clusteru s podporou převzetí služeb při selhání](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Vytvoření svazku SMB pro službu Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

SAP vyvinul nový přístup a alternativu ke sdíleným diskům clusteru pro clustering instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows. Místo použití sdílených disků clusteru může jedna použít sdílenou složku SMB k nasazení souborů globálního hostitele SAP. Azure NetApp Files podporuje SMBv3 (společně se systémem souborů NFS) se seznamem řízení přístupu NTFS pomocí služby Active Directory. Azure NetApp Files je automaticky vysoce dostupný (jako je to služba PaaS). Tyto funkce umožňují Azure NetApp Files skvělou možnost pro hostování sdílené složky SMB pro globální prostředí SAP.  
Jsou podporovány jak [služby Azure Active Directory (AD) Domain Services](../../../active-directory-domain-services/overview.md) , tak [Active Directory Domain Services (služba AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) . Pomocí Azure NetApp Files můžete použít existující řadiče domény služby Active Directory. Řadiče domény můžou být v Azure jako virtuální počítače nebo místně prostřednictvím sítě VPN ExpressRoute nebo S2S. V tomto článku použijeme řadič domény na virtuálním počítači Azure.  
Vysoká dostupnost (HA) pro služby SAP NetWeaver Central Services vyžaduje sdílené úložiště. Aby bylo možné v systému Windows, bylo by proto bylo nutné sestavit buď cluster SOFS, nebo použít sdílené disky clusteru s/w jako s. Nyní je možné dosáhnout dostupnosti SAP NetWeaver HA pomocí sdíleného úložiště nasazeného v Azure NetApp Files. Použití Azure NetApp Files pro sdílené úložiště eliminuje nutnost buď SOFS, nebo s.  

> [!NOTE]
> Clustering SAP ASCS/SCS Instances pomocí sdílené složky se podporuje pro SAP NetWeaver 7,40 (a novější) pomocí SAP kernel 7,49 (a novější).  

![Architektura SAP ASCS/SCS HA se sdílenou složkou SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Požadavky pro sdílenou složku SMB:
* Protokol SMB 3,0 (nebo novější).
* Možnost nastavit seznamy řízení přístupu (ACL) služby Active Directory pro skupiny uživatelů služby Active Directory a objekt počítače $ počítač.
* Sdílená složka musí mít povolený HA.

Sdílenou složku SAP Central Services v této referenční architektuře nabízí Azure NetApp Files:

![Architektura SAP ASCS/SCS HA s podrobnostmi sdílené složky SMB](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Vytvoření a připojení svazku SMB pro Azure NetApp Files

Proveďte následující kroky, jako přípravu na použití Azure NetApp Files.  

1. Použijte postup k [registraci Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md)  
2. Vytvořte účet Azure NetApp podle kroků popsaných v tématu [Vytvoření účtu NetApp](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md) .  
3. Nastavte fond kapacit podle pokynů v části [nastavení fondu kapacity](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md) .
4. Azure NetApp Files prostředky se musí nacházet v delegované podsíti. Podle pokynů v části [delegování podsítě Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) vytvořit delegovanou podsíť.  

   > [!IMPORTANT]
   > Před vytvořením svazku SMB musíte vytvořit připojení ke službě Active Directory. Projděte si [požadavky na připojení ke službě Active Directory](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections).  

5. Vytvořte připojení ke službě Active Directory, jak je popsáno v tématu [vytvoření připojení ke službě Active Directory](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#create-an-active-directory-connection) .  
6. Vytvořte svazek SMB Azure NetApp Files SMB podle pokynů v části [Přidání svazku SMB](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume) .  
7. Připojte svazek SMB na virtuálním počítači s Windows.

> [!TIP]
> Pokyny, jak připojit Azure NetApp Files svazek, najdete v tématu na webu [Azure Portal](https://portal.azure.com/#home) , kde přejdete na objekt Azure NetApp Files, klikněte na okno **svazky** a pak na **připojit pokyny**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Příprava infrastruktury pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows 

1. [Nastavte pravidla vyrovnávání zatížení ASCS/SCS pro interní nástroj pro vyrovnávání zatížení Azure](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [Přidejte virtuální počítače s Windows do domény](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [Přidání položek registru na obou uzlech clusteru instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158)
4. [Nastavení clusteru s podporou převzetí služeb při selhání Windows serveru pro instanci SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Pokud používáte Windows Server 2016, doporučujeme nakonfigurovat [sdílené složky Azure v cloudu](/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instalace instance SAP ASCS na obou uzlech

Od SAP budete potřebovat následující software:
   * Instalační nástroj SAP software Provisioning Manager (SWPM) verze SPS25 nebo novější.
   * Jádro SAP 7,49 nebo novější
   * Vytvořte název virtuálního hostitele (název sítě clusteru) pro clusterované instance SAP ASCS/SCS, jak je popsáno v tématu [vytvoření názvu virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Clustering SAP ASCS/SCS Instances pomocí sdílené složky se podporuje pro SAP NetWeaver 7,40 (a novější) pomocí SAP kernel 7,49 (a novější).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instalace instance ASCS/SCS na prvním uzlu clusteru ASCS/SCS

1. Nainstalujte instanci SAP ASCS/SCS do prvního uzlu clusteru. Spusťte instalační nástroj SAP SWPM a pak přejděte na: **produkt**  >  **DBMS** > instalace > aplikační server ABAP (nebo Java) > High-Availability System > ASCS/SCS instance > prvním uzlu clusteru.  

2. Jako konfiguraci sdílené složky clusteru v SWPM vyberte **cluster Shared File** .  
3. Po zobrazení výzvy ke kroku o **parametrech systémového clusteru SAP** zadejte název hostitele pro sdílenou složku SMB Azure NetApp Files, kterou jste už vytvořili jako **název hostitele pro sdílení souborů**.  V tomto příkladu je název hostitele sdílené složky SMB **anfsmb-9562**. 

   > [!IMPORTANT]
   > Pokud výsledky kontroly požadovaných součástí SWPM ukazují, že se nesplní podmínka funkce průběžné dostupnosti, můžete ji vyřešit podle pokynů v části [opožděná chybová zpráva při pokusu o přístup ke sdílené složce, která už v systému Windows neexistuje](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

   > [!TIP]
   > Pokud výsledky kontroly požadovaných součástí SWPM ukazují, že se podmínka změny velikosti nevyhověla, můžete velikost SWAPu upravit tak, že přejdete do složky počítač>vlastnosti systému>nastavení výkonu> Pokročilá> virtuální paměť> změnit.  

4. Nakonfigurujte prostředek clusteru SAP, `SAP-SID-IP` port testu pomocí prostředí PowerShell. Tuto konfiguraci spusťte na jednom z uzlů clusteru SAP ASCS/SCS, jak je popsáno v tématu [Konfigurace portu testu paměti](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instalace instance ASCS/SCS na druhý uzel clusteru ASCS/SCS

1. Nainstalujte instanci SAP ASCS/SCS na druhý uzel clusteru. Spusťte instalační nástroj SAP SWPM a pak přejděte na **produkt**  >  **DBMS** > instalace > aplikační server ABAP (nebo Java) > High-Availability System > ASCS/SCS instance > další uzel clusteru.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalace instance systému DBMS a aplikačních serverů SAP

Dokončete instalaci SAP tím, že nainstalujete:

   * Instance systému DBMS  
   * Primární aplikační Server SAP  
   * Další aplikační Server SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testování převzetí služeb při selhání instance SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Převzetí služeb při selhání z uzlu clusteru A na uzel clusteru B a back
V tomto scénáři testování odkazujeme na uzel clusteru sapascs1 jako Node A a na uzel Node sapascs2 jako uzel B.

1. Ověřte, zda jsou v uzlu A spuštěny prostředky clusteru. ![ Obrázek 1: prostředky clusteru s podporou převzetí služeb při selhání Windows serveru spuštěné v uzlu A před testem](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Restartujte uzel clusteru A. Prostředky clusteru SAP se přesunou na uzel clusteru B. ![ Obrázek 2: prostředky clusteru s podporou převzetí služeb při selhání systému Windows Server spuštěné v uzlu B po dokončení testu](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test položky zámku

1. Ověřte, že je aktivní server replikace fronty SAP (OLAJÍCÍCH).  
2. Přihlaste se k systému SAP, spusťte transakční SU01 a otevřete ID uživatele v režimu změn. Tím se vygeneruje položka zámku SAP.  
3. V případě, že jste se přihlásili do systému SAP, zobrazte položku zámku tak, že přejdete na ST12 transakce.  
4. Převzetí služeb při selhání ASCS prostředky z uzlu clusteru A na uzel clusteru B.  
5. Ověřte, že se zachová položka zámku vygenerovaná před převzetím služeb při selhání prostředků clusteru SAP ASCS/SCS.  

![Obrázek 3: po testu převzetí služeb při selhání se zachová položka zámku](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Další informace najdete v tématu [řešení potíží s převzetím služeb při selhání v ASCS pomocí olajících](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS) .
## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP 
* HANA v Azure (velké instance), přečtěte si téma [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuální počítače)][sap-hana-ha] .
