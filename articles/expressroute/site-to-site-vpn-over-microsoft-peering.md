---
title: Konfigurace VPN typu site-to-site přes partnerský vztah Microsoftu pro Azure ExpressRoute | Microsoft Docs
description: Nakonfigurujte připojení protokolu IPsec/IKE do Azure přes partnerského vztahu okruhu ExpressRoute Microsoft pomocí brána sítě VPN typu site-to-site.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2017
ms.author: cherylmc
ms.openlocfilehash: 86e101ee78cfa709c6957c7658f103ce787a6351
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37110850"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurace VPN typu site-to-site přes partnerského vztahu ExpressRoute Microsoftu

Tento článek vám pomůže nakonfigurovat zabezpečené šifrované připojení mezi místní sítí a virtuálních sítí Azure (virtuální sítě) přes privátní připojení ExpressRoute. Konfigurace zabezpečené tunelové propojení prostřednictvím ExpressRoute umožňuje výměna dat s důvěrnost, ochranu před zneužitím, pravosti a integrity.

## <a name="architecture"></a>Architektura

Můžete využít k vytvoření tunelu site-to-site VPN protokolu IPsec/IKE mezi vybrané místní sítě a sítě Azure Vnet partnerský vztah Microsoftu.

  ![připojení – přehled](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)

