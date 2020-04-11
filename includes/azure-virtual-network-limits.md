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
ms.openlocfilehash: ee4d629d80ff8265d58a1d05b8dca1652ddac6fb
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2020
ms.locfileid: "81112893"
---
### <a name="networking-limits---azure-resource-manager"></a><a name="azure-resource-manager-virtual-networking-limits"></a>Omezení pro sítě – Správce prostředků Azure
Následující omezení platí jenom pro síťové prostředky spravované prostřednictvím **Azure Resource Manager** u na oblast na předplatné. Přečtěte si, jak [zobrazit aktuální využití prostředků oproti limitům předplatného](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Nedávno jsme zvýšili všechny výchozí limity na jejich maximální limity. Pokud neexistuje žádný sloupec maximálního limitu, prostředek nemá nastavitelná omezení. Pokud se vám v minulosti tato omezení zvýšila o podporu a v následujících tabulkách se nezobrazují aktualizované limity, [otevřete online žádost o zákaznickou podporu zdarma.](../articles/azure-resource-manager/templates/error-resource-quota.md)

| Prostředek | Omezení | 
| --- | --- |
| Virtuální sítě |1 000 |
| Podsítě na virtuální síť |3 000 |
| Partnerské vztahy virtuální sítě v jedné virtuální síti |500 |
| [Brány virtuální sítě (brány VPN) pro virtuální síť](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku) |1 |
| [Brány virtuální sítě (brány ExpressRoute) pro virtuální síť](../articles/expressroute/expressroute-about-virtual-network-gateways.md#gwsku) |1 |
| DNS servery podle virtuální sítě |20 |
| Privátní IP adresy v jedné virtuální síti |65 536 |
| Privátní IP adresy podle síťového rozhraní |256 |
| Privátní IP adresy na virtuální počítač |256 |
| Veřejné IP adresy podle síťového rozhraní |256 |
| Veřejné IP adresy na virtuální počítač |256 |
| [Souběžné toky Protokolu TCP nebo UDP na jedno nic virtuálního počítače nebo instance role](../articles/virtual-network/virtual-machine-network-throughput.md#flow-limits-and-recommendations) |500 000 |
| Karty síťového rozhraní |65 536 |
| Network Security Groups (Skupiny zabezpečení sítě) |5 000 |
| Počet pravidel NSG na skupinu NSG |1 000 |
| Adresy IP a rozsahy určené pro zdroj nebo cíl ve skupině zabezpečení |4 000 |
| Skupiny zabezpečení aplikací |3 000 |
| Skupiny zabezpečení aplikací na konfiguraci PROTOKOLU IP, na |20 |
| Konfigurace IP adres pro skupinu zabezpečení aplikace |4 000 |
| Skupiny zabezpečení aplikací, které lze zadat v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Uživatelem definované směrovací tabulky |200 |
| Uživatelem definované trasy podle tabulky postupu |400 |
| Kořenové certifikáty typu Point-to-site podle brány Azure VPN Gateway |20 |
| TAPs virtuální sítě |100 |
| Konfigurace TAP síťového rozhraní podle protokolu TAP virtuální sítě |100 |

#### <a name="public-ip-address-limits"></a><a name="publicip-address"></a>Omezení veřejné IP adresy
| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy<sup>1</sup> | 10 pro základní. | Obraťte se na podporu. |
| Statické veřejné IP adresy<sup>1</sup> | 10 pro základní. | Obraťte se na podporu. |
| Standardní veřejné IP adresy<sup>1</sup> | 10 | Obraťte se na podporu. |
| Předpony veřejných IP adres | omezenpočtem standardních veřejných IP služeb v předplatném | Obraťte se na podporu. |
| Délka předpony veřejné IP adresy | /28 | Obraťte se na podporu. |

<sup>1.</sup> Výchozí limity pro veřejné IP adresy se liší podle typu kategorie nabídky, jako je bezplatná zkušební verze, průběžná platba, csp. Například výchozí pro předplatné smlouvy Enterprise je 1000.

#### <a name="load-balancer-limits"></a><a name="load-balancer"></a>Limity pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Přečtěte si, jak [zobrazit aktuální využití prostředků oproti limitům předplatného](../articles/networking/check-usage-against-limits.md).

**Standardní balancer zatížení**

| Prostředek                                | Omezení         |
|-----------------------------------------|-------------------------------|
| Nástroje pro vyrovnávání zatížení                          | 1 000                         |
| Pravidla pro každý zdroj                      | 1 500                         |
| Pravidla na nic (napříč všemi IP adresy na nic) | 300                           |
| Konfigurace IP adres front-endu              | 600                           |
| Velikost back-endu fondu                       | 1 000 konfigurací IP adres, jedna virtuální síť |
| Back-endové prostředky <sup>na vyvažovač zatížení 1<sup> | 150                   |
| Porty s vysokou dostupností                 | 1 na vnitřní front-end       |
| Odchozí pravidla na vyrovnávání zatížení        | 600                           |
| [Časový limit nečinnosti protokolu TCP](https://docs.microsoft.com/azure/load-balancer/load-balancer-tcp-idle-timeout#tcp-idle-timeout) | 4 minuty/30 minut          |

<sup>1.</sup> Limit je až 150 prostředků v libovolné kombinaci prostředků samostatného virtuálního počítače, prostředků sady dostupnosti a prostředků škálování virtuálních strojů.

**Základní systém vyrovnávání zatížení**

| Prostředek                                | Omezení        |
|-----------------------------------------|------------------------------|
| Nástroje pro vyrovnávání zatížení                          | 1 000                        |
| Pravidla pro každý zdroj                      | 250                          |
| Pravidla na nic (napříč všemi IP adresy na nic) | 300                          |
| Konfigurace IP adres front-endu              | 200                          |
| Velikost back-endu fondu                       | 300 konfigurací IP adres, jedna sada dostupnosti |
| Skupiny dostupnosti na vyrovnávání zatížení     | 150                          |

<a name="virtual-networking-limits-classic"></a>Následující omezení platí pouze pro síťové prostředky spravované prostřednictvím **klasického** modelu nasazení na předplatné. Přečtěte si, jak [zobrazit aktuální využití prostředků oproti limitům předplatného](../articles/networking/check-usage-against-limits.md).

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |100 |100 |
| Místní síťové lokality |20 |50 |
| DNS servery podle virtuální sítě |20 |20 |
| Privátní IP adresy v jedné virtuální síti |4,096 |4,096 |
| Souběžné toky Protokolu TCP nebo UDP na jedno nic virtuálního počítače nebo instance role |500 000, až 1 000 000 pro dvě nebo více nic. |500 000, až 1 000 000 pro dvě nebo více nic. |
| Skupiny zabezpečení sítě (NSG) |200 |200 |
| Počet pravidel NSG na skupinu NSG |1 000 |1 000 |
| Uživatelem definované směrovací tabulky |200 |200 |
| Uživatelem definované trasy podle tabulky postupu |400 |400 |
| Veřejné IP adresy (dynamické) |500 |500 |
| Vyhrazené veřejné IP adresy |500 |500 |
| Veřejné virtuální IP adresy na nasazení |5 |Kontaktování podpory |
| Privátní VIP (interní vyrovnávání zatížení) na nasazení |1 |1 |
| Seznamy řízení přístupu koncových bodů (Seznamy Řízení přístupu) |50 |50 |
