---
title: SAP HANA horizontálního navýšení kapacity pomocí HSR a Pacemaker na SLES | Microsoft Docs
description: SAP HANA horizontálního navýšení kapacity pomocí HSR a Pacemaker na SLES.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 637616c3698cc9ec0cd13a4584bad24b6ed02c34
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315396"
---
# <a name="high-availability-for-sap-hana-scale-out-system-with-hsr-on-suse-linux-enterprise-server"></a>Vysoká dostupnost pro SAP HANA systému škálování na více systémů s HSR na SUSE Linux Enterprise Server 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


Tento článek popisuje, jak nasadit vysoce dostupný SAP HANA systém v konfiguraci se škálováním na více systémů pomocí replikace systému HANA (HSR) a Pacemaker v Azure SUSE Linux Enterprise Server virtuálních počítačích (VM). Sdílené systémy souborů v prezentované architektuře poskytují [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) a jsou připojené přes systém souborů NFS.  

V ukázkových konfiguracích, instalačních příkazech atd. instance HANA je **03** a ID systému Hana je **HN1**. Příklady jsou založené na HANA 2,0 SP4 a SUSE Linux Enterprise Server 12 SP5. 

Než začnete, přečtěte si následující poznámky a dokumenty SAP:

* [Dokumentace k Azure NetApp Files][anf-azure-doc] 
* Poznámka [1928533] pro SAP obsahuje:  
  * Seznam velikostí virtuálních počítačů Azure, které jsou podporované pro nasazení softwaru SAP
  * Důležité informace o kapacitě pro velikosti virtuálních počítačů Azure
  * Podporovaný software SAP a kombinace operačního systému (OS) a databáze
  * Požadovaná verze jádra SAP pro Windows a Linux v Microsoft Azure
