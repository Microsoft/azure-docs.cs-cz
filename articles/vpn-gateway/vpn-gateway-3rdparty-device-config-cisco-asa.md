---
title: Ukázková konfigurace pro zařízení Cisco ASA připojovat k Azure VPN Gateway | Microsoft Docs
description: Tento článek obsahuje ukázková konfigurace pro zařízení Cisco ASA připojovat k Azure VPN Gateway.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: ''
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.openlocfilehash: fbe22b70b4fe3463ffc7b0e9a7ebd683f681117d
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2018
ms.locfileid: "27910754"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Ukázková konfigurace: Cisco ASA zařízení (BGP IKEv2/ne)
Tento článek obsahuje ukázkové konfigurace pro připojování zařízení Cisco adaptivní zabezpečení zařízení (ASA) do Azure VPN Gateway. Příklad se týká se zařízení Cisco ASA, systémem IKEv2 bez protokol BGP (Border Gateway). 

## <a name="device-at-a-glance"></a>Zařízení na první pohled

|                        |                                   |
| ---                    | ---                               |
| Výrobce zařízení          | Cisco                             |
| Model zařízení           | ASA                               |
| Cílová verze         | 8.4 a novější                     |
| Otestované modelu           | ASA 5505                          |
| Otestované verze         | 9.2                               |
| Verze IKE            | IKEv2                             |
| Protokol BGP                    | Ne                                |
| Typ brány Azure VPN | Brána sítě VPN založené na směrování           |
|                        |                                   |

