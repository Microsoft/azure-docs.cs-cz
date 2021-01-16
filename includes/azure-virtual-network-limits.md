---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 01/14/2020
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 59329fccda77f16e4a595e9b1789ef684c5cbf95
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2021
ms.locfileid: "98256444"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Omezení sítě – Azure Resource Manager
Následující omezení platí pouze pro síťové prostředky spravované prostřednictvím **Azure Resource Manager** v jednotlivých oblastech na jedno předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům předplatného](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Nedávno jsme zvýšili všechna výchozí omezení na jejich maximální limity. Pokud není sloupec maximální limit, prostředek nemá nastavitelný limit. Pokud jste tyto limity zvýšili díky podpoře v minulosti a nevidíte aktualizovaná omezení v následujících tabulkách, [otevřete online žádost o zákaznickou podporu bez poplatků](../articles/azure-resource-manager/templates/error-resource-quota.md) .

| Prostředek | Omezení | 
| --- | --- |
| Virtuální sítě |1 000 |
| Podsítě v jedné virtuální síti |3 000 |
| Partnerské vztahy virtuální sítě v jedné virtuální síti |500 |
| [Brány virtuální sítě (brány VPN) na virtuální síť](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Brány virtuální sítě (brány ExpressRoute) na virtuální síť](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| Servery DNS na virtuální síť |20 |
| Privátní IP adresy v jedné virtuální síti |65 536 |
| Privátní IP adresy na síťové rozhraní |256 |
| Privátní IP adresy na virtuální počítač |256 |
| Veřejné IP adresy na síťové rozhraní |256 |
| Veřejné IP adresy na virtuální počítač |256 |
| [Souběžné toky TCP nebo UDP na síťové karty virtuálního počítače nebo instance role](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) |500 000 |
| Karty síťového rozhraní |65 536 |
| Network Security Groups (Skupiny zabezpečení sítě) |5 000 |
| Počet pravidel NSG na skupinu NSG |1 000 |
| IP adresy a rozsahy zadané pro zdroj nebo cíl ve skupině zabezpečení |4 000 |
| Skupiny zabezpečení aplikací |3 000 |
| Skupiny zabezpečení aplikace na konfiguraci protokolu IP, na síťové kartě |20 |
| Konfigurace protokolu IP na jednu skupinu zabezpečení aplikace |4 000 |
| Skupiny zabezpečení aplikací, které se dají zadat v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Tabulky směrování definované uživatelem |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |
| Kořenové certifikáty Point-to-site na Azure VPN Gateway |20 |
| Klepnutí na virtuální síť |100 |
| Síťové rozhraní klepněte na konfigurace na virtuální síť klepněte na |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Omezení veřejných IP adres
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy<sup>1</sup> | 10 pro základní. | Obraťte se na podporu. |
| Statické veřejné IP adresy<sup>1</sup> | 10 pro základní. | Obraťte se na podporu. |
| Veřejné IP adresy standard<sup>1</sup> | 10 | Obraťte se na podporu. |
| [Veřejné IP adresy na skupinu prostředků](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md#microsoftnetwork) | 800 | Obraťte se na podporu. | 
| Předpony veřejných IP adres | omezeno počtem standardních veřejných IP adres v předplatném | Obraťte se na podporu. |
| Délka předpony veřejné IP adresy | za 28 | Obraťte se na podporu. |

<sup>1</sup> Výchozí omezení pro veřejné IP adresy se liší podle typu kategorie nabídky, jako je například bezplatná zkušební verze, s průběžnými platbami, CSP. Například výchozí hodnota pro smlouva Enterprise předplatná je 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Omezení pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům předplatného](../articles/networking/check-usage-against-limits.md).

**Standard Load Balancer**

| Prostředek                                | Omezení         |
|-----------------------------------------|-------------------------------|
| nástroje pro vyrovnávání zatížení,                          | 1 000                         |
| Pravidla na prostředek                      | 1 500                         |
| Pravidla na síťový adaptér (u všech IP adres na síťovém adaptéru) | 300                           |
| Konfigurace IP adresy front-endu              | 600                           |
| Velikost fondu back-endu                       | 1 000 konfigurace IP adres, jedna virtuální síť |
| Back-endové prostředky na Load Balancer <sup> 1<sup> | 250                   |
| Porty s vysokou dostupností                 | 1 za interní front-end       |
| Odchozí pravidla na Load Balancer        | 600                           |
| Nástroje pro vyrovnávání zatížení na virtuální počítač                   | 2 (1 veřejná a 1 interní)   |

<sup>1</sup> Limit je až 150 prostředků, v libovolné kombinaci prostředků samostatného virtuálního počítače, prostředků sady dostupnosti a skupin umístění virtuálních počítačů.

**Základní Load Balancer**

| Prostředek                                | Omezení        |
|-----------------------------------------|------------------------------|
| nástroje pro vyrovnávání zatížení,                          | 1 000                        |
| Pravidla na prostředek                      | 250                          |
| Pravidla na síťový adaptér (u všech IP adres na síťovém adaptéru) | 300                          |
| Konfigurace IP adresy front-endu              | 200                          |
| Velikost fondu back-endu                       | 300 konfigurace IP adres, jedna skupina dostupnosti |
| Skupiny dostupnosti na Load Balancer     | 1                            |
| Nástroje pro vyrovnávání zatížení na virtuální počítač                   | 2 (1 veřejná a 1 interní)  |

<a name="virtual-networking-limits-classic"></a>Následující omezení platí jenom pro síťové prostředky spravované přes model nasazení **Classic** na předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům předplatného](../articles/networking/check-usage-against-limits.md).

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |100 |100 |
| Místní síťové lokality |20 |50 |
| Servery DNS na virtuální síť |20 |20 |
| Privátní IP adresy v jedné virtuální síti |4 096 |4 096 |
| Souběžné toky TCP nebo UDP na síťové karty virtuálního počítače nebo instance role |500 000, až 1 000 000 pro dvě nebo více síťových adaptérů. |500 000, až 1 000 000 pro dvě nebo více síťových adaptérů. |
| Skupiny zabezpečení sítě (skupin zabezpečení sítě) |200 |200 |
| Počet pravidel NSG na skupinu NSG |200 |1 000 |
| Tabulky směrování definované uživatelem |200 |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |400 |
| Veřejné IP adresy (dynamické) |500 |500 |
| Vyhrazené veřejné IP adresy |500 |500 |
| Veřejná IP adresa na nasazení |5 |Kontaktování podpory |
| Privátní IP adresa (interní vyrovnávání zatížení) na nasazení |1 |1 |
| Seznamy řízení přístupu (ACL) koncového bodu |50 |50 |
