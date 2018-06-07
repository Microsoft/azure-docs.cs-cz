---
title: Vytvoření konfigurace aplikace SAP více SID v Azure | Microsoft Docs
description: Průvodce konfigurace více SID SAP NetWeaver s vysokou dostupností v systému Windows, virtuální počítače
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: goraco
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 570c427166c07e93d3c8a7d3be079d9007238243
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656818"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Vytvoření konfigurace aplikace SAP NetWeaver více SID

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
[sap-ha-guide-9.1.1]:sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 
[sap-ha-guide-8.8]:sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c
[sap-ha-guide-8.12.3.3]:sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 
[sap-ha-guide-9]:sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 
[sap-ha-guide-9.1.2]:sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 
[sap-ha-guide-9.1.3]:sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 
[sap-ha-guide-9.1.4]:sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 
[sap-ha-guide-9.4]:sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a 
[sap-ha-guide-9.5]:sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 
[sap-ha-guide-9.6]:sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 
[sap-ha-guide-10]:sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9

V září 2016 společnost Microsoft vydala funkce, kde můžete spravovat pomocí pro vyrovnávání zatížení Azure interní víc virtuálních IP adres. Tato funkce již existuje v Azure externím vyrovnáváním zatížení.

Pokud máte nasazení SAP, jak je uvedeno v, můžete použít interní nástroj pro vytvoření konfigurace clusteru systému Windows pro SAP ASC nebo SCS [průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows][sap-ha-guide].

Tento článek se zaměřuje na postup přesunutí z jedné instalace ASC nebo SCS ke konfiguraci více SID SAP nainstalováním další instance SAP ASC nebo SCS clusteru do existujícího clusteru Windows Server Failover Clustering (WSFC). Po dokončení tohoto procesu jste nakonfigurovali clusteru více SID služby SAP.


## <a name="prerequisites"></a>Požadavky
Jste již nakonfigurovali cluster služby WSFC, který se používá pro jednu instanci SAP ASC nebo SCS, jak je popsáno v [průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows] [ sap-ha-guide] a jak je znázorněno v tomto diagramu.