> [!NOTE]
> Ukázková konfigurace Cisco ASA zařízení připojí k Azure **založené na trasách** brány VPN. Připojení používá vlastní zásady protokolu IPsec/IKE s **UsePolicyBasedTrafficSelectors** možnost, jak je popsáno v [v tomto článku](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Ukázka vyžaduje zařízení využívající ASA **IKEv2** zásadou na základě přístup k seznamu konfigurace není na základě VTI. Naleznete v dokumentaci VPN dodavatele ověřit, jestli IKEv2 zásad se podporuje na vaše místní zařízení VPN.


## <a name="vpn-device-requirements"></a>Požadavky na zařízení VPN
Azure VPN Gateway pomocí standardní sady protokolu IPsec/IKE vytvořit tunelová propojení VPN typu Site-to-Site (S2S). Podrobné parametry protokolu IPsec/IKE a výchozí kryptografické algoritmy pro Azure VPN Gateway najdete v tématu [informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Volitelně můžete zadat přesný kombinace kryptografické algoritmy a klíče síly pro konkrétní připojení, jak je popsáno v [o požadavcích na kryptografických](vpn-gateway-about-compliance-crypto.md). Pokud zadáte přesný kombinace algoritmy a klíče síly, je nutné používat odpovídající specifikace na vaše zařízení VPN.

## <a name="single-vpn-tunnel"></a>Jedno tunelové propojení sítě VPN
Tato konfigurace se skládá z jedné tunel S2S VPN mezi Azure VPN gateway a místní zařízení VPN. Volitelně můžete nakonfigurovat [protokolu BGP přes tunel VPN](#bgp).

![Jeden tunel S2S VPN](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Podrobné pokyny k vytvoření konfigurace Azure najdete v tématu [nastavení tunelové propojení VPN jeden](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Virtuální síť a informace o bráně VPN
V této části jsou uvedeny parametry pro vzorovou.

| **Parameter**                | **Hodnota**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure IP adresa brány sítě VPN         | Azure_Gateway_Public_IP      |
| Předpony adres místní | 10.51.0.0/16<br>10.52.0.0/16 |
| IP adresa zařízení místní sítě VPN    | OnPrem_Device_Public_IP     |
| * Virtuální sítě ASN protokolu BGP                | 65010                        |
| * Azure IP adresa partnera BGP           | 10.12.255.30                 |
| * Místní ASN protokolu BGP         | 65050                        |
| * IP adresa partnera BGP na místě     | 10.52.255.254                |
|                              |                              |

\*Volitelný parametr pro protokol BGP jenom.

### <a name="ipsecike-policy-and-parameters"></a>Zásady protokolu IPsec/IKE a parametry
Následující tabulka uvádí algoritmy protokolu IPsec/IKE a parametry, které se používají ve vzorku. Naleznete v dokumentaci VPN ověřit algoritmy, které jsou podporovány pro modely zařízení VPN a verzí firmwaru.

| **IPsec/IKEv2**  | **Hodnota**                            |
| ---              | ---                                  |
| Šifrování protokolem IKEv2 | AES256                               |
| Integrita protokolu IKEv2  | SHA384                               |
| Skupina DH         | DHGroup24                            |
| * Šifrování protokolu IPsec | AES256                               |
| * Integrity protokolu IPsec  | SHA1                                 |
| Skupina PFS        | PFS24                                |
| Doba života přidružení zabezpečení v rychlém režimu   | 7200 sekund                         |
| Selektor provozu | UsePolicyBasedTrafficSelectors $True |
| Předsdílený klíč   | PreSharedKey                         |
|                  |                                      |

\*Na některých zařízeních Integrity protokolu IPsec musí mít hodnotu null při algoritmus šifrování pomocí protokolu IPsec je AES-GCM.

### <a name="asa-device-support"></a>Podpora zařízení ASA

* Podpora pro IKEv2 vyžaduje ASA verze 8.4 a novější.

* Podpora pro skupinu Diffie-Hellman a skupinu PFS nad rámec skupiny 5 vyžaduje verzi ASA 9.x.

* Podpora pro šifrování pomocí protokolu IPsec s AES-GCM a protokolu IPsec Integrity pomocí algoritmu SHA-256, SHA-384 a SHA-512, vyžaduje verzi ASA 9.x. Tento požadavek podpora platí pro novější ASA zařízení. V době publikace nepodporují ASA modely 5505, 5510, 5520, 5540, 5550 a 5580 tyto algoritmy. Naleznete v dokumentaci VPN ověřit algoritmy, které jsou podporovány pro modely zařízení VPN a verzí firmwaru.


### <a name="sample-device-configuration"></a>Ukázka konfigurace zařízení
Skript poskytuje ukázku, která je založená na konfiguraci a parametry, které jsou popsané v předchozích částech. Konfigurace tunel S2S VPN se skládá z následujících částí:

1. Rozhraní a trasy
2. Seznamy přístupu
3. Zásady IKE a parametry (fáze 1 nebo hlavního režimu)
4. Parametry (fáze 2 nebo rychlého režimu) a zásad protokolu IPsec
5. Další parametry, jako je například upínací MSS protokolu TCP

> [!IMPORTANT]
> Pomocí následujících kroků před použijte vzorový skript. Nahraďte zástupný symbol hodnoty ve skriptu pro konfiguraci nastavení zařízení.

* Zadejte v konfiguraci rozhraní pro i mimo ně rozhraní.
* Identifikujte trasy pro vaše uvnitř a privátního a mimo nebo veřejné sítě.
* Zajistěte, všechny názvy a zásad čísla jedinečná ve vašem zařízení.
* Ujistěte se, že kryptografické algoritmy jsou podporovány ve vašem zařízení.
* Nahraďte následující **zástupné hodnoty** skutečnými hodnotami pro svou konfiguraci:
  - Mimo název rozhraní: **mimo**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Virtuální síť a názvy brány místní sítě: **VNetName** a **LNGName**
  - Virtuální síť a místní síťová adresa **předpony**
  - Správné **masky sítě**

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
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
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

## <a name="simple-debugging-commands"></a>Jednoduché ladění příkazy

Použijte následující příkazy ASA pro účely ladění:

* Zobrazit protokol IPsec nebo IKE přidružení zabezpečení (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Zadejte režim ladění:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug` Příkazy může generovat významné výstup na konzolu.

* Zobrazit aktuální konfigurace v zařízení:
    ```
    show run
    ```
    Použití `show` dílčí na konkrétní části seznamu konfiguraci zařízení, například:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Další postup
Konfigurace aktivní aktivní mezi různými místy a připojení VNet-to-VNet najdete v tématu [konfiguraci brány VPN aktivní aktivní](vpn-gateway-activeactive-rm-powershell.md).
