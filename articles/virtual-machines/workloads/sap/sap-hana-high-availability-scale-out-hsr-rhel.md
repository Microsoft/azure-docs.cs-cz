---
title: SAP HANA horizontálního navýšení kapacity pomocí HSR a Pacemaker na RHEL | Microsoft Docs
description: SAP HANA škálování na více instancí pomocí HSR a Pacemaker v RHEL
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
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: c97975d6920cd0f04a7d2d4e73c00104a2b13235
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685608"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Vysoká dostupnost SAP HANA systému škálování na více systémů na Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Tento článek popisuje, jak nasadit vysoce dostupný SAP HANA systém v konfiguraci se škálováním na více systémů pomocí replikace systému HANA (HSR) a Pacemaker v Azure Red Hat Enterprise Linux virtuálních počítačích (VM). Sdílené systémy souborů v prezentované architektuře poskytují [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) a jsou připojené přes systém souborů NFS.  

V ukázkových konfiguracích, instalačních příkazech atd. instance HANA je **03** a ID systému Hana je **HN1**. Příklady jsou založené na HANA 2,0 SP4 a Red Hat Enterprise Linux pro SAP 7,6. 

Než začnete, přečtěte si následující poznámky a dokumenty SAP:

* Poznámka [1928533] pro SAP obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* SAP Note [2015553]: seznam požadavků pro nasazení softwaru SAP podporovaného SAP v Azure
* Poznámka SAP [2002167] má doporučená nastavení operačního systému pro Red Hat Enterprise Linux
* Poznámka SAP Poznámka [2009879] obsahuje pokyny pro SAP HANA Red Hat Enterprise Linux
* SAP Note [2178632]: obsahuje podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498]: obsahuje požadovanou verzi agenta hostitele SAP pro Linux v Azure.
* SAP Note [2243692]: obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure
* SAP Note [1999351]: obsahuje další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* Poznámka SAP poznámky [1900823]: obsahuje informace o požadavcích na úložiště SAP HANA.
* [Komunitní wikiweb pro SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): obsahuje všechny požadované poznámky SAP pro Linux
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [SAP HANA požadavky na síť](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Obecná dokumentace k RHEL
  * [Přehled Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Správa Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referenční informace o Add-On vysoké dostupnosti](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Průvodce Red Hat Enterprise Linux sítě](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [Návody konfigurace replikace SAP HANA Scale-Out systému v clusteru Pacemaker se systémy souborů HANA ve sdílených složkách NFS](https://access.redhat.com/solutions/5423971)
* Dokumentace k RHEL specifické pro Azure:
  * [Instalace SAP HANA na Red Hat Enterprise Linux pro použití v Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Red Hat Enterprise Linux řešení pro SAP HANA Scale-Out a replikaci systému](https://access.redhat.com/solutions/4386601)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]
* [Dokumentace k Azure NetApp Files][anf-azure-doc] 


## <a name="overview"></a>Přehled

Jedna z metod, jak dosáhnout vysoké dostupnosti pro Hana s vysokou dostupností pro instalace na více instancí HANA, je konfigurace replikace systému HANA a ochrana řešení pomocí clusteru Pacemaker, aby bylo možné automatické převzetí služeb při selhání. Když dojde k selhání aktivního uzlu, cluster převezme prostředky HANA do druhé lokality.  
Uvedená konfigurace zobrazuje tři uzly HANA na každé lokalitě, a to i majoritní uzel maker, aby se zabránilo scénáři děleného mozku. Pokyny mohou být upraveny tak, aby zahrnovaly více virtuálních počítačů jako uzly databáze HANA.  

Sdílený systém souborů HANA `/hana/shared` v prezentované architektuře poskytuje [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Je připojen přes NFSv 4.1 na každém uzlu HANA ve stejné lokalitě replikace systému HANA. Souborové systémy `/hana/data` a `/hana/log` jsou místní souborové systémy a mezi uzly databáze Hana se nesdílí. SAP HANA bude nainstalován v nesdíleném režimu. 

> [!TIP]
> Doporučené konfigurace úložiště SAP HANA najdete v tématu [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md).   

[![SAP HANA horizontálního navýšení kapacity pomocí clusteru HSR a Pacemaker](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

V předchozím diagramu se tři podsítě reprezentují v rámci jedné virtuální sítě Azure, a to podle doporučení SAP HANA sítě: 
* pro komunikaci s klientem – `client` 10.23.0.0/24  
* pro interní komunikaci mezi uzly HANA – `inter` 10.23.1.128/26  
* pro replikaci systému HANA – `hsr` 10.23.1.192/26  

Stejně jako `/hana/data` a `/hana/log` jsou nasazeny na místních discích není nutné nasazovat samostatnou podsíť a samostatné virtuální síťové karty pro komunikaci do úložiště.  

Svazky NetApp Azure se nasazují v samostatné podsíti, [delegované do Azure NetApp Files] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>Nastavení infrastruktury

V následujících pokynech předpokládáme, že jste už vytvořili skupinu prostředků, virtuální síť Azure se třemi síťovými podsítěmi Azure: `client` `inter` a `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Nasazení virtuálních počítačů se systémem Linux prostřednictvím Azure Portal
1. Nasaďte virtuální počítače Azure.  
Pro konfiguraci prezentovanou v tomto dokumentu nasaďte sedm virtuálních počítačů: 
   - tři virtuální počítače, které mají sloužit jako uzly databáze HANA pro replikační lokalitu HANA 1: **Hana-S1-DB1**, **Hana-S1-DB2** a **Hana-S1-DB3**  
   - tři virtuální počítače, které mají sloužit jako uzly databáze HANA pro replikační lokalitu HANA 2: **Hana-S2-DB1**, **Hana-S2-DB2** a **Hana-S2-DB3**  
   - malý virtuální počítač, který bude sloužit jako *většina maker*: **Hana-s-mm**

   Virtuální počítače nasazené jako uzly SAP DB HANA by měly být certifikovány SAP pro HANA jako publikované v [adresáři SAP HANAho hardwaru](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Při nasazování uzlů databáze HANA se ujistěte, že je vybraná možnost [akcelerovaná síť](../../../virtual-network/create-vm-accelerated-networking-cli.md) .  
  
   V uzlu většina Maker můžete nasadit malý virtuální počítač, protože tento virtuální počítač neběží na žádném z SAP HANAch prostředků. V konfiguraci clusteru se používá virtuální počítač pro většinu maker k dosažení lichého počtu uzlů clusteru ve scénáři děleného mozku. V tomto příkladu potřebuje virtuální počítač pro většinu maker jenom jedno rozhraní v `client` podsíti.        

   Nasaďte místní spravované disky pro `/hana/data` a `/hana/log` . Minimální doporučená konfigurace úložiště pro `/hana/data` a `/hana/log` je popsána v tématu [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md).

   Nasaďte primární síťové rozhraní pro každý virtuální počítač v `client` podsíti virtuální sítě.  
   Když se virtuální počítač nasadí prostřednictvím Azure Portal, automaticky se vygeneruje název síťového rozhraní. V těchto pokynech pro zjednodušení budeme odkazovat na automaticky generovaná primární síťová rozhraní, která jsou připojená k `client` podsíti virtuální sítě Azure jako **Hana-S1-DB1-Client**, **Hana-S1-DB2-Client**, **Hana-S1-DB3-** Client a tak dále.  


   > [!IMPORTANT]
   > Ujistěte se, že operační systém, který vyberete, je SAP – certifikováno pro SAP HANA na specifických typech virtuálních počítačů, které používáte. Seznam SAP HANA certifikovaných typů virtuálních počítačů a verzí operačních systémů pro tyto typy najdete na webu [SAP HANA Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) . Kliknutím na podrobnosti o typu tohoto virtuálního počítače získáte úplný seznam SAP HANA podporovaných verzí operačního systému pro daný typ.  
  

2. Vytvořte šest síťových rozhraní, jednu pro každý virtuální počítač databáze HANA, v `inter` podsíti virtuální sítě (v tomto příkladu **Hana-S1-DB1-Inter**, **Hana-S1-DB2-Inter**, Hana- **S1-DB3-Inter**, **Hana-S2-DB1-Inter**, Hana- **S2-DB2-Inter** a **Hana-S2-DB3-Inter**).  

3. Vytvořte šest síťových rozhraní, jednu pro každý virtuální počítač databáze HANA v `hsr` podsíti virtuální sítě (v tomto příkladu **Hana-S1-DB1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-DB3-HSR**, **Hana-S2-DB1-HSR**, **Hana-S2-DB2-HSR** a **Hana-S2-DB3-HSR**).  

4. Připojte nově vytvořená rozhraní virtuální sítě k odpovídajícím virtuálním počítačům:  

    a. Přejít na virtuální počítač v [Azure Portal](https://portal.azure.com/#home).  

    b. V levém podokně vyberte **Virtual Machines**. Vyfiltrujte název virtuálního počítače (například **Hana-S1-DB1**) a potom vyberte virtuální počítač.  

    c. V podokně **Přehled** vyberte možnost zastavit, čímž **zrušíte** přidělení virtuálního počítače.  

    d. Vyberte **sítě** a pak připojte síťové rozhraní. V rozevíracím seznamu **připojit síťové rozhraní** vyberte již vytvořená síťová rozhraní pro `inter` `hsr` podsítě a.  
    
    e. Vyberte **Uložit**. 
 
    f. Opakujte kroky b až e pro zbývající virtuální počítače (v našem příkladu je to  **Hana-S1-DB2**, **Hana-S1-DB3**, **Hana-S2-DB1**, **Hana-S2-DB2** a **Hana-S2-DB3**).
 
    například Virtuální počítače teď ponechte v zastaveném stavu. V dalším kroku povolíte [urychlené síťové služby](../../../virtual-network/create-vm-accelerated-networking-cli.md) pro všechna nově připojená síťová rozhraní.  

5. Pomocí následujících kroků povolte akcelerované sítě pro další síťová rozhraní `inter` pro `hsr` podsítě a:  

    a. Otevřete [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) v [Azure Portal](https://portal.azure.com/#home).  

    b. Spusťte následující příkazy, aby bylo možné zrychlit sítě pro další síťová rozhraní, která jsou připojena k `inter` `hsr` podsítím a.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. Spuštění virtuálních počítačů HANA DB

### <a name="deploy-azure-load-balancer"></a>Nasazení Azure Load Balancer

1. Doporučujeme použít službu Load Balancer úrovně Standard. Pomocí těchto kroků konfigurace nasaďte standardní nástroj pro vyrovnávání zatížení:
   1. Nejprve vytvořte front-end fond IP adres:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **front-end IP fond** a vyberte **Přidat**.
      1. Zadejte název nového fondu front-end IP adres (například **Hana-front-endu**).
      1. Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.23.0.18**).
      1. Vyberte **OK**.
      1. Až se vytvoří nový fond front-end IP adres, poznamenejte si IP adresu fondu.

   1. Dále vytvořte fond back-end a přidejte všechny virtuální počítače clusteru do back-endu fondu:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **fondy back-endu** a vyberte **Přidat**.
      1. Zadejte název nového fondu back-end (například **Hana-back-end**).
      1. Vyberte **Přidat virtuální počítač**.
      1. Vyberte **Virtuální počítač**.
      1. Vyberte virtuální počítače SAP HANA clusteru a jejich IP adresy pro `client` podsíť.
      1. Vyberte **Přidat**.

   1. Potom vytvořte sondu stavu:

      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **sondy stavu** a vyberte **Přidat**.
      1. Zadejte název nové sondy stavu (například **Hana-HP**).
      1. Jako protokol a port 625 **03** vyberte **TCP** . Hodnotu **intervalu** nastavte na 5 a mezní hodnota není v **pořádku** je nastavená na 2.
      1. Vyberte **OK**.

   1. Dále vytvořte pravidla vyrovnávání zatížení:
   
      1. Otevřete nástroj pro vyrovnávání zatížení, vyberte **pravidla vyrovnávání zatížení** a vyberte **Přidat**.
      1. Zadejte název nového pravidla nástroje pro vyrovnávání zatížení (například **Hana-kg**).
      1. Vyberte front-end IP adresu, fond back-end a sondu stavu, který jste vytvořili dříve (například **Hana-front-endu**, **Hana-back-endu** a **Hana-HP**).
      1. Vyberte **porty ha**.
      1. Zvyšte **časový limit nečinnosti** na 30 minut.
      1. Ujistěte se, že jste **povolili plovoucí IP adresu**.
      1. Vyberte **OK**.

   > [!IMPORTANT]
   > Plovoucí IP adresa není ve scénářích Vyrovnávání zatížení podporována u sekundární konfigurace IP adresy NIC. Podrobnosti najdete v tématu [omezení nástroje pro vyrovnávání zatížení Azure](../../../load-balancer/load-balancer-multivip-overview.md#limitations). Pokud pro virtuální počítač potřebujete další IP adresu, nasaďte druhou síťovou kartu.    
   
   > [!Note]
   > Pokud se virtuální počítače bez veřejných IP adres nacházejí v back-end fondu interní služby pro vyrovnávání zatížení (bez veřejné IP adresy), nebude žádné odchozí připojení k Internetu, pokud se neprovede další konfigurace, která umožní směrování na veřejné koncové body. Podrobnosti o tom, jak dosáhnout odchozího připojení, najdete v tématu [připojení k veřejnému koncovému bodu pro Virtual Machines používání Azure Standard Load Balancer ve scénářích s vysokou dostupností SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)  


   > [!IMPORTANT]
   > Nepovolujte časová razítka TCP na virtuálních počítačích Azure umístěných za Azure Load Balancer. Povolení časových razítek TCP způsobí selhání sond stavu. Nastavte parametr **net.IPv4.tcp_timestamps** na **hodnotu 0**. Podrobnosti najdete v tématu [Load Balancer sondy stavu](../../../load-balancer/load-balancer-custom-probe-overview.md).
   > Viz také SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>Nasazení infrastruktury Azure NetApp Files 

Nasaďte svazky ANF pro `/hana/shared` systém souborů. `/hana/shared`Pro každou lokalitu replikace systému Hana budete potřebovat samostatný svazek. Další informace najdete v tématu [Nastavení infrastruktury Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure).

V tomto příkladu byly použity následující svazky Azure NetApp Files: 

* Volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* Volume **HN1**-Shared-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)

## <a name="operating-system-configuration-and-preparation"></a>Konfigurace a příprava operačního systému

Pokyny v dalších částech jsou předem opraveny s jednou z následujících zkratek:
* **[A]**: platí pro všechny uzly
* **[Ah]**: platí pro všechny uzly databáze Hana
* **[M]**: platí pro majoritní uzel maker
* **[AH1]**: platí pro všechny uzly databáze Hana v lokalitě 1
* **[AH2]**: platí pro všechny uzly databáze Hana v lokalitě 2
* **[1]**: platí jenom pro uzel databáze Hana 1, lokalita 1.
* **[2]**: platí jenom pro uzel databáze Hana 1, lokalita 2.


Pomocí následujících kroků nakonfigurujte a připravte operační systém:

1. **[A]** Udržujte hostitelské soubory na virtuálních počítačích. Zahrnout položky pro všechny podsítě Do tohoto příkladu byly přidány následující položky `/etc/hosts` .  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** nainstalujte balíček klienta NFS.  

    ```yum install nfs-utils ```


3. **[Ah]** Konfigurace Red Hat pro HANA  

    Nakonfigurujte RHEL podle popisu v tématu <https://access.redhat.com/solutions/2447641> a v následujících komentářích SAP:  
   - [2292690-SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 – Linux: spouštění aplikací SAP s použitím RSZ 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 – Linux: běžící aplikace SAP s aplikací RSZ 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 – Linux: běžící aplikace SAP s aplikací RSZ 9. x](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>Příprava systémů souborů
### <a name="mount-the-shared-file-systems"></a>Připojte systémy souborů Shared File

V tomto příkladu jsou sdílené systémy souborů HANA nasazeny na Azure NetApp Files a připojené přes názvů NFSv4.  

1. **[Ah]** Vytvořte přípojné body pro databázové svazky HANA.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Ověřte nastavení domény systému souborů NFS. Ujistěte se, že je doména nakonfigurovaná jako výchozí doména Azure NetApp Files, to znamená **`defaultv4iddomain.com`** a mapování je nastavené na **nikdo**.  
   Tento krok je potřeba jenom v případě, že používáte Azure NetAppFiles NFSv 4.1.  

    > [!IMPORTANT]
    > Ujistěte se, že jste na virtuálním počítači nastavili doménu systému souborů NFS, `/etc/idmapd.conf` aby odpovídala výchozí konfiguraci domény v Azure NetApp Files: **`defaultv4iddomain.com`** . Pokud dojde k neshodě mezi konfigurací domény v klientovi NFS (tj. virtuálním počítačem) a serverem NFS, tj. konfigurací Azure NetApp, pak se budou zobrazovat oprávnění k souborům na svazcích Azure NetApp, které jsou připojené k virtuálním počítačům `nobody` .  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Ověřte `nfs4_disable_idmapping` . Měl by být nastaven na **Y**. Pokud chcete vytvořit adresářovou strukturu `nfs4_disable_idmapping` , kde se nachází, spusťte příkaz Mount. V/sys/modules nebudete moct ručně vytvořit adresář, protože přístup je vyhrazený pro jádro nebo ovladače.  
   Tento krok je potřeba jenom v případě, že používáte Azure NetAppFiles NFSv 4.1.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Další informace o tom, jak změnit `nfs4_disable_idmapping` parametr, naleznete v tématu https://access.redhat.com/solutions/1749883 .

4. **[AH1]** Připojte sdílené Azure NetApp Files svazky na virtuálních počítačích databáze SITE1 HANA.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Připojte sdílené Azure NetApp Files svazky na virtuálních počítačích databáze SITE2 HANA.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[Ah]** Ověřte, jestli `/hana/shared/` jsou odpovídající systémy souborů připojené ke všem virtuálním počítačům databáze Hana s protokolem NFS verze **názvů NFSv4**.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>Příprava dat a protokolování systémů místních souborů
V uvedené konfiguraci se systémy souborů `/hana/data` a `/hana/log` nasazují na spravovaný disk a místně se připojují ke každému virtuálnímu počítači Hana DB. Na každém virtuálním počítači HANA DB budete muset provést postup pro vytvoření místních svazků dat a protokolů. 

Nastavení rozložení disku pomocí  **Správce logických svazků (LVM)**. Následující příklad předpokládá, že každý virtuální počítač HANA má připojené tři datové disky, které se používají k vytvoření dvou svazků.

1. **[Ah]** Vypíše všechny dostupné disky:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Příklad výstupu:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Vytvořte fyzické svazky pro všechny disky, které chcete použít:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Vytvořte skupinu svazků pro datové soubory. Pro soubory protokolu použijte jednu skupinu svazků a jednu pro sdílený adresář SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Vytvořte logické svazky. 
   Při použití bez přepínače se vytvoří lineární svazek `lvcreate` `-i` . Doporučujeme vytvořit prokládaný svazek pro lepší vstupně-výstupní výkon a zarovnat velikosti pruhů k hodnotám popsaným v [SAP HANA konfiguracích úložiště virtuálních počítačů](./hana-vm-operations-storage.md). `-i`Argument by měl být počet základních fyzických svazků a `-I` argumentem je velikost pruhu. V tomto dokumentu se pro datový svazek používají dva fyzické svazky, takže `-i` je argument přepínače nastavený na **2**. Velikost pruhu pro datový svazek je **256 KiB**. Pro svazek protokolu se používá jeden fyzický svazek, takže žádné `-i` nebo `-I` přepínače nejsou explicitně použity pro příkazy svazků protokolu.  

   > [!IMPORTANT]
   > Použijte `-i` přepínač a nastavte jej na číslo základního fyzického svazku, pokud pro každý z nich použijete více fyzických svazků. `-I`Při vytváření prokládaného svazku použijte přepínač k určení velikosti pruhu.  
   > Doporučené konfigurace úložiště, včetně velikosti pruhů a počtu disků, najdete v tématu [SAP HANA konfigurace úložiště virtuálních počítačů](./hana-vm-operations-storage.md) .  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Vytvořte adresáře připojení a zkopírujte identifikátor UUID všech logických svazků:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** Vytvořit `fstab` položky pro logické svazky a připojení:
    ```bash
    sudo vi /etc/fstab
    ```

   Do souboru vložte následující řádek `/etc/fstab` :

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   Připojte nové svazky:

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>Instalace  

V tomto příkladu pro nasazení SAP HANA v konfiguraci škálování na více instancí s HSR na virtuálních počítačích Azure jsme použili HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Příprava instalace HANA

1. **[Ah]** Před instalací HANA nastavte kořenové heslo. Po dokončení instalace můžete kořenové heslo zakázat. Spustit jako `root` příkaz `passwd`  

2. **[1, 2]** změnit oprávnění pro `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** ověřte, že se můžete přihlásit přes SSH k virtuálním počítačům databáze Hana v této lokalitě **Hana-S1-DB2** a **Hana-S1-DB3**, aniž by se zobrazila výzva k zadání hesla.  
   V takovém případě se klíče Exchange SSH, jak je popsáno v části [použití ověřování založeného na klíčích](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** ověřte, že se můžete přihlásit přes SSH k virtuálním počítačům databáze Hana v této lokalitě **: Hana-S2-DB2** a **Hana-S2-DB3**, aniž by se zobrazila výzva k zadání hesla.  
   V takovém případě se klíče Exchange SSH, jak je popsáno v části [použití ověřování založeného na klíčích](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Nainstalujte další balíčky, které se vyžadují pro HANA 2,0 SP4. Další informace najdete v tématu SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) for RHEL 7. 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** dočasně zakáže bránu firewall, aby nebránila instalaci Hana. Po dokončení instalace HANA ji můžete znovu povolit. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>Instalace HANA na prvním uzlu v každé lokalitě

1. **[1]** nainstalujte SAP HANA podle pokynů v [Průvodci instalací a aktualizací SAP HANA 2,0](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). V následujících pokynech se zobrazuje SAP HANA instalace na prvním uzlu v lokalitě 1.   

   a. Spusťte program **hdblcm** jako `root` z adresáře instalačního softwaru Hana. Použijte `internal_network` parametr a předejte adresní prostor pro podsíť, který se používá pro interní komunikaci mezi uzly v Hana.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Do příkazového řádku zadejte následující hodnoty:

     * Pro **zvolit akci**: zadejte **1** (pro instalaci).
     * **Další součásti pro instalaci**: zadejte **2, 3**
     * Pro instalační cestu: stiskněte klávesu ENTER (výchozí hodnota je/Hana/Shared).
     * **Název místního hostitele**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Chcete **Přidat hostitele do systému?**: zadejte **n**
     * Pro **SAP HANA ID systému**: zadejte **HN1** .
     * **Číslo instance** [00]: zadejte **03**
     * Pro **skupinu Local Host Worker** [default]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **možnost vybrat využití systému/zadejte index [4]**: zadejte **4** (pro vlastní).
     * Pro **umístění datových svazků** [/Hana/data/HN1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **umístění svazků protokolu** [/Hana/log/HN1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Chcete-li **omezit maximální přidělení paměti?** [n]: zadejte **n**
     * Pro **název hostitele certifikátu pro hostitele Hana-S1-DB1** [Hana-S1-DB1]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * **Heslo uživatele agenta SAP pro uživatele (sapadm)**: zadejte heslo.
     * Pro **potvrďte heslo uživatele agenta SAP hostitele (sapadm)**: zadejte heslo.
     * **Heslo správce systému (hn1adm)**: zadejte heslo.
     * V **domovském adresáři správce systému** [/usr/SAP/HN1/Home]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **prostředí přihlášení správce systému** [/bin/sh]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **ID uživatele správce systému** [1001]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **Zadejte ID skupiny uživatelů (sapsys)** [79]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * **Heslo uživatele systémové databáze (systém)**: zadejte heslo systému.
     * Pro **potvrzení hesla uživatele systémové databáze (systém)**: zadejte heslo systému
     * Pro **restartování systému po restartování počítače?** [n]: zadejte **n** 
     * Chcete **pokračovat (a/n)**: ověřit souhrn a pokud vše vypadá dobře, zadejte **y** .

2. **[2]** Zopakováním výše uvedeného kroku nainstalujte SAP HANA v prvním uzlu v lokalitě 2.   

3. **[1, 2]** ověřit global.ini  

   Zobrazte global.ini a ujistěte se, že je nastavená konfigurace pro interní SAP HANA komunikace mezi uzly. Ověřte část **komunikace** . Měl by mít adresní prostor pro `inter` podsíť a `listeninterface` měl by být nastaven na `.internal` . Ověřte část **internal_hostname_resolution** . Měl by mít IP adresy pro virtuální počítače HANA, které patří do `inter` podsítě.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** Připravte `global.ini` se na instalaci v nesdíleném prostředí, jak je popsáno v tématu SAP Note [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** restartujte SAP HANA, aby se změny aktivovaly.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** ověřte, že klientské rozhraní bude používat IP adresy z `client` podsítě pro komunikaci.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Informace o tom, jak ověřit konfiguraci, najdete v tématu SAP Note [2183363 – konfigurace SAP HANA interní sítě](https://launchpad.support.sap.com/#/notes/2183363).  

7. **[Ah]** Změňte oprávnění k adresářům dat a protokolů, aby se zabránilo chybě instalace HANA.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** nainstalujte sekundární uzly Hana. Příklady instrukcí v tomto kroku jsou pro LOKALITu 1.  
   a. Spusťte rezidentský program **hdblcm** jako `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Do příkazového řádku zadejte následující hodnoty:

     * Pro **zvolit akci**: zadejte **2** (pro přidání hostitelů).
     * Pro **Zadejte názvy hostitelů oddělených čárkami, které se mají přidat**: Hana-S1-DB2, Hana-S1-DB3
     * **Další součásti pro instalaci**: zadejte **2, 3**
     * Pro **zadání kořenového uživatelského jména [root]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **možnost Vybrat role pro hostitele Hana-S1-DB2 [1]**: 1 (pro pracovní proces)
     * Pro **Zadejte skupinu převzetí služeb při selhání hostitele Hana-S1-DB2 [výchozí]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **Zadejte číslo oddílu úložiště pro hostitele Hana-S1-DB2 [<<assign automatically>>]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **Zadejte skupinu pracovních procesů pro hostitele Hana-S1-DB2 [výchozí]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **možnost Vybrat role pro hostitele Hana-S1-DB3 ' [1]**: 1 (pro pracovní proces)
     * Pro **Zadejte skupinu převzetí služeb při selhání pro hostitele Hana-S1-DB3 [výchozí]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **Zadejte číslo oddílu úložiště pro hostitele Hana-S1-DB3 ' [<<assign automatically>>]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **Zadejte skupinu pracovních procesů pro hostitele Hana-S1-DB3 [výchozí]**: stisknutím klávesy Enter přijměte výchozí nastavení.
     * **Heslo správce systému (hn1adm)**: zadejte heslo.
     * Pro **Zadejte heslo uživatele agenta SAP hostitele (sapadm)**: zadejte heslo.
     * Pro **potvrďte heslo uživatele agenta SAP hostitele (sapadm)**: zadejte heslo.
     * Pro **název hostitele certifikátu pro hostitele Hana-S1-DB2** [Hana-S1-DB2]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Pro **název hostitele certifikátu pro hostitele Hana-S1-DB3** [Hana-S1-DB3]: stisknutím klávesy Enter přijměte výchozí nastavení.
     * Chcete **pokračovat (a/n)**: ověřit souhrn a pokud vše vypadá dobře, zadejte **y** .

9. **[2]** Zopakováním výše uvedeného kroku nainstalujete sekundární SAP HANA uzly v lokalitě 2.   

## <a name="configure-sap-hana-20-system-replication"></a>Konfigurace replikace systému SAP HANA 2,0

1. **[1]** konfigurace replikace systému v lokalitě 1:

   Zálohujte databáze jako **HN1** ADM:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Zkopírujte systémové soubory PKI do sekundární lokality:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   Vytvořit primární lokalitu:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** konfigurace replikace systému v lokalitě 2:
    
   Zaregistrujte druhou lokalitu a spusťte replikaci systému. Spusťte následující příkaz <hanasid \> ADM:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** kontrolovat stav replikace

   Ověřte stav replikace a počkejte, dokud nebudou všechny databáze synchronizovány.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** změňte konfiguraci Hana tak, aby se komunikace pro replikaci systému Hana mohla posílat i přes virtuální síťová rozhraní replikace systému Hana.   
   - Zastavení HANA na obou webech
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Upravit global.ini pro přidání mapování hostitele pro replikaci systému HANA: použijte IP adresy z `hsr` podsítě.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - Spustit HANA na obou webech
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Další informace najdete v tématu [překlad názvů hostitelů pro replikaci systému](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[Ah]** Bránu firewall znovu povolte.  
   - Opětovné povolení brány firewall
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Otevřete potřebné porty brány firewall. Budete muset upravit porty pro číslo instance HANA.  

       > [!IMPORTANT]
       > Vytvořte pravidla brány firewall, která umožní komunikaci mezi uzly HANA a klientský provoz. Požadované porty jsou uvedené na [portech TCP/IP všech produktů SAP](https://help.sap.com/viewer/ports). Následující příkazy jsou pouze příkladem. V tomto scénáři s použitým systémovým číslem 03.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure](high-availability-guide-rhel-pacemaker.md) a vytvořte pro tento server Hana základní cluster Pacemaker.
Zahrňte všechny virtuální počítače, včetně většiny maker v clusteru.  

> [!IMPORTANT]
> Nenastavte `quorum expected-votes` na 2, protože se nejedná o cluster se dvěma uzly.  
> Ujistěte se, že je vlastnost clusteru `concurrent-fencing`  povolena, aby bylo deserializace uzlů.   

## <a name="create-file-system-resources"></a>Vytvoření prostředků systému souborů

1. **[1, 2]** zastavit SAP HANA na obou replikačních lokalitách. Spustit jako <\> ADM SID  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** Zrušit připojení systému souborů `/hana/shared` , které bylo dočasně připojeno k instalaci na všechny virtuální počítače Hana DB. Než budete moct zrušit připojení, budete muset zastavit všechny procesy a relace, které používají systém souborů. 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** vytvořte prostředky clusteru systému souborů pro `/hana/shared` v zakázaném stavu. Prostředky se vytvoří s možností `--disabled` , protože musíte definovat omezení umístění, než budou připojení povolená.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` do operace monitorování se přidá atribut, aby monitorování operací provádělo test čtení/zápisu v systému souborů. Bez tohoto atributu operace monitorování ověří, zda je systém souborů připojen. Může se jednat o problém, protože když dojde ke ztrátě připojení, systém souborů může zůstat připojený, i když není dostupný.  

   `on-fail=fence` do operace monitorování je také přidán atribut. V případě, že je tato možnost v uzlu neúspěšná, je tento uzel ihned ohrazen. Bez této možnosti je výchozím chováním zastavení všech prostředků, které jsou závislé na prostředku, který selhal, a pak restartujte prostředek, který selhal, a pak spusťte všechny prostředky, které jsou závislé na neúspěšném prostředku. Toto chování může trvat dlouhou dobu, pokud prostředek SAPHana závisí na neúspěšném prostředku, ale může také zcela selhat. Prostředek SAPHana nemůže být úspěšně zastaven, Pokud sdílená složka NFS, která je v binárních souborech HANA, je nepřístupná.  

4. **[1]** nakonfigurujte a ověřte atributy uzlu. Všem uzlům SAP HANA DB v lokalitě replikace 1 jsou přiřazeny atributy `S1` a na všech uzlech SAP HANA DB v replikační lokalitě 2 jsou přiřazeny atributy `S2` .  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** nakonfigurujte omezení, která určují, kam budou připojené SOUBOROVÉ systémy NFS, a povolte prostředky systému souborů.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Když povolíte prostředky systému souborů, cluster připojí `/hana/shared` systémy souborů.
 
6. **[Ah]** Ověřte, že jsou svazky ANF připojené pod `/hana/shared` na všechny virtuální počítače Hana DB na obou lokalitách.

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** nakonfigurujte prostředky atributů. Nakonfigurujte omezení, která nastaví atributy na `true` , pokud jsou připojení NFS pro `hana/shared` připojená.  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Pokud vaše konfigurace zahrnuje další systémy souborů, kromě/ `hana/shared` , které jsou připojené k systému souborů NFS, pak možnost zahrňte, `sequential=false` aby nedocházelo k žádným závislostem mezi systémy souborů. Všechny systémy souborů připojené k systému souborů NFS musí začínat před odpovídajícím prostředkem atributu, ale nemusí být spouštěny v libovolném pořadí, které je vzájemně relativní. Další informace najdete v tématu [návody konfigurace SAP HANA Scale-Out HSR v clusteru Pacemaker, když jsou systémy souborů Hana sdílené složkou NFS](https://access.redhat.com/solutions/5423971).  

8. **[1]** umístěte Pacemaker do režimu údržby a v přípravě na vytvoření prostředků clusteru Hana.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>Vytvoření prostředků clusteru SAP HANA

1. **[A]** nainstalujte agenta prostředků s možností škálování na více instancí na všechny uzly clusteru, včetně většiny maker.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Pokud chcete získat minimální podporovanou verzi balíčku pro verzi operačního systému, Projděte si [zásady podpory pro clustery RHEL ha – správa SAP HANA v clusteru](https://access.redhat.com/articles/3397471) `resource-agents-sap-hana-scaleout` .  

2. **[1, 2]** nainstalujte replikačního zavěšení systému Hana. Zavěšení musí být nainstalováno na jednom uzlu databáze HANA na všech lokalitách replikace systému. SAP HANA by měl být stále mimo provoz.        

   1. Příprava zavěšení jako `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Tvoř `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** Cluster vyžaduje konfiguraci sudoers na uzlu clusteru pro <\> ADM SID. V tomto příkladu je dosaženo vytvořením nového souboru. Spusťte příkazy jako `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** spusťte SAP HANA na obou replikačních lokalitách. Spustit jako <\> ADM SID  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** Ověřte instalaci zavěšení. Vykoná se <\> ADM SID na aktivní lokalitě replikace systému Hana.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** vytvořte prostředky clusteru Hana. Spusťte následující příkazy jako `root` .    
   1. Ujistěte se, že je cluster již v režimu údržby.  
    
   2. Potom vytvořte prostředek topologie HANA.  
      Při sestavování clusteru RHEL **7. x** použijte následující příkazy:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Při sestavování clusteru RHEL **8. x** použijte následující příkazy:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Potom vytvořte prostředek instance HANA.  
      > [!NOTE]
      > Tento článek obsahuje odkazy na *podřízený* termín, termín, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.  
 
      Při sestavování clusteru RHEL **7. x** použijte následující příkazy:    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Při sestavování clusteru RHEL **8. x** použijte následující příkazy:  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > Doporučujeme, abyste jako osvědčený postup nastavili pouze AUTOMATED_REGISTER na **ne** a prováděly důkladné testy pro převzetí služeb při selhání, aby se neúspěšná primární instance automaticky registrovala jako sekundární. Po úspěšném dokončení testů pro převzetí služeb při selhání nastavte AUTOMATED_REGISTER na **Ano**, aby se mohla replikace systému automaticky obnovit. 

   4. Vytvoření virtuální IP adresy a přidružených prostředků  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. Vytvoření omezení clusteru  
      Při sestavování clusteru RHEL **7. x** použijte následující příkazy:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Při sestavování clusteru RHEL **8. x** použijte následující příkazy:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** umístit cluster do režimu údržby. Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > Časové limity ve výše uvedené konfiguraci jsou jenom příklady a možná je budete muset přizpůsobit konkrétnímu nastavení HANA. Například může být nutné prodloužit časový limit spuštění, pokud bude trvat déle, než se spustí databáze SAP HANA.
  
## <a name="test-sap-hana-failover"></a>Test SAP HANA převzetí služeb při selhání 

1. Než začnete s testem, zkontrolujte stav replikace clusteru a SAP HANA systému.  

   a. Ověřte, že neexistují žádné neúspěšné akce clusteru.  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Ověřte, jestli je replikace systému SAP HANA synchronizovaná.

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Ověřte konfiguraci clusteru pro scénář selhání, když uzel ztratí přístup ke sdílené složce systému souborů NFS ( `/hana/shared` ).  

   Agenti SAP HANA prostředků závisí na binárních souborech uložených v `/hana/shared` k provádění operací během převzetí služeb při selhání. Systém souborů `/hana/shared` je v předložené konfiguraci připojen přes systém souborů NFS. Test, který může být proveden, je znovu připojit `/hana/shared` systém souborů *pouze pro čtení*. Tento přístup ověří, že cluster převezme služby při selhání, pokud dojde ke ztrátě přístupu k `/hana/shared` aktivní lokalitě replikace systému.  

   **Očekávaný výsledek**: když se znovu připojíte `/hana/shared` jako jen *pro čtení*, operace monitorování, která provádí operaci čtení/zápisu v systému souborů, selže, protože není možné zapisovat do systému souborů a aktivovat převzetí služeb při selhání prostředků Hana. Je očekáván stejný výsledek, pokud váš uzel HANA ztratí přístup ke sdílené složce systému souborů NFS.  
     
   Stav prostředků clusteru můžete zjistit spuštěním `crm_mon` nebo `pcs status` . Stav prostředku před spuštěním testu:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Chcete-li simulovat selhání pro `/hana/shared` některý z primárních virtuálních počítačů lokality replikace, spusťte následující příkaz:
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   Virtuální počítač HANA, který ztratil přístup k nástroji `/hana/shared` , by měl v závislosti na konfiguraci clusteru restartovat nebo zastavit. Prostředky clusteru se migrují do jiné lokality replikace systému HANA.  
         
   Pokud cluster nezačal na virtuálním počítači, který se restartoval, spusťte cluster spuštěním příkazu: 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   Po spuštění clusteru se `/hana/shared` automaticky připojí systém souborů.     
   Pokud nastavíte AUTOMATED_REGISTER = "false", budete muset nakonfigurovat SAP HANA systémová replikace v sekundární lokalitě. V takovém případě můžete spustit tyto příkazy k překonfigurování SAP HANA jako sekundární.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   Stav prostředků po testu: 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


Doporučujeme důkladně otestovat SAP HANA konfiguraci clusteru, a to tím, že provádíte testy popsané v části [ha pro SAP HANA na virtuálních počítačích Azure na RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).


## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuálních počítačích)][sap-hana-ha].