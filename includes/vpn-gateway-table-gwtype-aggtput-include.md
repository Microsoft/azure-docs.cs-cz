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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74828807"
---
|**<br>Generace služby VPN Gateway <br>** |**Skladová jednotka (SKU)**   | **Tunely<br> S2S/VNet-to-VNet** | **Připojení P2S<br> SSTP** | **<br>Připojení P2S IKEv2/OpenVPN** | **Srovnávací test<br>agregované propustnosti** | **BGP** | **Zóna – redundantní** |
|---            |---         | ---        | ---       | ---            | ---       | ---       | ---|
|**Generation1**|**Basic**   | Max. 10    | Max. 128  | Nepodporuje se  | 100 Mb/s  | Nepodporuje se| No |
|**Generation1**|**VpnGw1**  | Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporováno | No |
|**Generation1**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporováno | No |
|**Generation1**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporováno | No |
|**Generation1**|**VpnGw1AZ**| Max. 30*   | Max. 128  | Max. 250       | 650 Mb/s  | Podporováno | Yes |
|**Generation1**|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1 Gb/s    | Podporováno | Yes |
|**Generation1**|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gb/s | Podporováno | Yes |
|        |            |            |           |                |           |           |     |
|**Generation2**|**VpnGw2**  | Max. 30*   | Max. 128  | Max. 500       | 1,25 Gb/s | Podporováno | No |
|**Generation2**|**VpnGw3**  | Max. 30*   | Max. 128  | Max. 1000      | 2,5 GB/s  | Podporováno | No |
|**Generation2**|**VpnGw4**  | Max. 30*   | Max. 128  | Max. 5000      | 5 Gb/s    | Podporováno | No |
|**Generation2**|**VpnGw5**  | Max. 30*   | Max. 128  | Max. 10000      | 10 Gb/s   | Podporováno | No |
|**Generation2**|**VpnGw2AZ**| Max. 30*   | Max. 128  | Max. 500       | 1,25 Gb/s | Podporováno | Yes |
|**Generation2**|**VpnGw3AZ**| Max. 30*   | Max. 128  | Max. 1000      | 2,5 GB/s  | Podporováno | Yes |
|**Generation2**|**VpnGw4AZ**| Max. 30*   | Max. 128  | Max. 5000      | 5 Gb/s    | Podporováno | Yes |
|**Generation2**|**VpnGw5AZ**| Max. 30*   | Max. 128  | Max. 10000      | 10 Gb/s   | Podporováno | Yes |

(*) Pokud potřebujete více než 30 tunelů VPN S2S, použijte službu [Virtual WAN](../articles/virtual-wan/virtual-wan-about.md).

* Změna velikosti SKU VpnGw je povolena v rámci stejné generace s výjimkou změny velikosti základní SKU. Základní SKU je starší verze SKU a má omezení funkcí. Aby bylo možné přejít ze základního na jinou SKLADOVOU položku VpnGw, je nutné odstranit základní bránu VPN Gateway a vytvořit novou bránu s požadovanou kombinací velikosti pro generaci a SKU.

* Tato omezení připojení jsou nezávislá. Pro skladovou položku VpnGw1 můžete například mít 128 připojení SSTP a také 250 připojení IKEv2.

* Informace o cenách najdete na stránce [Ceny](https://azure.microsoft.com/pricing/details/vpn-gateway).

* Informace o smlouvě SLA (smlouva o úrovni služeb) můžete najít na stránce [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/).

* U jediného tunelu lze dosáhnout maximální propustnosti 1 GB/s. Srovnávací srovnávací test propustnosti ve výše uvedené tabulce je založený na měření více tunelů agregovaných prostřednictvím jedné brány. Srovnávací test agregované propustnosti pro VPN Gateway je kombinací S2S + P2S. **Pokud máte velké množství připojení typu P2S, může to vzhledem k omezením propustnosti negativně ovlivnit připojení S2S.** Srovnávací srovnávací test propustnosti není zaručená propustnost v důsledku podmínek internetového provozu a chování vaší aplikace.

Abychom zákazníkům porozuměli relativnímu výkonu SKU pomocí různých algoritmů, používali jsme veřejně dostupné nástroje iPerf a CTSTraffic k měření výkonností. V následující tabulce jsou uvedeny výsledky testů výkonnosti pro VpnGw SKU generace 1. Jak vidíte, dosáhnete nejlepšího výkonu, když jsme použili GCMAES256 algoritmus pro šifrování a integritu protokolu IPsec. Dostali jsme průměrný výkon při použití AES256 pro šifrování protokolem IPsec a SHA256 pro zajištění integrity. Když jsme používali DES3 pro šifrování protokolem IPsec a SHA256 pro zajištění integrity, máme nejnižší výkon.

|**Generace**|**Skladová jednotka (SKU)**   | **<br>Použité algoritmy** | **<br>Zjištěná propustnost** | **Pakety za sekundu <br> pozorovány** |
|---           |---       | ---                 | ---            | ---                    |
|**Generation1**|**VpnGw1**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 MB/s   | 58 000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 MB/s | 90,000<br>80,000<br>55 000|
|**Generation1**|**VpnGw3**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 MB/s<br>120 MB/s | 105 000<br>90,000<br>60 000|
|**Generation1**|**VpnGw1AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 650 Mb/s<br>500 Mb/s<br>120 MB/s   | 58 000<br>50,000<br>50,000|
|**Generation1**|**VpnGw2AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1 Gb/s<br>500 Mb/s<br>120 MB/s | 90,000<br>80,000<br>55 000|
|**Generation1**|**VpnGw3AZ**| GCMAES256<br>AES256 & SHA256<br>DES3 & SHA256| 1,25 Gb/s<br>550 MB/s<br>120 MB/s | 105 000<br>90,000<br>60 000|
