---
title: SAP ASCS/SCS multi-SID HA s WSFC a sdíleným diskem Azure | Microsoft Docs
description: Vysoká dostupnost více identifikátorů SID pro instanci SAP ASCS/SCS se službou WSFC a sdíleným diskem Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: cbf18abe-41cb-44f7-bdec-966f32c89325
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/12/2020
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c8116f3e00d13c0bd1e5f075a7fbe3264f337079
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970397"
---
# <a name="sap-ascsscs-instance-multi-sid-high-availability-with-windows-server-failover-clustering-and-azure-shared-disk"></a>Vysoká dostupnost služby SAP ASCS/SCS instance multi-SID s využitím clusteringu s podporou převzetí služeb při selhání Windows serveru a sdíleného disku Azure

> ![Operační systém Windows][Logo_Windows] Windows
>

Tento článek se zaměřuje na to, jak přejít z jedné instalace ASCS/SCS do konfigurace SAP multi-SID instalací dalších clusterových instancí SAP ASCS/SCS do stávajícího clusteru služby Windows Server Failover Clustering (WSFC) se sdíleným diskem Azure. Po dokončení tohoto procesu jste nakonfigurovali cluster SAP s více identifikátory SID.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

V současné době můžete pro instanci SAP ASCS/SCS použít disky Azure SSD úrovně Premium jako sdílený disk Azure. Jsou zavedena tato omezení:

