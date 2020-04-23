---
title: 'Azure ExpressRoute: Ukázky konfigurace směrovače'
description: Tato stránka obsahuje konfigurační ukázky směrovačů Cisco a Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024808"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ukázky konfigurace směrovače pro nastavení a správu směrování
Tato stránka obsahuje ukázky konfigurace rozhraní a směrování pro směrovače řady Cisco IOS-XE a Juniper MX při práci s Azure ExpressRoute.

> [!IMPORTANT]
> Ukázky na této stránce jsou čistě orientační. Musíte spolupracovat s prodejním/technickým týmem dodavatele a síťovým týmem, abyste našli vhodné konfigurace, které vyhovují vašim potřebám. Společnost Microsoft nebude podporovat problémy související s konfiguracemi uvedenými na této stránce. Problémy s podporou vám poskytne dodavatel zařízení.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Nastavení MTU a TCP MSS na rozhraních směrovačů
Maximální přenosová jednotka (MTU) pro rozhraní ExpressRoute je 1500, což je typické výchozí MTU pro ethernetové rozhraní na směrovači. Pokud váš směrovač nemá ve výchozím nastavení jinou mtu, není nutné zadat hodnotu v rozhraní směrovače.

Na rozdíl od brány Azure VPN není nutné specifikovat maximální velikost segmentu TCP (MSS) pro okruh ExpressRoute.

Ukázky konfigurace směrovače v tomto článku platí pro všechny partnerské partnerské partnerské společnosti. Další podrobnosti o směrování naleznete v [partnerských partnerech ExpressRoute](expressroute-circuit-peerings.md) a [požadavcích na směrování Služby ExpressRoute](expressroute-routing.md) a


## <a name="cisco-ios-xe-based-routers"></a>Routery založené na cisco IOS-XE
Ukázky v této části platí pro všechny směrovače se systémem řady IOS-XE OS.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurace rozhraní a dílčích rozhraní
V každém směrovači, který se připojujete k Microsoftu, budete potřebovat jedno dílčí rozhraní pro každý partnerský vztah. Dílčí rozhraní lze identifikovat pomocí ID sítě VLAN nebo skládané dvojice ID sítě VLAN a IP adresy.

**Definice rozhraní Dot1Q**

Tato ukázka poskytuje definici dílčího rozhraní pro podrozhraní s jedním ID sítě VLAN. ID sítě VLAN je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Definice rozhraní QinQ**

Tato ukázka obsahuje definici dílčího rozhraní pro podrozhraní se dvěma ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud je použito, zůstane stejné ve všech partnerských položkách. Vnitřní ID sítě VLAN (c-tag) je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Nastavení relací eBGP
Pro každý partnerský vztah je nutné nastavit relaci protokolu BGP se společností Microsoft. Nastavte relaci Protokolu BGP pomocí následující ukázky. Pokud adresa IPv4 použitá pro podrozhraní byla a.b.c.d, bude adresa IP souseda Protokolu BGP (Microsoft) a.b.c.d+1. Poslední oktet adresy IPv4 souseda Protokolu BGP bude vždy sudé číslo.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Nastavení předpon, které mají být inzerovány v relaci protokolu BGP
Nakonfigurujte směrovač tak, aby inzeroval vybrané předpony společnosti Microsoft pomocí následující ukázky.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Mapy tras
Pomocí map tras a seznamů předponami můžete filtrovat předpony šířené do sítě. Podívejte se na následující ukázku a ujistěte se, že máte příslušné seznamy předponek nastavit.

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

### <a name="configure-bfd"></a>Konfigurace BFD

BfD nakonfigurujete na dvou místech: na jedné na úrovni rozhraní a druhé na úrovni Protokolu BGP. Zde příklad je pro rozhraní QinQ. 

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

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurace rozhraní a dílčích rozhraní

**Definice rozhraní Dot1Q**

Tato ukázka poskytuje definici dílčího rozhraní pro podrozhraní s jedním ID sítě VLAN. ID sítě VLAN je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

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

Tato ukázka obsahuje definici dílčího rozhraní pro podrozhraní se dvěma ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud je použito, zůstane stejné ve všech partnerských položkách. Vnitřní ID sítě VLAN (c-tag) je jedinečné pro jeden partnerský vztah. Poslední oktet adresy IPv4 bude vždy liché číslo.

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

### <a name="set-up-ebgp-sessions"></a>Nastavení relací eBGP
Pro každý partnerský vztah je nutné nastavit relaci protokolu BGP se společností Microsoft. Nastavte relaci Protokolu BGP pomocí následující ukázky. Pokud adresa IPv4 použitá pro podrozhraní byla a.b.c.d, bude adresa IP souseda Protokolu BGP (Microsoft) a.b.c.d+1. Poslední oktet adresy IPv4 souseda Protokolu BGP bude vždy sudé číslo.

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

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Nastavení předpon, které mají být inzerovány v relaci protokolu BGP
Nakonfigurujte směrovač tak, aby inzeroval vybrané předpony společnosti Microsoft pomocí následující ukázky.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
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


### <a name="route-policies"></a>Zásady postupu
Mapy tras a seznamy předponek můžete použít k filtrování předpon šířených do sítě. Podívejte se na následující ukázku a ujistěte se, že máte příslušné seznamy předponek nastavit.

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

### <a name="configure-bfd"></a>Konfigurace BFD
Nakonfigurujte bfd pouze v části protokolu BGP.

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



