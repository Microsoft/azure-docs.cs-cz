---
title: Ukázková konfigurace pro připojení zařízení Cisco ASA k bráně Azure VPN
description: Tento článek obsahuje ukázkovou konfiguraci pro připojení zařízení Cisco ASA k bráně Azure VPN.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/19/2018
ms.author: yushwang
ms.openlocfilehash: 96e5c26ea7b5f1baa33fd8830491ee3aa1e60221
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75778078"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>Ukázková konfigurace: Zařízení Cisco ASA (IKEv2/no BGP)
Tento článek obsahuje ukázkové konfigurace pro připojení zařízení Cisco Adaptive Security Appliance (ASA) k bráně Azure VPN. Příklad platí pro zařízení Cisco ASA se systémem IKEv2 bez protokolu BGP (Border Gateway Protocol). 

## <a name="device-at-a-glance"></a>Zařízení na první pohled

|                        |                                   |
| ---                    | ---                               |
| Dodavatel zařízení          | Cisco                             |
| Model zařízení           | ASA                               |
| Cílová verze         | 8.4 a novější                     |
| Testovaný model           | ASA 5505                          |
| Testovaná verze         | 9.2                               |
| Verze IKE            | IKEv2                             |
| Protokol BGP                    | Ne                                |
| Typ brány Azure VPN | Brána VPN založená na směrování           |
|                        |                                   |

> [!NOTE]
> Ukázková konfigurace připojí zařízení Cisco ASA k bráně VPN **založené na trase** Azure. Připojení používá vlastní zásadu IPsec/IKE s možností **UsePolicyBasedTrafficSelectors,** jak je popsáno v [tomto článku](vpn-gateway-connect-multiple-policybased-rm-ps.md).
>
> Ukázka vyžaduje, aby zařízení ASA používala zásady **IKEv2** s konfiguracemi založenými na seznamu přístupu, nikoli na základě VTI. Informace o tom, že zásady IKEv2 jsou podporovány na místních zařízeních VPN, ověřte, zda jsou zásady IKEv2 podporovány.


## <a name="vpn-device-requirements"></a>Požadavky na zařízení VPN
Brány Azure VPN používají standardní sady protokolů IPsec/IKE k vytvoření tunelových propojení VPN site-to-site (S2S). Podrobné parametry protokolu IPsec/IKE a výchozí kryptografické algoritmy pro brány Azure VPN najdete [v tématu O zařízeních VPN](vpn-gateway-about-vpn-devices.md).

> [!NOTE]
> Volitelně můžete zadat přesnou kombinaci kryptografických algoritmů a silných stránek klíčů pro konkrétní připojení, jak je popsáno v [části O kryptografických požadavcích](vpn-gateway-about-compliance-crypto.md). Pokud zadáte přesnou kombinaci algoritmů a silných stránek klíčů, nezapomeňte použít odpovídající specifikace na vašich zařízeních VPN.

## <a name="single-vpn-tunnel"></a>Jeden tunel VPN
Tato konfigurace se skládá z jednoho tunelu S2S VPN mezi bránou Azure VPN a místním zařízením VPN. Volitelně můžete nakonfigurovat protokol BGP v rámci tunelového propojení VPN.

