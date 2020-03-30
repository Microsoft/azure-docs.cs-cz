---
title: Vytvoření konfigurace SAP s více SID v Azure | Dokumenty společnosti Microsoft
description: Průvodce vysoce dostupností konfigurace SAP NetWeaver s více SID na virtuálních počítačích se systémem Windows
services: virtual-machines-windows, virtual-network, storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 0b89b4f8-6d6c-45d7-8d20-fe93430217ca
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/05/2017
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d7938f7db22f004a0bf6cdf2e22dc8e103896719
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617402"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Vytvoření konfigurace SAP NetWeaver s více SID

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

V září 2016 vydalmicrosoft Microsoft funkci, kde můžete spravovat více virtuálních IP adres pomocí interního nástroje pro vyrovnávání zatížení Azure. Tato funkce již existuje v externím centru vyrovnávání zatížení Azure.

Pokud máte nasazení SAP, můžete použít interní nástroj pro vyrovnávání zatížení k vytvoření konfigurace clusteru Windows pro SAP ASCS/SCS, jak je popsáno v [příručce sap NetWeaver s vysokou dostupností na virtuálních počítačích se systémem Windows][sap-ha-guide].

Tento článek se zaměřuje na to, jak přejít z jedné instalace ASCS/SCS do konfigurace SAP s více SID instalací dalších clusterovaných instancí SAP ASCS/SCS do existujícího clusteru WSFC (Windows Server Failover Clustering). Po dokončení tohoto procesu nakonfigurujete cluster SAP s více SID.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Již jste nakonfigurovali cluster WSFC, který se používá pro jednu instanci SAP ASCS/SCS, jak je popsáno v [příručce SAP NetWeaver s vysokou dostupností na virtuálních počítačích se systémem Windows][sap-ha-guide] a jak je znázorněno v tomto diagramu.

