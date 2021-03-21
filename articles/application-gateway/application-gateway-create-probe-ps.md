---
title: Vytvoření vlastního testu pomocí PowerShellu
titleSuffix: Azure Application Gateway
description: Naučte se, jak vytvořit vlastní test pro Application Gateway pomocí prostředí PowerShell v Správce prostředků
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 63f4b28915a932398e5417f8ea9be3fe4d68e705
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397837"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Vytvoření vlastního testu pro Azure Application Gateway pomocí prostředí PowerShell pro Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell](application-gateway-create-probe-classic-ps.md)

V tomto článku přidáte vlastní test paměti do existující aplikační brány pomocí PowerShellu. Vlastní sondy jsou užitečné pro aplikace, které mají konkrétní stránku kontroly stavu nebo pro aplikace, které neposkytují úspěšnou odpověď na výchozí webovou aplikaci.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Vytvoření aplikační brány s vlastní sondou

### <a name="sign-in-and-create-resource-group"></a>Přihlášení a vytvoření skupiny prostředků

1. Použijte `Connect-AzAccount` k ověření.

   ```powershell
   Connect-AzAccount
   ```

1. Získejte předplatná pro tento účet.

   ```powershell
   Get-AzSubscription
   ```

1. Zvolte předplatné Azure, které chcete použít.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Vytvořte skupinu prostředků. Pokud máte existující skupinu prostředků, můžete tento krok přeskočit.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Azure Resource Manager vyžaduje, aby všechny skupiny prostředků určily umístění. Toto umístění slouží jako výchozí umístění pro prostředky v příslušné skupině prostředků. Ujistěte se, že všechny příkazy k vytvoření služby Application Gateway používají stejnou skupinu prostředků.

V předchozím příkladu jsme vytvořili skupinu prostředků s názvem **appgw-RG** v umístění **západní USA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Vytvoření virtuální sítě a podsítě

Následující příklad vytvoří virtuální síť a podsíť pro aplikační bránu. Aplikační brána vyžaduje vlastní podsíť pro použití. Z tohoto důvodu by podsíť vytvořená pro aplikační bránu měla být menší než adresní prostor virtuální sítě, aby bylo možné vytvářet a používat jiné podsítě.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Vytvoření veřejné IP adresy pro front-end konfiguraci

Vytvořte prostředek veřejné IP adresy **publicIP01** ve skupině prostředků **appgw-rg** pro oblast USA – západ. V tomto příkladu se používá veřejná IP adresa pro front-end IP adresu aplikační brány.  Služba Application Gateway vyžaduje, aby veřejná IP adresa měla dynamicky vytvořený název DNS, takže `-DomainNameLabel` nejde zadat během vytváření veřejné IP adresy.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Vytvoření brány Application Gateway

Před vytvořením služby Application Gateway se nastavují všechny položky konfigurace. Následující příklad vytvoří položky konfigurace, které jsou potřeba pro prostředek aplikační brány.

| **Komponenta** | **Popis** |
|---|---|
| **Konfigurace protokolu IP brány** | Konfigurace protokolu IP pro aplikační bránu.|
| **Back-endový fond** | Fond IP adres, plně kvalifikovaného názvu domény nebo síťových adaptérů, které jsou aplikačním serverům, které hostují webovou aplikaci.|
| **Sonda stavu** | Vlastní test, který slouží k monitorování stavu členů fondu back-end|
| **Nastavení HTTP** | Kolekce nastavení včetně, portu, protokolu, spřažení založeného na souborech cookie, sondy a časového limitu.  Tato nastavení určují způsob směrování provozu do členů fondu back-end.|
| **Port front-endu** | Port, na kterém brána Application Gateway naslouchá provozu|
| **Naslouchací proces** | Kombinace protokolu, konfigurace IP adresy front-endu a portu front-endu. To naslouchá příchozím žádostem.
|**Pravidlo**| Směruje provoz do příslušného back-endu na základě nastavení HTTP.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Přidání sondy do stávající aplikační brány

Následující fragment kódu přidá test do existující aplikační brány.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Odebrání testu z existující aplikační brány

Následující fragment kódu odebere test z existující aplikační brány.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Získání názvu DNS služby Application Gateway

Po vytvoření brány je dalším krokem konfigurace front-endu pro komunikaci. Při použití veřejné IP adresy služba Application Gateway vyžaduje dynamicky přidělený název DNS, který ale není popisný. Pokud chcete zajistit, aby se koncoví uživatelé mohli dostat ke službě Application Gateway, můžete použít záznam CNAME sloužící k odkazovaní na veřejný koncový bod služby Application Gateway. [Konfigurace vlastního názvu domény pro cloudovou službu Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Budete muset načíst podrobnosti o službě Application Gateway a název její přidružené IP adresy nebo DNS, a to pomocí elementu PublicIPAddress připojeného ke službě Application Gateway. Název DNS služby Application Gateway byste měli použít k vytvoření záznamu CNAME, který tyto dvě webové aplikace odkazuje na tento název DNS. Použití záznamů A se nedoporučuje z toho důvodu, že virtuální IP adresa se může změnit při restartování služby Application Gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Další kroky

Naučte se konfigurovat snižování zátěže TLS na webu: [Konfigurace snižování zátěže TLS](./tutorial-ssl-powershell.md)