---
title: Ukázky konfigurace směrovače zákazníka ExpressRoute | Microsoft Docs
description: Tato stránka obsahuje ukázky konfigurace směrovače pro směrovače Cisco a Juniper.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''
ms.assetid: d6ea716f-d5ee-4a61-92b0-640d6e7d6974
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
ms.openlocfilehash: 83a7da2db537a3c900e90432455d59e8ac56d917
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850683"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Ukázky konfigurace směrovače nastavit a spravovat NAT
Tato stránka obsahuje ukázky konfigurace NAT pro směrovače Cisco ASA a Juniper SRX řady. Tyto by měla být ukázky jenom pokyny a nesmí se používat, protože je. Můžete pracovat s vaším dodavatelem spolu s odpovídající konfigurací pro vaši síť. 

> [!IMPORTANT]
> Ukázky na této stránce by měla být čistě pokyny. Musíte pracovat se od dodavatele prodeje / technické vaší síťových adaptérů a spolu s odpovídající konfigurací podle svých potřeb. Problémy související s konfigurací, které jsou uvedené na této stránce nebudou podpory společnosti Microsoft. Pro problémy podpory, bude nutné kontaktovat dodavatele zařízení.
> 
> 

* Následující ukázky konfigurace směrovače platí pro partnerské vztahy Azure veřejné a společnosti Microsoft. Nakonfigurujete nesmí NAT pro soukromý partnerský vztah Azure. Zkontrolujte [partnerských vztahů ExpressRoute](expressroute-circuit-peerings.md) a [požadavky ExpressRoute NAT](expressroute-nat.md) další podrobnosti.

* Je nutné použít samostatné fondy IP adres NAT pro připojení k Internetu a ExpressRoute. Pomocí stejného fondu IP adres NAT v Internetu a ExpressRoute bude mít za následek asymetrické směrování a ztráty připojení.


## <a name="cisco-asa-firewalls"></a>Brány firewall Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Jan konfigurace pro provoz ze sítě zákazníků společnosti Microsoft
    object network MSFT-PAT
      range <SNAT-START-IP> <SNAT-END-IP>


    object-group network MSFT-Range
      network-object <IP> <Subnet_Mask>

    object-group network on-prem-range-1
      network-object <IP> <Subnet-Mask>

    object-group network on-prem-range-2
      network-object <IP> <Subnet-Mask>

    object-group network on-prem
      network-object object on-prem-range-1
      network-object object on-prem-range-2

    nat (outside,inside) source dynamic on-prem pat-pool MSFT-PAT destination static MSFT-Range MSFT-Range

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Jan konfigurace pro provoz od společnosti Microsoft do sítě zákazníka

**Rozhraní a směr:**

    Source Interface (where the traffic enters the ASA): inside
    Destination Interface (where the traffic exits the ASA): outside

**Konfigurace:**

Fond NAT:

    object network outbound-PAT
        host <NAT-IP>

Cílový Server:

    object network Customer-Network
        network-object <IP> <Subnet-Mask>

Objekt skupiny pro IP adresy zákazníka

    object-group network MSFT-Network-1
        network-object <MSFT-IP> <Subnet-Mask>

    object-group network MSFT-PAT-Networks
        network-object object MSFT-Network-1

NAT příkazy:

    nat (inside,outside) source dynamic MSFT-PAT-Networks pat-pool outbound-PAT destination static Customer-Network Customer-Network


