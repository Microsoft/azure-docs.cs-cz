---
title: Vytvořte konfiguraci několika identifikátorů SID služby SAP v Azure | Dokumentace Microsoftu
description: Průvodce konfigurace několika identifikátorů SID s vysokou dostupností SAP NetWeaver na Windows virtual machines
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
ms.openlocfilehash: 05fda1b4f71f67714b4723e15533d3555d497249
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54321666"
---
# <a name="create-an-sap-netweaver-multi-sid-configuration"></a>Vytvořte konfiguraci několika identifikátorů SID služby SAP NetWeaver

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

V září 2016 společnost Microsoft vydala funkce, kde můžete spravovat víc virtuálních IP adres pomocí Azure interního nástroje load balancer. Tato funkce již existuje v nástroji pro vyrovnávání zatížení Azure externí.

Pokud máte nasazení SAP, jak je uvedeno v, můžete použít interní nástroj pro vytvoření konfigurace clusteru Windows pro SAP ASCS/SCS, [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows] [ sap-ha-guide].

Tento článek se zaměřuje na přesunout z jednoho instalačního ASCS/SCS do konfigurace několika identifikátorů SID SAP nainstalováním dalších instancí SAP ASCS/SCS v clusteru do existujícího clusteru Windows Server Failover Clustering (WSFC). Po dokončení tohoto procesu jste nakonfigurovali clusteru s několika SID služby SAP.


## <a name="prerequisites"></a>Požadavky
Cluster služby WSFC, který se používá pro jednu instanci SAP ASCS/SCS, jste už nakonfigurovali, jak je popsáno v [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows] [ sap-ha-guide] a jak je znázorněno v tomto diagramu.

![Instanci SAP ASCS/SCS vysoké dostupnosti][sap-ha-guide-figure-6001]

## <a name="target-architecture"></a>Cílová architektura

Cílem je nainstalovat více SAP ABAP ASCS nebo SAP Java SCS Clusterované instance ve stejném clusteru služby WSFC jako ilustrované tady:

![Více instancí SAP ASCS/SCS v clusteru v Azure][sap-ha-guide-figure-6002]

> [!NOTE]
>Platí omezení na počet privátní front-endové IP adresy pro každý nástroj pro vyrovnávání zatížení Azure interní.
>
>Maximální počet instancí SAP ASCS/SCS v jednom clusteru služby WSFC je rovna hodnotě maximální počet privátní front-endové IP adresy pro každý nástroj pro vyrovnávání zatížení Azure interní.
>

Další informace o omezeních nástroj pro vyrovnávání zatížení, najdete v části "privátní front-endové IP adresy na nástroj pro vyrovnávání zatížení" v [síťová omezení: Azure Resource Manageru][networking-limits-azure-resource-manager].

Kompletní prostředí s dvěma systémy vysoké dostupnosti SAP bude vypadat takto:

![Nastavení s několika SID vysoké dostupnosti SAP pomocí systému SAP dva identifikátory SID][sap-ha-guide-figure-6003]

> [!IMPORTANT]
> Nastavení musí splňovat následující podmínky:
> - Instance SAP ASCS/SCS musejí sdílet stejný cluster služby WSFC.
> - Každé číslo SID DBMS musí mít svůj vlastní vyhrazený cluster služby WSFC.
> - Aplikační servery SAP, které patří do jednoho systému SAP SID musí mít vlastní vyhrazený virtuálních počítačů.


## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury
Připravíte infrastrukturu, můžete nainstalovat další instance SAP ASCS/SCS s následujícími parametry:

| Název parametru | Hodnota |
| --- | --- |
| SAP ASCS/SCS SID |pr1-lb-ascs |
| K SAP DBMS interní nástroj pro vyrovnávání zatížení | PR5 |
| Název virtuálního hostitele SAP | pr5-sap-cl |
| Hostování SAP ASCS/SCS virtuální IP adresa (IP adresa nástroje pro vyrovnávání zatížení Další Azure) | 10.0.0.50 |
| Číslo instance SAP ASCS/SCS | 50 |
| Port testu ILB pro další instanci SAP ASCS/SCS | 62350 |

