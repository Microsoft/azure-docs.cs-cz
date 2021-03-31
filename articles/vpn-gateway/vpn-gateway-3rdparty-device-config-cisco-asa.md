---
title: Ukázková konfigurace pro připojení zařízení Cisco ASA ke službě Azure VPN Gateway
description: Tento článek poskytuje ukázkovou konfiguraci pro připojení zařízení se systémem Cisco ASA ke službě Azure VPN Gateway.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426166"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Ukázková konfigurace: zařízení Cisco ASA (IKEv2/bez BGP)
Tento článek poskytuje ukázkové konfigurace pro připojení zařízení se zařízením s bránou Cisco Adaptive Security (ASA) do bran Azure VPN Gateway. Tento příklad se vztahuje na zařízení Cisco ASA, na kterých běží IKEv2 bez Border Gateway Protocol (BGP). 

## <a name="device-at-a-glance"></a>První pohled na zařízení

* Dodavatel zařízení: **Cisco**
* Model zařízení: **ASA**           
* Cílová verze: **8,4 a novější**
* Testovaný model: **ASA 5505**
* Testovaná verze: **9,2**             
* Verze protokolu IKE: **IKEv2**                  
* BGP: **ne**      
* Typ brány VPN Azure: **Brána sítě VPN založená na trasách**

