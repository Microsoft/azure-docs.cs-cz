---
title: Nasazení & konfiguraci Azure Firewall v hybridní síti pomocí PowerShellu
description: V tomto článku se dozvíte, jak nasadit a nakonfigurovat Azure Firewall pomocí Azure PowerShellu.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 01/08/2020
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 37bb28419f23fee2c179171a2e5c0e4e851ac9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471750"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Nasazení a konfigurace služby Azure Firewall v hybridní síti pomocí Azure PowerShellu

Když připojíte místní síť k virtuální síti Azure a vytvoříte hybridní síť, je možnost řídit přístup k prostředkům sítě Azure důležitou součástí celkového plánu zabezpečení.

Pomocí brány Azure Firewall můžete řídit přístup k síti v hybridní síti pomocí pravidel, která definují povolený a odepřený síťový provoz.

Pro tento článek vytvoříte tři virtuální sítě:

- **VNet-Hub** - brána firewall je v této virtuální síti.
- **Virtuální síť-Spoke** – virtuální síť pro paprsky představuje úlohu umístěnou v Azure.
- **VNet-Onprem** – místní virtuální síť představuje místní síť. Ve skutečném nasazení může být připojen pomocí připojení VPN nebo ExpressRoute. Pro jednoduchost tento článek používá připojení brány VPN a virtuální síť umístěná v Azure se používá k reprezentaci místní sítě.

