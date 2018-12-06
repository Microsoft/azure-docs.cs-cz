---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 555a8e3e92dc1d12cb7c6d6e06d2511f15a2c862
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52973115"
---
|**SKU**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení P2S<br> SSTP** | **Připojení P2S<br> IKEv2** | **Srovnávací test<br>agregované propustnosti** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Basic** | Max. 10    | Max. 128  | Nepodporuje se  | 100 Mb/s  | Nepodporuje se|
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporováno |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporováno |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporováno |


(*) Pokud potřebujete více než 30 tunelů VPN S2S, použijte službu [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Srovnávací test agregované propustnosti je založen na měření více tunelů agregovaných prostřednictvím jedné brány. Srovnávací test agregované propustnosti pro VPN Gateway je kombinací S2S + P2S. **Pokud máte velké množství připojení typu P2S, může to vzhledem k omezením propustnosti negativně ovlivnit připojení S2S.** Srovnávací test agregované propustnosti není zaručenou propustnost kvůli podmínkám v Internetu a chování aplikace.

* Tato omezení připojení jsou nezávislá. Pro skladovou položku VpnGw1 můžete například mít 128 připojení SSTP a také 250 připojení IKEv2.

* Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Brány VPN VpnGw1, VpnGw2 a VpnGw3 se podporují jenom u modelu nasazení Resource Manageru.

* Základní SKU se považuje za starší verze SKU. Základní skladová položka má určitá omezení funkce. Nelze změnit velikost brány využívající základní SKU na jednu z nové SKU brány, musíte místo toho změnit na novou skladovou Položku, která zahrnuje odstranění a opětovné vytvoření brány VPN. Ověřte, že je funkce, které potřebujete podporovat před použít základní SKU.
