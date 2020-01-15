---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/13/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 97ee10e0b286f1b007f2e807351301f2037d1754
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942764"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Omezení sítě – Azure Resource Manager následujících omezení se vztahují jenom na síťové prostředky spravované prostřednictvím **Azure Resource Manager** pro jednotlivé oblasti a předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům předplatného](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Nedávno jsme zvýšili všechna výchozí omezení na jejich maximální limity. Pokud není sloupec maximální limit, prostředek nemá nastavitelný limit. Pokud jste tyto limity zvýšili díky podpoře v minulosti a nevidíte aktualizovaná omezení v následujících tabulkách, [otevřete online žádost o zákaznickou podporu bez poplatků](../articles/azure-resource-manager/resource-manager-quota-errors.md) .

| Prostředek | Výchozí/maximální limit | 
| --- | --- |
| Virtuální sítě |1 000 |
| Podsítě na virtuální síť |3000 |
| Partnerské vztahy virtuálních sítí na virtuální síť |500 |
| [Brány virtuální sítě (brány VPN) na virtuální síť](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |30 |
| Servery DNS na virtuální síť |20 |
| Privátní IP adresy na virtuální síť |65 536 |
| Privátní IP adresy na síťové rozhraní |256 |
| Privátní IP adresy na virtuální počítač |256 |
| Veřejné IP adresy na síťové rozhraní |256 |
| Veřejné IP adresy na virtuální počítač |256 |
| [Souběžné toky TCP nebo UDP na síťové karty virtuálního počítače nebo instance role](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500,000 |
| Síťové karty |65 536 |
| Skupiny zabezpečení sítě |5 000 |
| Počet pravidel NSG na skupinu NSG |1 000 |
| IP adresy a rozsahy zadané pro zdroj nebo cíl ve skupině zabezpečení |4,000 |
| Skupiny zabezpečení aplikací |3000 |
| Skupiny zabezpečení aplikace na konfiguraci protokolu IP, na síťové kartě |20 |
| Konfigurace protokolu IP na jednu skupinu zabezpečení aplikace |4,000 |
| Skupiny zabezpečení aplikací, které se dají zadat v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Tabulky směrování definované uživatelem |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |
| Kořenové certifikáty Point-to-site na Azure VPN Gateway |20 |
| Klepnutí na virtuální síť |100 |
| Síťové rozhraní klepněte na konfigurace na virtuální síť klepněte na |100 |

#### <a name="publicip-address"></a>Omezení veřejných IP adres
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy – dynamická | 1 000 pro Basic. |Obraťte se na podporu. |
| Veřejné IP adresy – static | 1 000 pro Basic. |Obraťte se na podporu. |
| Veřejné IP adresy – static | 1 000 pro standard.|Obraťte se na podporu. |
| Délka předpony veřejné IP adresy | /28 | Obraťte se na podporu. |

#### <a name="load-balancer"></a>Omezení pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům předplatného](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Prostředek                                | Výchozí/maximální limit         |
|-----------------------------------------|-------------------------------|
| Nástroje pro vyrovnávání zatížení                          | 1 000                         |
| Pravidla na prostředek                      | 1,500                         |
| Pravidla na síťový adaptér (u všech IP adres na síťovém adaptéru) | 300                           |
| Konfigurace IP adresy front-endu              | 600                           |
| Velikost fondu back-endu                       | 1 000 konfigurace IP adres, jedna virtuální síť |
| Porty s vysokou dostupností                 | 1 za interní front-end       |
| Odchozí pravidla na Load Balancer         | 20                            |


**Základní Load Balancer**

| Prostředek                                | Výchozí/maximální limit        |
|-----------------------------------------|------------------------------|
| Nástroje pro vyrovnávání zatížení                          | 1 000                        |
| Pravidla na prostředek                      | 250                          |
| Pravidla na síťový adaptér (u všech IP adres na síťovém adaptéru) | 300                          |
| Konfigurace IP adresy front-endu              | 200                          |
| Velikost fondu back-endu                       | 300 konfigurace IP adres, jedna skupina dostupnosti |
| Skupiny dostupnosti na Load Balancer     | 150                          |

#### <a name="virtual-networking-limits-classic"></a>Následující omezení platí jenom pro síťové prostředky spravované přes model nasazení **Classic** na předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům předplatného](../articles/networking/check-usage-against-limits.md).

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |100 |100 |
| Místní síťové lokality |20 |50 |
| Servery DNS na virtuální síť |20 |20 |
| Privátní IP adresy na virtuální síť |4 096 |4 096 |
| Souběžné toky TCP nebo UDP na síťové karty virtuálního počítače nebo instance role |500 000, až 1 000 000 pro dvě nebo více síťových adaptérů. |500 000, až 1 000 000 pro dvě nebo více síťových adaptérů. |
| Skupiny zabezpečení sítě (skupin zabezpečení sítě) |200 |200 |
| Počet pravidel NSG na skupinu NSG |1 000 |1 000 |
| Tabulky směrování definované uživatelem |200 |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |400 |
| Veřejné IP adresy (dynamické) |500 |500 |
| Vyhrazené veřejné IP adresy |500 |500 |
| Veřejné virtuální IP adresy na nasazení |5 |Kontakty na podporu |
| Privátní VIP (interní vyrovnávání zatížení) na nasazení |1\. místo |1\. místo |
| Seznamy řízení přístupu (ACL) koncového bodu |50 |50 |
