---
title: Nasazení & konfigurace Azure Firewall v hybridní síti pomocí prostředí PowerShell
description: V tomto článku se naučíte, jak nasadit a nakonfigurovat Azure Firewall pomocí Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 03/26/2021
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: db60c26ed50dae3b4b28a6c44d152a921eb96a69
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627553"
---
# <a name="deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Nasazení a konfigurace služby Azure Firewall v hybridní síti pomocí Azure PowerShellu

Když připojíte místní síť k virtuální síti Azure a vytvoříte hybridní síť, bude mít možnost řídit přístup k síťovým prostředkům Azure důležitou součást celkového plánu zabezpečení.

S využitím služby Azure Firewall můžete řídit síťový přístup v hybridní síti pomocí pravidel, která definují povolený a zakázaný síťový provoz.

V tomto článku vytvoříte tři virtuální sítě:

- **VNet-hub** – brána firewall je v této virtuální síti.
- **VNet-paprsek** – virtuální síť paprsků představuje zatížení, které je umístěné v Azure.
- **VNet-OnPrem** -místní virtuální síť představuje místní síť. Ve skutečném nasazení je možné ho připojit buď pomocí sítě VPN, nebo připojení ExpressRoute. Pro zjednodušení Tento článek používá připojení brány VPN a k reprezentaci místní sítě se používá virtuální síť, která je umístěná v Azure.

