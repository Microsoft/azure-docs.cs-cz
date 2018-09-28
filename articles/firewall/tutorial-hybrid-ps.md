---
title: Nasazení a konfigurace služby Azure Firewall v hybridní síti pomocí Azure PowerShellu
description: V tomto kurzu se naučíte nasadit a konfigurovat bránu Azure Firewall pomocí webu Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 919051a945d423a104b286e9c5703c5b749cf026
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946455"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Kurz: Nasazení a konfigurace služby Azure Firewall v hybridní síti pomocí Azure PowerShellu


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení síťového prostředí
> * Konfigurace a nasazení brány firewall
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Otestovat bránu firewall

V tomto kurzu vytvoříte tři virtuální sítě:
- **VNet-Hub** – v této virtuální síti bude brána firewall.
- **VNet-Spoke** – virtuální síť paprsku představuje úlohu v Azure.
- **VNet-Onprem** – místní virtuální síť představuje místní síť. Ve skutečném nasazení může být připojená přes připojení VPN nebo ExpressRoute. Pro zjednodušení se v tomto kurzu používá připojení brány VPN a virtuální síť v Azure, která představuje místní síť.

![Brána firewall v hybridní síti](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Klíčové požadavky

Předpokladem správného fungování tohoto scénáře jsou tři klíčové požadavky:

- Trasa definovaná uživatelem v podsíti paprsku, která odkazuje na IP adresu služby Azure Firewall jako na výchozí bránu. U této směrovací tabulky musí být **Zakázáno** šíření tras protokolu BGP.
- Trasa definovaná uživatelem v podsíti brány rozbočovače musí odkazovat na IP adresu brány firewall jako na další segment směrování do sítí paprsků.
- V podsíti služby Azure Firewall není potřeba žádná trasa definovaná uživatelem, protože přebírá trasy z protokolu BGP.
- Při vytváření partnerského vztahu virtuální sítě VNet-Hub s virtuální sítí VNet-Spoke nezapomeňte nastavit **AllowGatewayTransit** a při vytváření partnerského vztahu virtuální sítě VNet-Spoke s virtuální sítí VNet-Hub nezapomeňte nastavit **UseRemoteGateways**.

Postup vytvoření těchto tras najdete v části [Vytvoření pravidel](#create-routes) v tomto kurzu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Deklarování proměnných
V následujícím příkladu se deklarují proměnné s použitím hodnot pro tento kurz. Ve většině případů byste měli hodnoty nahradit vlastními. Tyto hodnoty proměnných ale můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné podle potřeby a pak je zkopírujte a vložte do konzoly PowerShell.

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

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

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

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte skupinu prostředků, která bude obsahovat všechny prostředky potřebné pro tento kurz:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>Vytvoření a konfigurace virtuální sítě rozbočovače brány firewall

Definujte podsítě, které se mají zahrnout do virtuální sítě:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Teď vytvořte virtuální síť rozbočovače brány firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Vyžádejte si veřejnou IP adresu, která se přidělí bráně VPN, kterou vytvoříte pro příslušnou virtuální síť. Všimněte si, že metoda *AllocationMethod* je **dynamická**. Není možné určit IP adresu, kterou chcete používat. Bráně VPN se přidělí automaticky. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>Vytvoření a konfigurace virtuální sítě paprsku

Definujte podsítě, které se mají zahrnout do virtuální sítě paprsku:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Vytvořte virtuální síť paprsku:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="configure-and-deploy-the-firewall"></a>Konfigurace a nasazení brány firewall

Teď do virtuální sítě rozbočovače nasaďte bránu firewall.

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

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80
$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *
$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>Vytvoření a propojení bran VPN

Virtuální síť rozbočovače a místní virtuální síť jsou propojené přes brány VPN.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>Vytvoření brány VPN pro virtuální síť rozbočovače

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Teď vytvořte bránu VPN pro virtuální síť rozbočovače. Konfigurace propojení VNet-to-VNet vyžadují typ sítě VPN RouteBased. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>Vytvoření brány VPN pro místní virtuální síť

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Teď vytvořte bránu VPN pro místní virtuální síť. Konfigurace propojení VNet-to-VNet vyžadují typ sítě VPN RouteBased. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN
Teď můžete vytvořit připojení VPN mezi bránou rozbočovače a místní bránou.

#### <a name="get-the-vpn-gateways"></a>Získání bran VPN
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Vytvoření připojení

V tomto kroku vytvoříte připojení z virtuální sítě rozbočovače k místní virtuální síti. Zobrazí se sdílený klíč uváděný v příkladech. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení může nějakou dobu trvat.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Vytvořte připojení z místní virtuální sítě k virtuální síti rozbočovače. Tento krok je podobný předchozímu, vytvoříte však připojení z virtuální sítě Vnet-Onprem k virtuální síti VNet-hub. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

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

## <a name="create-and-configure-the-onprem-vnet"></a>Vytvoření a konfigurace místní virtuální sítě

Definujte podsítě, které se mají zahrnout do virtuální sítě:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Teď vytvořte místní virtuální síť:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Vyžádejte si veřejnou IP adresu, která se přidělí bráně, kterou vytvoříte pro příslušnou virtuální síť. Všimněte si, že metoda *AllocationMethod* je **dynamická**. Není možné určit IP adresu, kterou chcete používat. Přiděluje se pro bránu dynamicky. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="peer-the-hub-and-spoke-vnets"></a>Vytvoření partnerského vztahu mezi virtuální sítí rozbočovače a paprsku

Teď vytvořte partnerský vztah mezi virtuální sítí paprsku a rozbočovače.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Vytvoření tras

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

Teď vytvořte virtuální počítač úloh paprsku a místní virtuální počítač a umístíte je do příslušných podsítí.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh
Ve virtuální síti paprsku vytvořte virtuální počítač se službou IIS, bez veřejné IP adresy a povolenými příchozími příkazy Ping.
Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

```azurepowershell
# Create an inbound network security group rule for port 3389
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

#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1
```

### <a name="create-the-onprem-virtual-machine"></a>Vytvoření místního virtuálního počítače
Toto je jednoduchý virtuální počítač, ke kterému se můžete připojit pomocí Vzdálené plochy s použitím jeho veřejné IP adresy. Odtud se pak můžete připojit místnímu serveru přes bránu firewall. Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.
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
Nejprve získejte privátní IP adresu virtuálního počítače **VM-spoke-01** a poznamenejte si ji.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
2. Na virtuálním počítači **VM-Onprem** otevřete příkazový řádek Windows PowerShellu a příkazem Ping otestujte privátní IP adresu virtuálního počítače **VM-spoke-01**.

   Měli byste obdržet odpověď.
1. Na virtuálním počítači **VM-Onprem** otevřete webový prohlížeč a přejděte na adresu http://\<privátní IP adresa virtuálního počítače VM-spoke-01\>.

   Měla by se zobrazit výchozí stránka Internetové informační služby.

3. Na virtuálním počítači **VM-Onprem** otevřete připojení vzdálené plochy k privátní IP adrese virtuálního počítače **VM-spoke-01**.

   Přípojení by mělo proběhnout úspěšně a měli byste být schopni se přihlásit pomocí zvoleného uživatelského jména a hesla.

Nyní jste ověřili, že pravidla brány firewall fungují:

- Příkazem Ping můžete otestovat server ve virtuální síti paprsku.
- Můžete procházet webový server ve virtuální síti paprsku.
- Pomocí protokolu RDP se můžete připojit k serveru ve virtuální síti paprsku.

Dále změňte akci kolekce pravidel sítě brány firewall na **Odepřít**, abyste ověřili, že pravidla brány firewall fungují podle očekávání. Spuštěním následujícího skriptu změňte akci kolekce pravidel na **Odepřít**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Teď znovu spusťte testy. Tentokrát by všechny měly selhat. Před testováním změněných pravidel ukončete všechna existující připojení vzdálené plochy.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall si můžete ponechat pro další kurz, nebo můžete odstraněním skupiny prostředků **FW-Hybrid-Test** odstranit všechny prostředky související z bránou firewall, pokud už je nepotřebujete.


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nastavení síťového prostředí
> * Konfigurace a nasazení brány firewall
> * Vytvoření tras
> * Vytvoření virtuálních počítačů
> * Otestovat bránu firewall

Dál můžete pokračovat monitorováním protokolů brány Azure Firewall.

> [!div class="nextstepaction"]
> [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
