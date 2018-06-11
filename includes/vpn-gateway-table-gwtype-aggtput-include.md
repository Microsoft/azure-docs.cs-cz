---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: bc42697f756ec75d9a8f2c20c99b28b2f7886ca1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670271"
---
|**SKU**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení<br>P2S** | **Srovnávací test<br>agregované propustnosti** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30                         | Max. 128*              | 650 Mb/s                    |
|**VpnGw2**| Max. 30                         | Max. 128*              | 1 Gb/s                      |
|**VpnGw3**| Max. 30                         | Max. 128*              | 1,25 Gb/s                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mb/s                    | 

*Pokud jsou potřeba další připojení, kontaktujte podporu.
- Srovnávací test agregované propustnosti je založen na měření více tunelů agregovaných prostřednictvím jedné brány. Nejedná se o zaručenou propustnost kvůli provozním podmínkám v internetu a chování aplikace.

- Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

- Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

- Brány VPN VpnGw1, VpnGw2 a VpnGw3 se podporují jenom u modelu nasazení Resource Manageru.