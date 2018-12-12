---
title: Ukázky konfigurace směrovače - Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje ukázky konfigurace směrovače pro směrovačů Cisco a Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2d7fb060896de8df266489451a11ba343760c747
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079955"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ukázky konfigurace směrovače nastavit a spravovat směrování
Tato stránka obsahuje rozhraní a směrování ukázky konfigurace pro Cisco IOS-XE a Juniper MX řady směrovače, při práci se službou ExpressRoute. Ty by měla být ukázky pouze pokyny a nesmí se používat je. Můžete pracovat s vaším dodavatelem přijít s odpovídající konfigurací pro vaši síť. 

> [!IMPORTANT]
> Ukázky na této stránce jsou určené výhradně pro doprovodné materiály. Musí fungovat s vaším dodavatelem prodejní a technickým týmem a síťovým týmem přijít s odpovídající konfigurací podle svých potřeb. Problémy související s konfigurací, které jsou uvedené na této stránce nebude podpory společnosti Microsoft. Pro problémy podpory musí kontaktujte dodavatele zařízení.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Nastavení jednotek MTU a TCP MSS na rozhraní směrovače
* MTU pro rozhraní ExpressRoute je 1500, což je typické výchozí MTU pro rozhraní sítě Ethernet na směrovač. Pokud ve výchozím nastavení má směrovač jiné MTU, není nutné zadat hodnotu na rozhraní směrovače.
* Na rozdíl od služby Azure VPN Gateway MSS protokolu TCP pro okruh ExpressRoute není nutné zadat.

Ukázky konfigurace směrovače níže se vztahují na všechny partnerské vztahy. Kontrola [partnerské vztahy ExpressRoute](expressroute-circuit-peerings.md) a [požadavky na směrování služby ExpressRoute](expressroute-routing.md) podrobné informace o směrování.


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE na základě směrovače
Příklady v této části platí pro všechny směrovače řady operačního systému IOS XE.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurace rozhraní a dílčí rozhraní
Budete potřebovat sub rozhraní na partnerský vztah v každé směrovače, ke kterému se připojíte k Microsoftu. Dílčí rozhraní lze identifikovat s ID sítě VLAN nebo pár skládaný ID sítě VLAN a IP adresu.

**Definice rozhraní Dot1Q**

Tato ukázka obsahuje definici dílčí rozhraní pro dílčí rozhraní s jednotné ID sítě VLAN. ID sítě VLAN je pro každý partnerský vztah jedinečná. Poslední oktet si IPv4 adresu, bude vždy liché číslo.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definice rozhraní QinQ**

Tato ukázka obsahuje definici dílčí rozhraní pro dílčí rozhraní se dva identifikátory ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud používá zůstává stejná napříč všechny partnerské vztahy. Vnitřní ID sítě VLAN (c-tag) je pro každý partnerský vztah jedinečná. Poslední oktet si IPv4 adresu, bude vždy liché číslo.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Nastavení relace eBGP
Musíte nastavit relace protokolu BGP s Microsoftem pro každý partnerský vztah. Následující ukázka umožňuje nastavení relace protokolu BGP s Microsoftem. Pokud adresu IPv4, které jste použili pro vaše rozhraní sub a.b.c.d, IP adresa (Microsoft) okolní sítě BGP budou a.b.c.d+1. Poslední oktet IPv4 adresy okolní sítě BGP budou vždy sudé číslo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Nastavení předpon inzerovaných během relace protokolu BGP
Můžete nakonfigurovat směrovače inzerovat vyberte předpony společnosti Microsoft. Můžete tak učinit pomocí ukázky níže.

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
Můžete použít trasy mapy a předponu v seznamech předpony filtru rozšíří do vaší sítě. Následující ukázky můžete použít ke splnění úkolu. Ujistěte se, že máte příslušné předpony seznamy nastavení.

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


## <a name="juniper-mx-series-routers"></a>Řada směrovače Juniper MX
Příklady v této části platí pro všechny řady směrovače Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurace rozhraní a dílčí rozhraní

**Definice rozhraní Dot1Q**

Tato ukázka obsahuje definici dílčí rozhraní pro dílčí rozhraní s jednotné ID sítě VLAN. ID sítě VLAN je pro každý partnerský vztah jedinečná. Poslední oktet si IPv4 adresu, bude vždy liché číslo.

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

Tato ukázka obsahuje definici dílčí rozhraní pro dílčí rozhraní se dva identifikátory ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud používá zůstává stejná napříč všechny partnerské vztahy. Vnitřní ID sítě VLAN (c-tag) je pro každý partnerský vztah jedinečná. Poslední oktet si IPv4 adresu, bude vždy liché číslo.

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
Musíte nastavit relace protokolu BGP s Microsoftem pro každý partnerský vztah. Následující ukázka umožňuje nastavení relace protokolu BGP s Microsoftem. Pokud adresu IPv4, které jste použili pro vaše rozhraní sub a.b.c.d, IP adresa (Microsoft) okolní sítě BGP budou a.b.c.d+1. Poslední oktet IPv4 adresy okolní sítě BGP budou vždy sudé číslo.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Nastavení předpon inzerovaných během relace protokolu BGP
Můžete nakonfigurovat směrovače inzerovat vyberte předpony společnosti Microsoft. Můžete tak učinit pomocí ukázky níže.

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
Můžete použít trasy mapy a předponu v seznamech předpony filtru rozšíří do vaší sítě. Následující ukázky můžete použít ke splnění úkolu. Ujistěte se, že máte příslušné předpony seznamy nastavení.

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

