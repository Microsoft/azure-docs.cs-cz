---
title: 'Kurz: zabezpečení virtuálního centra pomocí Azure PowerShell'
description: Tento článek popisuje, jak vytvořit virtuální síť Azure v jedné oblasti s povoleným Azure Firewall v centru.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 5ef39118d16d47aeb46294658adf616515547ea6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729520"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Kurz: zabezpečení virtuálního centra pomocí Azure PowerShell

V tomto kurzu vytvoříte instanci virtuální sítě WAN s virtuálním rozbočovačem v jedné oblasti a nasadíte Azure Firewall ve virtuálním centru pro zabezpečení připojení. V tomto příkladu předvádíte zabezpečené připojení mezi virtuálními sítěmi. Provoz mezi virtuálními sítěmi a větvemi site-to-site, Point-to-site nebo ExpressRoute se podporuje i ve virtuálním zabezpečeném centru.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nasazení virtuální sítě WAN
> * Nasazení Azure Firewall a konfigurace vlastního směrování
> * Test připojení

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

- PowerShell 7

   Tento kurz vyžaduje, abyste v prostředí PowerShell 7 spustili Azure PowerShell místně. Pokud chcete nainstalovat PowerShell 7, přečtěte si téma [migrace z Windows powershellu 5,1 do PowerShellu 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7&preserve-view=true).
- AZ. Network Version 3.2.0

    Pokud máte AZ. Network Version 3.4.0 nebo novější, budete muset downgradovat na použití některých příkazů v tomto kurzu. Verzi modulu AZ. Network můžete ověřit pomocí příkazu `Get-InstalledModule -Name Az.Network` . Pokud chcete odinstalovat modul AZ. Network, spusťte příkaz `Uninstall-Module -name az.network` . Pokud chcete nainstalovat modul AZ. Network 3.2.0, spusťte příkaz `Install-Module az.network -RequiredVersion 3.2.0 -force` .

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Počáteční nasazení virtuální sítě WAN

V prvním kroku je potřeba nastavit některé proměnné a vytvořit skupinu prostředků, instanci virtuální sítě WAN a virtuální rozbočovač:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Vytvořte dvě virtuální sítě a připojte je k rozbočovači jako paprsky:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

V tuto chvíli máte plně funkční virtuální síť WAN, která poskytuje připojení typu any-to-Any. Pokud ho chcete rozšířit o zabezpečení, je potřeba nasadit Azure Firewall do každého virtuálního rozbočovače. Zásady brány firewall lze použít k efektivní správě instance Azure Firewall virtuální sítě WAN. Proto se vytvoří zásada brány firewall i v tomto příkladu:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Povolení protokolování z Azure Firewall do Azure Monitor je volitelné, ale v tomto příkladu se používají protokoly brány firewall k prokázání, že provoz prochází bránou firewall:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Nasazení Azure Firewall a konfigurace vlastního směrování

Nyní máte Azure Firewall v centru, ale stále potřebujete změnit směrování, aby virtuální síť WAN odesílala provoz z virtuálních sítí a z větví přes bránu firewall. Provedete to ve dvou krocích:

1. Konfigurace všech připojení k virtuální síti (a připojení větví, pokud existují) pro rozšíření do `None` směrovací tabulky. Tato konfigurace má za následek to, že se jiné virtuální sítě a větve nebudou učit své předpony, takže nemá žádné směrování, aby je bylo možné kontaktovat.
1. Nyní můžete do směrovací tabulky vložit statické trasy `Default` (kde jsou všechny virtuální sítě a větve přidruženy ve výchozím nastavení), aby se veškerý provoz odesílal do Azure firewall.

> [!NOTE]
> Toto je konfigurace nasazená při zabezpečení připojení z webu Azure Portal pomocí nástroje Azure Firewall Manager.

Začněte prvním krokem, abyste nakonfigurovali připojení virtuální sítě k rozšíření do `None` směrovací tabulky:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

