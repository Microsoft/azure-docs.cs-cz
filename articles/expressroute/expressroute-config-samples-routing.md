---
title: Ukázky konfigurace směrovače zákazníka ExpressRoute | Microsoft Docs
description: Tato stránka obsahuje ukázky konfigurace směrovače pro směrovače Cisco a Juniper.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: 564826bc-017a-4683-a385-37c9fa814948
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 032e584dc5abf59e9e3e8d80673b402f1fbf721b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850767"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ukázky konfigurace směrovače nastavit a spravovat směrování
Tato stránka obsahuje rozhraní a směrování ukázky konfigurace pro Cisco IOS-XE a Juniper MX směrovače řady. Tyto by měla být ukázky jenom pokyny a nesmí se používat, protože je. Můžete pracovat s vaším dodavatelem spolu s odpovídající konfigurací pro vaši síť. 

> [!IMPORTANT]
> Ukázky na této stránce by měla být čistě pokyny. Musíte pracovat se od dodavatele prodeje / technické vaší síťových adaptérů a spolu s odpovídající konfigurací podle svých potřeb. Problémy související s konfigurací, které jsou uvedené na této stránce nebudou podpory společnosti Microsoft. Pro problémy podpory, bude nutné kontaktovat dodavatele zařízení.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Nastavení jednotek MTU a MSS protokolu TCP na rozhraní směrovače
* MTU pro rozhraní ExpressRoute je 1500, což je typický výchozí MTU pro rozhraní sítě Ethernet na směrovač. Pokud ve výchozím nastavení má směrovač jiné MTU, není nutné zadat hodnotu na rozhraní směrovače.
* Na rozdíl od služby Azure VPN Gateway MSS protokolu TCP pro okruh ExpressRoute nemusí být zadaný.

Následující ukázky směrovač konfigurace platí pro všechny partnerské vztahy. Zkontrolujte [partnerských vztahů ExpressRoute](expressroute-circuit-peerings.md) a [požadavky na směrování služby ExpressRoute](expressroute-routing.md) Další informace o směrování.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE na základě směrovače
Ukázky v této části se vztahují na všechny směrovač se systémem IOS XE operačních systémů.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurace rozhraní a dílčí rozhraní
Budete potřebovat dílčí rozhraní na partnerský vztah v každé směrovače se připojíte k Microsoftu. Sub – rozhraní možné identifikovat pomocí ID sítě VLAN nebo pár skládaný ID sítě VLAN a IP adresu.

**Definice rozhraní Dot1Q**

Tato ukázka obsahuje definice dílčí rozhraní pro dílčí rozhraní s jeden ID sítě VLAN. ID sítě VLAN je jedinečný pro každého partnerského vztahu. Poslední oktet vaši adresu IPv4, bude vždy lichý počet.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definice rozhraní QinQ**

Tato ukázka obsahuje definice dílčí rozhraní pro dílčí rozhraní se dva identifikátory ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud se používá zůstává stejná napříč všech partnerských vztahů. Vnitřní ID sítě VLAN (c-tag) je jedinečný pro každého partnerského vztahu. Poslední oktet vaši adresu IPv4, bude vždy lichý počet.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Nastavení relace eBGP
Musíte nastavit relace protokolu BGP se společností Microsoft pro každý partnerský vztah. Následující ukázka umožňuje nastavit relace protokolu BGP se společností Microsoft. Pokud adresu IPv4, která jste použili pro vaše rozhraní dílčí a.b.c.d, IP adresu BGP sousedním (Microsoft) bude a.b.c.d+1. Poslední oktet sousedním BGP adresy IPv4, bude vždy sudé číslo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Nastavení předpony chcete inzerovat přes relaci protokolu BGP
Můžete nakonfigurovat směrovači inzerovat vyberte předpony společnosti Microsoft. Můžete tak učinit pomocí následující ukázky.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapuje trasy
Můžete použít mapy trasy a předponu seznamu filtru předpony rozšíří do vaší sítě. Následující ukázky můžete použít k provedení úlohy. Ujistěte se, že máte příslušné předpony zobrazí instalační program.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !


## <a name="juniper-mx-series-routers"></a>Směrovače Juniper MX řady
Ukázky v této části se vztahují na veškeré směrovače Juniper MX řady.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurace rozhraní a dílčí rozhraní

**Definice rozhraní Dot1Q**

Tato ukázka obsahuje definice dílčí rozhraní pro dílčí rozhraní s jeden ID sítě VLAN. ID sítě VLAN je jedinečný pro každého partnerského vztahu. Poslední oktet vaši adresu IPv4, bude vždy lichý počet.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Definice rozhraní QinQ**

Tato ukázka obsahuje definice dílčí rozhraní pro dílčí rozhraní se dva identifikátory ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud se používá zůstává stejná napříč všech partnerských vztahů. Vnitřní ID sítě VLAN (c-tag) je jedinečný pro každého partnerského vztahu. Poslední oktet vaši adresu IPv4, bude vždy lichý počet.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="2-setting-up-ebgp-sessions"></a>2. Nastavení relace eBGP
Musíte nastavit relace protokolu BGP se společností Microsoft pro každý partnerský vztah. Následující ukázka umožňuje nastavit relace protokolu BGP se společností Microsoft. Pokud adresu IPv4, která jste použili pro vaše rozhraní dílčí a.b.c.d, IP adresu BGP sousedním (Microsoft) bude a.b.c.d+1. Poslední oktet sousedním BGP adresy IPv4, bude vždy sudé číslo.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Nastavení předpony chcete inzerovat přes relaci protokolu BGP
Můžete nakonfigurovat směrovači inzerovat vyberte předpony společnosti Microsoft. Můžete tak učinit pomocí následující ukázky.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4. Mapuje trasy
Můžete použít mapy trasy a předponu seznamu filtru předpony rozšíří do vaší sítě. Následující ukázky můžete použít k provedení úlohy. Ujistěte se, že máte příslušné předpony zobrazí instalační program.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
        prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>Další kroky
Další podrobnosti najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

