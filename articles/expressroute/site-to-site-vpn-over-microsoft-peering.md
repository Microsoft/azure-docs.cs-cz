---
title: 'Azure ExpressRoute: Konfigurace S2S VPN přes partnerský vztah Microsoftu'
description: Pomocí brány VPN typu Site-to-site můžete nakonfigurovat připojení protokolem IPsec/IKE k Azure přes okruh ExpressRoute s partnerským vztahem Microsoftu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 366f27a0e2a22e9aa10dda20e105bf644255bdd4
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89393135"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurace VPN typu Site-to-site přes partnerský vztah Microsoftu ExpressRoute

Tento článek vám pomůže s konfigurací zabezpečeného šifrovaného připojení mezi vaší místní sítí a virtuálními sítěmi Azure (virtuální sítě) prostřednictvím privátního připojení ExpressRoute. Partnerský vztah Microsoftu můžete použít k navázání tunelu VPN typu Site-to-site s protokolem IPsec/IKE mezi vybranými místními sítěmi a Azure virtuální sítě. Konfigurace zabezpečeného tunelového propojení přes ExpressRoute umožňuje výměnu dat s důvěrnými daty, proti nim, pravě a integritě.

>[!NOTE]
>Když nastavíte síť Site-to-Site VPN přes partnerský vztah Microsoftu, budou se vám účtovat přenosy VPN Gateway a VPN. Další informace najdete v tématu [VPN Gateway ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektura


  ![Přehled připojení](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Pro zajištění vysoké dostupnosti a redundance můžete nakonfigurovat několik tunelových propojení přes dva páry MSEE-PE okruhu ExpressRoute a povolit vyrovnávání zatížení mezi tunely.

  ![možnosti vysoké dostupnosti](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunely VPN přes partnerský vztah Microsoftu se můžou ukončit buď pomocí služby VPN Gateway, nebo pomocí vhodného síťového virtuálního zařízení (síťové virtuální zařízení) dostupného prostřednictvím Azure Marketplace. Trasy můžete vyměňovat staticky nebo dynamicky prostřednictvím šifrovaných tunelů, aniž byste vystavili výměnu trasy k základnímu partnerskému vztahu Microsoftu. V příkladech v tomto článku se protokol BGP (jiný z relace protokolu BGP použitý k vytvoření partnerského vztahu Microsoftu) používá k dynamické výměně předpon přes šifrovaná tunely.

>[!IMPORTANT]
>Pro místní stranu se obvykle ukončí partnerský vztah Microsoftu na DMZ a privátní partnerské vztahy se ukončí v zóně základní sítě. Tyto dvě zóny budou oddělené pomocí bran firewall. Pokud konfigurujete partnerský vztah Microsoftu pro povolení zabezpečeného tunelování přes ExpressRoute, nezapomeňte filtrovat jenom veřejné IP adresy, které jsou inzerovány prostřednictvím partnerského vztahu Microsoftu.
>
>

## <a name="workflow"></a><a name="workflow"></a>Pracovní postup

1. Nakonfigurujte partnerský vztah Microsoftu pro okruh ExpressRoute.
2. Inzerovat vybrané místní veřejné předpony Azure do místní sítě prostřednictvím partnerského vztahu Microsoftu.
3. Konfigurace brány VPN a vytvoření tunelů IPsec
4. Nakonfigurujte místní zařízení VPN.
5. Vytvořte připojení Site-to-site s protokolem IPsec/IKE.
6. Volitelné Nakonfigurujte brány firewall nebo filtrování na místním zařízení VPN.
7. Otestujte a ověřte komunikaci protokolu IPsec přes okruh ExpressRoute.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. konfigurace partnerského vztahu Microsoftu

Pokud chcete nakonfigurovat připojení VPN typu Site-to-site přes ExpressRoute, musíte využít partnerský vztah Microsoft ExpressRoute.

* Pokud chcete nakonfigurovat nový okruh ExpressRoute, začněte v článku [požadavky ExpressRoute](expressroute-prerequisites.md) a pak [vytvořte a upravte okruh ExpressRoute](expressroute-howto-circuit-arm.md).

* Pokud už máte okruh ExpressRoute, ale nemáte nakonfigurovaného partnerského vztahu Microsoftu, nakonfigurujte partnerský vztah Microsoftu pomocí článku [Vytvoření a úprava partnerského vztahu pro okruh ExpressRoute](expressroute-howto-routing-arm.md#msft) .

Po nakonfigurování okruhu a partnerského vztahu Microsoftu ho můžete snadno zobrazit pomocí stránky **Přehled** v Azure Portal.

![přerušení](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. konfigurace filtrů tras

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. V podstatě je seznam povolených všech hodnot komunity protokolu BGP. 

![filtr tras](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

V tomto příkladu je nasazení jenom v oblasti *Azure západní USA 2* . Přidá se pravidlo filtru tras, které umožní jenom reklamu Západní USA 2 regionální předpony Azure, která má komunitu BGP hodnotu *12076:51026*. Místní předpony, které chcete zakázat, určíte tak, že vyberete **Spravovat pravidlo**.

V rámci filtru tras je také nutné zvolit okruhy ExpressRoute, pro které je použit filtr směrování. Okruhy ExpressRoute můžete vybrat tak, že vyberete **Přidat okruh**. Na předchozím obrázku je filtr tras přidružený k ukázkovému okruhu ExpressRoute.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2,1 konfigurace filtru tras

Nakonfigurujte filtr tras. Postup najdete v tématu [Konfigurace filtrů směrování pro partnerský vztah Microsoftu](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2,2 ověření tras protokolu BGP

Po úspěšném vytvoření partnerského vztahu Microsoftu přes okruh ExpressRoute a přidružení filtru tras k okruhu můžete ověřit trasy protokolu BGP přijaté z směrovači msee na zařízeních PE, která jsou v partnerském vztahu s směrovači msee. Příkaz ověřování se liší v závislosti na operačním systému vašich zařízení PE.

#### <a name="cisco-examples"></a>Příklady Cisco

V tomto příkladu se používá příkaz Cisco IOS-XE. V tomto příkladu se k izolaci přenosů partnerských vztahů používá instance virtuálního směrování a předávání (VRF).

```
show ip bgp vpnv4 vrf 10 summary
```

Následující částečný výstup ukazuje, že 68 předpony byly přijaty z souseda \* . 243.229.34 s číslem ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Pokud chcete zobrazit seznam předpon přijatých ze souseda, použijte následující příklad:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Pokud chcete potvrdit, že jste obdrželi správnou sadu předpon, můžete křížově ověřit. Následující příkaz Azure PowerShell výstupu obsahuje seznam předpon inzerovaných prostřednictvím partnerského vztahu Microsoftu pro každou službu a pro každou oblast Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. konfigurace služby VPN Gateway a tunelů IPsec

V této části se vytvoří tunelové propojení VPN IPsec mezi službou Azure VPN Gateway a místním zařízením VPN. V příkladech se používají zařízení VPN sítě Cisco Cloud Service router (CSR1000).

Následující diagram znázorňuje tunelová propojení VPN IPsec vytvořená mezi místními zařízeními VPN 1 a dvojicí instance služby Azure VPN Gateway. V diagramu nejsou znázorněné dva tunelové propojení VPN IPsec navázaná mezi místními zařízeními VPN 2 a dvojicí instancí služby Azure VPN Gateway. Podrobnosti o konfiguraci nejsou uvedené. Ale další tunely VPN zvyšují vysokou dostupnost.

  ![Tunely VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Přes dvojici tunelu protokolu IPsec se naváže relace eBGP k výměně tras privátních sítí. Následující diagram ukazuje relaci eBGP vytvořenou v páru tunelů IPsec:

  ![eBGP relace přes pár tunelů](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Následující diagram znázorňuje abstraktní přehled ukázkové sítě:

  ![Příklad sítě](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Příklady šablon Azure Resource Manager

V příkladech se Brána VPN a ukončení tunelu IPsec konfigurují pomocí šablony Azure Resource Manager. Pokud s použitím šablon Správce prostředků nepoužíváte, nebo chcete-li pochopit základy Správce prostředků šablony, přečtěte si téma [porozumění struktuře a syntaxi šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Šablona v této části vytvoří prostředí Azure bezserverová (VNet). Pokud ale máte existující virtuální síť, můžete na ni odkazovat v šabloně. Pokud nejste obeznámeni s konfigurací site-to-site služby VPN Gateway IPsec/IKE, přečtěte si téma [vytvoření připojení typu Site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>K vytvoření této konfigurace nemusíte používat šablony Azure Resource Manager. Tuto konfiguraci můžete vytvořit pomocí Azure Portal nebo PowerShellu.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3,1 deklarace proměnných

V tomto příkladu deklarace proměnných odpovídají ukázkové síti. Při deklaraci proměnných upravte tuto část tak, aby odrážela vaše prostředí.

* Proměnná **localAddressPrefix** je pole místních IP adres pro ukončení tunelů IPsec.
* **GatewaySku** určuje propustnost sítě VPN. Další informace o gatewaySku a vpnType najdete v tématu [nastavení konfigurace VPN Gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Ceny najdete v tématu [VPN Gateway ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Nastavte **vpnType** na **RouteBased**.

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

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3,2 vytvoření virtuální sítě (VNet)

Pokud přidružíte existující virtuální síť k tunelovým propojením VPN, můžete tento krok přeskočit.

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

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3,3 přiřazení veřejných IP adres instancím služby VPN Gateway
 
Přiřaďte veřejnou IP adresu pro každou instanci brány VPN Gateway.

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

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3,4 Zadejte ukončení tunelu místního připojení VPN (místní síťová brána).

Místní zařízení VPN se označují jako **Brána místní sítě**. Následující fragment kódu JSON také určuje podrobnosti vzdáleného partnerského uzlu protokolu BGP:

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

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3,5 Vytvoření brány VPN

Tato část šablony konfiguruje bránu VPN s požadovaným nastavením pro konfiguraci typu aktivní-aktivní. Pamatujte na následující požadavky:

* Vytvořte bránu VPN s VpnType **"RouteBased"** . Toto nastavení je povinné, pokud chcete povolit směrování protokolu BGP mezi bránou VPN a místní sítí VPN.
* Aby bylo možné vytvořit tunely VPN mezi dvěma instancemi brány VPN a místním zařízením v režimu aktivní-aktivní, je parametr **"aktivní"** v šabloně správce prostředků nastaven na **hodnotu true** . Další informace o bránách VPN s vysokou dostupností najdete v tématu [připojení k bráně VPN s vysokou dostupností](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Pokud chcete nakonfigurovat eBGP relace mezi tunely sítě VPN, musíte na obou stranách zadat dvě různé čísla ASN. Je vhodnější zadat čísla privátních ASN. Další informace najdete v tématu [Přehled služby BGP a bran Azure VPN](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3,6 Vytvoření tunelů IPsec

Poslední akce skriptu vytvoří tunely IPsec mezi službou Azure VPN Gateway a místním zařízením VPN.

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

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. konfigurace místního zařízení VPN

Brána Azure VPN Gateway je kompatibilní s mnoha zařízeními VPN od různých dodavatelů. Informace o konfiguraci a zařízeních, která byla ověřena pro práci s bránou VPN, najdete v tématu [informace o zařízeních VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Při konfiguraci zařízení VPN potřebujete následující položky:

* Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáte při vytváření připojení VPN typu Site-to-site. V příkladech se používá základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
* Veřejná IP adresa vaší brány VPN. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu brány VPN pomocí webu Azure Portal, přejděte na Brány virtuální sítě a klikněte na název brány.

Obvykle jsou eBGP partneři přímo připojeni (často prostřednictvím připojení WAN). Pokud ale konfigurujete eBGP přes tunelová propojení VPN přes protokol IPsec prostřednictvím partnerského vztahu Microsoftu ExpressRoute, existuje více domén směrování mezi partnerskými uzly eBGP. Pomocí příkazu **ebgp-multihop** vytvořte vztah ebgp souseda mezi dvěma nepřímými připojenými partnery. Celé číslo, které následuje za příkazem ebgp-multihop, určuje hodnotu TTL v paketech protokolu BGP. Příkaz **maximální cesty eibgp 2** umožňuje vyrovnávání zatížení přenosu mezi dvěma cestami protokolu BGP.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Příklad Cisco CSR1000

Následující příklad ukazuje konfiguraci pro Cisco CSR1000 na virtuálním počítači s technologií Hyper-V jako místní zařízení VPN:

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

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Konfigurace filtrování a bran firewall zařízení VPN (volitelné)

Nakonfigurujte bránu firewall a filtrování podle svých požadavků.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. testování a ověření tunelu IPsec

Stav tunelových propojení IPsec lze ověřit pomocí příkazů PowerShellu v bráně Azure VPN:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Příklad výstupu:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Pokud chcete zjistit stav tunelových propojení na instancích služby Azure VPN Gateway nezávisle, použijte následující příklad:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
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

Můžete také kontrolovat stav tunelu na místním zařízení VPN.

Příklad Cisco CSR1000:

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

Protokol řádku na virtuálním tunelovém rozhraní (VTI) se nemění, dokud se nedokončí fáze 2 protokolu IKE. Následující příkaz ověří přidružení zabezpečení:

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

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Ověření komplexního připojení mezi místní sítí a virtuální sítí Azure

Pokud jsou tunely IPsec v pořádku a jsou správně nastavené statické trasy, měli byste být schopni testovat IP adresu vzdáleného partnerského uzlu protokolu BGP:

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

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Ověření relací protokolu BGP přes IPsec

V bráně Azure VPN Gateway ověřte stav partnerského uzlu BGP:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Příklad výstupu:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Pokud chcete ověřit seznam předpon sítě přijatých prostřednictvím eBGP z koncentrátoru sítě VPN v místním prostředí, můžete filtrovat podle atributu "Origin":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

V příkladu výstupu je číslo ASN 65010 číslem autonomního systému protokolu BGP v místní síti VPN.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Seznam inzerovaných tras zobrazíte takto:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
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

Příklad pro místní Cisco CSR1000:

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

Seznam sítí inzerovaných z místních Cisco CSR1000 a Azure VPN Gateway je možné uvést pomocí tohoto příkazu:

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

## <a name="next-steps"></a>Další kroky

* [Konfigurace Network Performance Monitoru pro ExpressRoute](how-to-npm.md)

* [Přidání připojení typu Site-to-site k virtuální síti s existujícím připojením služby VPN Gateway](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
