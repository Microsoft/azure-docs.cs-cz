---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/02/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4cbbb64489acf23c1248e35269e1441dd2a6878e
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39513739"
---
|**SKU**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení<br>P2S** | **Srovnávací test<br>agregované propustnosti** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30*                         | Max. 128**             | 650 Mb/s                    |
|**VpnGw2**| Max. 30*                         | Max. 128**             | 1 Gb/s                      |
|**VpnGw3**| Max. 30*                         | Max. 128**             | 1,25 Gb/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mb/s                    | 

* (*) Pokud potřebujete více než 30 tunelů VPN S2S, použijte službu [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* (**) Pokud jsou potřeba další připojení, kontaktujte podporu. To platí jenom pro IKEv2, počet připojení pro SSTP se nedá zvýšit.

* Srovnávací test agregované propustnosti je založen na měření více tunelů agregovaných prostřednictvím jedné brány. Nejedná se o zaručenou propustnost kvůli provozním podmínkám v internetu a chování aplikace.

* Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Brány VPN VpnGw1, VpnGw2 a VpnGw3 se podporují jenom u modelu nasazení Resource Manageru.