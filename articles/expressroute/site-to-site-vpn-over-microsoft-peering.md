---
title: Konfigurace VPN typu site-to-site přes partnerský vztah Azure – ExpressRoute - Microsoftu | Dokumentace Microsoftu
description: Konfigurace protokolu IPsec/IKE připojení k Azure přes partnerský vztah okruhu ExpressRoute Microsoft pomocí brány VPN typu site-to-site.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ba9d7ab9e05c3c5480e1832cc5ddd0ce91a3ae1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53094198"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurace VPN typu site-to-site přes partnerský vztah ExpressRoute Microsoftu

Tento článek vám pomůže nakonfigurovat zabezpečené šifrovaná připojení mezi vaší místní sítí a virtuálním sítím Azure (Vnet) přes privátní připojení ExpressRoute. Můžete vytvořit tunelové propojení site-to-site VPN IPsec/IKE mezi vybrané místní sítě a sítě Azure Vnet partnerského vztahu Microsoftu. Konfigurace zabezpečeného tunelového propojení prostřednictvím ExpressRoute umožňuje výměna dat s důvěrnost, zneužitím, pravosti a integrita.

>[!NOTE]
>Při nastavování VPN typu site-to-site přes Microsoft partnerský vztah, bude vám účtována VPN gateway a výchozí přenos dat sítě VPN. Další informace najdete v tématu [ceny služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

## <a name="architecture"></a>Architektura


  ![Přehled připojení](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Pro vysokou dostupnost a redundance můžete nakonfigurovat více tunelových propojení přes dvě dvojice směrovači MSEE PE okruhu ExpressRoute a povolit Vyrovnávání zatížení mezi tunely.

  ![Možnosti vysoké dostupnosti](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunely VPN přes partnerský vztah Microsoftu můžete ukončit pomocí VPN gateway, nebo pomocí příslušné síťové virtuální zařízení (NVA) k dispozici prostřednictvím Azure Marketplace. Můžete si mohou vyměňovat trasy staticky nebo dynamicky přes šifrované tunely bez vystavení výměna tras základní partnerského vztahu Microsoftu. V příkladech v tomto článku protokol BGP (jiné relace protokolu BGP použitý k vytvoření partnerského vztahu Microsoftu) umožňuje dynamicky exchange předpony přes šifrované tunely.

>[!IMPORTANT]
>Pro stranu místní obvykle partnerského vztahu Microsoftu je ukončený v hraniční síti a soukromého partnerského vztahu je ukončený v zóně základní sítě. Dvě zóny by být odděleny pomocí brány firewall. Pokud konfigurujete partnerský vztah Microsoftu výhradně pro povolení zabezpečené tunelové propojení prostřednictvím ExpressRoute, nezapomeňte filtrovat jenom veřejné IP adresy zájmu, které jsou získávání neinzerují prostřednictvím partnerského vztahu Microsoftu.
>
>

## <a name="workflow"></a>Pracovní postup

1. Konfigurace partnerského vztahu Microsoftu pro váš okruh ExpressRoute.
2. Inzerovat vybrané Azure regionální veřejné předpony k místní síti prostřednictvím partnerského vztahu Microsoftu.
3. Konfigurace brány VPN a vytvořit tunely IPsec
4. Nakonfigurujte místní zařízení VPN.
5. Vytvoření připojení site-to-site protokolu IPsec/IKE.
6. (Volitelné) Konfigurace brány firewall nebo filtrování na místní zařízení VPN.
7. Testování a ověřování IPsec komunikace v rámci okruhu ExpressRoute.

## <a name="peering"></a>1. Nakonfigurujte partnerský vztah Microsoftu

Ke konfiguraci připojení VPN typu site-to-site přes ExpressRoute, musí využívat, partnerský vztah ExpressRoute Microsoftu.

* Ke konfiguraci nového okruhu ExpressRoute, začínat [požadavky služby ExpressRoute](expressroute-prerequisites.md) článku a potom [vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-arm.md).

* Pokud už máte okruh ExpressRoute, ale není nutné nakonfigurovat partnerský vztah Microsoftu, konfigurace partnerského vztahu Microsoftu pomocí [vytvořit a upravit partnerský vztah pro okruh ExpressRoute](expressroute-howto-routing-arm.md#msft) článku.

Po nakonfigurování okruh a partnerský vztah Microsoftu můžete snadno zobrazit pomocí **přehled** stránky na webu Azure Portal.

![okruh](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Konfigurace filtrů směrování

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. Je v podstatě seznamem povolných všechny tyto hodnoty komunity protokolu BGP. 

![filtr tras](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

V tomto příkladu je nasazení jenom v *Azure USA – západ 2* oblasti. Pravidlo filtru tras se přidá do povolit pouze oznámení o inzerovaném programu z Azure USA – západ 2 regionální předpon, který má hodnotu komunity protokolu BGP *12076:51026*. Zadejte místní předpony, které chcete povolit tak, že vyberete **Správa pravidel**.

V rámci se filtr tras musíte také zvolit okruhy ExpressRoute, pro které platí filtr tras. Okruhy ExpressRoute můžete zvolit výběrem **přidat okruh**. Na předchozím obrázku filtr tras souvisí s příkladem okruh ExpressRoute.

### <a name="configfilter"></a>2.1 konfigurace filtr tras

Konfigurujte filtr tras. Pokyny najdete v tématu [konfigurace filtrů směrování pro partnerský vztah Microsoftu](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 ověřte trasy protokolu BGP

Jakmile úspěšně jste vytvořili přes váš okruh ExpressRoute partnerského vztahu Microsoftu a přidružené k filtru tras okruh, můžete ověřit, trasy protokolu BGP poslal Msee na zařízeních PE, které jsou vytvoření partnerského vztahu se směrovači Msee. Příkaz ověření se liší v závislosti na operačním systému zařízení PE.

#### <a name="cisco-examples"></a>Příklady Cisco

Tento příklad používá příkaz Cisco IOS-XE. V tomto příkladu virtuální směrování a předávání instance (VRF) slouží k izolaci provozu partnerského vztahu.

```
show ip bgp vpnv4 vrf 10 summary
```

Následující částečný výstup ukazuje, že byly 68 předpony přijaté od souseda *.243.229.34 s 12076 ASN (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Pokud chcete zobrazit seznam předpony přijaté od souseda, použijte následující příklad:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Pokud chcete potvrdit, že vám posíláme, správnou sadu předpon, můžete mezi ověřit. Následující výstup příkazového prostředí Azure PowerShell seznam předpony inzerované prostřednictvím Microsoft partnerský vztah pro každou službu a pro každou oblast Azure:

```azurepowershell-interactive
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Konfigurace brány VPN a tunelových propojení IPsec

V této části se vytvářejí tunely IPsec VPN mezi Azure VPN gateway a místním zařízením VPN. V příkladech se používá zařízení VPN směrovač Cisco cloudových služeb (CSR1000).

Následující diagram znázorňuje IPsec VPN tunely mezi místním zařízením VPN 1 a pár instance brány Azure VPN. Vytváří se mezi místním zařízením VPN 2 dva tunely IPsec VPN a pár instance brány Azure VPN není znázorněné v diagramu a nejsou uvedené podrobnosti o konfiguraci. S další tunely VPN však zlepšuje vysokou dostupnost.

  ![Tunelová propojení sítě VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Za pár tunel IPsec eBGP se relace k výměně tras privátní sítě. Následující diagram znázorňuje relace eBGP byly vytvořené během pár tunel IPsec:

  ![relace eBGP přes tunel pár](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Následující diagram znázorňuje abstrahovanou přehled sítě příkladu:

  ![Příklad sítě](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>O Příklady šablon Azure Resource Manageru

V příkladech ukončení tunelu IPsec a brány VPN jsou nakonfigurované pomocí šablony Azure Resource Manageru. Pokud začínáte pomocí šablon Resource Manageru, nebo pochopit základy šablony Resource Manageru, najdete v článku [Princip struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md). Šablona v této části se vytvoří úplně nové prostředí Azure (VNet). Nicméně pokud máte existující virtuální síť, můžete na něj mohli odkazovat v šabloně. Pokud nejste obeznámeni s konfigurací site-to-site protokolu IPsec/IKE VPN gateway, přečtěte si téma [vytvoření připojení site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Nemusíte pomocí šablon Azure Resource Manageru k vytvoření této konfigurace. Můžete vytvořit tuto konfiguraci pomocí webu Azure portal nebo Powershellu.
>
>

### <a name="variables3"></a>3.1 deklarujte proměnné

V tomto příkladu odpovídají deklarace proměnných příklad sítě. Při deklarování proměnné, upravte tuto část, aby se zohlednilo vaše prostředí.

* Proměnná **localAddressPrefix** je pole místních IP adres k ukončení tunelových propojení IPsec.
* **GatewaySku** určuje propustnosti sítě VPN. Další informace o gatewaySku a typ sítě VPN najdete v tématu [nastavení konfigurace služby VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Ceny najdete v tématu [ceny služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Nastavte **vpnType** k **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 vytvoření virtuální sítě (VNet)

Pokud přiřazujete existující virtuální síť s tunely VPN, můžete tento krok přeskočit.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 přiřadíte veřejné IP adresy instance brány sítě VPN
 
Přiřadíte veřejnou IP adresu pro každou instanci brány sítě VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 ukončení tunelu VPN s místními (brány místní sítě) zadejte

Místní zařízení VPN se označují jako **bránu místní sítě**. Následující fragment kódu json také určuje vzdálené podrobnosti o partnerském vztahu protokolu BGP:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 vytvoření brány VPN

Tato část šablony nakonfiguruje bránu VPN se požadovaná nastavení pro konfiguraci aktivní aktivní. Mějte na paměti následující požadavky:

* Vytvoření brány VPN pomocí služby **"RouteBased"** typ sítě VPN. Toto nastavení je povinný, pokud chcete povolit směrování protokolu BGP mezi bránou VPN a síti VPN v místním.
* K navázání VPN s2s mezi dvě instance brány sítě VPN a daný místních zařízení v režimu aktivní aktivní, **"aktivní"** parametr je nastaven na **true** v šabloně Resource Manageru . Další informace o vysoce dostupné brány VPN najdete v tématu [vysoce dostupných připojení brány VPN](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Konfigurace relace eBGP mezi tunelová propojení sítě VPN, je nutné zadat dvě různá čísla ASN na obou stranách. Doporučuje se určit privátní čísla ASN. Další informace najdete v tématu [přehled protokolu BGP a sítě Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 vytvořit tunely IPsec

Poslední akce skriptu vytvoří tunely IPsec mezi Azure VPN gateway a místním zařízením VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Konfigurace místního zařízení VPN

Azure VPN gateway je kompatibilní s mnoha zařízení VPN od různých dodavatelů. Informace o konfiguraci a zařízení, která se ověřily pro práci se službou VPN gateway najdete v tématu [informace o zařízeních VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Při konfiguraci zařízení VPN, budete potřebovat následující položky:

* Sdílený klíč. Jde o stejný sdílený klíč, který jste zadali při vytvoření připojení site-to-site VPN. V příkladech se používá základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
* Veřejnou IP adresu vaší brány VPN. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Chcete-li najít veřejnou IP adresu brány VPN pomocí webu Azure portal, přejděte do brány virtuální sítě a pak klikněte na název brány.

Partnerské uzly eBGP jsou obvykle připojeny přímo (často přes sítě WAN připojení). Při konfiguraci eBGP přes tunely IPsec VPN přes partnerský vztah ExpressRoute Microsoftu, existuje ale několik domén směrování mezi rovnocennými počítači eBGP. Použití **ebgp pokus** příkaz, který vytvoří vztah eBGP sousední mezi těmito dvěma není – přímo připojených partnerských uzlů. Celé číslo, který následuje ebgp pokus příkaz určuje hodnota TTL v paketech protokolu BGP. Příkaz **maximální cesty eibgp 2** umožňuje Vyrovnávání zatížení provozu mezi dvěma cestami protokolu BGP.

### <a name="cisco1"></a>Příklad CSR1000 Cisco

Následující příklad znázorňuje konfiguraci pro Cisco CSR1000 ve virtuálním počítači Hyper-V tak, aby v místním zařízením VPN:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. Konfigurace filtrování zařízení VPN a brány firewall (volitelné)

Konfigurace brány firewall a filtrování podle vašich požadavků.

## <a name="testipsec"></a>6. Testování a ověřování tunelu IPsec

Stav tunelových propojení IPsec dá ověřit ve službě Azure VPN gateway pomocí příkazů prostředí Powershell:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Příklad výstupu:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Pokud chcete zkontrolovat stav tunely na instance brány Azure VPN nezávisle na sobě, použijte následující příklad:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Příklad výstupu:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Tunelové propojení stav můžete zkontrolovat také na vaše místní zařízení VPN.

Příklad CSR1000 Cisco:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Příklad výstupu:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Protokol řádku na virtuální rozhraní tunelového propojení (VTI) nezmění na "nahoru", dokud se nedokončí protokolu IKE fáze 2. Následující příkaz ověří přidružení zabezpečení:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Zkontrolujte připojení začátku do konce uvnitř sítě místní a virtuální síť Azure

Pokud se tunely IPsec nastavené a statické trasy správně nastavené, byste měli poslat příkaz ping IP adresu vzdáleného partnerského uzlu protokolu BGP:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Ověřte relací protokolu BGP prostřednictvím protokolu IPsec

Ve službě Azure VPN gateway ověřte stav partnerského uzlu protokolu BGP:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Příklad výstupu:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Pokud chcete ověřit seznam přijatých prostřednictvím eBGP ze sítě VPN koncentrátor místní předpony sítě, můžete filtrovat podle atributu "Zdroj":

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

Ukázkový výstup je číslo ASN 65010 BGP číslo autonomního systému v síti VPN v místním.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Pokud chcete zobrazit seznam Inzerovat trasy:

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Příklad výstupu:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Příklad pro Cisco CSR1000 on-premises:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Seznam sítí inzerované CSR1000 Cisco místní ke službě Azure VPN gateway je možný, pomocí následujícího příkazu:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Další postup

* [Konfigurace Network Performance Monitor pro ExpressRoute](how-to-npm.md)

* [Přidat připojení site-to-site k virtuální síti s existujícím připojením brány VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)