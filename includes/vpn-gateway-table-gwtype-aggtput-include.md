---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b698dd03473dd3cb708c47c6554869eebba48bf9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597589"
---
|**SKU**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení P2S<br> SSTP** | **Připojení P2S<br> IKEv2** | **Srovnávací test<br>agregované propustnosti** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Basic** | Max. 10    | Max. 128  | Nepodporuje se  | 100 Mb/s  | Nepodporuje se|
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporuje se |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporuje se |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporuje se |


(*) Pokud potřebujete více než 30 tunelů VPN S2S, použijte službu [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Srovnávací test agregované propustnosti pro VPN Gateway je kombinací S2S + P2S. **Pokud máte velké množství připojení typu P2S, může to vzhledem k omezením propustnosti negativně ovlivnit připojení S2S.** Srovnávací test agregované propustnosti je založen na měření více tunelů agregovaných prostřednictvím jedné brány. Nejedná se o zaručenou propustnost kvůli provozním podmínkám v internetu a chování aplikace.

* Tato omezení připojení jsou nezávislá. Pro skladovou položku VpnGw1 můžete například mít 128 připojení SSTP a také 250 připojení IKEv2.

* Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Brány VPN VpnGw1, VpnGw2 a VpnGw3 se podporují jenom u modelu nasazení Resource Manageru.
