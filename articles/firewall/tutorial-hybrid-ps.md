---
title: 'Kurz: Nasazení a konfigurace služby Azure Firewall v hybridní síti pomocí Azure PowerShellu'
description: V tomto kurzu se dozvíte, jak nasadit a nakonfigurovat Firewall služby Azure pomocí Azure Powershellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 1/30/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 29af70988cf77b9fad47e5c2478e5c86529fe9cf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458211"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Kurz: Nasazení a konfigurace služby Azure Firewall v hybridní síti pomocí Azure PowerShellu

Při připojení místní sítě k virtuální síti vytvořit hybridní síť, Azure, možnost řízení přístupu k prostředkům Azure sítě je důležitou součástí celkového plánu zabezpečení.

Brána Firewall služby Azure můžete použít k řízení přístupu k síti v hybridní síť s využitím pravidel, která definují povolené a zakázané síťový provoz.

V tomto kurzu vytvoříte tři virtuální sítě:

- **Připojení typu VNet-Hub** – brána firewall je v této virtuální síti.
- **Virtuální sítě paprsků** -virtuální sítě paprsků představuje úlohy na Azure.
- **Připojení typu VNet-Onprem** – místní síť představuje místní virtuální sítě. Skutečné nasazení se dá připojit pomocí připojení VPN nebo trasy. Pro zjednodušení tento kurz používá připojení brány VPN a Azure umístěný virtuální síť se používá k reprezentování v místní síti.

