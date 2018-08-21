---
title: Konfigurace BFD přes ExpressRoute | Dokumentace Microsoftu
description: Tento dokument obsahuje pokyny ke konfiguraci BFD přes partnerský vztah privátní okruhu ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/17/2018
ms.author: rambala
ms.openlocfilehash: acac0a866d1f494830be45c23ebca6844431c9de
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40225891"
---
# <a name="configure-bfd-over-expressroute"></a>Konfigurace BFD přes ExpressRoute

ExpressRoute podporuje obousměrné předávání detekce (BFD) přes privátní partnerský vztah. Když zapnete BFD přes ExpressRoute, můžete urychlit rozpoznávání selhání připojení mezi Microsoft Enterprise (MSEE) hraniční zařízení a směrovačů, ve kterých je ukončit okruh ExpressRoute (PE). ExpressRoute můžete ukončit přes hranice zákazníka zařízení pro směrování nebo směrování partnera hraničních zařízeních (Pokud jste provedli pomocí spravované služby připojení vrstvy 3). Tento dokument vás provede potřebu BFD a jak povolit BFD přes ExpressRoute.

## <a name="need-for-bfd"></a>Potřebu BFD

Následující diagram znázorňuje výhodu, že povolení BFD přes okruh ExpressRoute: [ ![1]][1]

Můžete povolit okruh ExpressRoute, buď pomocí připojení vrstvy 2 nebo spravovaná připojení vrstvy 3. V obou případech Pokud jsou na cestě připojení ExpressRoute, jeden nebo více zařízení vrstvy 2 odpovědnost zjišťování jakýchkoliv odkazů v cestě je nadřízená protokolu BGP.

Na zařízeních směrovači MSEE keepalive protokolu BGP a blokování času jsou obvykle nakonfigurované jako 60 – 180 sekund v uvedeném pořadí. Po selhání odkazu, by trvat až proto se tři minuty k nalezení propojit selhání a přepínat provoz připojení.

Časovače protokolu BGP můžete řídit konfigurací keepalive nižší protokolu BGP a dobu uchování na partnerské vztahy hraniční zařízení zákazníka. Pokud časovače protokolu BGP se neshoda mezi dvě partnerské zařízení, využije relace protokolu BGP mezi partnerské uzly nižší hodnota časovače. Keepalive protokolu BGP je možné nastavit pouhých tří sekund a doby uchování v pořadí desítky vteřin. Však nastavení protokolu BGP časovačů méně agresivní vhodnější než protože protokol je náročné na zpracování.

V tomto scénáři může pomoct BFD. BFD poskytuje rozpoznávání selhání připojení s nízkou režií v časovém intervalu subsecond. 


## <a name="enabling-bfd"></a>Povolení BFD

BFD je nakonfigurované ve výchozím nastavení v části všechny nově vytvořené ExpressRoute privátního partnerského vztahu rozhraní na směrovači Msee. Proto povolit BFD, budete muset jenom konfigurace BFD na váš server. Konfigurace BFD je dvoustupňový proces: potřebujete nakonfigurovat BFD na rozhraní a propojit ho se relace protokolu BGP.

Příklad konfigurace PE (pomocí Cisco IOS XE) je uveden níže. 

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
>Povolit BFD v již existující soukromý partnerský vztah; budete muset obnovit partnerský vztah. Zobrazit [partnerské vztahy ExpressRoute resetování][ResetPeering]
>

## <a name="bfd-timer-negotiation"></a>Vyjednávání BFD časovače

Mezi rovnocennými počítači BFD určit pomalejší dvě spolupracovníků přenosovou rychlost. Msee BFD přijímání přenosu intervalech jsou nastaveny na 300 milisekund. Nakonfigurováním vyšší hodnoty můžete vynutit tyto intervaly delší; ale ne kratší.

>[!NOTE]
>Pokud jste nakonfigurovali soukromého partnerského vztahu okruhy geograficky redundantní ExpressRoute, nebo použijte protokol IPSec Site-to-Site VPN připojení jako zálohu pro soukromý partnerský vztah ExpressRoute; povolení BFD přes privátní partnerský vztah by pomohl převzetí služeb při selhání rychlejší následující chybu připojení ExpressRoute. 
>

## <a name="next-steps"></a>Další kroky

Další informace a nápovědu najdete na následujících odkazech:

- [Vytvoření a úprava okruhu ExpressRoute][CreateCircuit]
- [Vytvoření a úprava směrování pro okruh ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "BFD urychluje propojování odvození selhání"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/en-us/azure/expressroute/expressroute-howto-reset-peering






