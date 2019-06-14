---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/22/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8fb9e9ea0e126509697b4874bf1e5e0b6a380e7f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66425740"
---
|**SKU**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení P2S<br> SSTP** | **P2S<br> připojení IKEv2/OpenVPN** | **Srovnávací test<br>agregované propustnosti** | **BGP** | **Zone-redundant** |
|---       | ---        | ---       | ---            | ---       | --- | --- |
|**Basic** | Max. 10    | Max. 128  | Nepodporuje se  | 100 Mb/s  | Nepodporuje se| Ne |
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporováno | Ne |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporováno | Ne |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporováno | Ne |
|**VpnGw1AZ**| Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporováno | Ano |
|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporováno | Ano |
|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporováno | Ano |


(*) Pokud potřebujete více než 30 tunelů VPN S2S, použijte službu [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Srovnávací test agregované propustnosti je založen na měření více tunelů agregovaných prostřednictvím jedné brány. Srovnávací test agregované propustnosti pro VPN Gateway je kombinací S2S + P2S. **Pokud máte velké množství připojení typu P2S, může to vzhledem k omezením propustnosti negativně ovlivnit připojení S2S.** Srovnávací test agregované propustnosti není zaručenou propustnost kvůli podmínkám v Internetu a chování aplikace.

* Tato omezení připojení jsou nezávislá. Pro skladovou položku VpnGw1 můžete například mít 128 připojení SSTP a také 250 připojení IKEv2.

* Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* Brány VPN VpnGw1, VpnGw2 a VpnGw3 se podporují jenom u modelu nasazení Resource Manageru.

* Základní SKU se považuje za starší verze SKU. Základní skladová položka má určitá omezení funkce. Nelze změnit velikost brány využívající základní SKU na jednu z nové SKU brány, musíte místo toho změnit na novou skladovou Položku, která zahrnuje odstranění a opětovné vytvoření brány VPN. Ověřte, že je funkce, které potřebujete podporovat před použít základní SKU.