![Brána firewall v hybridní síti](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Deklarování proměnných
> * Vytvoření virtuální sítě centra brány firewall
> * Vytvoření virtuální sítě paprsku
> * Vytvoření místní virtuální sítě
> * Konfigurace a nasazení brány firewall
> * Vytvoření a propojení bran VPN
> * Vzájemné připojení virtuálních sítí rozbočovače a paprsků
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Testování brány firewall

Pokud chcete místo toho pomocí webu Azure Portal dokončit tento kurz, přečtěte si [téma Nasazení a konfigurace Azure Firewall v hybridní síti pomocí portálu Azure](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje, abyste prostředí PowerShell spouštěli místně. Musíte mít nainstalovaný modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.

Předpokladem správného fungování tohoto scénáře jsou tři klíčové požadavky:

- Uživatelem definovaná trasa (UDR) v podsíti paprsku, která odkazuje na IP adresu Azure Firewall jako výchozí bránu. Šíření trasy brány virtuální sítě musí být v této směrovací tabulce **zakázáno.**
- UDR v podsíti brány rozbočovače musí ukazovat na ip adresu brány firewall jako další směrování do sítí paprsků.

   V podsíti Azure Firewall není vyžadováno žádné UDR, protože se učí trasy z protokolu BGP.
- Při vytváření partnerského vztahu virtuální sítě VNet-Hub s virtuální sítí VNet-Spoke nezapomeňte nastavit **AllowGatewayTransit** a při vytváření partnerského vztahu virtuální sítě VNet-Spoke s virtuální sítí VNet-Hub nezapomeňte nastavit **UseRemoteGateways**.

Podívejte se na část [Vytvořit trasy](#create-the-routes) v tomto článku, kde najdete způsob vytváření těchto tras.

>[!NOTE]
>Azure Firewall musí mít přímé připojení k Internetu. Pokud se vaše síť AzureFirewallSubnet učí výchozí trasu do místní sítě prostřednictvím protokolu BGP, musíte to přepsat udr 0.0.0.0/0 s hodnotou **NextHopType** nastavenou jako **Internet,** aby bylo zachováno přímé připojení k Internetu.
>
>Azure Firewall lze nakonfigurovat tak, aby podporovala vynucené tunelové propojení. Další informace naleznete v tématu [Vynucené tunelové propojení azure brány Azure Firewall](forced-tunneling.md).

>[!NOTE]
>Provoz mezi přímo partnerskými virtuálními sítěmi se směruje přímo, i když UDR odkazuje na Azure Firewall jako výchozí bránu. Chcete-li odeslat podsíť do provozu podsítě do brány firewall v tomto scénáři, musí udr obsahovat předponu sítě cílové podsítě explicitně v obou podsítích.

Informace o související referenční dokumentaci k Azure PowerShellu najdete v tématu [Reference Azure PowerShellu](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="declare-the-variables"></a>Deklarování proměnných

Následující příklad deklaruje proměnné pomocí hodnot pro tento článek. V některých případech může být nutné nahradit některé hodnoty vlastními, aby fungovaly v předplatném. Upravte proměnné podle potřeby a pak je zkopírujte a vložte do konzoly PowerShell.

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

Nejprve vytvořte skupinu prostředků, která bude obsahovat prostředky pro tento článek:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Definujte podsítě, které mají být zahrnuty do virtuální sítě:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Nyní vytvořte virtuální síť centra firewall:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Požádejte o přidělení veřejné IP adresy bráně VPN, kterou vytvoříte pro virtuální síť. Všimněte si, že *AllocationMethod* je **dynamic**. Nelze zadat IP adresu, kterou chcete použít. Bráně VPN se přidělí automaticky.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Vytvoření virtuální sítě paprsku

Definujte podsítě, které mají být zahrnuty do virtuální sítě paprsků:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Vytvoření virtuální sítě paprsků:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Vytvoření místní virtuální sítě

Definujte podsítě, které mají být zahrnuty do virtuální sítě:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Teď vytvořte místní virtuální síť:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Požádejte o přidělení veřejné IP adresy bráně, kterou vytvoříte pro virtuální síť. Všimněte si, že *AllocationMethod* je **dynamic**. Nelze zadat IP adresu, kterou chcete použít. Přiděluje se pro bránu dynamicky.

  ```azurepowershell
  $gwOnprempip = New-AzPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurace a nasazení brány firewall

Teď nasaďte bránu firewall do virtuální sítě rozbočovače.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Konfigurace pravidel sítě

<!--- $Rule3 = New-AzFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Vytvoření a propojení bran VPN

Rozbočovač a místní virtuální sítě jsou propojené přes brány VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Vytvoření brány VPN pro virtuální síť rozbočovače

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Teď vytvořte bránu VPN pro virtuální síť rozbočovače. Konfigurace sítě-sítě vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Vytvoření brány VPN pro místní virtuální síť

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
$vnet2 = Get-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Teď vytvořte bránu VPN pro místní virtuální síť. Konfigurace sítě-sítě vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN

Nyní můžete vytvořit připojení VPN mezi rozbočovačem a místními branami.

#### <a name="get-the-vpn-gateways"></a>Získání bran VPN

```azurepowershell
$vnetHubgw = Get-AzVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Vytvoření připojení

V tomto kroku vytvoříte připojení z virtuální sítě rozbočovače k místní virtuální síti. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

```azurepowershell
New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Vytvořte místní připojení k rozbočovači virtuální sítě. Tento krok je podobný předchozímu kroku, s výjimkou vytvoření připojení z virtuální sítě-onprem do centra virtuální sítě. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Ověření připojení

Úspěšné připojení můžete ověřit pomocí rutiny *Get-AzVirtualNetworkGatewayConnection* s nebo bez *-Debug*. Použijte následující příklad rutiny a nakonfigurujte hodnoty tak, aby odpovídaly vašemu prostředí. Pokud se zobrazí výzva, vyberte možnost **A**, aby se spustilo **Vše**. V příkladu *-Name* odkazuje na název připojení, které chcete testovat.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Po dokončení zpracování rutiny si prohlédněte hodnoty. V následujícím příkladu se zobrazí stav připojení *Připojeno* a vidíte příchozí a odchozí bajty.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Vzájemné připojení virtuálních sítí rozbočovače a paprsků

Nyní peer rozbočovač a mluvil virtuální sítě.

```azurepowershell
# Peer hub to spoke
Add-AzVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Vytvoření tras

Dále vytvořte několik tras:

- Trasa z podsítě brány rozbočovače do podsítě paprsku přes IP adresu brány firewall
- Výchozí trasa z podsítě paprsku přes IP adresu brány firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled. The property is now called "Virtual network gateway route propagation," but the API still refers to the parameter as "DisableBgpRoutePropagation."
$routeTableSpokeDG = New-AzRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzRouteConfig `
  -Name "ToFirewall" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzRouteTable

#Associate the route table to the subnet

Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzVirtualNetwork
```

## <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvořte zatížení paprsku a místní virtuální počítače a umístěte je do příslušných podsítí.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh

Vytvořte virtuální počítač ve virtuální síti s paprsky, ve které se spouštějí služby IIS bez veřejné IP adresy a umožňuje připojení ping.
Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzVMExtension `
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
Set-AzVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Vytvoření místního virtuálního počítače

Jedná se o jednoduchý virtuální počítač, který slouží k připojení pomocí vzdálené plochy k veřejné IP adrese. Odtud se pak připojíte k místnímu serveru přes bránu firewall. Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

```azurepowershell
New-AzVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testování brány firewall

Nejprve získejte a poznamenejte si privátní IP adresu **virtuálního počítače s paprsky-01.**

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Otevřete webový prohlížeč na **VM-Onprem**\<a přejděte na http://\>soukromé IP adresy VM-spoke-01 .

Měla by se zobrazit výchozí stránka Internetové informační služby.

Na virtuálním počítači **VM-Onprem** otevřete připojení vzdálené plochy k privátní IP adrese virtuálního počítače **VM-spoke-01**.

Přípojení by mělo proběhnout úspěšně a měli byste být schopni se přihlásit pomocí zvoleného uživatelského jména a hesla.

Takže teď jste ověřili, že pravidla brány firewall fungují:

<!---- You can ping the server on the spoke VNet.--->
- Webový server můžete procházet ve virtuální síti s paprsky.
- K serveru ve virtuální síti s paprsky se můžete připojit pomocí programu RDP.

Dále změňte akci kolekce pravidel sítě brány firewall na **Odepřít**, abyste ověřili, že pravidla brány firewall fungují podle očekávání. Spuštěním následujícího skriptu změňte akci kolekce pravidel na **Odepřít**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzFirewall -AzureFirewall $azfw
```

Teď znovu spusťte testy. Tentokrát by všechny měly selhat. Před testováním změněných pravidel ukončete všechna existující připojení vzdálené plochy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **FW-Hybrid-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.

## <a name="next-steps"></a>Další kroky

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

[Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
