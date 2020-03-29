---
title: 'Azure ExpressRoute: Konfigurace BFD'
description: Tento článek obsahuje pokyny, jak nakonfigurovat BFD (Detekce obousměrného předávání) přes soukromý partnerský vztah okruhu ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 11/1/2018
ms.author: rambala
ms.openlocfilehash: 378b639e89ffd46f6b32d7004f934104dd4b5407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064843"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurace BFD přes ExpressRoute

ExpressRoute podporuje obousměrné detekce předávání (BFD) přes soukromé a Microsoft peering. Povolením BFD přes ExpressRoute můžete urychlit zjišťování selhání propojení mezi zařízeními Microsoft Enterprise edge (MSEE) a směrovači, na kterých ukončíte okruh ExpressRoute (CE/PE). ExpressRoute můžete ukončit přes směrovací zařízení Customer Edge nebo směrovací zařízení Partner Edge (pokud jste přešli se spravovanou službou připojení Vrstva 3). Tento dokument vás provede potřebou BFD a jak povolit BFD přes ExpressRoute.

## <a name="need-for-bfd"></a>Potřeba BFD

Následující diagram znázorňuje výhodu povolení BFD přes okruh ExpressRoute: [![1]][1]

Okruh ExpressRoute můžete povolit buď připojením vrstvy 2, nebo spravovanými připojeními vrstvy 3. V obou případech, pokud existuje jedno nebo více zařízení vrstvy 2 v cestě připojení ExpressRoute, odpovědnost za zjištění jakékoli selhání propojení v cestě spočívá v překročení bgp.

Na zařízeních MSEE je udržování a držení protokolu BGP obvykle konfigurováno jako 60 sekund a 180 sekund. Proto po selhání propojení by trvalo až tři minuty zjistit selhání propojení a přepnout provoz na alternativní připojení.

Časovače Protokolu BGP můžete řídit konfigurací nižší doby udržování protokolu BGP a doby držení na zařízení partnerského vztahu s okraji zákazníka. Pokud časovače Protokolu BGP jsou neodpovídající mezi dvěma partnerskými zařízeními, relace Protokolu BGP mezi partnery by použít nižší hodnotu časovače. Udržování BGP může být nastaveno tak nízké, jak tři sekundy a hold-time v řádu desítek sekund. Nastavení časovačů Protokolu BGP agresivně je však méně vhodnější, protože protokol je náročný na proces.

V tomto scénáři BFD může pomoci. BFD poskytuje detekci selhání propojení s nízkou režií v časovém intervalu podsekundy. 


## <a name="enabling-bfd"></a>Povolení BFD

BFD je ve výchozím nastavení nakonfigurován a ve všech nově vytvořených privátních partnerských rozhraních ExpressRoute na msees. Proto chcete-li povolit BFD, stačí nakonfigurovat BFD na vašich CE /PEs (na primárních i sekundárních zařízeních). Konfigurace BFD je dvoustupňový proces: je třeba nakonfigurovat BFD na rozhraní a potom jej propojit s relací Protokolu BGP.

Příklad konfigurace CE/PE (pomocí Cisco IOS XE) je uveden níže. 

    interface TenGigabitEthernet2/0/0.150
       description private peering to Azure
       encapsulation dot1Q 15 second-dot1q 150
       ip vrf forwarding 15
       ip address 192.168.15.17 255.255.255.252
       bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
       address-family ipv4 vrf 15
          network 10.1.15.0 mask 255.255.255.128
          neighbor 192.168.15.18 remote-as 12076
          neighbor 192.168.15.18 fall-over bfd
          neighbor 192.168.15.18 activate
          neighbor 192.168.15.18 soft-reconfiguration inbound
       exit-address-family

>[!NOTE]
>Povolení BFD v rámci již existujícího soukromého partnerského vztahu; je třeba obnovit partnerský vztah. Viz [Obnovení partnerských stran ExpressRoute][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Vyjednávání časovače BFD

Mezi partnery BFD, pomalejší ze dvou vrstevníků určit přenosovou rychlost. Intervaly přenosu/příjmu BFD msees jsou nastaveny na 300 milisekund. V některých scénářích interval může být nastavena na vyšší hodnotu 750 milisekund. Konfigurací vyšších hodnot můžete vynutit, aby tyto intervaly byly delší; ale ne kratší.

>[!NOTE]
>Pokud jste nakonfigurovali geograficky redundantní obvody ExpressRoute nebo používáte připojení VPN protokolu IPSec mezi lokalitami jako zálohu. povolení BFD by pomohlo převzetí služeb při selhání rychleji po selhání připojení ExpressRoute. 
>

## <a name="next-steps"></a>Další kroky

Další informace nebo nápovědu najdete v následujících odkazech:

- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD urychluje čas odpočtu selhání propojení"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