> [!NOTE]
> Pro SAP ASCS/SCS instance clusteru vyžaduje každou IP adresu portu sondy jedinečný. Například pokud jedna IP adresa ve službě Azure interní nástroj používá port testu 62300, žádná další IP adresa na tento nástroj pro vyrovnávání zatížení můžete použít port testu 62300.
>
>Pro naše účely protože je už rezervovaná portu sondy 62300, používáme portu sondy 62350.

V existujícím clusteru služby WSFC se dvěma uzly můžete nainstalovat další instance SAP ASCS/SCS:

| Role virtuálního počítače | Název hostitele virtuálního počítače | Statická IP adresa |
| --- | --- | --- |
| 1 uzel clusteru pro instanci ASCS/SCS |pr1-ascs-0 |10.0.0.10 |
| 2. uzel clusteru pro instanci ASCS/SCS |pr1-ascs-1 |10.0.0.9 |

### <a name="create-a-virtual-host-name-for-the-clustered-sap-ascsscs-instance-on-the-dns-server"></a>Vytvořte virtuální hostitel název Clusterové instance SAP ASCS/SCS na serveru DNS

Můžete vytvořit položku DNS pro název virtuálního hostitele ASCS/SCS instance pomocí následujících parametrů:

| Nový název virtuálního hostitele SAP ASCS/SCS | Přidružené IP adresy |
| --- | --- | --- |
|pr5-sap-cl |10.0.0.50 |

Nový název hostitele a IP adresa se zobrazí ve Správci DNS, jak je znázorněno na následujícím snímku obrazovky:

![Správce DNS seznamu zvýraznění definovanou položku DNS pro novou SAP ASCS/SCS clusteru virtuální název a adresu protokolu TCP/IP][sap-ha-guide-figure-6004]

Postup pro vytvoření položky DNS je také popsáno podrobněji v hlavním [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-9.1.1].

> [!NOTE]
> Novou IP adresu, která je přiřazena k názvu virtuální hostitel další instance ASCS/SCS musí být stejný jako nové IP adresy, který jste přiřadili k SAP Azure load balancer.
>
>V tomto scénáři IP adresa je 10.0.0.50.

### <a name="add-an-ip-address-to-an-existing-azure-internal-load-balancer-by-using-powershell"></a>Přidat IP adresu pro existující interní Azure load balancer pomocí prostředí PowerShell

Chcete-li vytvořit více než jednu instanci SAP ASCS/SCS ve stejném clusteru služby WSFC, přidat IP adresu do stávajícího Azure interního nástroje pro vyrovnávání pomocí Powershellu. Každou IP adresu vyžaduje svou vlastní pravidla Vyrovnávání zatížení, port testu, front-endový fond IP adres a back endového fondu.

Následující skript přidá novou IP adresu existující nástroj pro vyrovnávání zatížení. Aktualizujte proměnné prostředí PowerShell pro vaše prostředí. Tento skript vytvoří všechny potřebné pravidla Vyrovnávání zatížení pro všechny porty SAP ASCS/SCS.

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

Write-Host "Successfully added new IP '$ILBIP' to the internal load balancer '$ILBName'!" -ForegroundColor Green