![Tunel OVÁVPN single S2S](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Podrobné pokyny k sestavení konfigurací Azure najdete v tématu [nastavení tunelového propojení Single VPN](vpn-gateway-3rdparty-device-config-overview.md#singletunnel).

### <a name="virtual-network-and-vpn-gateway-information"></a>Informace o virtuální síti a bráně VPN
V této části jsou uvedeny parametry pro ukázku.

| **Parametr**                | **Hodnotu**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP adresa brány Azure VPN         | Azure_Gateway_Public_IP      |
| Předpony místní adresy | 10.51.0.0/16<br>10.52.0.0/16 |
| Adresa IP místního zařízení VPN    | OnPrem_Device_Public_IP     |
| * Virtuální síť BGP ASN                | 65010                        |
| * Ip partnerské strany Azure BGP           | 10.12.255.30                 |
| * Místní protokol BGP ASN         | 65050                        |
| * Místní IP adresa partnera Protokolu BGP     | 10.52.255.254                |
|                              |                              |

\*Volitelný parametr pouze pro protokol BGP.

### <a name="ipsecike-policy-and-parameters"></a>Zásady a parametry Protokolu IPsec/IKE
V následující tabulce jsou uvedeny algoritmy a parametry Protokolu IPsec/IKE, které se používají v ukázce. Informace o algoritmech, které jsou podporovány pro modely zařízení VPN a verze firmwaru, naleznete ve specifikacích zařízení VPN.

| **IPsec/IKEv2**  | **Hodnotu**                            |
| ---              | ---                                  |
| Šifrování protokolem IKEv2 | AES256                               |
| Integrita protokolu IKEv2  | SHA384                               |
| Skupina DH         | DHGroup24                            |
| * Šifrování IPsec | AES256                               |
| * Integrita IPsec  | SHA1                                 |
| Skupina PFS        | PFS24                                |
| Doba života přidružení zabezpečení v rychlém režimu   | 7 200 sekund                         |
| Selektor provozu | UsePolicyBasedTrafficSelectors $True |
| Předsdílený klíč   | Předsdílený klíč                         |
|                  |                                      |

\*Na některých zařízeních musí být integrita IPsec nulovou hodnotou, pokud je šifrovací algoritmus IPsec AES-GCM.

### <a name="asa-device-support"></a>Podpora zařízení ASA

* Podpora pro IKEv2 vyžaduje ASA verze 8.4 a novější.

* Podpora pro DH Group a PFS Group nad rámec skupiny 5 vyžaduje ASA verze 9.x.

* Podpora šifrování IPsec s integritou AES-GCM a IPsec s SHA-256, SHA-384 nebo SHA-512 vyžaduje ASA verze 9.x. Tento požadavek na podporu se vztahuje na novější zařízení ASA. V době publikování asa modely 5505, 5510, 5520, 5540, 5550 a 5580 nepodporují tyto algoritmy. Informace o algoritmech, které jsou podporovány pro modely zařízení VPN a verze firmwaru, naleznete ve specifikacích zařízení VPN.


### <a name="sample-device-configuration"></a>Ukázková konfigurace zařízení
Skript obsahuje ukázku, která je založena na konfiguraci a parametry, které jsou popsány v předchozích částech. Konfigurace tunelového propojení S2S VPN se skládá z následujících částí:

1. Rozhraní a trasy
2. Přístupové seznamy
3. Zásady a parametry Protokolu IKE (fáze 1 nebo hlavní režim)
4. Zásady a parametry protokolu IPsec (fáze 2 nebo rychlý režim)
5. Další parametry, jako je upínání TCP MSS

> [!IMPORTANT]
> Před použitím ukázkového skriptu proveďte následující kroky. Nahraďte zástupné hodnoty ve skriptu nastavením zařízení pro vaši konfiguraci.

* Zadejte konfiguraci rozhraní pro vnitřní i vnější rozhraní.
* Identifikujte trasy pro vnitřní/soukromé a vnější/veřejné sítě.
* Ujistěte se, že všechna jména a čísla zásad jsou v zařízení jedinečná.
* Ujistěte se, že kryptografické algoritmy jsou podporovány ve vašem zařízení.
* Nahraďte následující **zástupné hodnoty** skutečnými hodnotami pro konfiguraci:
  - Název vnějšího rozhraní: **mimo**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE: **Pre_Shared_Key**
  - Názvy virtuálních sítí a místních síťových bran: **VNetName** a **LNGName**
  - **Předpony** virtuální sítě a místní síťové adresy
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

## <a name="simple-debugging-commands"></a>Jednoduché ladicí příkazy

Pro účely ladění použijte následující příkazy ASA:

* Zobrazit přidružení zabezpečení protokolu IPsec nebo IKE (SA):
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* Zadejte režim ladění:
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    Příkazy `debug` mohou generovat významný výstup na konzoli.

* Zobrazit aktuální konfigurace na zařízení:
    ```
    show run
    ```
    Pomocí `show` dílčích příkazů uveďte konkrétní části konfigurace zařízení, například:
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>Další kroky
Pokud chcete nakonfigurovat aktivní aktivní připojení mezi místními a virtuálními sítěmi, přečtěte si informace o konfiguraci bran VPN s [aktivní mincovní](vpn-gateway-activeactive-rm-powershell.md)sítí .