>[!NOTE]
>Při nastavování site-to-site VPN přes Microsoft partnerský vztah, vám budou účtovat brána sítě VPN a sítě VPN odchozí. Další informace najdete v tématu [ceny služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

Pro vysokou dostupnost a redundance můžete nakonfigurovat více tunelových propojení přes dvě dvojice MSEE PE okruhu ExpressRoute a povolení vyrovnávání zátěže mezi tunely.

  ![Možnosti s vysokou dostupností](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunelová propojení VPN přes partnerského vztahu Microsoftu ukončovat lze pomocí brány sítě VPN, nebo pomocí příslušné síti virtuální zařízení chyb zabezpečení sítě k dispozici prostřednictvím Azure Marketplace. Vám může provést výměnu směrování staticky nebo dynamicky přes šifrované tunely bez vystavení výměna tras základní partnerského vztahu Microsoftu. V příkladech v tomto článku se používá protokol BGP (jiný než relaci protokolu BGP použít k vytvoření partnerského vztahu Microsoftu) dynamicky Exchange předpony přes šifrované tunely.

>[!IMPORTANT]
>Pro místní stranu obvykle partnerského vztahu Microsoftu je ukončený v hraniční síti a soukromého partnerského vztahu je ukončený v zóně základní sítě. Oddělí dvě zóny pomocí brány firewall. Pokud konfigurujete partnerský vztah Microsoftu výhradně pro povolení zabezpečené tunelování přes ExpressRoute, nezapomeňte filtrovat prostřednictvím jenom veřejné IP adresy zájmu, které jsou získávání inzerované prostřednictvím partnerského vztahu Microsoftu.
>
>

## <a name="workflow"></a>Pracovní postup

1. Nakonfigurujte partnerský vztah Microsoftu pro váš okruh ExpressRoute.
2. Inzerovat vybrané Azure místní předpony veřejných do místní sítě prostřednictvím partnerského vztahu Microsoftu.
3. Konfigurovat bránu VPN a vytvořit tunelových propojení IPsec
4. Nakonfigurujte místní zařízení VPN.
5. Vytvoření připojení site-to-site protokolu IPsec/IKE.
6. (Volitelné) Konfigurace brány firewall nebo filtrování na místní zařízení VPN.
7. Testování a ověření protokolu IPsec komunikaci přes okruh ExpressRoute.

## <a name="peering"></a>1. Nakonfigurujte partnerský vztah Microsoftu

Konfigurace připojení site-to-site VPN přes ExpressRoute, musí využívat partnerského vztahu ExpressRoute Microsoftu.

* Nakonfigurujte novou okruh ExpressRoute, začínat [požadavky služby ExpressRoute](expressroute-prerequisites.md) článek a potom [vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-arm.md).

* Pokud už máte okruh ExpressRoute, ale nemají partnerského vztahu Microsoftu nakonfigurované, nakonfigurujte partnerský vztah Microsoftu pomocí [vytvořit a upravit partnerský vztah pro okruh ExpressRoute](expressroute-howto-routing-arm.md#msft) článku.

Jakmile jste nakonfigurovali okruhu a partnerský vztah Microsoftu, můžete snadno zobrazit pomocí **přehled** na portálu Azure.

![okruh](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Nastavit filtry tras

Filtr trasy umožňuje identifikoval služby, které chcete využívat prostřednictvím partnerského vztahu Microsoftu okruhu ExpressRoute. Je v podstatě povolených ze všech hodnot komunity protokolu BGP. 

![Filtr trasy](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

V tomto příkladu je nasazení jenom v *Azure západní USA 2* oblast. Povolit pouze oznámení o inzerovaném programu Azure západní USA 2 regionální předpon, které má hodnotu komunity protokolu BGP se přidá pravidlo filtru trasy *12076:51026*. Zadejte místní předpon, které chcete povolit výběrem **Správa pravidel**.

V rámci filtru tras musíte taky zvolit okruhy ExpressRoute, pro které platí filtru tras. Okruhy ExpressRoute můžete výběrem **přidat okruh**. Na předchozím obrázku je přidružena k příklad okruh ExpressRoute filtru tras.

### <a name="configfilter"></a>2.1 konfigurace filtru tras

Konfigurovat filtr trasy. Pokyny najdete v tématu [filtry konfigurace směrování pro partnerský vztah Microsoftu](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 ověřte trasy protokolu BGP

Jakmile úspěšně jste vytvořili Microsoft partnerský vztah v okruhu ExpressRoute a přidružené filtr trasy okruh, můžete ověřit trasy protokolu BGP přijata z Msee PE zařízení, která jsou partnerského vztahu směrovači Msee. Příkaz ověření se liší podle operačního systému zařízení PE.

#### <a name="cisco-examples"></a>Příklady Cisco

Tento příklad používá příkaz Cisco IOS-XE. V příkladu virtuální směrování a předávání instance (VRF) se používá k izolaci provozu partnerského vztahu.

```
show ip bgp vpnv4 vrf 10 summary
```

Následující částečné výstup ukazuje, že 68 předpony bylo přijato z sousedním *.243.229.34 s 12076 ASN (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Chcete-li zobrazit seznam předpony přijaté z sousedním, použijte následující příklad:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Potvrďte, že se vám správnou sadu předpon, můžete mezi ověřit. Tento výstup příkazového prostředí Azure PowerShell seznam předpon inzerovaných prostřednictvím Microsoft partnerský vztah pro jednotlivé služby a pro všechny oblasti Azure:

```powershell
Get-AzureRmBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Konfigurace brány sítě VPN a tunelových propojení IPsec

V této části jsou vytvořeny tunelových propojení IPsec pro síť VPN mezi Azure VPN gateway a místní zařízení VPN. Příklady používají zařízení VPN směrovač Cisco cloudových služeb (CSR1000).

Následující diagram znázorňuje IPsec VPN tunelová propojení mezi místní zařízení VPN 1 a pár instance brány Azure VPN. Dva tunely IPsec pro síť VPN navázat mezi místní zařízení VPN 2 a pár instance brány Azure VPN není znázorněné na obrázku a podrobnosti o konfiguraci nejsou uvedené. Však s další tunelových propojení VPN zlepšuje vysokou dostupnost.

  ![Tunelová propojení sítě VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Přes pár tunelového propojení protokolu IPsec existuje relace eBGP vytvořeno pro výměnu směrování privátní sítě. Následující diagram znázorňuje relace eBGP navázat přes pár tunelu IPsec:

  ![relace eBGP přes tunel pár](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Následující diagram znázorňuje abstraktní přehled sítě příklad:

  ![Příklad sítě](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>O příklady šablony Azure Resource Manager

V příkladech služby VPN gateway a ukončení tunelového propojení protokolu IPsec se konfigurují pomocí šablony Azure Resource Manager. Pokud jsou nové pomocí šablony Resource Manageru, nebo pochopit základy šablony Resource Manageru, najdete v části [pochopit strukturu a syntaxe šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Šablony v této části vytvoří greenfield prostředí Azure (VNet). Ale pokud máte existující virtuální síť, můžete odkazovat ho v šabloně. Pokud nejste obeznámeni s konfigurací pro protokolu IPsec/IKE site-to-site VPN gateway, přečtěte si téma [vytvořit připojení site-to-site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Není nutné používat šablony Azure Resource Manager za účelem vytvoření této konfigurace. Můžete vytvořit této konfigurace pomocí portálu Azure nebo prostředí PowerShell.
>
>

### <a name="variables3"></a>3.1 deklarujte proměnné

V tomto příkladu deklarace proměnných odpovídají příklad sítě. Pokud deklarace proměnné, upravte tuto část, aby se zohlednilo vaše prostředí.

* Proměnná **localAddressPrefix** je pole IP adres místní ukončit tunelových propojení IPsec.
* **GatewaySku** určuje propustnost sítě VPN. Další informace o gatewaySku a vpnType najdete v tématu [nastavení konfigurace brány VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Ceny, najdete v části [ceny služby VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway).
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

### <a name="vnet"></a>3.2 vytvořit virtuální síť (VNet)

Pokud přiřazujete stávající virtuální síť s tunelových propojení VPN, můžete tento krok přeskočit.

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

### <a name="termination"></a>3.4 zadejte místní ukončení tunelového propojení sítě VPN (brány místní sítě)

Místní zařízení VPN se označují jako **brány místní sítě**. Následující fragment kódu json také určuje vzdáleného podrobnosti partnera BGP:

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

### <a name="creategw"></a>3.5 vytvořit bránu sítě VPN

Tato část šablony nakonfiguruje bránu VPN s požadovaná nastavení konfigurace aktivní aktivní. Mějte na paměti následující požadavky:

* Vytvoření brány VPN s **"RouteBased"** typ sítě VPN. Toto nastavení je povinný, pokud chcete povolit směrování protokolu BGP mezi bránu VPN a sítě VPN na místě.
* K vytvoření tunelových propojení VPN mezi dvě instance brány VPN a daný místního zařízení v režimu aktivní aktivní **"activeActive"** parametr je nastaven na **true** v šabloně Resource Manager . Bližší informace o vysoce dostupné brány sítě VPN naleznete v tématu [vysoce dostupné připojení brány VPN](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Chcete-li nakonfigurovat eBGP relací mezi tunelových propojení VPN, musíte zadat dvě různá čísla ASN na obou stranách. Je vhodnější určit privátní čísla ASN. Další informace najdete v tématu [přehled protokolu BGP a sítě Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="ipsectunnel"></a>3.6 navázat tunelových propojení IPsec

Poslední akce skriptu vytvoří tunelových propojení IPsec mezi Azure VPN gateway a místní zařízení VPN.

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

## <a name="device"></a>4. Nakonfigurujte místní zařízení VPN

Službě Azure VPN gateway je kompatibilní s mnoha zařízení VPN od různých výrobců. Informace o konfiguraci a zařízení, která byla ověřena pro práci s brány VPN najdete v tématu [informace o zařízeních VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Při konfiguraci zařízení VPN, potřebujete následující položky:

* Sdílený klíč. Toto je stejný sdílený klíč, který zadáte při vytváření připojení site-to-site VPN. Příklady používají základní sdílený klíč. Doporučujeme, abyste pro použití vygenerovali složitější klíč.
* Veřejná IP adresa vaší brány sítě VPN. Veřejnou IP adresu můžete zobrazit pomocí webu Azure Portal, PowerShellu nebo rozhraní příkazového řádku. Najít veřejnou IP adresu brány VPN pomocí portálu Azure, přejděte do brány virtuální sítě a potom klikněte na název brány.

Obvykle eBGP partnerské uzly jsou připojeny přímo (často přes připojení WAN). Při konfiguraci eBGP přes tunely IPsec pro síť VPN prostřednictvím partnerského vztahu Microsoftu ExpressRoute, existují však několik domén směrování mezi rovnocennými počítači eBGP. Použití **ebgp pokus** příkaz k navázání vztahu sousedním eBGP mezi nimi není – přímo připojené partnerské uzly. Celé číslo, které následuje ebgp pokus příkaz určuje hodnota TTL v paketech protokolu BGP. Příkaz **maximální cesty eibgp 2** povoluje provoz mezi dvě cesty protokolu BGP Vyrovnávání zatížení.

### <a name="cisco1"></a>Příklad CSR1000 Cisco

Následující příklad ukazuje konfigurace Cisco CSR1000 ve virtuálním počítači technologie Hyper-V jako místního zařízení VPN:

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

Nakonfigurujte bránu firewall a filtrování podle svých požadavků.

## <a name="testipsec"></a>6. Testování a ověření tunelu IPsec

Stav tunelových propojení IPsec na bráně Azure VPN lze ověřit pomocí příkazů prostředí Powershell:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Příklad výstupu:

```powershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Pokud chcete zkontrolovat stav tunely na instancích brány Azure VPN nezávisle, použijte následující příklad:

```powershell
Get-AzureRmVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Příklad výstupu:

```powershell
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

Také můžete zkontrolovat stav tunelového propojení na vaše místní zařízení VPN.

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

Protokol řádku na virtuální rozhraní tunelového propojení (VTI) nezmění na "nahoru", dokud se nedokončí IKE fáze 2. Následující příkaz ověřuje přidružení zabezpečení:

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

### <a name="verifye2e"></a>Ověření připojení klient server mezi uvnitř sítě místní a virtuální síť Azure

Pokud jsou tunelových propojení IPsec a jsou správně nastaveny statické trasy, byste měli testovat na IP adresu vzdáleného partnerského uzlu protokolu BGP:

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

Na bráně Azure VPN ověřte stav partnerského uzlu protokolu BGP:

```powershell
Get-AzureRmVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Příklad výstupu:

```powershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Pokud chcete ověřit seznam přijatých prostřednictvím eBGP z VPN koncentrátor místní předpony sítě, můžete filtrovat podle atributu "Původ":

```powershell
Get-AzureRmVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

V příklad výstupu ASN 65010 je číslo autonomního systému pro protokol BGP v VPN na místě.

```powershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Chcete-li zobrazit seznam Inzerovat trasy:

```powershell
Get-AzureRmVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Příklad výstupu:

```powershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Příklad pro CSR1000 Cisco místně.

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

Seznam sítí, inzerované CSR1000 Cisco místní ke službě Azure VPN gateway může být uvedený pomocí následujícího příkazu:

```powershell
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

* [Přidat připojení site-to-site k virtuální síti pomocí existujícího připojení brány sítě VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)