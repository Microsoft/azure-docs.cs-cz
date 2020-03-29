---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1643b20c6c157c43e93967cef364e703dbf4478e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74828807"
---
|**Generování<br><br>brány VPN** |**Sku**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení P2S<br> SSTP** | **P2S<br> IKEv2/OpenVPN Připojení** | **Srovnávací test<br>agregované propustnosti** | **BGP** | **Zóna-redundantní** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generace1**|**Basic**   | Max. 10    | Max. 128  | Nepodporuje se  | 100 Mb/s  | Nepodporuje se| Ne |
|**Generace1**|**VpnGw1**  | Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporuje se | Ne |
|**Generace1**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporuje se | Ne |
|**Generace1**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporuje se | Ne |
|**Generace1**|**Vpngw1Az**| Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporuje se | Ano |
|**Generace1**|**Vpngw2Az**| Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporuje se | Ano |
|**Generace1**|**Vpngw3Az**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporuje se | Ano |
|        |            |            |           |                |           |           |     |
|**Generace2**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1,25 Gb/s | Podporuje se | Ne |
|**Generace2**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gb/s  | Podporuje se | Ne |
|**Generace2**|**VpnGw4**  | Max. 30*   | Max. 128  | Max. 5000      | 5 Gb/s    | Podporuje se | Ne |
|**Generace2**|**VpnGw5**  | Max. 30*   | Max. 128  | Max. 10000      | 10 Gb/s   | Podporuje se | Ne |
|**Generace2**|**Vpngw2Az**| Max. 30*   | Max. 128  | Max. 500       | 1,25 Gb/s | Podporuje se | Ano |
|**Generace2**|**Vpngw3Az**| Max. 30*   | Max. 128  | Max. 1000      | 2,5 Gb/s  | Podporuje se | Ano |
|**Generace2**|**Vpngw4Az**| Max. 30*   | Max. 128  | Max. 5000      | 5 Gb/s    | Podporuje se | Ano |
|**Generace2**|**Vpngw5Az**| Max. 30*   | Max. 128  | Max. 10000      | 10 Gb/s   | Podporuje se | Ano |

(*) Pokud potřebujete více než 30 tunelů VPN S2S, použijte službu [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Změna velikosti vpngw skladové položky je povolena v rámci stejné generace, s výjimkou změna velikosti základní skladové položky. Základní skladová položka je starší skladová položka a má omezení funkcí. Chcete-li přejít ze základní ho na jinou jednotku VpnGw, musíte odstranit základní bránu VPN sku a vytvořit novou bránu s požadovanou kombinací velikosti Generace a SKU.

* Tato omezení připojení jsou nezávislá. Pro skladovou položku VpnGw1 můžete například mít 128 připojení SSTP a také 250 připojení IKEv2.

* Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* V jednom tunelu lze dosáhnout maximální propustnosti 1 Gb/s. Agregační benchmark propustnosti ve výše uvedené tabulce je založen na měření více tunelových propojení agregovaných prostřednictvím jedné brány. Srovnávací test agregované propustnosti pro VPN Gateway je kombinací S2S + P2S. **Pokud máte velké množství připojení typu P2S, může to vzhledem k omezením propustnosti negativně ovlivnit připojení S2S.** Agregační propustnost Benchmark není zaručená propustnost z důvodu stavu internetového provozu a chování vaší aplikace.

Abychom našim zákazníkům pomohli pochopit relativní výkon slok pomocí různých algoritmů, použili jsme k měření výkonu veřejně dostupné nástroje iPerf a CTSTraffic. V následující tabulce jsou uvedeny výsledky testů výkonu pro generace 1, VpnGw SKU. Jak můžete vidět, nejlepší výkon je dosaženo, když jsme použili GCMAES256 algoritmus pro iPsec šifrování a integrity. Průměrný výkon jsme získali při použití AES256 pro šifrování IPsec a SHA256 pro integritu. Když jsme použili DES3 pro šifrování IPsec a SHA256 pro integritu, máme nejnižší výkon.

|**Generace**|**Sku**   | **Použité<br>algoritmy** | **Pozorovaná<br>propustnost** | **Pozorované pakety za sekundu<br>** |
|---           |---       | ---                 | ---            | ---                    |
|**Generace1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 Mb/s   | 58,000<br>50 000<br>50 000|
|**Generace1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 Mb/s | 90,000<br>80,000<br>55,000|
|**Generace1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 Mb/s<br>120 Mb/s | 105,000<br>90,000<br>60 000|
|**Generace1**|**Vpngw1Az**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 Mb/s   | 58,000<br>50 000<br>50 000|
|**Generace1**|**Vpngw2Az**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 Mb/s | 90,000<br>80,000<br>55,000|
|**Generace1**|**Vpngw3Az**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 Mb/s<br>120 Mb/s | 105,000<br>90,000<br>60 000|
