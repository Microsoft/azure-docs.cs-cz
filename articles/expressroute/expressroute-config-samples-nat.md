---
title: Ukázky konfigurace směrovače - NAT - Azure ExpressRoute | Dokumentace Microsoftu
description: Tato stránka obsahuje ukázky konfigurace směrovače pro směrovačů Cisco a Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9764a03b0f3a3f70e59097359d5a714da821d3b1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105984"
---
# <a name="router-configuration-samples-to-set-up-and-manage-nat"></a>Ukázky konfigurace směrovače pro nastavení a správu NAT

Tato stránka obsahuje ukázky NAT konfigurace Cisco ASA a směrovače řady softwaru Juniper SRX při práci se službou ExpressRoute. Ty by měla být ukázky pouze pokyny a nesmí se používat je. Můžete pracovat s vaším dodavatelem přijít s odpovídající konfigurací pro vaši síť.

> [!IMPORTANT]
> Ukázky na této stránce jsou určené výhradně pro doprovodné materiály. Musí fungovat s vaším dodavatelem prodejní a technickým týmem a síťovým týmem přijít s odpovídající konfigurací podle svých potřeb. Problémy související s konfigurací, které jsou uvedené na této stránce nebude podpory společnosti Microsoft. Pro problémy podpory musí kontaktujte dodavatele zařízení.
> 
> 

* Ukázky konfigurace směrovače níže se vztahují na partnerské vztahy Azure veřejný a Microsoftu. Nakonfigurujete nesmí NAT pro soukromý partnerský vztah Azure. Kontrola [partnerské vztahy ExpressRoute](expressroute-circuit-peerings.md) a [požadavky NAT pro ExpressRoute](expressroute-nat.md) další podrobnosti.

* Je nutné použít oddělené fondy IP adres NAT pro připojení k Internetu i ExpressRoute. Používat stejný fond IP adres NAT na Internetu i ExpressRoute bude mít za následek asymetrického směrování a ke ztrátě připojení.


## <a name="cisco-asa-firewalls"></a>Brány firewall Cisco ASA
### <a name="pat-configuration-for-traffic-from-customer-network-to-microsoft"></a>Token PAT konfigurace pro provoz v síti zákazníka do Microsoftu
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

### <a name="pat-configuration-for-traffic-from-microsoft-to-customer-network"></a>Token PAT konfigurace pro provoz z Microsoftu k síti zákazníka

**Rozhraní a směrování:**

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


## <a name="juniper-srx-series-routers"></a>Směrovače Juniper SRX řady
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
* Zóny zabezpečení pro interní síť a nedůvěryhodných zóny pro externí síť směřující hraniční směrovače
* Přiřaďte odpovídající rozhraní zóny
* Povolit službám na rozhraních

    zabezpečení {zóny {zóny zabezpečení důvěryhodnosti {-inbound provozu hostitele {systému služby {ping;                   } protokoly {bgp;                   rozhraní}} {reth0.100;               }} nedůvěryhodných zóny zabezpečení {-inbound provozu hostitele {systému služby {ping;                   } protokoly {bgp;                   rozhraní}} {reth1.100;               }           }       }   }


### <a name="3-create-security-policies-between-zones"></a>3. Vytvoření zásad zabezpečení mezi zónami
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


### <a name="4-configure-nat-policies"></a>4. Konfigurace zásad NAT
* Vytvořte dva fondy NAT. Jeden se použije k provozu NAT odchozí společnosti Microsoft a jiných od Microsoftu k zákazníkovi.
* Vytvoření pravidel NAT příslušných provozu
  
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

### <a name="5-configure-bgp-to-advertise-selective-prefixes-in-each-direction"></a>5. Nakonfigurovat protokol BGP k inzerování selektivní předpony v každém směru
Podívejte se na ukázky v [ukázky konfigurace směrování ](expressroute-config-samples-routing.md) stránky.

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

## <a name="next-steps"></a>Další postup
Další podrobnosti najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).