-  [Azure Ultra disk](../../disks-types.md#ultra-disk) se nepodporuje jako sdílený disk Azure pro úlohy SAP. V současné době není možné umístit virtuální počítače Azure pomocí Azure Ultra disk v sadě dostupnosti.
-  [Sdílený disk Azure](../../windows/disks-shared.md) s SSD úrovně Premium disky se podporuje jenom s virtuálními počítači ve skupině dostupnosti. V nasazení Zóny dostupnosti se nepodporuje. 
-  Hodnota sdíleného disku Azure [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes) určuje, kolik uzlů clusteru může používat sdílený disk. V případě instance SAP ASCS/SCS nakonfigurujete dva uzly v clusteru s podporou převzetí služeb při selhání systému Windows, takže hodnota vlastnosti `maxShares` musí být nastavena na hodnotu dvě.
-  Všechny virtuální počítače s clustery SAP ASCS/SCS musí být nasazené ve stejné [skupině umístění služby Azure Proximity](../../windows/proximity-placement-groups.md).   
   I když můžete nasadit virtuální počítače clusterů Windows ve skupině dostupnosti se sdíleným diskem Azure bez PPG, PPG zajistí uzavření fyzické blízkosti sdílených disků Azure a virtuálních počítačů clusteru, čímž se dosáhne nižší latence mezi virtuálními počítači a vrstvou úložiště.    

Další podrobnosti o omezeních pro sdílený disk Azure najdete v části s [omezeními](../../linux/disks-shared.md#limitations) v dokumentaci ke sdíleným diskům Azure.  

> [!IMPORTANT]
> Při nasazování clusteru SAP ASCS/SCS Windows s podporou převzetí služeb při selhání se sdíleným diskem Azure Pamatujte na to, že nasazení bude pracovat s jedním sdíleným diskem v jednom úložném clusteru. Vaše instance SAP ASCS/SCS bude ovlivněna v případě problémů s clusterem úložiště, kde je nasazen sdílený disk Azure.  

> [!IMPORTANT]
> Nastavení musí splňovat následující podmínky:
> * Každý identifikátor SID systému správy databáze (DBMS) musí mít vlastní vyhrazený cluster WSFC.  
> * Aplikační servery SAP, které patří k jednomu identifikátoru zabezpečení systému SAP, musí mít vlastní vyhrazené virtuální počítače.  
> * Kombinace replikačního serveru fronty 1 a fronty replikačního serveru 2 ve stejném clusteru není podporovaná.  


## <a name="supported-os-versions"></a>Podporované verze operačního systému

Jsou podporovány systémy Windows Server 2016 i Windows Server 2019 (použijte nejnovější obrázky datového centra).

Důrazně doporučujeme používat **Windows Server 2019 Datacenter**, jako je:
- Clusterová služba s podporou převzetí služeb při selhání systému Windows 2019 podporuje Azure
- Monitorování pro události plánu Azure vám přidává integraci a povědomí o údržbě hostitelů Azure a zlepšení prostředí.
- Je možné použít název distribuované sítě (Jedná se o výchozí možnost). Proto není nutné mít vyhrazenou IP adresu pro síťový název clusteru. Kromě toho není potřeba konfigurovat tuto IP adresu v interních Load Balancer Azure. 

## <a name="architecture"></a>Architektura

V konfiguraci s více identifikátory SID jsou podporovány oba servery replikace ve frontě (ERS1) a server replikační replikace 2 (ERS2).  Kombinace ERS1 a ERS2 není ve stejném clusteru podporovaná. 

1. První příklad ukazuje dvě identifikátory SID SAP s architekturou ERS1, kde:

   - SAP SID1 se nasazuje na sdílený disk s ERS1. Instance OLAJÍCÍCH je nainstalovaná na místním hostiteli a na místním disku.
     SAP SID1 má svou vlastní (virtuální) IP adresu (SID1 (A) SCS IP1), která je nakonfigurovaná na interním nástroji pro vyrovnávání zatížení Azure.

   - SAP SID2 se nasazuje na sdílený disk s ERS1. Instance OLAJÍCÍCH je nainstalovaná na místním hostiteli a na místním disku.
     SAP SID2 má vlastní (virtuální) IP adresu (SID2 (A) SCS IP2), která je nakonfigurovaná taky na interním nástroji pro vyrovnávání zatížení Azure.

![Instance SAP ASCS/SCS s vysokou dostupností – dvě instance s konfigurací ERS1][sap-ha-guide-figure-6007]

2. Druhý příklad ukazuje dvě identifikátory SID SAP s architekturou ERS2, kde: 

   - SAP SID1 s ERS2, je také clusterovaný a nasazený na místním disku.  
     SAP SID1 má vlastní (virtuální) IP adresu (SID1 (A) SCS IP1), která je nakonfigurovaná na interním nástroji pro vyrovnávání zatížení Azure.
     SAP ERS2, který používá systém SAP SID1, má svou vlastní (virtuální) IP adresu (SID1 ERS2 IP2), která je nakonfigurovaná na interním nástroji pro vyrovnávání zatížení Azure.

   - SAP SID2 s ERS2, je také clusterovaný a nasazený na místním disku.  
     SAP SID2 má vlastní (virtuální) IP adresu (SID2 (A) SCS IP3), která je nakonfigurovaná na interním nástroji pro vyrovnávání zatížení Azure.
     SAP ERS2, který používá systém SAP SID2, má svou vlastní (virtuální) IP adresu (SID2 ERS2 IP4), která je nakonfigurovaná na interním nástroji pro vyrovnávání zatížení Azure.

   Tady máme celkem čtyři virtuální IP adresy:  
   - SID1 (A) SCS IP1
   - SID2 ERS2 IP2
   - SID2 (A) SCS IP3
   - SID2 ERS2 IP4

![Instance SAP ASCS/SCS s vysokou dostupností – dvě instance s konfigurací ERS1 a ERS2][sap-ha-guide-figure-6008]

## <a name="infrastructure-preparation"></a>Příprava infrastruktury

Kromě **existující instance clusteru** SAP **PR1** ASCS/SCS budeme instalovat nové **PR2**SAP SID.  

### <a name="host-names-and-ip-addresses"></a>Názvy hostitelů a IP adresy

| Role názvu hostitele | Název hostitele | Statická IP adresa | Skupina dostupnosti | Skupina umístění blízkosti |
| --- | --- | --- |---| ---|
| cluster ASCS/SCS prvního uzlu clusteru |PR1-ASCS-10 |10.0.0.4 |PR1-ASCS-avset |PR1PPG |
| druhý cluster uzlu clusteru ASCS/SCS |PR1-ASCS-11 |10.0.0.5 |PR1-ASCS-avset |PR1PPG |
| Název sítě s clustery | pr1clust |10.0.0.42 (**pouze** pro cluster se Win 2016) | neuvedeno | neuvedeno |
| **SID1** Název sítě clusteru ASCS | pr1-ascscl |10.0.0.43 | neuvedeno | neuvedeno |
| **SID1** Název sítě clusteru OLAJÍCÍCH (**jenom** pro ERS2) | pr1-erscl |10.0.0.44 | neuvedeno | neuvedeno |
| **SID2** Název sítě clusteru ASCS | pr2-ascscl |10.0.0.45 | neuvedeno | neuvedeno |
| **SID2** Název sítě clusteru OLAJÍCÍCH (**jenom** pro ERS2) | pr1-erscl |10.0.0.46 | neuvedeno | neuvedeno |

### <a name="create-azure-internal-load-balancer"></a>Vytvoření interního nástroje pro vyrovnávání zatížení Azure

SAP ASCS, SAP SCS a nový ERS2 SAP použijte virtuální název hostitele a virtuální IP adresy. V Azure musí [Nástroj pro vyrovnávání zatížení](../../../load-balancer/load-balancer-overview.md) používat virtuální IP adresu. Důrazně doporučujeme použít službu [Load Balancer úrovně Standard](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md). 

Do existujícího nástroje pro vyrovnávání zatížení budete muset přidat konfiguraci pro druhou instanci SAP SID ASCS/SCS/OLAJÍCÍCH **PR2**. Konfigurace pro první **PR1** SAP SID by měla být již na místě.  

**Určitého SCS PR2 [instance číslo 02]**
- Konfigurace front-endu
    - Statická ASCS/SCS IP adresa **10.0.0.45**
- Konfigurace back-endu  
    Již je na místě – virtuální počítače byly již přidány do back-endového fondu, zatímco konfigurace SAP SID **PR1**
- Port testu paměti
    - Port 620**Nr** [**62002**] ponechá výchozí možnost protokolu (TCP), intervalu (5), prahové hodnoty v pořádku (2).
- Pravidla vyrovnávání zatížení
    - Pokud používáte Standard Load Balancer, vyberte porty HA.
    - Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
        - 32**Nr** TCP [**3202**]
        - 36**Nr** TCP [**3602**]
        - 39**Nr** TCP [**3902**]
        - 81**Nr** TCP [**8102**]
        - 5**Nr**13 TCP [**50213**]
        - 5**Nr**14 TCP [**50214**]
        - 5**Nr**16 TCP [**50216**]
        - Přidružte se k IP adrese front-endu **PR2** ASCS, stavu a stávajícímu back-end fondu.  

    - Ujistěte se, že je časový limit nečinnosti (minuty) nastavený na maximální hodnotu 30 a že je povolená plovoucí IP adresa (přímá návratová hodnota serveru).

**ERS2 PR2 [číslo instance 12]** 

Jako cluster replikace ERS2 (Replication Server 2) je také nutné nakonfigurovat virtuální IP adresu ERS2 na Azure interního nástroje kromě výše uvedeného protokolu SAP ASCS/SCS. Tato část platí jenom v případě, že se používá architektura serveru fronty replikace 2 pro **PR2**.  
- Nová konfigurace front-endu
    - Statické IP adresy SAP ERS2 **10.0.0.46**

- Konfigurace back-endu  
  Virtuální počítače se už přidaly do back-endu interního nástroje fondu.  

- Nový port testu paměti
    - Port 621**Nr**  [**62112**] ponechá výchozí možnost protokolu (TCP), intervalu (5), prahové hodnoty v pořádku (2).

- Nová pravidla vyrovnávání zatížení
    - Pokud používáte Standard Load Balancer, vyberte porty HA.
    - Pokud používáte základní Load Balancer, vytvořte pravidla vyrovnávání zatížení pro následující porty.
        - 32**Nr** TCP [**3212**]
        - 33**Nr** TCP [**3312**]
        - 5**Nr**13 TCP [**51212**]
        - 5**Nr**14 TCP [**51212**]
        - 5**Nr**16 TCP [**51212**]
        - Přidružte se ke sondu stavu **PR2** ERS2 front-endu a stavu a stávajícímu back-end fondu.  

    - Ujistěte se, že časový limit nečinnosti (v minutách) je nastavený na maximální hodnotu, třeba 30, a že je povolená plovoucí IP adresa (přímá návratová hodnota serveru).


### <a name="create-and-attach-second-azure-shared-disk"></a>Vytvoření a připojení druhého sdíleného disku Azure

Spusťte tento příkaz na jednom z uzlů clusteru. Budete muset upravit hodnoty pro skupinu prostředků, oblast Azure, SAPSID a tak dále.  

```powershell
    $ResourceGroupName = "MyResourceGroup"
    $location = "MyRegion"
    $SAPSID = "PR2"
    $DiskSizeInGB = 512
    $DiskName = "$($SAPSID)ASCSSharedDisk"
    $NumberOfWindowsClusterNodes = 2
    $diskConfig = New-AzDiskConfig -Location $location -SkuName Premium_LRS  -CreateOption Empty  -DiskSizeGB $DiskSizeInGB -MaxSharesCount $NumberOfWindowsClusterNodes
    
    $dataDisk = New-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName -Disk $diskConfig
    ##################################
    ## Attach the disk to cluster VMs
    ##################################
    # ASCS Cluster VM1
    $ASCSClusterVM1 = "pr1-ascs-10"
    # ASCS Cluster VM2
    $ASCSClusterVM2 = "pr1-ascs-11"
    # next free LUN number
    $LUNNumber = 1
    # Add the Azure Shared Disk to Cluster Node 1
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM1 
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
    # Add the Azure Shared Disk to Cluster Node 2
    $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $ASCSClusterVM2
    $vm = Add-AzVMDataDisk -VM $vm -Name $DiskName -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun $LUNNumber
    Update-AzVm -VM $vm -ResourceGroupName $ResourceGroupName -Verbose
   ```
### <a name="format-the-shared-disk-with-powershell"></a>Formátování sdíleného disku pomocí prostředí PowerShell
1. Získejte číslo disku. Spusťte příkazy PowerShellu na jednom z uzlů clusteru:

   ```powershell
    Get-Disk | Where-Object PartitionStyle -Eq "RAW"  | Format-Table -AutoSize 
    # Example output
    # Number Friendly Name     Serial Number HealthStatus OperationalStatus Total Size Partition Style
    # ------ -------------     ------------- ------------ ----------------- ---------- ---------------
    # 3      Msft Virtual Disk               Healthy      Online                512 GB RAW            

   ```
2. Naformátujte disk. V tomto příkladu je to disk číslo 3. 

   ```powershell
    # Format SAP ASCS Disk number '3', with drive letter 'S'
    $SAPSID = "PR2"
    $DiskNumber = 3
    $DriveLetter = "S"
    $DiskLabel = "$SAPSID" + "SAP"
    
    Get-Disk -Number $DiskNumber | Where-Object PartitionStyle -Eq "RAW" | Initialize-Disk -PartitionStyle GPT -PassThru |  New-Partition -DriveLetter $DriveLetter -UseMaximumSize | Format-Volume  -FileSystem ReFS -NewFileSystemLabel $DiskLabel -Force -Verbose
    # Example outout
    # DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining      Size
    # ----------- --------------- ---------- --------- ------------ ----------------- -------------      ----
    # S           PR2SAP          ReFS       Fixed     Healthy      OK                    504.98 GB 511.81 GB
   ```

3. Ověřte, zda je disk nyní viditelný jako disk clusteru.  
   ```powershell
    # List all disks
    Get-ClusterAvailableDisk -All
    # Example output
    # Cluster    : pr1clust
    # Id         : c469b5ad-d089-4d8f-ae4c-d834cbbde1a2
    # Name       : Cluster Disk 2
    # Number     : 3
    # Size       : 549755813888
    # Partitions : {\\?\GLOBALROOT\Device\Harddisk3\Partition2\}
   ```

4. Zaregistrujte disk v clusteru.  
   ```powershell
    # Add the disk to cluster 
    Get-ClusterAvailableDisk -All | Add-ClusterDisk
    # Example output     
    # Name           State  OwnerGroup        ResourceType 
    # ----           -----  ----------        ------------ 
    # Cluster Disk 2 Online Available Storage Physical Disk
   ```

## <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance"></a>Vytvoří název virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS.

1. Vytvořte položku DNS pro název virtuálního hostitele pro novou instanci SAP ASCS/SCS ve Správci DNS systému Windows.  
   IP adresa, kterou přiřadíte k názvu virtuálního hostitele v DNS, musí být stejná jako IP adresa, kterou jste přiřadili v Azure Load Balancer.  

   ![_Define položku DNS pro virtuální název a IP adresu clusteru SAP ASCS/SCS][sap-ha-guide-figure-6009]
 
   _Zadejte položku DNS pro virtuální název a IP adresu clusteru SAP ASCS/SCS._

2. Pokud používáte server replikace SAP pro replikaci 2, který je taky Clusterová instance, pak je potřeba pro ERS2 také rezervovat v DNS název virtuálního hostitele. 
   IP adresa, kterou přiřadíte k názvu virtuálního hostitele pro ERS2 ve službě DNS, musí být stejná jako IP adresa, kterou jste přiřadili v Azure Load Balancer.  

   ![_Define položku DNS pro virtuální název clusteru SAP ERS2 a IP adresu][sap-ha-guide-figure-6010]
 
   _Zadejte položku DNS pro virtuální název clusteru SAP ERS2 a IP adresu._

3. Pokud chcete zadat IP adresu, která je přiřazená k názvu virtuálního hostitele, vyberte doména **Správce DNS**  >  **Domain**.

   ![Nový virtuální název a IP adresa pro SAP ASCS/SCS a konfigurace clusteru ERS2][sap-ha-guide-figure-6011]

   _Nový virtuální název a TCP/IP adresa pro SAP ASCS/SCS a konfigurace clusteru ERS2_

## <a name="sap-installation"></a>Instalace SAPu 

### <a name="install-the-sap-first-cluster-node"></a>Instalace prvního uzlu clusteru SAP

Postupujte podle pokynů v tématu o instalaci SAP. Zajistěte, aby byla v možnosti spustit instalaci "první uzel clusteru" a zvolena možnost konfigurace "sdílený disk clusteru".  
Vyberte nově sdílený disk, který chcete vytvořit.  

### <a name="modify-the-sap-profile-of-the-ascsscs-instance"></a>Úprava profilu SAP instance ASCS/SCS

Pokud používáte server replikace fronty 1, přidejte parametr profilu SAP, `enque/encni/set_so_keepalive` jak je popsáno níže. Parametr Profile brání propojení mezi pracovními procesy SAP a serverem fronty v době, kdy jsou nečinné příliš dlouho. Parametr SAP není vyžadován pro ERS2. 

1. Pokud používáte ERS1, přidejte tento parametr profilu do profilu instance SAP ASCS/SCS.

   ```
   enque/encni/set_so_keepalive = true
   ```
   
   Pro ERS1 i ERS2 se ujistěte, že `keepalive` jsou nastavené parametry operačního systému, jak je popsáno v tématu SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).   

2. Pokud chcete použít změny parametru profilu SAP, restartujte instanci SAP ASCS/SCS.

### <a name="configure-probe-port-on-the-cluster-resource"></a>Konfigurace portu testu na prostředku clusteru

Pomocí funkce testu interního nástroje pro vyrovnávání zatížení můžete zajistit, aby celá konfigurace clusteru fungovala s Azure Load Balancer. Interní nástroj pro vyrovnávání zatížení Azure obvykle distribuuje příchozí zatížení rovnoměrně mezi zúčastněnými virtuálními počítači.

To ale v některých konfiguracích clusteru nebude fungovat, protože je aktivní jenom jedna instance. Druhá instance je pasivní a nemůže přijmout žádnou z úloh. Funkce sondy pomáhá, když interní nástroj pro vyrovnávání zatížení Azure zjistí, která instance je aktivní a jenom cílí na aktivní instanci.  

> [!IMPORTANT]
> V tomto příkladu konfigurace je **ProbePort** nastaven na 620**Nr**. V případě instance SAP ASCS s číslem **02** je 620**02**.
> Bude nutné upravit konfiguraci tak, aby odpovídala číslům instancí SAP a vašemu protokolu SAP SID.

Pokud chcete přidat port testu, spusťte tento modul PowerShellu na jednom z virtuálních počítačů clusteru:

- V případě instance SAP ASC/SCS s číslem instance **02** 
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62002
   ```

- Pokud používáte ERS2, s instancí číslo **12**, která je clusterovaný. Není potřeba konfigurovat port testu pro ERS1, protože není clusterovaný.  
   ```powershell
   Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID PR2 -ProbePort 62012 -IsSAPERSClusteredInstance $True
   ```

 Kód pro funkci `Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource` by vypadal jako:
   ```powershell
    function Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource {
    <#
    .SYNOPSIS 
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    
    .DESCRIPTION
    Set-AzureLoadBalancerHealthProbePortOnSAPClusterIPResource will set a new Azure Load Balancer Health Probe Port on 'SAP $SAPSID IP' cluster resource.
    It will also restart SAP Cluster group (default behavior), to activate the changes. 
    
    You need to run it on one of the SAP ASCS/SCS Windows cluster nodes.
    
    Expectation is that SAP group is installed with official SWPM installation tool, which will set default expected naming convention for:
    - SAP Cluster Group:               'SAP $SAPSID'
    - SAP Cluster IP Address Resource: 'SAP $SAPSID IP' 
    
    .PARAMETER SAPSID 
    SAP SID - 3 characters staring with letter.
    
    .PARAMETER ProbePort 
    Azure Load Balancer Health Check Probe Port.
    
    .PARAMETER RestartSAPClusterGroup 
    Optional parameter. Default value is '$True', so SAP cluster group will be restarted to activate the changes.
    
    .PARAMETER IsSAPERSClusteredInstance 
    Optional parameter.Default value is '$False'.
    If set to $True , then handle clsutered new SAP ERS2 instance.
    
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP', and restart the SAP cluster group 'SAP AB1', to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 
    
    .EXAMPLE 
    # Set probe port to 62000, on SAP cluster resource 'SAP AB1 IP'. SAP cluster group 'SAP AB1' IS NOT restarted, therefore changes are NOT active.
    # To activate the changes you need to manualy restart 'SAP AB1' cluster group.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -RestartSAPClusterGroup $False
    
    .EXAMPLE 
    # Set probe port to 62001, on SAP cluster resource 'SAP AB1 ERS IP'. SAP cluster group 'SAP AB1 ERS' IS restarted, to activate the changes.
    Set-AzureLoadBalancerHealthCheckProbePortOnSAPClusterIPResource -SAPSID AB1 -ProbePort 62000 -IsSAPERSClusteredInstance $True
        
    #> 
    
        [CmdletBinding()]
        param(
            
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]  
            [ValidateLength(3,3)]      
            [string]$SAPSID,
                  
            [Parameter(Mandatory=$True)]
            [ValidateNotNullOrEmpty()]        
            [int] $ProbePort,
    
            [Parameter(Mandatory=$False)] 
            [bool] $RestartSAPClusterGroup = $True,
    
            [Parameter(Mandatory=$False)] 
            [bool] $IsSAPERSClusteredInstance = $False
        
        )
    
        BEGIN{}
        
        PROCESS{
            try{                                      
                
                if($IsSAPERSClusteredInstance){
                    #Handle clustered SAP ERS Instance
                    $SAPClusterRoleName = "SAP $SAPSID ERS"
                    $SAPIPresourceName = "SAP $SAPSID ERS IP"            
                }else{
                    #Handle clustered SAP ASCS/SCS Instance
                    $SAPClusterRoleName = "SAP $SAPSID"
                    $SAPIPresourceName = "SAP $SAPSID IP"
                }

                $SAPIPResourceClusterParameters =  Get-ClusterResource $SAPIPresourceName | Get-ClusterParameter
                $IPAddress = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Address" }).Value
                $NetworkName = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "Network" }).Value
                $SubnetMask = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "SubnetMask" }).Value
                $OverrideAddressMatch = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "OverrideAddressMatch" }).Value
                $EnableDhcp = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "EnableDhcp" }).Value
                $OldProbePort = ($SAPIPResourceClusterParameters | Where-Object {$_.Name -eq "ProbePort" }).Value
    
                $var = Get-ClusterResource | Where-Object {  $_.name -eq $SAPIPresourceName  }
    
                #Write-Host "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" -ForegroundColor Cyan
                Write-Output "Current configuration parameters for SAP IP cluster resource '$SAPIPresourceName' are:" 
    
                Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
    
                Write-Output " "
                Write-Output "Current probe port property of the SAP cluster resource '$SAPIPresourceName' is '$OldProbePort'." 
                Write-Output " "
                Write-Output "Setting the new probe port property of the SAP cluster resource '$SAPIPresourceName' to '$ProbePort' ..." 
                Write-Output " "
    
                $var | Set-ClusterParameter -Multiple @{"Address"=$IPAddress;"ProbePort"=$ProbePort;"Subnetmask"=$SubnetMask;"Network"=$NetworkName;"OverrideAddressMatch"=$OverrideAddressMatch;"EnableDhcp"=$EnableDhcp}
    
                Write-Output " "
    
                #$ActivateChanges = Read-Host "Do you want to take restart SAP cluster role '$SAPClusterRoleName', to activate the changes (yes/no)?"
    
                if($RestartSAPClusterGroup){
                    Write-Output ""
                    Write-Output "Activating changes..." 
    
                    Write-Output " "
                    Write-Output "Taking SAP cluster IP resource '$SAPIPresourceName' offline ..."
                    Stop-ClusterResource -Name $SAPIPresourceName
                    sleep 5
    
                    Write-Output "Starting SAP cluster role '$SAPClusterRoleName' ..."
                    Start-ClusterGroup -Name $SAPClusterRoleName
    
                    Write-Output "New ProbePort parameter is active." 
                    Write-Output " "
    
                    Write-Output "New configuration parameters for SAP IP cluster resource '$SAPIPresourceName':" 
                    Write-Output " " 
                    Get-ClusterResource -Name $SAPIPresourceName | Get-ClusterParameter
                }else
                {
                    Write-Output "SAP cluster role '$SAPClusterRoleName' is not restarted, therefore changes are not activated."
                }
            }
            catch{
               Write-Error  $_.Exception.Message
           }
    
        }
    
        END {}
    }
   ```

### <a name="continue-with-the-sap-installation"></a>Pokračování v instalaci SAP

1. Pomocí postupu popsaného v instalační příručce SAP Nainstalujte instanci databáze.  
2. Nainstalujte SAP na druhý uzel clusteru podle kroků popsaných v instalační příručce SAP.  
3. Do virtuálního počítače, který jste určili pro hostování PAS, nainstalujte instanci primárního aplikačního serveru SAP (PAS).   
   Postupujte podle kroků popsaných v instalační příručce SAP. V Azure neexistují žádné závislosti.
4. Nainstalujte na virtuální počítače další aplikační servery SAP určené k hostování instancí aplikačního serveru SAP.  
   Postupujte podle kroků popsaných v instalační příručce SAP. V Azure neexistují žádné závislosti. 

## <a name="test-the-sap-ascsscs-instance-failover"></a>Testování převzetí služeb při selhání instance SAP ASCS/SCS
U vypsaných testů převzetí služeb při selhání předpokládáme, že je v uzlu A aktivní služba SAP ASCS.  

1. Ověřte, zda může systém SAP úspěšně převzít služby při selhání z uzlu A na uzel B. V tomto příkladu je test proveden pro SAPSID **PR2**.  
   Ujistěte se, že každý SAPSID se může úspěšně přesunout na jiný uzel clusteru.   
   Vyberte jednu z těchto možností pro zahájení převzetí služeb při selhání \<SID\> skupiny clusterů SAP z uzlu clusteru a na uzel clusteru B:
    - Správce clusteru s podporou převzetí služeb při selhání  
    - PowerShellový cluster s podporou převzetí služeb

    ```powershell
    $SAPSID = "PR2"     # SAP <SID>
 
    $SAPClusterGroup = "SAP $SAPSID"
    Move-ClusterGroup -Name $SAPClusterGroup

    ```
2. Restartujte uzel clusteru A v hostovaném operačním systému Windows. Tím se inicializuje automatické převzetí služeb při selhání \<SID\> skupiny clusterů SAP z uzlu A na uzel B.  
3. Restartujte uzel clusteru A z Azure Portal. Tím se inicializuje automatické převzetí služeb při selhání \<SID\> skupiny clusterů SAP z uzlu A na uzel B.  
4. Restartujte uzel clusteru A pomocí Azure PowerShell. Tím se inicializuje automatické převzetí služeb při selhání \<SID\> skupiny clusterů SAP z uzlu A na uzel B.

## <a name="next-steps"></a>Další kroky

* [Příprava infrastruktury Azure pro SAP HA pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-shared-disk]
* [Instalace SAP NetWeaver HA do clusteru s podporou převzetí služeb při selhání systému Windows a sdíleného disku pro instanci SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1869038]:https://launchpad.support.sap.com/#/notes/1869038
[2287140]:https://launchpad.support.sap.com/#/notes/2287140
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md


[sap-net-weaver-ports]:https://help.sap.com/viewer/ports
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md
[sap-net-weaver-ports-ascs-scs-ports]:sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md

[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk-install-ascs]:sap-high-availability-installation-wsfc-shared-disk.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-high-availability-installation-wsfc-shared-disk-modify-ascs-profile]:sap-high-availability-installation-wsfc-shared-disk.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556
[sap-high-availability-installation-wsfc-shared-disk-add-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761
[sap-high-availability-installation-wsfc-shared-disk-win-firewall-probe-port]:sap-high-availability-installation-wsfc-shared-disk.md#4498c707-86c0-4cde-9c69-058a7ab8c3ac
[sap-high-availability-installation-wsfc-shared-disk-change-ers-service-startup-type]:sap-high-availability-installation-wsfc-shared-disk.md#094bc895-31d4-4471-91cc-1513b64e406a
[sap-high-availability-installation-wsfc-shared-disk-test-ascs-failover-and-sios-repl]:sap-high-availability-installation-wsfc-shared-disk.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9


[sap-high-availability-installation-wsfc-file-share]:sap-high-availability-installation-wsfc-file-share.md
[sap-high-availability-infrastructure-wsfc-shared-disk-install-sios]:sap-high-availability-infrastructure-wsfc-shared-disk.md#5c8e5482-841e-45e1-a89d-a05c0907c868

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6001]:media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png

[sap-ha-guide-figure-6007]:media/virtual-machines-shared-sap-high-availability-guide/6007-sap-multi-sid-ascs-azure-shared-disk-sid1.png
[sap-ha-guide-figure-6008]:media/virtual-machines-shared-sap-high-availability-guide/6008-sap-multi-sid-ascs-azure-shared-disk-sid2.png
[sap-ha-guide-figure-6009]:media/virtual-machines-shared-sap-high-availability-guide/6009-sap-multi-sid-ascs-azure-shared-disk-dns1.png
[sap-ha-guide-figure-6010]:media/virtual-machines-shared-sap-high-availability-guide/6010-sap-multi-sid-ascs-azure-shared-disk-dns2.png
[sap-ha-guide-figure-6011]:media/virtual-machines-shared-sap-high-availability-guide/6011-sap-multi-sid-ascs-azure-shared-disk-dns3.png

[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md