* SAP Note [2015553]: seznam požadavků pro nasazení softwaru SAP podporovaného SAP v Azure
* SAP Note [2205917]: obsahuje doporučené nastavení operačního systému pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [1944799]: obsahuje pokyny pro sap pro SUSE Linux Enterprise Server pro aplikace SAP
* SAP Note [2178632]: obsahuje podrobné informace o všech metrikách monitorování hlášených pro SAP v Azure.
* SAP Note [2191498]: obsahuje požadovanou verzi agenta hostitele SAP pro Linux v Azure.
* SAP Note [2243692]: obsahuje informace o LICENCOVÁNí SAP v systému Linux v Azure
* SAP Note [1984787]: obsahuje obecné informace o SUSE Linux Enterprise Server 12
* SAP Note [1999351]: obsahuje další informace o řešení potíží pro rozšíření Azure Enhanced Monitoring pro SAP.
* Poznámka SAP poznámky [1900823]: obsahuje informace o požadavcích na úložiště SAP HANA.
* [Komunitní wikiweb pro SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): obsahuje všechny požadované poznámky SAP pro Linux
* [Plánování a implementace služby Azure Virtual Machines pro SAP v systému Linux][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP v systému Linux][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP v systému Linux][dbms-guide]
* [Průvodce osvědčenými postupy pro SAP SUSE][suse-ha-guide]: obsahuje všechny požadované informace pro nastavení vysoké dostupnosti NetWeaver SAP Hana a místní replikaci systému (pro použití jako obecné standardní hodnoty), které poskytují mnohem podrobnější informace.
* [SUSE zpráva k vydání verze 12 SP5 pro rozšíření pro vysokou dostupnost](https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP5/)
* [Zpracování neúspěšné sdílené složky systému souborů NFS v clusteru SUSE HA pro replikaci systému HANA](https://www.suse.com/support/kb/doc/?id=000019904)
* [NetApp aplikace SAP na Microsoft Azure pomocí Azure NetApp Files][anf-sap-applications-azure]
* [Svazky NFS v4.1 ve službě Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Přehled

Jedna z metod, jak dosáhnout vysoké dostupnosti pro Hana s vysokou dostupností pro instalace na více instancí HANA, je konfigurace replikace systému HANA a ochrana řešení pomocí clusteru Pacemaker, aby bylo možné automatické převzetí služeb při selhání. Když dojde k selhání aktivního uzlu, cluster převezme prostředky HANA do druhé lokality.  
Uvedená konfigurace zobrazuje tři uzly HANA na každé lokalitě, a to i majoritní uzel maker, aby se zabránilo scénáři děleného mozku. Pokyny mohou být upraveny tak, aby zahrnovaly více virtuálních počítačů jako uzly databáze HANA.  

Sdílený systém souborů HANA `/hana/shared` v prezentované architektuře poskytuje [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Je připojen přes NFSv 4.1 na každém uzlu HANA ve stejné lokalitě replikace systému HANA. Souborové systémy `/hana/data` a `/hana/log` jsou místní souborové systémy a mezi uzly databáze Hana se nesdílí. SAP HANA bude nainstalován v nesdíleném režimu. 

> [!TIP]
> Doporučené konfigurace úložiště SAP HANA najdete v tématu [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md).   

[![SAP HANA horizontálního navýšení kapacity s clusterem HSR a Pacemaker v SLES](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse.png)](./media/sap-hana-high-availability/sap-hana-high-availability-scale-out-hsr-suse-detail.png#lightbox)

V předchozím diagramu se tři podsítě reprezentují v rámci jedné virtuální sítě Azure, a to podle doporučení SAP HANA sítě: 
* pro komunikaci s klientem – `client` 10.23.0.0/24  
* pro interní komunikaci mezi uzly HANA – `inter` 10.23.1.128/26  
* pro replikaci systému HANA – `hsr` 10.23.1.192/26  

Stejně jako `/hana/data` a `/hana/log` jsou nasazeny na místních discích není nutné nasazovat samostatnou podsíť a samostatné virtuální síťové karty pro komunikaci do úložiště.  

Svazky NetApp Azure se nasazují v samostatné podsíti, [delegované do Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet): `anf` 10.23.1.0/26.   

> [!IMPORTANT]
> Replikace systému na třetí lokalitu se nepodporuje. Podrobnosti najdete v části "důležité požadavky" v tématu [SLES SAP HANA – scénář optimalizace výkonu replikace systému](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_important_prerequisites)na více systémů.     

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
      1. Nastavte **přiřazení** na **statické** a zadejte IP adresu (například **10.23.0.27**).
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

Nasaďte svazky ANF pro `/hana/shared` systém souborů. `/hana/shared`Pro každou lokalitu replikace systému Hana budete potřebovat samostatný svazek. Další informace najdete v tématu [Nastavení infrastruktury Azure NetApp Files](./sap-hana-scale-out-standby-netapp-files-suse.md#set-up-the-azure-netapp-files-infrastructure).

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
     10.23.0.19      hana-s1-db1
     10.23.0.20      hana-s1-db2
     10.23.0.21      hana-s1-db3
     10.23.0.22      hana-s2-db1
     10.23.0.23      hana-s2-db2
     10.23.0.24      hana-s2-db3
     10.23.0.25      hana-s-mm    
     # Internode subnet
     10.23.1.132     hana-s1-db1-inter
     10.23.1.133     hana-s1-db2-inter
     10.23.1.134     hana-s1-db3-inter
     10.23.1.135     hana-s2-db1-inter
     10.23.1.136     hana-s2-db2-inter
     10.23.1.137     hana-s2-db3-inter
     # HSR subnet
     10.23.1.196     hana-s1-db1-hsr
     10.23.1.197     hana-s1-db2-hsr
     10.23.1.198     hana-s1-db3-hsr
     10.23.1.199     hana-s2-db1-hsr
     10.23.1.200     hana-s2-db2-hsr
     10.23.1.201     hana-s2-db3-hsr
    ```

2. **[A]** SUSE poskytuje speciální agenty prostředků pro SAP Hana a ve výchozím nastavení se nainstalují agenti pro SAP HANA ScaleUp. Odinstalujte balíčky pro ScaleUp, pokud jsou nainstalované, a nainstalujte balíčky pro scénář SAP HANAScaleOut. Tento krok je potřeba provést na všech virtuálních počítačích clusteru, včetně většiny maker.   

    ```bash
     # Uninstall ScaleUp packages and patterns
     zypper remove patterns-sap-hana
     zypper remove SAPHanaSR 
     zypper remove SAPHanaSR-doc
     zypper remove yast2-sap-ha
     # Install the ScaleOut packages and patterns
     zypper in SAPHanaSR-ScaleOut  SAPHanaSR-ScaleOut-doc 
     zypper in -t pattern ha_sles
    ```

3. **[Ah]** Příprava virtuálních počítačů – použijte doporučené nastavení pro SAP Note [2205917] pro SUSE Linux Enterprise Server pro aplikace SAP.  

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
    mount 10.23.1.7:/HN1-share-s1 /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

4. **[AH1]** Připojte sdílené Azure NetApp Files svazky na virtuálních počítačích databáze SITE1 HANA.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s1 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    ```

5. **[AH2]** Připojte sdílené Azure NetApp Files svazky na virtuálních počítačích databáze SITE2 HANA.  

    ```bash
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.7:/HN1-shared-s2 /hana/shared nfs rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
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

## <a name="create-a-pacemaker-cluster"></a>Vytvoření clusteru Pacemaker

Postupujte podle kroků v části [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) a vytvořte pro tento server Hana základní cluster Pacemaker.
Zahrňte všechny virtuální počítače, včetně většiny maker v clusteru.  

> [!IMPORTANT]
> Nenastavte `quorum expected-votes` na 2, protože se nejedná o cluster se dvěma uzly.  
> Ujistěte se, že je vlastnost clusteru `concurrent-fencing`  povolena, aby bylo deserializace uzlů.   

## <a name="installation"></a>Instalace  

V tomto příkladu pro nasazení SAP HANA v konfiguraci škálování na více instancí s HSR na virtuálních počítačích Azure jsme použili HANA 2,0 SP4.  

### <a name="prepare-for-hana-installation"></a>Příprava instalace HANA

1. **[Ah]** Před instalací HANA nastavte kořenové heslo. Po dokončení instalace můžete kořenové heslo zakázat. Spustit jako `root` příkaz `passwd`  

2. **[1, 2]** změnit oprávnění pro `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** ověřte, že se můžete přihlásit přes SSH k virtuálním počítačům databáze Hana v této lokalitě **Hana-S1-DB2** a **Hana-S1-DB3**, aniž by se zobrazila výzva k zadání hesla. Pokud se nejedná o tento případ, klíče protokolu SSH systému Exchange, jak je popsáno v tématu [Povolení přístupu SSH přes Veřejný klíč](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_enable_ssh_access_via_public_key_optional).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** ověřte, že se můžete přihlásit přes SSH k virtuálním počítačům databáze Hana v této lokalitě **: Hana-S2-DB2** a **Hana-S2-DB3**, aniž by se zobrazila výzva k zadání hesla.  
   Pokud se nejedná o tento případ, klíče protokolu SSH systému Exchange.    
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Nainstalujte další balíčky, které se vyžadují pro HANA 2,0 SP4. Další informace najdete v tématu SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) pro vaši verzi SLES. 

    ```bash
    # In this example, using SLES12 SP5
    sudo zypper install libgcc_s1 libstdc++6 libatomic1
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
     10.23.1.132 = hana-s1-db1
     10.23.1.133 = hana-s1-db2
     10.23.1.134 = hana-s1-db3
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
    "hana-s2-db1","net_publicname","10.23.0.22"
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
    10.23.1.196 = hana-s1-db1
    10.23.1.197 = hana-s1-db2
    10.23.1.198 = hana-s1-db3
    10.23.1.199 = hana-s2-db1
    10.23.1.200 = hana-s2-db2
    10.23.1.201 = hana-s2-db3
    ```

   - Spustit HANA na obou webech
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Další informace najdete v tématu [překlad názvů hostitelů pro replikaci systému](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

## <a name="create-file-system-resources"></a>Vytvoření prostředků systému souborů

Vytvořte soubor s fiktivním clusterem systému souborů, který bude monitorovat a nahlásit chyby v případě, že dojde k potížím při přístupu k systému souborů připojenému k systému souborů NFS `/hana/shared` . Díky tomu může cluster aktivovat převzetí služeb při selhání, pokud dojde k potížím při přístupu `/hana/shared` . Další podrobnosti najdete v článku [zpracování neúspěšné sdílené složky NFS v clusteru SUSE ha pro replikaci systému Hana](https://www.suse.com/support/kb/doc/?id=000019904) . 

1. **[1]** umístěte Pacemaker do režimu údržby a v přípravě na vytvoření prostředků clusteru Hana.  
    ```bash
    crm configure property maintenance-mode=true
    ```

2. **[1, 2]** vytvoří adresář na svazku ANF/Hana/sahred, který se použije ve speciálním prostředku monitorování systému souborů. V obou lokalitách je nutné vytvořit adresáře.  
    ```bash
    mkdir -p /hana/shared/HN1/check
    ```

2. **[Ah]** Vytvořte adresář, který bude použit k připojení zvláštního prostředku monitorování systému souborů. Adresář musí být vytvořený na všech uzlech clusteru HANA.  
    ```bash
    mkdir -p /hana/check
    ```

3. **[1]** vytvořte prostředky clusteru souborového systému.     

    ```bash
    crm configure primitive fs_HN1_HDB03_fscheck Filesystem \
      params device="/hana/shared/HN1/check" \
      directory="/hana/check" fstype=nfs4 \
      options="bind,defaults,rw,hard,proto=tcp,intr,noatime,vers=4.1,lock" \
      op monitor interval=120 timeout=120 on-fail=fence \
      op_params OCF_CHECK_LEVEL=20 \
      op start interval=0 timeout=120 op stop interval=0 timeout=120

    crm configure clone cln_fs_HN1_HDB03_fscheck fs_HN1_HDB03_fscheck \
      meta clone-node-max=1 interleave=true

    crm configure location loc_cln_fs_HN1_HDB03_fscheck_not_on_mm \
      cln_fs_HN1_HDB03_fscheck -inf: hana-s-mm    
    ```
 
   `OCF_CHECK_LEVEL=20` do operace monitorování se přidá atribut, aby monitorování operací provádělo test čtení/zápisu v systému souborů. Bez tohoto atributu operace monitorování ověří, zda je systém souborů připojen. Může se jednat o problém, protože když dojde ke ztrátě připojení, systém souborů může zůstat připojený, i když není dostupný.  

   `on-fail=fence` do operace monitorování je také přidán atribut. V případě, že je tato možnost v uzlu neúspěšná, je tento uzel ihned ohrazen.   

## <a name="create-sap-hana-cluster-resources"></a>Vytvoření prostředků clusteru SAP HANA

1. **[1, 2]** nainstalujte replikačního zavěšení systému Hana. Zavěšení musí být nainstalováno na jednom uzlu databáze HANA na všech lokalitách replikace systému.         

   1. Příprava zavěšení jako `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Zastavte HANA na obou lokalitách replikace systému. Spustit jako <\> ADM SID:
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Tvoř `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[Ah]** Cluster vyžaduje konfiguraci sudoers na uzlu clusteru pro <\> ADM SID. V tomto příkladu je dosaženo vytvořením nového souboru. Spusťte příkazy jako `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
    Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
    EOF
    ```

3. **[1, 2]** spusťte SAP HANA na obou replikačních lokalitách. Spustit jako <\> ADM SID  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** Ověřte instalaci zavěšení. Vykoná se <\> ADM SID na aktivní lokalitě replikace systému Hana.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # 2021-03-31 01:02:42.695244 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:02:58.966856 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.453100 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.619768 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:03:04.743444 ha_dr_SAPHanaSR SFAIL
     # 2021-03-31 01:04:15.062181 ha_dr_SAPHanaSR SOK

    ```

5. **[1]** vytvořte prostředky clusteru Hana. Spusťte následující příkazy jako `root` .    
   1. Ujistěte se, že je cluster již v režimu údržby.  
    
   2. Potom vytvořte prostředek topologie HANA.  
      ```bash
      sudo crm configure primitive rsc_SAPHanaTopology_HN1_HDB03 ocf:suse:SAPHanaTopology \
        op monitor interval="10" timeout="600" \
        op start interval="0" timeout="600" \
        op stop interval="0" timeout="300" \
        params SID="HN1" InstanceNumber="03"

      sudo crm configure clone cln_SAPHanaTopology_HN1_HDB03 rsc_SAPHanaTopology_HN1_HDB03 \
       meta clone-node-max="1" target-role="Started" interleave="true"
      ```

   3. Potom vytvořte prostředek instance HANA.  
      > [!NOTE] 
      > Tento článek obsahuje odkazy na *Hlavní* a *podřízené* výrazy, které Microsoft už nepoužívá. Po odebrání těchto podmínek ze softwaru je odebereme z tohoto článku.
 
      ```bash
      sudo crm configure primitive rsc_SAPHana_HN1_HDB03 ocf:suse:SAPHanaController \
        op start interval="0" timeout="3600" \
        op stop interval="0" timeout="3600" \
        op promote interval="0" timeout="3600" \
        op monitor interval="60" role="Master" timeout="700" \
        op monitor interval="61" role="Slave" timeout="700" \
        params SID="HN1" InstanceNumber="03" PREFER_SITE_TAKEOVER="true" \
        DUPLICATE_PRIMARY_TIMEOUT="7200" AUTOMATED_REGISTER="false"

      sudo crm configure ms msl_SAPHana_HN1_HDB03 rsc_SAPHana_HN1_HDB03 \
        meta clone-node-max="1" master-max="1" interleave="true"
      ```
      > [!IMPORTANT]
      > Doporučujeme, abyste jako osvědčený postup nastavili pouze AUTOMATED_REGISTER na **ne** a prováděly důkladné testy pro převzetí služeb při selhání, aby se neúspěšná primární instance automaticky registrovala jako sekundární. Po úspěšném dokončení testů pro převzetí služeb při selhání nastavte AUTOMATED_REGISTER na **Ano**, aby se mohla replikace systému automaticky obnovit. 

   4. Vytvoření virtuální IP adresy a přidružených prostředků  
      ```bash
      sudo crm configure primitive rsc_ip_HN1_HDB03 ocf:heartbeat:IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="10.23.0.27"

      sudo crm configure primitive rsc_nc_HN1_HDB03 azure-lb port=62503 \
        meta resource-stickiness=0

      sudo crm configure group g_ip_HN1_HDB03 rsc_ip_HN1_HDB03 rsc_nc_HN1_HDB03
      ```

   5. Vytvoření omezení clusteru  
      ```bash
      # Colocate the IP with HANA master
      sudo crm configure colocation col_saphana_ip_HN1_HDB03 4000: g_ip_HN1_HDB03:Started \
        msl_SAPHana_HN1_HDB03:Master  

      # Start HANA Topology before HANA  instance
      sudo crm configure order ord_SAPHana_HN1_HDB03 Optional: cln_SAPHanaTopology_HN1_HDB03 \
        msl_SAPHana_HN1_HDB03

      # HANA resources don't run on the majority maker node
      sudo crm configure location loc_SAPHanaCon_not_on_majority_maker msl_SAPHana_HN1_HDB03 -inf: hana-s-mm
      sudo crm configure location loc_SAPHanaTop_not_on_majority_maker cln_SAPHanaTopology_HN1_HDB03 -inf: hana-s-mm
      ```

6. **[1]** konfigurace dalších vlastností clusteru   
    ```bash
    sudo crm configure rsc_defaults resource-stickiness=1000
    sudo crm configure rsc_defaults migration-threshold=50
    ```
7. **[1]** ověřit komunikaci mezi zavěšením a clusterem
    ```bash
    crm_attribute -G -n hana_hn1_glob_srHook
    # Expected result
    # crm_attribute -G -n hana_hn1_glob_srHook
    # scope=crm_config  name=hana_hn1_glob_srHook value=SOK
    ```

8. **[1]** umístit cluster do režimu údržby. Ujistěte se, že stav clusteru je OK a že jsou spuštěné všechny prostředky.  
    ```bash
    # Cleanup any failed resources - the following command is example 
    crm resource cleanup rsc_SAPHana_HN1_HDB03

    # Place the cluster out of maintenance mode
    sudo crm configure property maintenance-mode=false
    ```
  
   > [!NOTE]
   > Časové limity ve výše uvedené konfiguraci jsou jenom příklady a možná je budete muset přizpůsobit konkrétnímu nastavení HANA. Například může být nutné prodloužit časový limit spuštění, pokud bude trvat déle, než se spustí databáze SAP HANA.
  

## <a name="test-sap-hana-failover"></a>Test SAP HANA převzetí služeb při selhání 

> [!NOTE]
> Tento článek obsahuje odkazy na *Hlavní* a *podřízené* výrazy, které Microsoft už nepoužívá. Po odebrání těchto podmínek ze softwaru je odebereme z tohoto článku.

1. Než začnete s testem, zkontrolujte stav replikace clusteru a SAP HANA systému.    

   a. Ověřte, že neexistují žádné neúspěšné akce clusteru.  
     ```bash
     #Verify that there are no failed cluster actions
     crm status
     # Example 
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Full list of resources:
     #
     # stonith-sbd    (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #     Stopped: [ hana-s-mm ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     ```

   b. Ověřte, jestli je replikace systému SAP HANA synchronizovaná.

     ```bash
     # Verify HANA HSR is in sync
     sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
     #| Database | Host         | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary    | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
     #|          |              |       |              |           |         |           | Host         | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
     #| -------- | ------------ | ----- | ------------ | --------- | ------- | --------- | ------------ | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
     #| SYSTEMDB | hana-s1-db1  | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1  |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1  |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db1  | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db3  | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db3  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #| HN1      | hana-s1-db2  | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db2  |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
     #
     #status system replication site "1": ACTIVE
     #overall system replication status: ACTIVE
     #
     #Local System Replication State
     #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
     #
     #mode: PRIMARY
     #site id: 1
     #site name: HANA_S1
     ```

2. Doporučujeme důkladně ověřit konfiguraci SAP HANA clusterů, a to provedením testů popsaných v části [ha pro SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md#test-the-cluster-setup) a ve [scénáři optimalizace výkonu replikace SLES pro škálování](https://documentation.suse.com/sbp/all/html/SLES4SAP-hana-scaleOut-PerfOpt-12/index.html#_testing_the_cluster)na více instancí.

3. Ověřte konfiguraci clusteru pro scénář selhání, když uzel ztratí přístup ke sdílené složce systému souborů NFS ( `/hana/shared` ).  

   Agenti SAP HANA prostředků závisí na binárních souborech uložených v `/hana/shared` k provádění operací během převzetí služeb při selhání. Systém souborů `/hana/shared` je v předložené konfiguraci připojen přes systém souborů NFS. Test, který může být proveden, je vytvořit dočasné pravidlo brány firewall pro blokování přístupu ke `/hana/shared` svazku ANF na jednom z virtuálních počítačů primární lokality. Tento přístup ověří, že cluster převezme služby při selhání, pokud dojde ke ztrátě přístupu k `/hana/shared` aktivní lokalitě replikace systému.  

   **Očekávaný výsledek**: když zablokujete přístup ke `/hana/shared` svazku ANF na jednom z virtuálních počítačů primární lokality, operace monitorování, která provádí operace čtení/zápisu v systému souborů, selže, protože nebude mít přístup k systému souborů a bude aktivovat převzetí služeb při selhání prostředku Hana. Je očekáván stejný výsledek, pokud váš uzel HANA ztratí přístup ke sdílené složce systému souborů NFS.  
     
   Stav prostředků clusteru můžete zjistit spuštěním `crm_mon` nebo `crm status` . Stav prostředku před spuštěním testu:
     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s1-db1 ]
     #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1     
      ```

   Chcete-li simulovat selhání pro `/hana/shared` , nejprve potvrďte IP adresu pro `/hana/shared` svazek ANF v primární lokalitě. To lze provést spuštěním `df -kh|grep /hana/shared` . 
   Pak nastavte dočasné pravidlo brány firewall pro blokování přístupu k IP adrese `/hana/shared` ANF svazku, a to spuštěním následujícího příkazu na jednom z primárních virtuálních počítačů lokality replikace systému Hana.
   V tomto příkladu se příkaz spustil v Hana-S1-DB1.

     ```bash
     iptables -A INPUT -s 10.23.1.7 -j DROP; iptables -A OUTPUT -d 10.23.1.7 -j DROP
     ```

   Prostředky clusteru budou migrovány do jiného webového serveru replikace systému HANA.    
              
   Pokud nastavíte AUTOMATED_REGISTER = "false", budete muset nakonfigurovat SAP HANA systémová replikace v sekundární lokalitě. V takovém případě můžete spustit tyto příkazy k překonfigurování SAP HANA jako sekundární.   

     ```bash
     # Execute on the secondary 
     su - hn1adm
     # Make sure HANA is not running on the secondary site. If it is started, stop HANA
     sapcontrol -nr 03 -function StopWait 600 10
     # Register the HANA secondary site
     hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
     # Switch back to root and cleanup failed resources
     crm resource cleanup SAPHana_HN1_HDB03
     ```

   Stav prostředků po testu: 

     ```bash
     # Output of crm_mon
     #7 nodes configured
     #24 resource instances configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #stonith-sbd     (stonith:external/sbd): Started hana-s-mm
     # Clone Set: cln_fs_HN1_HDB03_fscheck [fs_HN1_HDB03_fscheck]
     #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Clone Set: cln_SAPHanaTopology_HN1_HDB03 [rsc_SAPHanaTopology_HN1_HDB03]
     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     # Master/Slave Set: msl_SAPHana_HN1_HDB03 [rsc_SAPHana_HN1_HDB03]
     #     Masters: [ hana-s2-db1 ]
     #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
     # Resource Group: g_ip_HN1_HDB03
     #     rsc_ip_HN1_HDB03   (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
     #     rsc_nc_HN1_HDB03   (ocf::heartbeat:azure-lb):      Started hana-s2-db1
     ```


## <a name="next-steps"></a>Další kroky

* [Plánování a implementace Azure Virtual Machines pro SAP][planning-guide]
* [Nasazení Azure Virtual Machines pro SAP][deployment-guide]
* [Nasazení Azure Virtual Machines DBMS pro SAP][dbms-guide]
* [Svazky NFS v4.1 ve službě Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)
* Další informace o tom, jak vytvořit vysokou dostupnost a naplánovat zotavení po havárii SAP HANA na virtuálních počítačích Azure, najdete v tématu [Vysoká dostupnost SAP HANA na azure Virtual Machines (virtuálních počítačích)][sap-hana-ha].