![Brána firewall v hybridní síti](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

V tomto článku získáte informace o těchto tématech:

* Deklarování proměnných
* Vytvoření virtuální sítě centra firewallu
* Vytvoření virtuální sítě paprsků
* Vytvoření místní virtuální sítě
* Konfigurace a nasazení brány firewall
* Vytvoření a propojení bran VPN
* Vytvoření partnerského vztahu mezi virtuálními sítěmi hub a paprsek
* Vytvoření tras
* Vytvoření virtuálních počítačů
* Testování brány firewall

Pokud chcete použít Azure Portal k dokončení tohoto kurzu, přečtěte si téma [kurz: nasazení a konfigurace Azure firewall v hybridní síti pomocí Azure Portal](tutorial-hybrid-portal.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Tento článek vyžaduje, abyste spustili PowerShell místně. Musíte mít nainstalovaný modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps). Po ověření verze PowerShellu spusťte příkaz `Login-AzAccount`, abyste vytvořili připojení k Azure.

Předpokladem správného fungování tohoto scénáře jsou tři klíčové požadavky:

- Trasa definovaná uživatelem (UDR) v podsíti paprsků, která odkazuje na IP adresu Azure Firewall jako výchozí bránu. Šíření trasy brány virtuální sítě musí být v této směrovací tabulce **zakázané** .
- UDR v podsíti brány centra musí ukazovat na IP adresu brány firewall jako další směrování na sítě paprsků.

   V Azure Firewall podsíti se nevyžadují žádné UDR, protože se učí trasy od protokolu BGP.
- Při vytváření partnerského vztahu virtuální sítě VNet-Hub s virtuální sítí VNet-Spoke nezapomeňte nastavit **AllowGatewayTransit** a při vytváření partnerského vztahu virtuální sítě VNet-Spoke s virtuální sítí VNet-Hub nezapomeňte nastavit **UseRemoteGateways**.

V části [Vytvoření tras](#create-the-routes) v tomto článku najdete informace o tom, jak se tyto trasy vytvářejí.

>[!NOTE]
>Služba Azure Firewall musí mít přímé připojení k internetu. Pokud vaše AzureFirewallSubnet zjistí výchozí trasu k místní síti přes protokol BGP, je nutné nakonfigurovat Azure Firewall v režimu vynuceného tunelování. Pokud se jedná o existující Azure Firewall, kterou není možné překonfigurovat v režimu vynuceného tunelového propojení, doporučujeme přidat UDR 0.0.0.0/0 na AzureFirewallSubnet s hodnotou **typem** nastavenou jako **Internet** pro zachování přímého připojení k Internetu.
>
>Další informace najdete v tématu [Azure firewall vynucené tunelování](forced-tunneling.md).

>[!NOTE]
>Provoz mezi přímo rovnocenným virtuální sítě je směrován přímo, i když jako výchozí bránu UDR body Azure Firewall. Aby bylo možné odeslat podsíť do brány firewall v tomto scénáři, musí UDR v obou podsítích explicitně obsahovat předponu sítě cílové podsítě.

Chcete-li si projít referenční dokumentaci související Azure PowerShell, přečtěte si téma [Azure PowerShell reference](/powershell/module/az.network/new-azfirewall).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="declare-the-variables"></a>Deklarování proměnných

Následující příklad deklaruje proměnné pomocí hodnot tohoto článku. V některých případech může být nutné nahradit některé hodnoty vlastními, aby fungovaly v rámci vašeho předplatného. Upravte proměnné podle potřeby a pak je zkopírujte a vložte do konzoly PowerShell.

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


## <a name="create-the-firewall-hub-virtual-network"></a>Vytvoření virtuální sítě centra firewallu

Nejdřív vytvořte skupinu prostředků, která bude obsahovat prostředky pro tento článek:

```azurepowershell
  New-AzResourceGroup -Name $RG1 -Location $Location1
  ```

Zadejte podsítě, které se mají zahrnout do virtuální sítě:

```azurepowershell
$FWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Teď vytvořte virtuální síť centra brány firewall:

```azurepowershell
$VNetHub = New-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Vyžádejte si veřejnou IP adresu, kterou chcete přidělit pro bránu VPN, kterou vytvoříte pro virtuální síť. Všimněte si, že *element allocationmethod* je **dynamický**. Nelze zadat IP adresu, kterou chcete použít. Bráně VPN se přidělí automaticky.

  ```azurepowershell
  $gwpip1 = New-AzPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Vytvoření virtuální sítě paprsků

Zadejte podsítě, které se mají zahrnout do virtuální sítě paprsků:

```azurepowershell
$Spokesub = New-AzVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Vytvořit virtuální síť paprsků:

```azurepowershell
$VNetSpoke = New-AzVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Vytvoření místní virtuální sítě

Zadejte podsítě, které se mají zahrnout do virtuální sítě:

```azurepowershell
$Onpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Teď vytvořte místní virtuální síť:

```azurepowershell
$VNetOnprem = New-AzVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Vyžádejte si veřejnou IP adresu, kterou chcete přidělit bráně, kterou vytvoříte pro virtuální síť. Všimněte si, že *element allocationmethod* je **dynamický**. Nelze zadat IP adresu, kterou chcete použít. Přiděluje se pro bránu dynamicky.

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

Rozbočovač a místní virtuální sítě se připojují prostřednictvím bran VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Vytvoření brány VPN pro virtuální síť centrální sítě

Vytvořte konfiguraci brány VPN. Konfigurace brány VPN definuje podsíť a veřejnou IP adresu, která se bude používat.

  ```azurepowershell
  $vnet1 = Get-AzVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Nyní vytvořte bránu VPN pro virtuální síť centrální sítě. Konfigurace sítě na síť vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

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

Teď vytvořte bránu VPN pro místní virtuální síť. Konfigurace sítě na síť vyžadují RouteBased VpnType. Vytvoření brány VPN může obvykle trvat 45 minut nebo déle, a to v závislosti na vybrané skladové položce brány VPN.

```azurepowershell
New-AzVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Vytvoření připojení VPN

Teď můžete vytvořit připojení VPN mezi centrem a místními branami.

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
Vytvořte připojení k virtuální síti z místního prostředí k rozbočovači. Tento krok je podobný předchozímu, s tím rozdílem, že vytvoříte připojení z VNet-Onprem k rozbočovači VNet. Ověřte, že se sdílené klíče shodují. Připojení se vytvoří během několika minut.

  ```azurepowershell
  New-AzVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```

#### <a name="verify-the-connection"></a>Ověření připojení

Úspěšné připojení můžete ověřit pomocí rutiny *Get-AzVirtualNetworkGatewayConnection* s nebo bez *ladění*. Použijte následující příklad rutiny a nakonfigurujte hodnoty tak, aby odpovídaly vašemu prostředí. Pokud se zobrazí výzva, vyberte možnost **A**, aby se spustilo **Vše**. V příkladu *-Name* odkazuje na název připojení, které chcete testovat.

```azurepowershell
Get-AzVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Po dokončení zpracování rutiny si prohlédněte hodnoty. V následujícím příkladu se zobrazí stav připojení *Připojeno* a vidíte příchozí a odchozí bajty.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Vytvoření partnerského vztahu mezi virtuálními sítěmi hub a paprsek

Nyní můžete vytvořit partnerský vztah mezi virtuálními sítěmi hub a paprsek.

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

Teď vytvořte úlohu paprsků a místní virtuální počítače a umístěte je do příslušných podsítí.

### <a name="create-the-workload-virtual-machine"></a>Vytvoření virtuálního počítače úloh

Vytvořte virtuální počítač ve virtuální síti paprsků, spusťte službu IIS bez veřejné IP adresy a v nástroji můžete povolit příkazy k zadání příkazů.
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

Toto je jednoduchý virtuální počítač, který používáte k připojení pomocí vzdálené plochy k veřejné IP adrese. Odtud se pak pomocí brány firewall připojíte k místnímu serveru. Po zobrazení výzvy zadejte pro virtuální počítač uživatelské jméno a heslo.

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

Nejprve získejte a pak Poznamenejte si privátní IP adresu virtuálního počítače **VM-paprsek-01** .

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

Na webu Azure Portal se připojte k virtuálnímu počítači **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Otevřete webový prohlížeč na **virtuálním počítači-OnPrem** a přejděte na http:// \<VM-spoke-01 private IP\> .

Měla by se zobrazit výchozí stránka Internetové informační služby.

Na virtuálním počítači **VM-Onprem** otevřete připojení vzdálené plochy k privátní IP adrese virtuálního počítače **VM-spoke-01**.

Přípojení by mělo proběhnout úspěšně a měli byste být schopni se přihlásit pomocí zvoleného uživatelského jména a hesla.

Takže teď ověříte, že pravidla brány firewall fungují:

<!---- You can ping the server on the spoke VNet.--->
- Webový server můžete procházet ve virtuální síti paprsků.
- Pomocí protokolu RDP se můžete připojit k serveru ve virtuální síti paprsků.

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

[Kurz: Monitorování protokolů brány Azure Firewall](./firewall-diagnostics.md)