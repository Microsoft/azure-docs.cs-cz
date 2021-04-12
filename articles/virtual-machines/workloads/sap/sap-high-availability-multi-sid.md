---
title: Vytvoření konfigurace s vysokou dostupností pro SAP s více identifikátory SID v Azure
description: Naučte se vytvořit konfiguraci multi-SID SAP NetWeaver s vysokou dostupností na virtuálních počítačích s Windows.
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 079ac273f7b2aa91b61ad2cdbcfe62cd989bb2f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675267"
---
# <a name="create-a-high-availability-sap-netweaver-multi-sid-configuration"></a>Vytvoření konfigurace s vysokou dostupností SAP NetWeaver s více identifikátory SID

[load-balancer-multivip-overview]:../../../load-balancer/load-balancer-multivip-overview.md
[sap-ha-guide]:sap-high-availability-guide.md 
[sap-ha-guide-figure-6001]:./media/virtual-machines-shared-sap-high-availability-guide/6001-sap-multi-sid-ascs-scs-sid1.png
[sap-ha-guide-figure-6002]:./media/virtual-machines-shared-sap-high-availability-guide/6002-sap-multi-sid-ascs-scs.png
[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png
[sap-ha-guide-figure-6004]:./media/virtual-machines-shared-sap-high-availability-guide/6004-sap-multi-sid-dns.png
[sap-ha-guide-figure-6005]:./media/virtual-machines-shared-sap-high-availability-guide/6005-sap-multi-sid-azure-portal.png
[sap-ha-guide-figure-6006]:./media/virtual-machines-shared-sap-high-availability-guide/6006-sap-multi-sid-sios-replication.png
[networking-limits-azure-resource-manager]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits
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

V září 2016 společnost Microsoft vydala funkci, kde můžete spravovat více virtuálních IP adres pomocí interního nástroje pro vyrovnávání zatížení Azure. Tato funkce už v externím nástroji pro vyrovnávání zatížení Azure existuje.

Pokud máte nasazení SAP, můžete pomocí interního nástroje pro vyrovnávání zatížení vytvořit konfiguraci clusteru Windows pro SAP ASCS/SCS, jak je popsáno v [Průvodci pro vysoce DOSTUPNOU SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide].

Tento článek se zaměřuje na to, jak přejít z jedné instalace ASCS/SCS do konfigurace SAP multi-SID instalací dalších clusterových instancí SAP ASCS/SCS do stávajícího clusteru služby Windows Server Failover Clustering (WSFC). Po dokončení tohoto procesu budete mít nakonfigurovaný cluster SAP s více identifikátory SID.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Cluster služby WSFC byl již nakonfigurován pro jednu instanci SAP ASCS/SCS, jak je popsáno v [Průvodci pro vysoce DOSTUPNOU SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide] a jak je znázorněno v tomto diagramu.

![Instance SAP ASCS/SCS s vysokou dostupností][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Cílová architektura

Cílem je nainstalovat několik clusterových instancí SAP ABAP ASCS nebo SAP Java SCS do stejného clusteru služby WSFC, jak je znázorněno zde:

![Několik clusterových instancí SAP ASCS/SCS v Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Počet privátních IP adres pro každý interní nástroj pro vyrovnávání zatížení Azure je omezený.
>
>Maximální počet instancí SAP ASCS/SCS v jednom clusteru WSFC se rovná maximálnímu počtu privátních IP adres pro každý interní nástroj pro vyrovnávání zatížení Azure.
>

Další informace o limitech pro vyrovnávání zatížení najdete v části "soukromé IP adresy front-endu na nástroj pro vyrovnávání zatížení" v části [omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager].

Kompletní na šířku se dvěma systémy SAP s vysokou dostupností by vypadaly takto:

![Nastavení Multi-SID s vysokou dostupností se dvěma čísly SID systému SAP][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Nastavení musí splňovat následující podmínky:
> - Instance SAP ASCS/SCS musí sdílet stejný cluster služby WSFC.
> - Každý identifikátor zabezpečení DBMS musí mít vlastní vyhrazený cluster WSFC.
> - Aplikační servery SAP, které patří k jednomu identifikátoru zabezpečení systému SAP, musí mít vlastní vyhrazené virtuální počítače.


## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury
K přípravě infrastruktury můžete nainstalovat další instanci SAP ASCS/SCS s následujícími parametry:

| Název parametru | Hodnota |
| --- | --- |
| IDENTIFIKÁTOR SID SAP ASCS/SCS |PR1-9,1-ASCS |
| Interní nástroj pro vyrovnávání zatížení SAP DBMS | PR5 |
| Název virtuálního hostitele SAP | PR5-SAP-CL |
| IP adresa virtuálního hostitele SAP ASCS/SCS (další IP adresa služby Azure Load Balancer) | 10.0.0.50 |
| Číslo instance SAP ASCS/SCS | 50 |
| Port testu interního nástroje pro další instanci SAP ASCS/SCS | 62350 |

> [!NOTE]
> U clusterových instancí SAP ASCS/SCS vyžaduje Každá IP adresa jedinečný port testu paměti. Pokud třeba jedna IP adresa na interním nástroji pro vyrovnávání zatížení Azure používá test port 62300, nemůže na tomto nástroji vyrovnávání zatížení používat žádnou jinou IP adresu, která by mohla používat test port 62300.
>
>Pro naše účely, protože je již rezervován port 62300, používáme port testu 62350.

V existujícím clusteru služby WSFC se dvěma uzly můžete nainstalovat další instance SAP ASCS/SCS:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Statická IP adresa |
| --- | --- | --- |
| 1 uzel clusteru pro instanci ASCS/SCS |PR1-ASCS-0 |10.0.0.10 |
| druhý uzel clusteru pro instanci ASCS/SCS |PR1-ASCS-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Vytvořte název virtuálního hostitele pro clusterovanou instanci SAP ASCS/SCS na serveru DNS.

Záznam DNS pro název virtuálního hostitele instance ASCS/SCS můžete vytvořit pomocí následujících parametrů:

| Nový název virtuálního hostitele SAP ASCS/SCS | Přidružená IP adresa |
| --- | --- |
|PR5-SAP-CL |10.0.0.50 |

Nový název hostitele a IP adresa se zobrazí ve Správci DNS, jak je znázorněno na následujícím snímku obrazovky:

![Seznam Správce DNS – zvýrazní se definovaný záznam DNS pro nový virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP.][sap-ha-guide-figure-6004]

Postup vytvoření položky DNS je podrobně popsaný v hlavní [příručce pro vysoce dostupné SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Nová IP adresa, kterou přiřadíte názvu virtuálního hostitele další instance ASCS/SCS, musí být stejná jako nová IP adresa, kterou jste přiřadili ke službě SAP Azure Load Balancer.
>
>V našem scénáři je IP adresa 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Přidání IP adresy do stávajícího interního nástroje pro vyrovnávání zatížení Azure pomocí PowerShellu

Pokud chcete ve stejném clusteru služby WSFC vytvořit víc instancí SAP ASCS/SCS, přidejte IP adresu do stávajícího interního nástroje pro vyrovnávání zatížení Azure pomocí PowerShellu. Každá IP adresa vyžaduje vlastní pravidla vyrovnávání zatížení, port testu, front-end fond IP adres a fond back-end.

Následující skript přidá do stávajícího nástroje pro vyrovnávání zatížení novou IP adresu. Aktualizujte proměnné PowerShellu pro vaše prostředí. Skript vytvoří všechna potřebná pravidla vyrovnávání zatížení pro všechny porty SAP ASCS/SCS.

```powershell

# Select-AzSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>
Clear-Host
$ResourceGroupName = "SAP-MULTI-SID-Landscape"      # Existing resource group name
$VNetName = "pr2-vnet"                        # Existing virtual network name
$SubnetName = "Subnet"                        # Existing subnet name
$ILBName = "pr2-lb-ascs"                      # Existing ILB name                      
$ILBIP = "10.0.0.50"                          # New IP address
$VMNames = "pr2-ascs-0","pr2-ascs-1"          # Existing cluster virtual machine names
$SAPInstanceNumber = 50                       # SAP ASCS/SCS instance number: must be a unique value for each cluster
[int]$ProbePort = "623$SAPInstanceNumber"     # Probe port: must be a unique value for each IP and load balancer

$ILB = Get-AzLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName

$count = $ILB.FrontendIpConfigurations.Count + 1
$FrontEndConfigurationName ="lbFrontendASCS$count"
$LBProbeName = "lbProbeASCS$count"

# Get the Azure VNet and subnet
$VNet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName
$Subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

# Add second front-end and probe configuration
Write-Host "Adding new front end IP Pool '$FrontEndConfigurationName' ..." -ForegroundColor Green
$ILB | Add-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id
$ILB | Add-AzLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 10  | Set-AzLoadBalancer

# Get new updated configuration
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
# Get new updated LP FrontendIP COnfig
$FEConfig = Get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

# Add new back-end configuration into existing ILB
$BackEndConfigurationName  = "backendPoolASCS$count"
Write-Host "Adding new backend Pool '$BackEndConfigurationName' ..." -ForegroundColor Green
$BEConfig = Add-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB | Set-AzLoadBalancer

# Get new updated config
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

# Assign VM NICs to backend pool
$BEPool = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
foreach($VMName in $VMNames){
        $VM = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $VMName
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)        
        $NIC = Get-AzNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName                
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools += $BEPool
        Write-Host "Assigning network card '$NICName' of the '$VMName' VM to the backend pool '$BackEndConfigurationName' ..." -ForegroundColor Green
        Set-AzNetworkInterface -NetworkInterface $NIC
        #start-AzVM -ResourceGroupName $ResourceGroupName -Name $VM.Name
}


# Create load-balancing rules
$Ports = "445","32$SAPInstanceNumber","33$SAPInstanceNumber","36$SAPInstanceNumber","39$SAPInstanceNumber","5985","81$SAPInstanceNumber","5$SAPInstanceNumber`13","5$SAPInstanceNumber`14","5$SAPInstanceNumber`16"
$ILB = Get-AzLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName
$FEConfig = get-AzLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB
$BEConfig = Get-AzLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB
$HealthProbe  = Get-AzLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

Write-Host "Creating load balancing rules for the ports: '$Ports' ... " -ForegroundColor Green

foreach ($Port in $Ports) {

        $LBConfigrulename = "lbrule$Port" + "_$count"
        Write-Host "Creating load balancing rule '$LBConfigrulename' for the port '$Port' ..." -ForegroundColor Green

        $ILB | Add-AzLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $HealthProbe -Protocol tcp -FrontendPort  $Port -BackendPort $Port -IdleTimeoutInMinutes 30 -LoadDistribution Default -EnableFloatingIP   
}

$ILB | Set-AzLoadBalancer

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po spuštění skriptu se výsledky zobrazí v Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Nový fond front-end IP adres v Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Přidat disky do clusterových počítačů a nakonfigurovat sdílený disk clusteru

Pro každou další instanci SAP ASCS/SCS je nutné přidat nový disk pro sdílení clusteru. Pro Windows Server 2012 R2 se sdílený disk clusteru služby WSFC, který se právě používá, je softwarové řešení s.

Postupujte následovně:
1. Přidejte další disk nebo disky stejné velikosti (které je třeba protáhnout) do každého uzlu clusteru a naformátujte je.
2. Nakonfigurujte replikaci úložiště s využitím datakeep.

Tento postup předpokládá, že jste už nainstalovali s modulem datakeep na počítačích clusteru služby WSFC. Pokud jste ho nainstalovali, musíte teď u těchto počítačů nakonfigurovat replikaci. Tento postup je podrobně popsaný v hlavní [příručce pro vysoce dostupné SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-8.12.3.3].  

![Synchronní zrcadlení datakeep pro nový disk sdílené složky SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Nasazení virtuálních počítačů pro aplikační servery SAP a cluster DBMS

Pro dokončení přípravy infrastruktury pro druhý systém SAP postupujte takto:

1. Nasaďte vyhrazené virtuální počítače pro aplikační servery SAP a umístěte je do vlastní vyhrazené skupiny dostupnosti.
2. Nasaďte vyhrazené virtuální počítače pro cluster DBMS a umístěte je do vlastní vyhrazené skupiny dostupnosti.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Nainstalujte druhý systém SAP SID2 NetWeaver.

Úplný proces instalace druhého systému SAP SID2 je popsaný v hlavní [příručce pro vysoce dostupné SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-9].

Postup vysoké úrovně je následující:

1. [Nainstalujte první uzel clusteru SAP][sap-ha-guide-9.1.2].  
 V tomto kroku instalujete SAP s instancí ASCS/SCS s vysokou dostupností na **existujícím uzlu clusteru služby WSFC 1**.

2. [Upravte profil SAP instance ASCS/SCS][sap-ha-guide-9.1.3].

3. [Nakonfigurujte port testu paměti][sap-ha-guide-9.1.4].  
 V tomto kroku konfigurujete port testu SAP-SID2-IP clusteru SAP pomocí prostředí PowerShell. Tuto konfiguraci spusťte na jednom z uzlů clusteru SAP ASCS/SCS.

4. [Instalace instance databáze] [SAP-ha-Guide-9,2].  
 V tomto kroku instalujete systém DBMS do vyhrazeného clusteru služby WSFC.

5. [Instalace druhého uzlu clusteru] [SAP-ha-Guide-9,3].  
 V tomto kroku instalujete SAP s instancí ASCS/SCS s vysokou dostupností na existujícím uzlu clusteru služby WSFC 2.

6. Otevřete porty brány Windows Firewall pro instanci SAP ASCS/SCS a ProbePort.  
 Na obou uzlech clusteru, které se používají pro instance SAP ASCS/SCS, otevíráte všechny porty brány Windows Firewall, které používají SAP ASCS/SCS. Tyto porty jsou uvedené v [Průvodci pro vysoce DOSTUPNOU SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-8.8].  
 Otevřete také port testu interního nástroje pro vyrovnávání zatížení Azure, který je 62350 v našem scénáři.

7. [Změňte typ spuštění instance služby SAP Olajících systému Windows][sap-ha-guide-9.4].

8. [Nainstalujte primární aplikační Server SAP][sap-ha-guide-9.5] na nový vyhrazený virtuální počítač.

9. [Nainstalujte další aplikační Server SAP][sap-ha-guide-9.6] na nový vyhrazený virtuální počítač.

10. [Otestujte převzetí služeb při selhání a replikaci instance SAP ASCS/SCS][sap-ha-guide-10].

## <a name="next-steps"></a>Další kroky

- [Omezení sítě: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Více VIP pro Azure Load Balancer][load-balancer-multivip-overview]
- [Příručka pro vysoce dostupnou SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide]