![Brána firewall v hybridní síti](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Deklarování proměnných
> * Vytvoření virtuální sítě centra brány firewall
> * Vytvoření virtuální sítě paprsků
> * Vytvořit místní virtuální sítě
> * Konfigurace a nasazení brány firewall
> * Vytvoření a propojení bran VPN
> * Vytvoření partnerského vztahu centra a virtuální sítě paprsků
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Otestovat bránu firewall

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje místně spusťte PowerShell. Musí mít modul Azure PowerShell verze 6.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzureRmAccount`, abyste vytvořili připojení k Azure.

Předpokladem správného fungování tohoto scénáře jsou tři klíčové požadavky:

- Uživatelem definovanou trasou (UDR) v podsíti paprsku, který odkazuje na adresu IP brány Firewall Azure jako výchozí brána. U této směrovací tabulky musí být **Zakázáno** šíření tras protokolu BGP.
- Uživatelem definovaná TRASA v podsíti brány centra musí odkazovat na IP adresu brány firewall jako další segment pro sítě paprsků.
- Žádné uživatelem definovaná TRASA se vyžaduje v podsíti brány Firewall Azure jako zjišťuje směrování z protokolu BGP.
- Při vytváření partnerského vztahu virtuální sítě VNet-Hub s virtuální sítí VNet-Spoke nezapomeňte nastavit **AllowGatewayTransit** a při vytváření partnerského vztahu virtuální sítě VNet-Spoke s virtuální sítí VNet-Hub nezapomeňte nastavit **UseRemoteGateways**.

Postup vytvoření těchto tras najdete v části [Vytvoření pravidel](#create-routes) v tomto kurzu.

>[!NOTE]
>Azure brány Firewall musí mít přímé připojení k Internetu. Pokud jste povolili vynuceného tunelování k místnímu přes ExpressRoute nebo služby Application Gateway, budete muset nakonfigurovat 0.0.0.0/0 uživatelem definovaná TRASA s **NextHopType** hodnota nastavená na **Internet**a pak ji přiřaďte  **AzureFirewallSubnet**.

>[!NOTE]
>Přenos dat mezi přímo partnerských virtuálních sítích je směrován přímo i v případě, že trasu UDR odkazuje na jako výchozí brána Firewall služby Azure. K odeslání podsítě pro podsíť provozu do brány firewall v tomto scénáři, musí obsahovat trasu UDR předpona cílové podsítě sítě explicitně v obou podsítích.

Související referenční dokumentace k Azure Powershellu najdete v tématu [referenční informace k Azure Powershellu](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="declare-the-variables"></a>Deklarování proměnných

V následujícím příkladu se deklarují proměnné s použitím hodnot pro tento kurz. V některých případech může být potřeba nahradit některé hodnoty vlastními pro práci v rámci vašeho předplatného. Upravte proměnné podle potřeby a pak je zkopírujte a vložte do konzoly PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```


## <a name="create-the-firewall-hub-virtual-network"></a>Vytvoření virtuální sítě centra brány firewall

Nejprve vytvořte skupinu prostředků obsahující prostředky pro účely tohoto kurzu:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

Definujte podsítě, které mají být zahrnuty ve virtuální síti:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Teď vytvořte virtuální síť centra brány firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Požádat o veřejnou IP adresu, která bude přidělena pro bránu VPN, kterou vytvoříte pro vaše virtuální síť. Všimněte si, že metoda *AllocationMethod* je **dynamická**. Není možné určit IP adresu, kterou chcete používat. Bráně VPN se přidělí automaticky. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Vytvoření virtuální sítě paprsků

Definujte podsítě, které mají být zahrnuty do virtuální sítě paprsků:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Vytvoření virtuální sítě paprsků:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Vytvořit místní virtuální sítě

Definujte podsítě, které mají být zahrnuty ve virtuální síti:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Teď vytvořte virtuální místní sítě:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Požádat o veřejnou IP adresu, která bude přidělena pro bránu, kterou vytvoříte pro virtuální síť. Všimněte si, že metoda *AllocationMethod* je **dynamická**. Není možné určit IP adresu, kterou chcete používat. Přiděluje se pro bránu dynamicky. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurace a nasazení brány firewall

Teď do centrální virtuální síti nasaďte bránu firewall.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Konfigurace pravidel sítě

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Vytvoření a propojení bran VPN

Virtuální sítě centra a místní připojení prostřednictvím bran VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Vytvoření brány VPN pro virtuální síť centra

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Teď vytvořte bránu VPN pro virtuální síť centra. Konfigurace sítě pro sítě vyžadují typ sítě VPN RouteBased. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Vytvoření brány VPN pro místní virtuální sítě

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Teď vytvořte bránu VPN pro místní virtuální sítě. Konfigurace sítě pro sítě vyžadují typ sítě VPN RouteBased. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN

Nyní můžete vytvořit připojení VPN mezi centrem a místní brány

#### <a name="get-the-vpn-gateways"></a>Získání bran VPN

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Vytvoření připojení

V tomto kroku vytvoříte připojení z virtuální sítě centra pro místní virtuální sítě. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Vytvořte místní k připojení k virtuální síti centra. Tento krok je podobný předchozímu, s výjimkou vytvoření připojení z virtuální sítě místního prostředí do virtuální sítě centra. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Ověření připojení

Úspěšné připojení můžete ověřit pomocí rutiny *Get-AzureRmVirtualNetworkGatewayConnection* s přepínačem *-Debug* nebo bez něj. Použijte následující příklad rutiny a nakonfigurujte hodnoty tak, aby odpovídaly vašemu prostředí. Pokud se zobrazí výzva, vyberte možnost **A**, aby se spustilo **Vše**. V příkladu odkazuje *-Name* na název připojení, které chcete otestovat.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Po dokončení zpracování rutiny si prohlédněte hodnoty. V následujícím příkladu se zobrazí stav připojení *Připojeno* a vidíte příchozí a odchozí bajty.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Vytvoření partnerského vztahu centra a virtuální sítě paprsků

Nyní vytvořit partnerský vztah centra a virtuální sítě paprsků.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Vytvoření tras

Dále vytvořte několik tras:

- Trasa z podsítě brány rozbočovače do podsítě paprsku přes IP adresu brány firewall
- Výchozí trasa z podsítě paprsku přes IP adresu brány firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte paprsků pracovního vytížení a místní virtuální počítače a umístit je do příslušné podsítě.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh

Vytvoření virtuálního počítače ve virtuální síti paprsku službou IIS, se žádná veřejná IP adresa a umožňuje příkazy ping pro zjištění v.
Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Vytvořit místní virtuální počítač

Toto je jednoduchý virtuální počítač, který používáte pro připojení pomocí vzdálené plochy veřejnou IP adresu. Odtud pak připojíte k místnímu serveru prostřednictvím brány firewall. Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Otestovat bránu firewall

Nejprve získejte a poznamenejte si privátní IP adresu pro **VM-spoke-01** virtuálního počítače.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Otevřete webový prohlížeč na **VM Onprem**a přejděte na http://\<privátní IP adresa virtuálního počítače spoke-01\>.

Měla by se zobrazit výchozí stránka Internetové informační služby.

Na virtuálním počítači **VM-Onprem** otevřete připojení vzdálené plochy k privátní IP adrese virtuálního počítače **VM-spoke-01**.

Přípojení by mělo proběhnout úspěšně a měli byste být schopni se přihlásit pomocí zvoleného uživatelského jména a hesla.

Nyní jste ověřili, že pravidla brány firewall fungují:

<!---- You can ping the server on the spoke VNet.--->
- Můžete procházet webový server na virtuální sítě paprsků.
- Můžete připojit k serveru v paprsku virtuální síť pomocí protokolu RDP.

Dále změňte akci kolekce pravidel sítě brány firewall na **Odepřít**, abyste ověřili, že pravidla brány firewall fungují podle očekávání. Spuštěním následujícího skriptu změňte akci kolekce pravidel na **Odepřít**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

Teď znovu spusťte testy. Tentokrát by všechny měly selhat. Před testováním změněných pravidel ukončete všechna existující připojení vzdálené plochy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **FW-Hybrid-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další postup

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorujte protokoly brány Firewall na Azure](./tutorial-diagnostics.md)