![Instance SAP ASCS/SCS s vysokou dostupností][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Cílová architektura

Cílem je nainstalovat více clusterovaných instancí SAP ABAP ASCS nebo SAP Java SCS ve stejném clusteru WSFC, jak je znázorněno zde:

![Více clusterovaných instancí SAP ASCS/SCS v Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Počet privátních front-endových IP adresy pro každý interní systém vyrovnávání zatížení Azure je omezen.
>
>Maximální počet instancí SAP ASCS/SCS v jednom clusteru WSFC se rovná maximálnímu počtu privátních front-endových IP adresy pro každý interní systém vyrovnávání zatížení Azure.
>

Další informace o limitech nástroje pro vyrovnávání zatížení najdete v tématu Private front-end IP per balancer v [tématu Síťové limity: Azure Resource Manager][networking-limits-azure-resource-manager].

Kompletní krajina se dvěma vysoce dostupnostmi systémů SAP bude vypadat takto:

![SAP high-dostupnost multi-SID nastavení se dvěma SAP systémU SID][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Nastavení musí splňovat následující podmínky:
> - Instance SAP ASCS/SCS musí sdílet stejný cluster WSFC.
> - Každé sid DBMS musí mít vlastní vyhrazený cluster WSFC.
> - Aplikační servery SAP, které patří do jednoho systému SAP SID systému musí mít své vlastní vyhrazené virtuální chod.


## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury
Chcete-li připravit infrastrukturu, můžete nainstalovat další instanci SAP ASCS/SCS s následujícími parametry:

| Název parametru | Hodnota |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| Interní systém vyrovnávání zatížení SAP DBMS | PR5 |
| Název virtuálního hostitele SAP | pr5-sap-cl |
| IP adresa virtuálního hostitele SAP ASCS/SCS (další IP adresa nástroje pro vyrovnávání zatížení Azure) | 10.0.0.50 |
| Číslo instance SAP ASCS/SCS | 50 |
| Port sondy ILB pro další instanci SAP ASCS/SCS | 62350 |

> [!NOTE]
> Pro instance clusteru SAP ASCS/SCS vyžaduje každá adresa IP jedinečný port sondy. Například pokud jedna IP adresa na interní matný počítač Azure používá port sondy 62300, žádná jiná IP adresa v tomto systému vyrovnávání zatížení můžete použít probe port 62300.
>
>Pro naše účely, protože sonda port 62300 je již vyhrazena, používáme sonda port 62350.

Do existujícího clusteru WSFC můžete nainstalovat další instance SAP ASCS/SCS se dvěma uzly:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Statická IP adresa |
| --- | --- | --- |
| První uzel clusteru pro instanci ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| Druhý uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Vytvoření názvu virtuálního hostitele pro clusterovnou instanci SAP ASCS/SCS na serveru DNS

Položku DNS pro název virtuálního hostitele instance ASCS/SCS můžete vytvořit pomocí následujících parametrů:

| Nový název virtuálního hostitele SAP ASCS/SCS | Přidružená IP adresa |
| --- | --- |
|pr5-sap-cl |10.0.0.50 |

Nový název hostitele a IP adresa jsou zobrazeny ve Správci DNS, jak je znázorněno na následujícím snímku obrazovky:

![Seznam Správce DNS zvýrazňující definovanou položku DNS pro nový virtuální název clusteru SAP ASCS/SCS a adresu TCP/IP][sap-ha-guide-figure-6004]

Postup vytvoření položky DNS je také podrobně popsán v hlavní [příručce pro vysoce dostupnost SAP NetWeaver na virtuálních počítačích se systémem Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Nová IP adresa, kterou přiřadíte k názvu virtuálního hostitele další instance ASCS/SCS, musí být stejná jako nová IP adresa, kterou jste přiřadili k nástroji pro vyrovnávání zatížení SAP Azure.
>
>V našem scénáři je ip adresa 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Přidání IP adresy do existujícího interního systému vyrovnávání zatížení Azure pomocí PowerShellu

Chcete-li vytvořit více než jednu instanci SAP ASCS/SCS ve stejném clusteru WSFC, přidejte pomocí prostředí PowerShell ip adresu do existujícího interního systému vyrovnávání zatížení Azure. Každá adresa IP vyžaduje vlastní pravidla vyrovnávání zatížení, port sondy, front-endový fond IP adres a back-endový fond.

Následující skript přidá novou adresu IP do existujícího vykladače zatížení. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Skript vytvoří všechna potřebná pravidla vyrovnávání zatížení pro všechny porty SAP ASCS/SCS.

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
Po spuštění skriptu se výsledky zobrazí na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Nový front-endový fond IP adres na webu Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Přidání disků do počítačů clusteru a konfigurace disku sdílené ho shluku SIOS

Je nutné přidat nový disk sdílené složky clusteru pro každou další instanci SAP ASCS/SCS. V systému Windows Server 2012 R2 je aktuálně používán disk sdílení clusteru WSFC softwarovým řešením SIOS DataKeeper.

Udělejte toto:
1. Přidejte další disk nebo disky stejné velikosti (které je třeba prokládaný) do každého uzly clusteru a naformátujte je.
2. Konfigurace replikace úložiště pomocí správce dat SIOS.

Tento postup předpokládá, že jste již nainstalovali SIOS DataKeeper v počítačích clusteru WSFC. Pokud jste ji nainstalovali, je nyní nutné nakonfigurovat replikaci mezi počítači. Tento proces je podrobně popsán v hlavní [příručce pro vysoce dostupnost SAP NetWeaver na virtuálních počítačích se systémem Windows][sap-ha-guide-8.12.3.3].  

![Synchronní zrcadlení DataKeeper pro nový disk sdílení SAP ASCS/SCS][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Nasazení virtuálních měn pro aplikační servery SAP a cluster DBMS

Chcete-li dokončit přípravu infrastruktury pro druhý systém SAP, postupujte takto:

1. Nasaďte vyhrazené virtuální servery pro aplikační servery SAP a vložte je do vlastní vyhrazené skupiny dostupnosti.
2. Nasaďte vyhrazené virtuální hody pro cluster DBMS a vložte je do vlastní vyhrazené skupiny dostupnosti.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalace druhého systému SAP SID2 NetWeaver

Celý proces instalace druhého systému SAP SID2 je popsán v hlavní [příručce pro vysoce dostupnost SAP NetWeaver na virtuálních počítačích se systémem Windows][sap-ha-guide-9].

Postup na vysoké úrovni je následující:

1. [Nainstalujte uzel prvního clusteru SAP][sap-ha-guide-9.1.2].  
 V tomto kroku instalujete SAP s vysokou dostupností instance ASCS/SCS na **existující uzel clusteru WSFC 1**.

2. [Upravte profil SAP instance ASCS/SCS][sap-ha-guide-9.1.3].

3. [Konfigurace portu sondy][sap-ha-guide-9.1.4].  
 V tomto kroku konfigurujete port sondy SAP-SID2-IP pomocí prostředí PowerShell. Tuto konfiguraci spusťte na jednom z uzlů clusteru SAP ASCS/SCS.

4. [Instalace instance databáze] [sap-ha-guide-9.2].  
 V tomto kroku instalujete dbms na vyhrazeném clusteru WSFC.

5. [Instalace druhého uzlu clusteru] [sap-ha-guide-9.3].  
 V tomto kroku instalujete SAP s vysokou dostupností instance ASCS/SCS na existující uzel clusteru WSFC 2.

6. Otevřete porty brány Windows Firewall pro instanci SAP ASCS/SCS a ProbePort.  
 Na obou uzlech clusteru, které se používají pro instance SAP ASCS/SCS, otevíráte všechny porty brány Windows Firewall, které používá SAP ASCS/SCS. Tyto porty jsou uvedeny v [příručce pro vysoce dostupnost SAP NetWeaver na virtuálních počítačích se systémem Windows][sap-ha-guide-8.8].  
 Otevřete také port sondy Azure internal balancer, což je v našem scénáři 62350.

7. [Změňte typ spuštění instance služby SAP ERS Windows][sap-ha-guide-9.4].

8. [Nainstalujte primární aplikační server SAP][sap-ha-guide-9.5] na nový vyhrazený virtuální počítač.

9. [Nainstalujte další aplikační server SAP][sap-ha-guide-9.6] na nový vyhrazený virtuální počítač.

10. [Otestujte převzetí služeb při selhání instance SAP ASCS/SCS a replikaci SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Další kroky

- [Omezení pro sítě: Správce prostředků Azure][networking-limits-azure-resource-manager]
- [Více VIP pro Azure Load Balancer][load-balancer-multivip-overview]
- [Průvodce pro vysoce dostupnost SAP NetWeaver na virtuálních počítačích se systémem Windows][sap-ha-guide]
