---
title: Virtuální počítače Azure HA pro SAP NW ve Windows se soubory Azure NetApp (SMB)| Dokumenty společnosti Microsoft
description: Vysoká dostupnost pro SAP NetWeaver ve virtuálních počítačích Azure ve Windows se soubory Azure NetApp (SMB) pro aplikace SAP
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
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591349"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>Vysoká dostupnost pro SAP NetWeaver ve virtuálních počítačích Azure ve Windows se soubory Azure NetApp (SMB) pro aplikace SAP

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

Tento článek popisuje, jak nasadit, nakonfigurovat virtuální počítače, nainstalovat architekturu clusteru a nainstalovat vysoce dostupný systém SAP NetWeaver 7.50 do virtuálních počítačů se systémem Windows pomocí [smb](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) v [souborech Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/).  

Databázová vrstva není podrobně popsána v tomto článku. Předpokládáme, že [virtuální síť](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) Azure již byla vytvořena.  

Nejprve si přečtěte následující poznámky a dokumenty SAP:

* [Dokumentace ke službě Azure NetApp Files][anf-azure-doc] 
* SAP Poznámka [1928533][1928533], která obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačních systémů (OS) a databází
  * Požadovaná verze jádra SAP pro Windows v Microsoft Azure
* SAP Note [2015553][2015553] uvádí předpoklady pro nasazení softwaru SAP v Azure podporované sapem.
* SAP Note [2178632][2178632] má podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [1999351][1999351] má další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring Extension pro SAP.
* POZNÁMKA SAP [2287140](https://launchpad.support.sap.com/#/notes/2287140) uvádí předpoklady pro funkci sap podporované certifikační autority protokolu SMB 3.x.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770) má informace o řešení potíží pro pomalu běžící transakci SAP AL11 v systémech Windows 2012 a 2016.
* Poznámka SAP [1911507](https://launchpad.support.sap.com/#/notes/1911507) obsahuje informace o transparentní funkci převzetí služeb při selhání pro sdílenou složku v systému Windows Server s protokolem SMB 3.0.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452) má doporučení (deaktivace generování názvů 8.3) k řešení výkonu/chyby systému souborů špatný během přístupu k datům.
* [Instalace sap netweaveru s vysokou dostupností do clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [Architektura a scénáře azure virtuálních počítačů a scénáře pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [Přidání portu sondy v konfiguraci clusteru ASCS](sap-high-availability-installation-wsfc-file-share.md)
* [Instalace instance (A)SCS v clusteru s podporou převzetí služeb při selhání](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Vytvoření svazku SMB pro Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [Aplikace NetApp SAP v Microsoft Azure pomocí souborů Azure NetApp][anf-sap-applications-azure]

## <a name="overview"></a>Přehled

Společnost SAP vyvinula nový přístup a alternativu ke sdíleným diskům clusteru pro clusterování instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows. Namísto použití sdílených disků clusteru lze použít sdílenou složku SMB k nasazení globálních hostitelských souborů SAP. Azure NetApp Files podporuje SMBv3 (spolu se systémem souborů NFS) s seznamem ACL NTFS pomocí služby Active Directory. Soubory Azure NetApp je automaticky vysoce dostupné (protože se jedná o službu PaaS). Díky těmto funkcím jsou soubory Azure NetApp skvělou volbou pro hostování sdílené složky SMB pro globální SAP.  
Podporovány jsou [služby Azure Active Directory (AD) Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) a Služba [Active Directory Domain Services (AD DS).](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) S azure netapp soubory můžete použít existující řadiče domény služby Active Directory. Řadiče domény mohou být v Azure jako virtuální počítače nebo místně prostřednictvím ExpressRoute nebo S2S VPN. V tomto článku použijeme řadič domény ve virtuálním počítači Azure.  
Vysoká dostupnost (HA) pro centrální služby SAP Netweaver vyžaduje sdílené úložiště. K dosažení tohoto cíle v systému Windows bylo zatím nutné vytvořit buď cluster SOFS, nebo použít cluster sdílený disk s/w jako SIOS. Nyní je možné dosáhnout SAP Netweaver HA pomocí sdíleného úložiště, nasazené na Soubory Azure NetApp. Použití souborů Azure NetApp pro sdílené úložiště eliminuje potřebu sofs nebo SIOS.  

> [!NOTE]
> Clustering instance SAP ASCS/SCS pomocí sdílené složky je podporován pro SAP NetWeaver 7.40 (a novější), s jádrem SAP 7.49 (a novější).  

![Architektura SAP ASCS/SCS HA se sdílenou spořiteho](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

Předpoklady pro sdílenou složku SMB jsou:
* Protokol SMB 3.0 (nebo novější).
* Možnost nastavit seznamy řízení přístupu služby Active Directory (ACL) pro skupiny uživatelů služby Active Directory a objekt počítače$
* Sdílená složka musí mít povolenou technologii HA.

Sdílená složka pro služby SAP Central v této referenční architektuře nabízí soubory Azure NetApp:

![Architektura SAP ASCS/SCS HA se sdílenou spořiteho](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Vytvoření a připojení svazku SMB pro soubory Azure NetApp

Proveďte následující kroky jako příprava na použití souborů Azure NetApp.  

1. Podle pokynů k [registraci pro soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. Vytvoření účtu Azure NetApp podle kroků popsaných v [části Vytvoření účtu NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)  
3. Nastavení fondu kapacit podle pokynů v části [Nastavení fondu kapacit](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Prostředky Azure NetApp Files musí být umístěny v delegované podsíti. Podle pokynů v [části Delegovat podsíť na soubory Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) vytvořte delegovanou podsíť.  

> [!IMPORTANT]
> Před vytvořením svazku SMB je třeba vytvořit připojení služby Active Directory. Zkontrolujte [požadavky na připojení služby Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections).  

5. Vytvoření připojení služby Active Directory, jak je popsáno v [seznamu Vytvořit připojení služby Active Directory](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)  
6. Vytvoření svazku SMB Azure NetApp Files SMB podle pokynů v části [Přidání svazku SMB](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)  
7. Připojte svazek SMB do virtuálního počítače se systémem Windows.

> [!TIP]
> Najdete pokyny, jak připojit svazek Souborů Azure NetApp, pokud přejdete na [webu Azure Portal](https://portal.azure.com/#home) do objektu Azure NetApp Files, kliknete na okno **Svazky** a pak **připojíte pokyny**.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Příprava infrastruktury pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows 

1. [Nastavení požadovaných adres IP DNS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [Nastavte statické IP adresy pro virtuální počítače SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30).
3. [Nastavte statickou IP adresu pro interní systém vyrovnávání zatížení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e).
4. [Nastavte výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyvažovač zatížení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c).
5. [Změňte výchozí pravidla vyrovnávání zatížení ASCS/SCS pro interní vyvažovač zatížení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716).
6. [Přidejte virtuální počítače windows do domény](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
7. [Přidání položek registru v obou uzlech clusteru instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [Nastavení clusteru s podporou převzetí služeb při selhání systému Windows Server pro instanci SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. Pokud používáte Windows Server 2016, doporučujeme nakonfigurovat [Azure Cloud Witness](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).


## <a name="install-sap-ascs-instance-on-both-nodes"></a>Instalace instance SAP ASCS na obou uzlech

Potřebujete následující software od společnosti SAP:
   * SAP Software Provisioning Manager (SWPM) instalační nástroj verze SPS25 nebo novější.
   * Jádro SAP 7.49 nebo novější
   * Vytvořte název virtuálního hostitele (název sítě clusteru) pro clusterovnou instanci SAP ASCS/SCS, jak je popsáno v [části Vytvoření názvu virtuálního hostitele pro clusterověvnou instanci SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097).

> [!NOTE]
> Clustering instance SAP ASCS/SCS pomocí sdílené složky je podporován pro SAP NetWeaver 7.40 (a novější), s jádrem SAP 7.49 (a novější).  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>Instalace instance ASCS/SCS na první uzel clusteru ASCS/SCS

1. Nainstalujte instanci SAP ASCS/SCS do prvního uzlu clusteru. Spusťte instalační nástroj SAP SWPM a přejděte na: **Product** > **DBMS** > Installation > Application Server ABAP (nebo Java) > High-Availability System > instance ASCS/SCS > prvním uzlu clusteru.  

2. Vyberte **Cluster sdílení souborů** jako konfigurace sdílené složky clusteru v SWPM.  
3. Po zobrazení výzvy v kroku **Parametry systémového clusteru SAP**zadejte název hostitele sdílené složky SMB souborů Azure NetApp, kterou jste již vytvořili jako **název hostitele sdílené složky**.  V tomto příkladu je název hostitele sdílené složky SMB **anfsmb-9562**. 

> [!IMPORTANT]
> Pokud předběžná kontrola výsledky v SWPM zobrazuje nepřetržitá dostupnost podmínku funkce není splněna, může být vyřešena podle pokynů v [zpožděné chybové zprávě při pokusu o přístup ke sdílené složce, která již neexistuje v systému Windows](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l).  

> [!TIP]
> Pokud předběžné zaškrtnutí výsledků v SWPM ukazuje, že podmínka velikosti odkládací velikost není splněna, můžete upravit velikost SWAP přechodem na položku Vlastnosti systém>u>vlastnosti systému> Rozšířené> Virtuální paměť> Změnit.  

4. Nakonfigurujte prostředek `SAP-SID-IP` clusteru SAP, port sondy, pomocí prostředí PowerShell. Tuto konfiguraci proveďte na jednom z uzlů clusteru SAP ASCS/SCS, jak je popsáno v [části Konfigurovat port sondy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761).

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>Instalace instance ASCS/SCS do druhého uzlu clusteru ASCS/SCS

1. Nainstalujte instanci SAP ASCS/SCS do druhého uzlu clusteru. Spusťte instalační nástroj SAP SWPM a přejděte na **produkt** > **DBMS** > instalace > aplikačním serveru ABAP (nebo Javu) > > instance ASCS/SCS > další uzel clusteru.  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>Instalace instance DBMS a aplikačních serverů SAP

Dokončete instalaci SAP instalací:

   * Instance DBMS  
   * Primární aplikační server SAP  
   * Další aplikační server SAP  

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testování převzetí služeb při selhání instance SAP ASCS/SCS 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>Převzetí služeb při selhání z uzlu clusteru A na uzel Cluster B a zpět
V tomto testovacím scénáři budeme odkazovat na uzel clusteru sapascs1 jako uzel A a clusteru uzlu sapascs2 jako uzel B.

1. Před testem převzetí služeb při selhání ![ověřte, zda jsou prostředky clusteru spuštěny na uzlu A. Obrázek 1: Prostředky clusteru s podporou převzetí služeb při selhání systému Windows Server spuštěné na uzlu A před testem převzetí služeb při selhání](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. Restartujte uzel clusteru A. Prostředky clusteru SAP se přesunou do ![uzlu clusteru B. Obrázek 2: Prostředky clusteru s podporou převzetí služeb při selhání systému Windows Server spuštěné na uzlu B po testu převzetí služeb při selhání](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>Test zámku

1.Ověřte, zda je server replikace fronty SAP (ERS) aktivní  
2. Přihlaste se do systému SAP, spusťte transakci SU01 a otevřete ID uživatele v režimu změny. To bude generovat sap zámek vstupu.  
3. Při přihlášení do systému SAP zobrazte položku zámku přechodem na transakci ST12.  
4. Prostředky ASCS při selhání z uzlu Clusteru A do uzlu B clusteru.  
5. Ověřte, zda je zachována položka zámku vygenerovaná před převzetím služeb při selhání prostředků clusteru SAP ASCS/SCS.  

![Obrázek 3: Položka zámku je zachována po testu převzetí služeb při selhání](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

Další informace naleznete [v tématu Poradce při potížích s převzetím služeb při selhání fronty v ASCS s ERS](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>Další kroky

* [Plánování a implementace virtuálních počítačů Azure pro SAP][planning-guide]
* [Nasazení virtuálních počítačů Azure pro SAP][deployment-guide]
* [Nasazení DBMS virtuálních počítačů Azure pro SAP][dbms-guide]
* Chcete-li se dozvědět, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP 
* HANA v Azure (velké instance), viz [SAP HANA (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).
* Informace o tom, jak vytvořit vysokou dostupnost a plán pro zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v [tématu vysoká dostupnost SAP HANA na virtuálních počítačích Azure.][sap-hana-ha]