## <a name="juniper-srx-series-routers"></a>Juniper SRX řady směrovače
### <a name="1-create-redundant-ethernet-interfaces-for-the-cluster"></a>1. Vytvoření redundantní rozhraní sítě Ethernet pro cluster
    interfaces {
        reth0 {
            description "To Internal Network";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 1;
            }
            unit 100 {
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
        reth1 {
            description "To Microsoft via Edge Router";
            vlan-tagging;
            redundant-ether-options {
                redundancy-group 2;
            }
            unit 100 {
                description "To Microsoft via Edge Router";
                vlan-id 100;
                family inet {
                    address <IP-Address/Subnet-mask>;
                }
            }
        }
    }


### <a name="2-create-two-security-zones"></a>2. Vytvořte dvě zóny zabezpečení
* Vztah důvěryhodnosti zóny pro interní sítě a Untrust zóny pro externí síť směřující hraniční směrovače
* Přiřaďte odpovídající rozhraní zóny
* Povolit službám v rozhraních

    zabezpečení {zón {zóny zabezpečení důvěryhodnosti {-příchozí-přenosů dat hostitelského {systému services {ping;                   } protokoly {bgp;                   rozhraní}} {reth0.100;               }} Untrust zóny zabezpečení {-příchozí-přenosů dat hostitelského {systému services {ping;                   } protokoly {bgp;                   rozhraní}} {reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Vytvoření zásad zabezpečení mezi zóny
    security {
        policies {
            from-zone Trust to-zone Untrust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
            from-zone Untrust to-zone Trust {
                policy allow-any {
                    match {
                        source-address any;
                        destination-address any;
                        application any;
                    }
                    then {
                        permit;
                    }
                }
            }
        }
    }


### <a name="4-configure-nat-policies"></a>4. Nakonfigurovat zásady NAT
* Vytvořte dva NAT fondy. Jeden se použije k NAT provoz odchozí společnosti Microsoft a jiných od společnosti Microsoft pro zákazníka.
* Umožňuje vytvořit pravidla pro NAT odpovídající provoz
  
       security {
           nat {
               source {
                   pool SNAT-To-ExpressRoute {
                       routing-instance {
                           External-ExpressRoute;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   pool SNAT-From-ExpressRoute {
                       routing-instance {
                           Internal;
                       }
                       address {
                           <NAT-IP-address/Subnet-mask>;
                       }
                   }
                   rule-set Outbound_NAT {
                       from routing-instance Internal;
                       to routing-instance External-ExpressRoute;
                       rule SNAT-Out {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-To-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
                   rule-set Inbound-NAT {
                       from routing-instance External-ExpressRoute;
                       to routing-instance Internal;
                       rule SNAT-In {
                           match {
                               source-address 0.0.0.0/0;
                           }
                           then {
                               source-nat {
                                   pool {
                                       SNAT-From-ExpressRoute;
                                   }
                               }
                           }
                       }
                   }
               }
           }
       }

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Nakonfigurovat protokol BGP inzerovat selektivní předpony v každém směru
Odkazovat na ukázky v [ukázky konfigurace směrování ](expressroute-config-samples-routing.md) stránky.

### <a name="6-create-policies"></a>6. Vytvoření zásad
    routing-options {
                  autonomous-system <Customer-ASN>;
    }
    policy-options {
        prefix-list Microsoft-Prefixes {
            <IP-Address/Subnet-Mask;
            <IP-Address/Subnet-Mask;
        }
        prefix-list private-ranges {
            10.0.0.0/8;
            172.16.0.0/12;
            192.168.0.0/16;
            100.64.0.0/10;
        }
        policy-statement Advertise-NAT-Pools {
            from {
                protocol static;
                route-filter <NAT-Pool-Address/Subnet-mask> prefix-length-range /32-/32;
            }
            then accept;
        }
        policy-statement Accept-from-Microsoft {
            term 1 {
                from {
                    instance External-ExpressRoute;
                    prefix-list-filter Microsoft-Prefixes orlonger;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
        policy-statement Accept-from-Internal {
            term no-private {
                from {
                    instance Internal;
                    prefix-list-filter private-ranges orlonger;
                }
                then reject;
            }
            term bgp {
                from {
                    instance Internal;
                    protocol bgp;
                }
                then accept;
            }
            term deny {
                then reject;
            }
        }
    }
    routing-instances {
        Internal {
            instance-type virtual-router;
            interface reth0.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Microsoft;
            }
            protocols {
                bgp {
                    group customer {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-ASN-1>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
        External-ExpressRoute {
            instance-type virtual-router;
            interface reth1.100;
            routing-options {
                static {
                    route <NAT-Pool-IP-Address/Subnet-mask> discard;
                }
                instance-import Accept-from-Internal;
            }
            protocols {
                bgp {
                    group edge-router {
                        export <Advertise-NAT-Pools>;
                        peer-as <Customer-Public-ASN>;
                        neighbor <BGP-Neighbor-IP-Address>;
                    }
                }
            }
        }
    }

## <a name="next-steps"></a>Další kroky
Další podrobnosti najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