```
Po spuštění skriptu, výsledky se zobrazí na webu Azure Portal, jak je znázorněno na následujícím snímku obrazovky:

![Nový front-endový fond IP na webu Azure Portal][sap-ha-guide-figure-6005]

### <a name="add-disks-to-cluster-machines-and-configure-the-sios-cluster-share-disk"></a>Přidejte disky do clusteru počítačů a konfiguraci disku SIOS clusteru sdílené složky

Je nutné přidat nový disk clusteru sdílenou složku pro každý další instanci SAP ASCS/SCS. Pro Windows Server 2012 R2 je disk sdílená složka clusteru služby WSFC aktuálně používaných SIOS DataKeeper softwarové řešení.

Udělejte toto:
1. Přidejte další disk nebo disky stejné velikosti (které je potřeba prokládanou) na každém uzlu clusteru a naformátovat.
2. Konfigurace replikace úložiště se SIOS Datakeeperem.

Tento postup předpokládá, že jste již nainstalovali SIOS DataKeeper na počítačích clusteru služby WSFC. Pokud jste nainstalovali ji, teď musíte nakonfigurovat replikaci mezi počítači. Proces je podrobně popsány v hlavním [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-8.12.3.3].  

![DataKeeper synchronní zrcadlení pro nové SAP ASCS/SCS sdílet disk][sap-ha-guide-figure-6006]

### <a name="deploy-vms-for-sap-application-servers-and-dbms-cluster"></a>Nasazení virtuálních počítačů pro aplikační servery SAP a DBMS cluster

K dokončení přípravy infrastruktury pro druhý systém SAP, postupujte takto:

1. Nasazení vyhrazených virtuálních počítačích pro aplikační servery SAP a umístili je na své vlastní vyhrazený dostupnosti skupiny.
2. Nasazení vyhrazených virtuálních počítačích pro DBMS cluster a umístit je do své vlastní vyhrazený dostupnosti skupiny.


## <a name="install-the-second-sap-sid2-netweaver-system"></a>Instalaci druhého systému SAP SID2 NetWeaver

Dokončení procesu instalace druhý systém SAP SID2 je popsán v hlavním [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-9].

Podrobný postup je následující:

1. [Instalace SAP prvním uzlu clusteru][sap-ha-guide-9.1.2].  
 V tomto kroku, na který instalujete SAP s instanci a vysoké dostupnosti ASCS/SCS **uzlu clusteru služby WSFC existující 1**.

2. [Upravit profil SAP ASCS/SCS instance][sap-ha-guide-9.1.3].

3. [Konfigurace portu sondy][sap-ha-guide-9.1.4].  
 V tomto kroku nakonfigurujete prostředek clusteru SAP portu sondy SAP SID2 IP pomocí prostředí PowerShell. Spusťte tuto konfiguraci na jeden z uzlů clusteru SAP ASCS/SCS.

4. [Instalace instance databáze] [sap-ha-guide-9.2].  
 V tomto kroku instalujete DBMS na vyhrazeném clusteru služby WSFC.

5. [Instalace druhém uzlu clusteru] [sap-ha-guide-9.3].  
 V tomto kroku nainstalujete SAP s ASCS/SCS instancí vysoké dostupnosti na stávající uzel clusteru služby WSFC 2.

6. Otevřete porty brány Windows Firewall pro instanci SAP ASCS/SCS a ProbePort.  
 Na oba uzly, které se používají pro instance SAP ASCS/SCS které otevíráte, všechny porty brány Windows Firewall, které se používají SAP ASCS/SCS. Tyto porty jsou uvedeny v [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide-8.8].  
 Také otevřete port testu Azure interního nástroje pro vyrovnávání, tedy 62350 v tomto scénáři.

7. [Změnit typ spuštění instance služby Windows Lajících SAP][sap-ha-guide-9.4].

8. [Nainstalovat primární aplikační server SAP] [ sap-ha-guide-9.5] na novém vyhrazený virtuální počítač.

9. [Nainstalovat další aplikační server SAP] [ sap-ha-guide-9.6] na novém vyhrazený virtuální počítač.

10. [Testovací převzetí služeb při selhání SAP ASCS/SCS instance a replikace SIOS][sap-ha-guide-10].

## <a name="next-steps"></a>Další postup

- [Síťová omezení: Azure Resource Manager][networking-limits-azure-resource-manager]
- [Více virtuálních IP adres Azure Load Balancer][load-balancer-multivip-overview]
- [Příručka pro vysokou dostupnost SAP NetWeaver na virtuálních počítačích s Windows][sap-ha-guide]