> [!NOTE]
> Ukázková konfigurace připojí zařízení Cisco ASA k bráně sítě VPN **založené na trasách** Azure. Připojení používá vlastní zásadu IPsec/IKE s možností **UsePolicyBasedTrafficSelectors** , jak je popsáno v [tomto článku](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Ukázka vyžaduje, aby zařízení ASA používala zásadu **IKEv2** s konfiguracemi založenými na přístupových seznamech, nikoli na VTI. Projděte si specifikace dodavatele zařízení VPN a ověřte, jestli je na vašich místních zařízeních VPN podporovaná zásada IKEv2.


## <a name="vpn-device-requirements"></a>Požadavky na zařízení VPN
Brány VPN Azure používají standardní sady protokolu IPsec/IKE k vytvoření tunelů VPN typu Site-to-Site (S2S). Podrobné informace o parametrech protokolu IPsec/IKE a výchozích šifrovacích algoritmech pro Azure VPN Gateway najdete v tématu [informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Volitelně můžete zadat přesnou kombinaci kryptografických algoritmů a pevnosti klíčů pro konkrétní připojení, jak je popsáno v [tématu o kryptografických požadavcích](vpn-gateway-about-compliance-crypto.md). Pokud zadáte přesnou kombinaci algoritmů a pevnosti klíčů, nezapomeňte použít odpovídající specifikace v zařízeních VPN.

## <a name="single-vpn-tunnel"></a>Jedno tunelové připojení VPN
Tato konfigurace se skládá z jednoho tunelu VPN S2S mezi službou Azure VPN Gateway a místním zařízením VPN. Protokol BGP můžete volitelně nakonfigurovat napříč tunelem sítě VPN.

![Tunelové propojení VPN typu Single S2S](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Podrobné pokyny k sestavování konfigurací Azure najdete v tématu [Nastavení tunelu pro jedno připojení k síti VPN](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informace o virtuální síti a bráně VPN
Tato část obsahuje seznam parametrů pro ukázku.

| **Parametr**                | **Hodnota**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP adresa brány VPN Azure         | Azure_Gateway_Public_IP      |
| Předpony místních adres | 10.51.0.0/16<br>10.52.0.0/16 |
| Místní IP adresa zařízení VPN    | OnPrem_Device_Public_IP     |
| * ASN protokolu BGP virtuální sítě                | 65010                        |
| * IP adresa partnerského uzlu protokolu BGP Azure           | 10.12.255.30                 |
| * Místní ASN BGP         | 65050                        |
| * Místní IP adresa partnerského uzlu protokolu BGP     | 10.52.255.254                |
|                              |                              |

\* Volitelný parametr jenom pro BGP.

### <a name="ipsecike-policy-and-parameters"></a>Zásady a parametry protokolu IPsec/IKE
V následující tabulce jsou uvedeny algoritmy a parametry protokolu IPsec/IKE používané v ukázce. Podívejte se na specifikace zařízení VPN a ověřte algoritmy, které jsou podporované pro modely zařízení VPN a verze firmwaru.

| **IPsec/IKEv2**  | **Hodnota**                            |
| ---              | ---                                  |
| Šifrování protokolem IKEv2 | AES256                               |
| Integrita protokolu IKEv2  | SHA384                               |
| Skupina DH         | DHGroup24                            |
| * Šifrování IPsec | AES256                               |
| * Integrita protokolu IPsec  | SHA1                                 |
| Skupina PFS        | PFS24                                |
| Doba života přidružení zabezpečení v rychlém režimu   | 7 200 sekund                         |
| Selektor provozu | UsePolicyBasedTrafficSelectors $True |
| Předsdílený klíč   | PreSharedKey                         |
|                  |                                      |

\* U některých zařízení musí být integrita protokolu IPsec nastavena na hodnotu null, pokud je šifrovací algoritmus IPsec AES-GCM.

### <a name="asa-device-support"></a>Podpora zařízení ASA

* Podpora IKEv2 vyžaduje ASA verze 8,4 a novější.

* Podpora skupiny DH a skupiny PFS mimo skupinu 5 vyžaduje ASA verze 9. x.

* Podpora šifrování protokolu IPsec pomocí algoritmu AES-GCM a integrity protokolu IPsec pomocí algoritmu SHA-256, SHA-384 nebo SHA-512 vyžaduje ASA verze 9. x. Tento požadavek na podporu platí pro novější zařízení ASA. V době publikace nepodporují tyto algoritmy modely ASA 5505, 5510, 5520, 5540, 5550 a 5580. Podívejte se na specifikace zařízení VPN a ověřte algoritmy, které jsou podporované pro modely zařízení VPN a verze firmwaru.


### <a name="sample-device-configuration"></a>Ukázka konfigurace zařízení
Skript poskytuje ukázku, která je založena na konfiguraci a parametrech, které jsou popsány v předchozích částech. Konfigurace tunelu S2S VPN se skládá z následujících částí:

1. Rozhraní a trasy
2. Seznamy přístupu
3. Zásady a parametry protokolu IKE (fáze 1 nebo hlavní režim)
4. Zásady a parametry protokolu IPsec (fáze 2 nebo rychlý režim)
5. Další parametry, jako je například přichycení protokolu TCP MSS

> [!IMPORTANT]
> Před použitím ukázkového skriptu proveďte následující kroky. Nahraďte zástupné hodnoty ve skriptu nastavením zařízení pro vaši konfiguraci.

* Určete konfiguraci rozhraní pro rozhraní uvnitř i vně.
* Identifikujte trasy pro interní/privátní a vnější/veřejné sítě.
* Ujistěte se, že jsou všechny názvy a čísla zásad na vašem zařízení jedinečné.
* Ujistěte se, že jsou v zařízení podporované kryptografické algoritmy.
* Nahraďte následující **zástupné hodnoty** skutečnými hodnotami pro vaši konfiguraci:
  - Název vnějšího rozhraní: **mimo**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Názvy bran virtuální sítě a místní sítě: **VNetName** a **LNGName**
  - **Předpony** adresy virtuální sítě a místní sítě
  - Správné **síťové masky**

#### <a name="sample-script"></a>Ukázkový skript

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>Jednoduché příkazy ladění

Pro účely ladění použijte následující příkazy ASA:

* Zobrazit přidružení zabezpečení (SA) protokolu IPsec nebo IKE:
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Zadejte režim ladění:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug`Příkazy mohou generovat významný výstup v konzole nástroje.

* Zobrazit aktuální konfigurace na zařízení:
    ```
    show run
    ```
    Pomocí `show` dílčích příkazů můžete vypsat konkrétní části Konfigurace zařízení, například:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Další kroky
Pokud chcete nakonfigurovat připojení typu aktivní-aktivní pro více míst a VNET-to-VNet, přečtěte si téma [Konfigurace bran VPN typu aktivní-aktivní](vpn-gateway-activeactive-rm-powershell.md).
