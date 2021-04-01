---
title: 'Azure ExpressRoute: Ukázky konfigurace směrovače'
description: Použijte toto rozhraní a ukázky konfigurace směrování pro směrovače Cisco IOS – XE a Juniper MX řady jako příklady pro práci s Azure ExpressRoute.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89397402"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Ukázky konfigurace směrovače pro nastavení a správu směrování
Tato stránka poskytuje při práci se službou Azure ExpressRoute ukázky konfigurace rozhraní a směrování pro směrovače Cisco IOS – XE a Juniper MX řady.

> [!IMPORTANT]
> Ukázky na této stránce jsou čistě pro doprovodné materiály. Aby bylo možné najít odpovídající konfigurace pro splnění vašich požadavků, je nutné pracovat s prodejním a technickým týmem vašeho dodavatele a týmem vaší sítě. Microsoft nebude podporovat problémy související s konfiguracemi uvedenými na této stránce. Pro problémy s podporou se obraťte na dodavatele zařízení.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Nastavení MTU a TCP MSS na rozhraních směrovačů
Maximální přenosová jednotka (MTU) pro rozhraní ExpressRoute je 1500, což je typická výchozí jednotka MTU pro rozhraní sítě Ethernet ve směrovači. Pokud ve výchozím nastavení má směrovač jinou jednotku MTU, není nutné zadávat hodnotu v rozhraní směrovače.

Na rozdíl od brány Azure VPN není nutné zadávat maximální velikost segmentu (MSS) TCP pro okruh ExpressRoute.

Ukázky konfigurace směrovače v tomto článku se týkají všech partnerských vztahů. Další podrobnosti o směrování najdete v požadavcích na [ExpressRoute partnerské vztahy](expressroute-circuit-peerings.md) a [požadavky na směrování ExpressRoute](expressroute-routing.md) .


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS – směrovače založené na XE
Ukázky v této části se vztahují na jakýkoli směrovač se systémem IOS – XE OS.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurace rozhraní a podrozhraní
V každém směrovači, který se připojíte k Microsoftu, budete potřebovat jedno podrozhraní pro každý partnerský vztah. Podrozhraní se dá identifikovat pomocí ID sítě VLAN nebo skládané dvojice ID sítě VLAN a IP adresy.

**Definice rozhraní Dot1Q**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní s jedním ID sítě VLAN. ID sítě VLAN je jedinečné pro každý partnerský vztah. Poslední oktet vaší adresy IPv4 bude vždycky liché číslo.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**Definice rozhraní QinQ**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní se dvěma ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud je použito, zůstává stejné napříč všemi partnerskými vztahy. ID vnitřní sítě VLAN (značka c) je jedinečné pro každý partnerský vztah. Poslední oktet vaší adresy IPv4 bude vždycky liché číslo.

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>Nastavení relací eBGP
Pro každý partnerský vztah musíte nastavit relaci protokolu BGP s Microsoftem. Pomocí následující ukázky nastavte relaci protokolu BGP. Pokud adresa IPv4, kterou jste použili pro vaše podrozhraní, byla. b. c. d, pak IP adresa souseda BGP (Microsoft) bude a. b. c. d + 1. Poslední oktet adresy IPv4 souseda protokolu BGP bude vždycky sudé číslo.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Nastavte předpony, které se budou inzerovat přes relaci protokolu BGP.
Nakonfigurujte směrovač tak, aby inzeroval vybrané předpony do Microsoftu pomocí následující ukázky.

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>Mapy tras
Pomocí map tras a seznamů předpon můžete filtrovat předpony šířené do vaší sítě. Podívejte se na následující ukázku a ujistěte se, že máte nastavené odpovídající seznamy předpon.

```console
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
```

### <a name="configure-bfd"></a>Konfigurace BFD

BFD nakonfigurujete na dvou místech: jednu na úrovni rozhraní a jinou na úrovni BGP. Tady je příklad pro rozhraní QinQ. 

```console
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
```


## <a name="juniper-mx-series-routers"></a>Směrovače společnosti Juniper MX Series
Ukázky v této části se vztahují na směrovač Juniper MX Series.

### <a name="configure-interfaces-and-subinterfaces"></a>Konfigurace rozhraní a podrozhraní

**Definice rozhraní Dot1Q**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní s jedním ID sítě VLAN. ID sítě VLAN je jedinečné pro každý partnerský vztah. Poslední oktet vaší adresy IPv4 bude vždycky liché číslo.

```console
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
```


**Definice rozhraní QinQ**

Tato ukázka poskytuje definici dílčího rozhraní pro dílčí rozhraní se dvěma ID sítě VLAN. Vnější ID sítě VLAN (s-tag), pokud je použito, zůstává stejné napříč všemi partnerskými vztahy. ID vnitřní sítě VLAN (značka c) je jedinečné pro každý partnerský vztah. Poslední oktet vaší adresy IPv4 bude vždycky liché číslo.

```console
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
```

### <a name="set-up-ebgp-sessions"></a>Nastavení relací eBGP
Pro každý partnerský vztah musíte nastavit relaci protokolu BGP s Microsoftem. Pomocí následující ukázky nastavte relaci protokolu BGP. Pokud adresa IPv4, kterou jste použili pro vaše podrozhraní, byla. b. c. d, pak IP adresa souseda BGP (Microsoft) bude a. b. c. d + 1. Poslední oktet adresy IPv4 souseda protokolu BGP bude vždycky sudé číslo.

```console
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
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Nastavte předpony, které se budou inzerovat přes relaci protokolu BGP.
Nakonfigurujte směrovač tak, aby inzeroval vybrané předpony do Microsoftu pomocí následující ukázky.

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
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
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>Zásady směrování
Pomocí map tras a seznamů předpon můžete filtrovat předpony šířené do vaší sítě. Podívejte se na následující ukázku a ujistěte se, že máte nastavené odpovídající seznamy předpon.

```console
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
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>Konfigurace BFD
Nakonfigurujte BFD jenom v části protokol BGP.

```console
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
```

### <a name="configure-macsec"></a>Konfigurace MACSec
V případě konfigurace MACSec musí klíč přidružení připojení (CAK) a název klíče přidružení připojení (CKN) odpovídat nakonfigurovaným hodnotám prostřednictvím příkazů PowerShellu.

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>Další kroky
Další podrobnosti najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).



