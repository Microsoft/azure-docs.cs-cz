---
title: 'Azure ExpressRoute: Konfigurace sítě VPN S2S přes partnerský vztah Microsoftu'
description: Nakonfigurujte připojení IPsec/IKE k Azure přes okruh partnerského vztahu Microsoftu ExpressRoute pomocí brány VPN typu site-to-site.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f3044a2701b0f1cd0e5f9ab3ab60c1d60cfb8f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436813"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurace sítě VPN typu site-to-site přes partnerský vztah společnosti ExpressRoute Microsoft

Tento článek vám pomůže nakonfigurovat zabezpečené šifrované připojení mezi místní sítí a virtuálními sítěmi Azure (Virtuální sítě) přes privátní připojení ExpressRoute. Partnerský vztah Microsoftu můžete použít k vytvoření tunelového propojení IPsec/IKE VPN mezi vybranými místními sítěmi a virtuálními sítěmi Azure. Konfigurace zabezpečeného tunelu přes ExpressRoute umožňuje výměnu dat s důvěrností, anti-replay, pravost a integritu.

>[!NOTE]
>Když nastavíte síť VPN mezi lokalitami prostřednictvím partnerského vztahu Microsoftu, bude se vám účtovat brána VPN a odchozí přenos VPN. Další informace naleznete v tématu [ceny služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektura


  ![přehled připojení](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Pro vysokou dostupnost a redundanci můžete nakonfigurovat více tunelových propojení přes dva dvojice MSEE-PE okruhu ExpressRoute a povolit vyrovnávání zatížení mezi tunely.

  ![možnosti vysoké dostupnosti](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunely VPN přes partnerský vztah Microsoftu lze ukončit buď pomocí brány VPN, nebo pomocí příslušného síťového virtuálního zařízení (NVA), které je dostupné prostřednictvím Azure Marketplace. Trasy můžete vyměňovat staticky nebo dynamicky přes šifrovaná tunelová propojení bez vystavení výměny tras podkladovému partnerovi microsoftu. V příkladech v tomto článku bgp (odlišné od relace Protokolu BGP slouží k vytvoření partnerského vztahu Společnosti Microsoft) se používá k dynamické výměně předpon přes šifrované tunely.

>[!IMPORTANT]
>Pro místní straně obvykle partnerský vztah Microsoftu je ukončena na DMZ a privátní partnerský vztah je ukončena v zóně základní sítě. Obě zóny by byly odděleny pomocí bran firewall. Pokud konfigurujete partnerský vztah Microsoftu výhradně pro povolení zabezpečeného tunelového propojení přes ExpressRoute, nezapomeňte filtrovat pouze veřejné IP adresy, které jsou inzerovány prostřednictvím partnerského vztahu Microsoftu.
>
>

## <a name="workflow"></a><a name="workflow"></a>Pracovního postupu

1. Nakonfigurujte partnerský vztah Microsoftu pro okruh ExpressRoute.
2. Inzerujte vybrané místní veřejné předpony Azure do místní sítě prostřednictvím partnerského vztahu Microsoftu.
3. Konfigurace brány VPN a vytvoření tunelových propojení Protokolu IPsec
4. Nakonfigurujte místní zařízení VPN.
5. Vytvořte připojení IPsec/IKE mezi lokalitami.
6. (Nepovinné) Konfigurace bran firewall/filtrování v místním zařízení VPN.
7. Otestujte a ověřte komunikaci IPsec přes okruh ExpressRoute.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Konfigurace partnerského vztahu Microsoftu

Chcete-li nakonfigurovat připojení VPN mezi lokalitami přes ExpressRoute, je nutné využít partnerský vztah společnosti ExpressRoute microsoft.

* Chcete-li nakonfigurovat nový okruh ExpressRoute, začněte s [článkem předpokladů ExpressRoute](expressroute-prerequisites.md) a potom [vytvořte a upravte okruh ExpressRoute](expressroute-howto-circuit-arm.md).

* Pokud již máte okruh ExpressRoute, ale nemáte nakonfigurovaný partnerský vztah Microsoftu, nakonfigurujte partnerský vztah Microsoftu pomocí [vytvoření a úpravy partnerského vztahu pro článek okruhu ExpressRoute.](expressroute-howto-routing-arm.md#msft)

Jakmile nakonfigurujete okruh a partnerský vztah Microsoftu, můžete ho snadno zobrazit pomocí stránky **Přehled** na webu Azure Portal.

![Okruh](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Konfigurace filtrů tras

Filtr tras umožňuje identifikovat služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu s vaším okruhem ExpressRoute. Jedná se v podstatě o seznam všech hodnot komunity BGP. 

![filtr trasy](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

V tomto příkladu je nasazení jenom v oblasti *Azure – západ USA 2.* Pravidlo filtru trasy je přidáno tak, aby umožňovalo pouze inzerování regionálních předpon Azure West US 2, které má hodnotu komunity BGP *12076:51026*. Regionální předpony, které chcete povolit, určíte výběrem **možnosti Spravovat pravidlo**.

V rámci filtru trasy je také nutné zvolit okruhy ExpressRoute, pro které se použije filtr trasy. Okruhy ExpressRoute můžete zvolit výběrem možnosti **Přidat okruh**. Na předchozím obrázku je filtr trasy přidružen k příkladu okruhu ExpressRoute.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 Konfigurace filtru trasy

Konfigurace filtru trasy. Postup naleznete v [tématu Konfigurace filtrů postupu pro partnerský vztah microsoftu](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 Ověření tras Protokolu BGP

Po úspěšném vytvoření partnerského vztahu Microsoftu přes okruh ExpressRoute a přidružení filtru trasy k okruhu můžete ověřit trasy Protokolu BGP přijaté od msees na zařízeních PE, které jsou partnerský vztah s MSEEs. Příkaz ověření se liší v závislosti na operačním systému vašich zařízení PE.

#### <a name="cisco-examples"></a>Příklady společnosti Cisco

Tento příklad používá příkaz Cisco IOS-XE. V příkladu virtuální směrování a předávání (VRF) instance se používá k izoluje přenosy partnerského vztahu.

```
show ip bgp vpnv4 vrf 10 summary
```

Následující částečný výstup ukazuje, že 68 předpony byly přijaty ze sousedního \*.243.229.34 s ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Chcete-li zobrazit seznam předpon přijatých od souseda, použijte následující příklad:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Chcete-li potvrdit, že přijímáte správnou sadu předpon, můžete je křížově ověřit. Následující výstup příkazu Azure PowerShell uvádí předpony inzerované prostřednictvím partnerského vztahu Microsoftu pro každou ze služeb a pro každou oblast Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Konfigurace brány VPN a tunelů IPsec

V této části se tunely IPsec VPN vytvářejí mezi bránou Azure VPN a místním zařízením VPN. Příklady používají zařízení VPN Cisco Cloud Service Router (CSR1000).

Následující diagram znázorňuje tunely VPN Protokolu IPsec vytvořené mezi místním zařízením VPN 1 a dvojicí instancí brány Azure VPN. Dva tunely VPN Protokolu IPsec vytvořené mezi místním zařízením VPN 2 a dvojicí instancí brány Azure VPN nejsou v diagramu znázorněny a podrobnosti konfigurace nejsou uvedeny. Však s další tunely VPN zlepšuje vysokou dostupnost.

  ![VPN tunely](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Přes dvojici tunelů IPsec je vytvořena relace eBGP pro výměnu tras privátní sítě. Následující diagram znázorňuje relaci eBGP vytvořenou přes dvojici tunelů IPsec:

  ![eBGP relace přes dvojici tunelů](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Následující diagram znázorňuje abstraktní přehled ukázkové sítě:

  ![ukázková síť](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Příklady šablon Azure Resource Manageru

V příkladech se brána VPN a ukončení tunelového propojení IPsec nakonfigurují pomocí šablony Azure Resource Manager. Pokud s používáním šablon Správce prostředků tesete nebo rozumíte základům šablon Správce prostředků, přečtěte [si informace o struktuře a syntaxi šablon Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md). Šablona v této části vytvoří prostředí Azure na zelené louce (VNet). Pokud však máte existující virtuální síť, můžete na něj odkazovat v šabloně. Pokud nejste obeznámeni s konfiguracemi brány VPN IPsec/IKE mezi lokalitami, přečtěte si informace [o vytvoření připojení mezi lokalitami](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>K vytvoření této konfigurace není nutné používat šablony Azure Resource Manageru. Tuto konfiguraci můžete vytvořit pomocí portálu Azure nebo PowerShellu.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 Uvést proměnné

V tomto příkladu deklarace proměnných odpovídají příkladsítě. Při deklarování proměnných upravte tuto část tak, aby odrážela vaše prostředí.

* Proměnná **localAddressPrefix** je pole místních adres IP pro ukončení tunelových propojení IPsec.
* **GatewaySku** určuje propustnost VPN. Další informace o gatewaySku a vpnType naleznete v [tématu nastavení konfigurace brány VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Ceny najdete v tématu [ceny služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
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

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 Vytvoření virtuální sítě (Virtuální síť)

Pokud přizpůsobujete existující virtuální síť k tunelovým propojením VPN, můžete tento krok přeskočit.

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

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 Přiřazení veřejných IP adres instancí brány VPN
 
Přiřaďte veřejnou IP adresu pro každou instanci brány VPN.

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

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 Určení místního ukončení tunelového propojení VPN (brána místní sítě)

Místní zařízení VPN se označují jako **brána místní sítě**. Následující fragment json také určuje vzdálené podrobnosti partnera Protokolu BGP:

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

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 Vytvoření brány VPN

Tato část šablony konfiguruje bránu VPN s požadovaným nastavením pro konfiguraci aktivní a aktivní. Mějte na paměti následující požadavky:

* Vytvořte bránu VPN s **typem VpnType "RouteBased".** Toto nastavení je povinné, pokud chcete povolit směrování protokolu BGP mezi bránou VPN a místní vpn.
* Chcete-li vytvořit tunelové propojení VPN mezi dvěma instancemi brány VPN a daným místním zařízením v aktivním aktivním režimu, je parametr **activeActive** nastaven na **hodnotu true** v šabloně Správce prostředků. Další informace o vysoce dostupných privátových branách najdete v tématu [Vysoce dostupné připojení brány VPN](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Chcete-li konfigurovat relace eBGP mezi tunely VPN, musíte zadat dvě různá síť ASN na obou stranách. Je vhodnější zadat soukromá čísla ASN. Další informace najdete v [tématu Přehled brány Protokolu BGP a Azure VPN](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 Vytvoření tunelů IPsec

Konečná akce skriptu vytvoří tunely IPsec mezi bránou Azure VPN a místním zařízením VPN.

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

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Konfigurace místního zařízení VPN

Brána Azure VPN je kompatibilní s mnoha zařízeními VPN od různých dodavatelů. Informace o konfiguraci a zařízení, která byla ověřena pro práci s bránou VPN, naleznete [v tématu O zařízeních VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Při konfiguraci zařízení VPN potřebujete následující položky:

* Sdílený klíč. Jedná se o stejný sdílený klíč, který zadáte při vytváření připojení VPN mezi lokalitami. Příklady používají základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
* Veřejná IP adresa vaší brány VPN. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Pokud chcete zjistit veřejnou IP adresu brány VPN pomocí webu Azure Portal, přejděte na Brány virtuální sítě a klikněte na název brány.

Obvykle jsou eBGP partnery přímo připojeny (často přes připojení WAN). Pokud však konfigurujete eBGP přes tunely VPN Protokolu IPsec prostřednictvím partnerského vztahu Microsoft expressroute, existuje mezi partnerskými sítěmi eBGP více směrovacích domén. Pomocí příkazu **ebgp-multihop** vytvořte vztah souseda eBGP mezi dvěma nepřímo připojenými partnery. Celé číslo, které následuje za příkazem ebgp-multihop, určuje hodnotu TTL v paketech Protokolu BGP. Příkaz **maximální cesty eibgp 2** umožňuje vyrovnávání zatížení provozu mezi dvěma cestami Protokolu BGP.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Příklad cisco CSR1000

Následující příklad ukazuje konfiguraci cisco CSR1000 ve virtuálním počítači Hyper-V jako místní zařízení VPN:

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

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Konfigurace filtrování zařízení VPN a firewallů (volitelné)

Nakonfigurujte bránu firewall a filtrování podle vašich požadavků.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. Testování a ověření tunelu IPsec

Stav tunelových propojení IPsec lze ověřit na bráně Azure VPN pomocí příkazů Powershellu:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Příklad výstupu:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Chcete-li nezávisle zkontrolovat stav tunelových propojení v instancích brány Azure VPN, použijte následující příklad:

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

Můžete také zkontrolovat stav tunelového propojení na místním zařízení VPN.

Příklad cisco CSR1000:

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

Protokol linky na rozhraní virtuálního tunelového propojení (VTI) se nezmění na "nahoru", dokud nebude dokončena fáze 2 protokolu IKE. Následující příkaz ověřuje přidružení zabezpečení:

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

Pokud jsou tunely Protokolu IPsec vrežimu a statické trasy jsou správně nastaveny, měli byste být schopni příkazem ping ping ovat adresu IP vzdáleného partnera Protokolu BGP:

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

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Ověření relací protokolu BGP přes protokol IPsec

V bráně Azure VPN ověřte stav partnera Protokolu BGP:

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

Chcete-li ověřit seznam síťových předpon přijatých prostřednictvím eBGP z místního koncentrátoru VPN, můžete filtrovat podle atributu "Původ":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

V ukázkovém výstupu je ČÍSLO AUTONOMNÍHO SYSTÉMU BGP v místním prostředí VPN.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Zobrazení seznamu inzerovaných tras:

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

Příklad místního řešení CISCO CSR1000:

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

Seznam sítí inzerovaných z místního cisco CSR1000 do brány Azure VPN lze uvést pomocí následujícího příkazu:

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

* [Konfigurace Network Performance Monitor pro ExpressRoute](how-to-npm.md)

* [Přidání připojení k webu k virtuální síti s existujícím připojením brány VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
