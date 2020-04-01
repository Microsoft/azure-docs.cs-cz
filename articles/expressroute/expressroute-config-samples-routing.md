---
title: 'Azure ExpressRoute: Ukázky konfigurace směrovače'
description: Tato stránka obsahuje konfigurační ukázky směrovačů Cisco a Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 5304aefaf3ad70bb552b4b0d1b26fcce9867c9c0
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397736"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ukázky konfigurace směrovače pro nastavení a správu směrování
Tato stránka poskytuje ukázky konfigurace rozhraní a směrování pro směrovače řady Cisco IOS-XE a Juniper MX při práci s ExpressRoute. Jsou určeny pouze jako vzorky, které jsou orientační, a nesmějí být používány tak, jak jsou. Můžete spolupracovat s dodavatelem a přijít s vhodnými konfiguracemi pro vaši síť. 

> [!IMPORTANT]
> Vzorky na této stránce jsou určeny pouze pro orientaci. Musíte pracovat s prodejním / technickým týmem vašeho dodavatele a vaším síťovým týmem, abyste přišli s vhodnými konfiguracemi, které splní vaše potřeby. Společnost Microsoft nebude podporovat problémy související s konfiguracemi uvedenými na této stránce. Problémy s podporou vám poskytne dodavatel zařízení.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Nastavení MTU a TCP MSS na rozhraních směrovačů
* MTU pro rozhraní ExpressRoute je 1500, což je typické výchozí MTU pro ethernetové rozhraní na směrovači. Pokud váš směrovač nemá ve výchozím nastavení jinou mtu, není nutné zadat hodnotu v rozhraní směrovače.
* Na rozdíl od brány Azure VPN není nutné zadat protokol TCP MSS pro okruh ExpressRoute.

Ukázky konfigurace směrovače níže platí pro všechny partnerské partnerské partnerské společnosti. Další podrobnosti o směrování naleznete v [partnerských partnerech ExpressRoute](expressroute-circuit-peerings.md) a [požadavcích na směrování Služby ExpressRoute](expressroute-routing.md) a


## <a name="cisco-ios-xe-based-routers"></a>Routery založené na cisco IOS-XE
Ukázky v této části platí pro všechny směrovače se systémem řady IOS-XE OS.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurace rozhraní a podrozhraní
V každém směrovači, který se připojujete k microsoftu, budete na partnerské propojení vyžadovat dílčí rozhraní pro každý partnerský vztah. Dílčí rozhraní lze identifikovat pomocí ID sítě VLAN nebo skládané dvojice ID sítě VLAN a IP adresy.

**Definice rozhraní Dot1Q**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní s jedním ID sítě VLAN. ID sítě VLAN je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definice rozhraní QinQ**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní se dvěma ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud se používá, zůstane stejné ve všech partnerských položkách. Vnitřní ID sítě VLAN (c-tag) je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2. Nastavení eBGP relací
Pro každý partnerský vztah je nutné nastavit relaci protokolu BGP se společností Microsoft. Následující ukázka umožňuje nastavit relaci protokolu BGP se společností Microsoft. Pokud byla adresa IPv4 použitá pro dílčí rozhraní a.b.c.d, bude adresa IP souseda Protokolu BGP (Microsoft) a.b.c.d+1. Poslední oktet adresy IPv4 souseda Protokolu BGP bude vždy sudé číslo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Nastavení předpon, které mají být inzerovány v průběhu relace Protokolu BGP
Směrovač můžete nakonfigurovat tak, aby inzeroval vybrané předpony společnosti Microsoft. Můžete tak učinit pomocí níže uvedeného vzorku.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4. Mapy tras
Mapy tras a seznamy předponek můžete použít k filtrování předpon šířených do sítě. K provedení úkolu můžete použít následující ukázku. Ujistěte se, že máte příslušnou instalaci předpon.

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

### <a name="5-configuring-bfd"></a>5. Konfigurace BFD

BfD nakonfigurujete na dvou místech. Jeden na úrovni rozhraní a druhý na úrovni Protokolu BGP. Níže uvedený příklad je pro rozhraní QinQ. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Juniper MX série směrovačů
Ukázky v této části platí pro všechny směrovače řady Juniper MX.

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1. Konfigurace rozhraní a podrozhraní

**Definice rozhraní Dot1Q**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní s jedním ID sítě VLAN. ID sítě VLAN je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

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

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní se dvěma ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud se používá, zůstane stejné ve všech partnerských položkách. Vnitřní ID sítě VLAN (c-tag) je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

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

### <a name="2-setting-up-ebgp-sessions"></a>2. Nastavení eBGP relací
Pro každý partnerský vztah je nutné nastavit relaci protokolu BGP se společností Microsoft. Následující ukázka umožňuje nastavit relaci protokolu BGP se společností Microsoft. Pokud byla adresa IPv4 použitá pro dílčí rozhraní a.b.c.d, bude adresa IP souseda Protokolu BGP (Microsoft) a.b.c.d+1. Poslední oktet adresy IPv4 souseda Protokolu BGP bude vždy sudé číslo.

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>3. Nastavení předpon, které mají být inzerovány v průběhu relace Protokolu BGP
Směrovač můžete nakonfigurovat tak, aby inzeroval vybrané předpony společnosti Microsoft. Můžete tak učinit pomocí níže uvedeného vzorku.

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


### <a name="4-route-policies"></a>4. Zásady trasy
Mapy tras a seznamy předponek můžete použít k filtrování předpon šířených do sítě. K provedení úkolu můžete použít následující ukázku. Ujistěte se, že máte příslušnou instalaci předpon.

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

### <a name="4-configuring-bfd"></a>4. Konfigurace BFD
BfD nakonfigurujete pouze v části protokolu BGP.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }

## <a name="next-steps"></a>Další kroky
Další podrobnosti najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).