![Instance SAP ASC nebo SCS vysokou dostupnost][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Cílová architektura

Cílem je nainstalovat více ASC ABAP SAP nebo SAP Java SCS Clusterované instance ve stejném clusteru služby WSFC, jako ilustrované tady:

![Více instancí SAP ASC nebo SCS clusteru v Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Existuje omezení počtu privátní front-end IP adresy pro každý nástroj pro vyrovnávání zatížení Azure interní.
>
>Maximální počet instancí SAP ASC nebo SCS do jednoho clusteru služby WSFC se rovná maximální počet privátní front-end IP adresy pro každý nástroj pro vyrovnávání zatížení Azure interní.
>

Další informace o omezeních pro vyrovnávání zatížení, najdete v části "privátní front-end IP adresy na nástroj pro vyrovnávání zatížení" v [omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager].

Dokončení na šířku s dvěma systémy SAP vysoké dostupnosti bude vypadat takto:

![Nastavení vysoké dostupnosti více SID SAP s dvě systému SAP identifikátorů SID][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Nastavení musí splňovat následující podmínky:
> - Instance SAP ASC nebo SCS musejí sdílet stejný cluster služby WSFC.
> - Každý SID databázového systému musí mít svůj vlastní vyhrazený cluster služby WSFC.
> - SAP aplikační servery, které patří do jednoho systému SAP SID musí mít vlastní vyhrazených virtuálních počítačích.


## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury
Příprava infrastruktury, můžete nainstalovat další instance SAP ASC nebo SCS s následujícími parametry:

| Název parametru | Hodnota |
| --- | --- |
| SAP ASC NEBO SCS SID |PR1-lb ASC |
| Databázového systému SAP interní nástroj pro vyrovnávání zatížení | PR5 |
| Název virtuálního hostitele SAP | pr5-sap-cl |
| SAP ASC nebo SCS virtuální hostitele IP adresu (IP adresa služby Vyrovnávání zatížení Další Azure) | 10.0.0.50 |
| Čísla instance SAP ASC nebo SCS | 50 |
| Port testu ILB pro další instance SAP ASC nebo SCS | 62350 |

> [!NOTE]
> Každou IP adresu pro SAP ASC nebo SCS instance clusteru, vyžaduje port jedinečný testu. Například pokud jednu IP adresu na Azure interní nástroj používá port testu 62300, žádné jiné IP adresy na tento nástroj pro vyrovnávání zatížení můžete použít port testu 62300.
>
>Pro naše účely protože port testu 62300 je rezervovaná, se používá port testu 62350.

V existujícím clusteru služby WSFC s dvěma uzly můžete nainstalovat další instance SAP ASC nebo SCS:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Statická IP adresa |
| --- | --- | --- |
| 1. uzel clusteru pro instanci ASC nebo SCS |pr1-ascs-0 |10.0.0.10 |
| 2. uzel clusteru pro instanci ASC nebo SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Vytvořte název virtuálního hostitele pro skupinu prostředků clusteru SAP ASC nebo SCS na serveru DNS

Položku DNS pro název virtuálního hostitele instance ASC nebo SCS můžete vytvořit pomocí následujících parametrů:

| Nový název virtuálního hostitele SAP ASC nebo SCS | Přidružené IP adresu |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

Novým názvem hostitele a IP adresa se zobrazí ve Správci DNS, jak je znázorněno na následujícím snímku obrazovky:

![Správce DNS seznamu zvýraznění definované položky DNS pro novou SAP ASC nebo SCS clusteru virtuální název a adresu TCP/IP][sap-ha-guide-figure-6004]

Postup pro vytvoření položky DNS je také popsáno podrobně v hlavní [průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Novou IP adresu, která přiřadíte název virtuálního hostitele další instance ASC nebo SCS musí být stejný jako novou IP adresu, který jste přiřadili ke službě Vyrovnávání zatížení SAP Azure.
>
>V našem scénáři je IP adresa 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Přidat existující Vyrovnávání zatížení Azure interní IP adresu pomocí prostředí PowerShell

Pokud chcete vytvořit více než jedna instance SAP ASC nebo SCS ve stejném clusteru služby WSFC, přidat existující Vyrovnávání zatížení Azure interní IP adresu pomocí prostředí PowerShell. Každou IP adresu vyžaduje vlastní pravidla Vyrovnávání zatížení, port testu, front-end fond IP adres a fond back-end.

Následující skript přidá novou IP adresu do existující pro vyrovnávání zatížení. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Skript se vytvoří všechny potřebné pravidla Vyrovnávání zatížení pro všechny porty SAP ASC nebo SCS.

```powershell

# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzureRmLoadBalancer

# Get new updated configuration
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzureRmLoadBalancer

# Get new updated config
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        #start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzureRmLoadBalancer

Write-Host "Succesfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po spuštění skriptu, výsledky se zobrazí na portálu Azure, jak je znázorněno na následujícím snímku obrazovky:

![Nový fond IP front-endu na portálu Azure][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Přidat disky do clusteru počítačů a konfiguraci disku SIOS clusteru sdílené složky

Musíte přidat nový disk clusteru sdílené složky pro každou další instanci SAP ASC nebo SCS. Pro Windows Server 2012 R2 je disk sdílené složky clusteru služby WSFC aktuálně používán s DataKeeper softwarové řešení.

Udělejte toto:
1. Přidat další disk nebo disky stejnou velikost (které je třeba rozkládají) na všech uzlech clusteru a jejich formátování.
2. Nakonfigurujte s DataKeeper replikace úložiště.

Tento postup předpokládá, že jste již nainstalovali s DataKeeper u počítačů clusteru služby WSFC. Pokud jste ho nainstalovali, musíte teď nakonfigurovat replikace mezi počítači. Proces je podrobně popsány v hlavní [průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows][sap-ha-guide-8.12.3.3].  

![DataKeeper synchronní zrcadlení pro nové SAP ASC nebo SCS sdílet disk][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Nasazení virtuálních počítačů pro SAP aplikační servery a cluster databázového systému

K dokončení Příprava infrastruktury pro druhý systému SAP, postupujte takto:

1. Nasazení vyhrazených virtuálních počítačích pro SAP aplikační servery a vložte je do své vlastní vyhrazené dostupnosti skupiny.
2. Nasazení vyhrazených virtuálních počítačích pro cluster databázového systému a vložte je do své vlastní vyhrazené dostupnosti skupiny.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalace druhé systému SAP SID2 NetWeaver

Dokončení procesu instalace druhé systému SAP SID2 je popsaný v hlavní [průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows][sap-ha-guide-9].

Podrobný postup je následující:

1. [Instalace prvního uzlu clusteru SAP][sap-ha-guide-9.1.2].  
 V tomto kroku instalujete SAP s vysokou dostupností ASC nebo SCS instancí na **uzlu clusteru služby WSFC existující 1**.

2. [Upravit profil SAP instance ASC nebo SCS][sap-ha-guide-9.1.3].

3. [Nakonfigurujte port testu][sap-ha-guide-9.1.4].  
 V tomto kroku nakonfigurujete prostředek clusteru SAP port testu SAP. SID2 IP pomocí prostředí PowerShell. Tuto konfiguraci proveďte v jednom z uzlů clusteru SAP ASC nebo SCS.

4. [Nainstalovat instanci databáze] [sap-ha Průvodce-9.2].  
 V tomto kroku instalujete databázového systému na vyhrazeném clusteru služby WSFC.

5. [Install druhého uzlu clusteru] [sap-ha Průvodce-9.3].  
 V tomto kroku instalujete SAP s vysokou dostupností ASC nebo SCS instancí na stávající uzel clusteru služby WSFC 2.

6. Otevřete porty brány Windows Firewall pro instance SAP ASC nebo SCS a ProbePort.  
 Na obou uzlů clusteru, které se používají pro instance SAP ASC nebo SCS otevíráte všechny porty brány Windows Firewall, které SAP ASC nebo SCS. Tyto porty jsou uvedeny v [průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows][sap-ha-guide-8.8].  
 Také otevřete port testu nástroje pro vyrovnávání Azure interní služby load, což je 62350 v tomto scénáři.

7. [Změnit typ spuštění instance služby Windows YBRAT SAP][sap-ha-guide-9.4].

8. [Instalace serveru primární aplikace SAP] [ sap-ha-guide-9.5] na novém vyhrazeném virtuálních počítačů.

9. [Instalace serveru SAP další aplikaci] [ sap-ha-guide-9.6] na novém vyhrazeném virtuálních počítačů.

10. [Testovací převzetí služeb při selhání SAP ASC nebo SCS instance a replikace SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Další postup

- [Omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Nástroj pro vyrovnávání zatížení několika virtuálními IP adresami pro Azure.][load-balancer-multivip-overview]
- [Průvodci pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích Windows][sap-ha-guide]