Nyní můžete pokračovat v druhém kroku a přidat statické trasy do `Default` směrovací tabulky. V tomto příkladu použijete výchozí konfiguraci, kterou Azure Firewall Manager vygeneroval při zabezpečení připojení ve virtuální síti WAN, ale můžete změnit seznam předpon ve statické trase tak, aby vyhovovaly vašim konkrétním požadavkům:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Test připojení

Teď máte plně funkční zabezpečený rozbočovač. K otestování připojení budete potřebovat jeden virtuální počítač v každé virtuální síti rozbočovače připojené k rozbočovači:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

Výchozí konfigurací v zásadách brány firewall je vyřazení všeho. Proto je nutné nakonfigurovat některá pravidla. Začněte s DNAT pravidly, aby byly testovací virtuální počítače přístupné přes veřejnou IP adresu brány firewall:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Teď můžete nakonfigurovat několik ukázkových pravidel. Definujte síťové pravidlo, které umožní provoz SSH, a pravidlo aplikace, které umožní přístup k Internetu plně kvalifikovanému názvu domény `ifconfig.co` . Tato adresa URL vrátí zdrojovou IP adresu, kterou uvidí v požadavku HTTP:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Před odesláním jakéhokoli provozu můžete zkontrolovat efektivní trasy virtuálních počítačů. Musí obsahovat předpony získané z virtuální sítě WAN ( `0.0.0.0/0` plus RFC1918), ale ne prefix druhého paprsku:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Nyní vygenerujte provoz z jednoho virtuálního počítače do druhého a ověřte, zda je v Azure Firewall vyřazen. V následujících příkazech SSH musíte přijmout otisky prstů virtuálních počítačů a zadat heslo, které jste definovali při vytváření virtuálních počítačů. V tomto příkladu se chystáte odeslat pět paketů požadavku na odezvu ICMP z virtuálního počítače v spoke1 do spoke2 a pokus o připojení TCP na portu 22 pomocí nástroje pro Linux `nc` (s `-vz` příznaky, který pouze pošle požadavek na připojení a zobrazí výsledek). Měli byste vidět, že se příkazy příkazového testu nezdařily a pokus o připojení TCP na portu 22 bude úspěšný, protože je povolený síťovým pravidlem, které jste předtím nakonfigurovali:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

Můžete také ověřit internetový provoz. Požadavky HTTP prostřednictvím nástroje `curl` na plně kvalifikovaný název domény, který jste povolili v zásadách brány firewall ( `ifconfig.co` ), by měly fungovat, ale požadavky HTTP na jakýkoliv jiný cíl by měly selhat (v tomto příkladu se testujete pomocí `bing.com` ):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

Nejjednodušší způsob, jak ověřit, že pakety jsou vyřazeny bránou firewall, je zkontrolovat protokoly. Vzhledem k tomu, že jste nakonfigurovali Azure Firewall, aby odesílaly protokoly do Azure Monitor, můžete pomocí dotazovacího jazyka Kusto načíst příslušné protokoly z Azure Monitor:

> [!NOTE]
> Může to trvat přibližně 1 minutu, než se protokoly zobrazí, aby byly odesílány do Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

V předchozím příkazu byste měli vidět různé položky:

* Vaše připojení SSH se DNAT'ed
* Vyřazené pakety protokolu ICMP mezi virtuálními počítači v paprskech (10.1.1.4 a 10.1.2.4)
* Povolená připojení SSH mezi virtuálními počítači v paprskech

Tady je ukázkový výstup vytvořený příkazem výše:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Pokud chcete zobrazit protokoly pro pravidla použití (s popisem povolených a zakázaných připojení HTTP) nebo změnit způsob zobrazení protokolů, můžete zkusit použít jiné dotazy KQL. Některé příklady najdete v [protokolech Azure Monitor Azure firewall](../firewall/firewall-workbook.md).


## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li odstranit testovací prostředí, můžete odebrat skupinu prostředků se všemi obsažených objekty:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o důvěryhodných partnerech zabezpečení](trusted-security-partners.md)