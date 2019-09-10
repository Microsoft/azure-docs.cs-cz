---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: a37280fddf34221a9d08501ef55c6b069cd877a4
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70242627"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Omezení sítě – Azure Resource Manager následujících omezení se vztahují jenom na síťové prostředky spravované prostřednictvím **Azure Resource Manager** pro jednotlivé oblasti a předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům](../articles/networking/check-usage-against-limits.md)předplatného.

> [!NOTE]
> Nedávno jsme zvýšili všechna výchozí omezení na jejich maximální limity. Pokud není sloupec maximální limit, prostředek nemá nastavitelný limit. Pokud jste tyto limity zvýšili díky podpoře v minulosti a nevidíte aktualizovaná omezení v následujících tabulkách, [otevřete online žádost o zákaznickou podporu bez poplatků](../articles/azure-resource-manager/resource-manager-quota-errors.md) .

| Resource | Výchozí/maximální limit | 
| --- | --- |
| Virtuální sítě |1 000 |
| Podsítě na virtuální síť |3 000 |
| Partnerské vztahy virtuálních sítí na virtuální síť |500 |
| Servery DNS na virtuální síť |20 |
| Privátní IP adresy na virtuální síť |65 536 |
| Privátní IP adresy na síťové rozhraní |256 |
| Privátní IP adresy na virtuální počítač |256 |
| Veřejné IP adresy na síťové rozhraní |256 |
| Veřejné IP adresy na virtuální počítač |256 |
| Souběžné toky TCP nebo UDP na síťové karty virtuálního počítače nebo instance role |500,000 |
| Síťové karty |65 536 |
| Network Security Groups (Skupiny zabezpečení sítě) |5,000 |
| Počet pravidel NSG na skupinu NSG |1 000 |
| IP adresy a rozsahy zadané pro zdroj nebo cíl ve skupině zabezpečení |4,000 |
| Skupiny zabezpečení aplikace |3 000 |
| Skupiny zabezpečení aplikace na konfiguraci protokolu IP, na síťové kartě |20 |
| Konfigurace protokolu IP na jednu skupinu zabezpečení aplikace |4,000 |
| Skupiny zabezpečení aplikací, které se dají zadat v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Tabulky směrování definované uživatelem |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |
| Kořenové certifikáty Point-to-site na Azure VPN Gateway |20 |
| Klepnutí na virtuální síť |100 |
| Síťové rozhraní klepněte na konfigurace na virtuální síť klepněte na |100 |

#### <a name="publicip-address"></a>Omezení veřejných IP adres
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy – dynamická | 1 000 pro Basic. |Kontaktujte podporu. |
| Veřejné IP adresy – static | 1 000 pro Basic. |Kontaktujte podporu. |
| Veřejné IP adresy – static | 1 000 pro standard.|Kontaktujte podporu. |
| Délka předpony veřejné IP adresy | /28 | Kontaktujte podporu. |

#### <a name="load-balancer"></a>Omezení pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům](../articles/networking/check-usage-against-limits.md)předplatného.

| Resource | Výchozí/maximální limit |
| --- | --- |
| Nástroje pro vyrovnávání zatížení | 1 000 | 
| Pravidla podle prostředku, Basic | 250 |
| Pravidla podle prostředku, Standard | 1,500 | 
| Pravidla pro konfiguraci protokolu IP | 299 |
| Pravidla na síťový adaptér | 300 |
| Konfigurace front-endové IP adresy, Basic | 200 |
| Konfigurace front-endové IP adresy, Standard | 600 |
| Fond back-end, základní | 100, jedna skupina dostupnosti |
| Fond back-end, Standard | 1 000, jedna virtuální síť |
| Prostředky back-endu na nástroj pro vyrovnávání zatížení, standard<sup>1</sup> | 150 |
| Porty s vysokou dostupností, Standard | 1 za interní front-end |

<sup>1</sup> Limit je až 150 prostředků, v libovolné kombinaci prostředků samostatného virtuálního počítače, prostředků skupiny dostupnosti a prostředků sady škálování virtuálních počítačů.

#### <a name="virtual-networking-limits-classic"></a>Následující omezení platí jenom pro síťové prostředky spravované přes model nasazení **Classic** na předplatné. Naučte se, jak [Zobrazit aktuální využití prostředků proti limitům](../articles/networking/check-usage-against-limits.md)předplatného.

| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |100 |100 |
| Místní síťové lokality |20 |50 |
| Servery DNS na virtuální síť |20 |20 |
| Privátní IP adresy na virtuální síť |4 096 |4 096 |
| Souběžné toky TCP nebo UDP na síťové karty virtuálního počítače nebo instance role |500 000, až 1 000 000 pro dvě nebo více síťových adaptérů. |500 000, až 1 000 000 pro dvě nebo více síťových adaptérů. |
| Skupiny zabezpečení sítě (skupin zabezpečení sítě) |200 |200 |
| Počet pravidel NSG na skupinu NSG |1 000 |1 000 |
| Tabulky směrování definované uživatelem |200 |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |400 |
| Veřejné IP adresy (dynamické) |500 |500 |
| Vyhrazené veřejné IP adresy |500 |500 |
| Veřejné virtuální IP adresy na nasazení |5 |Kontaktujte podporu |
| Privátní VIP (interní vyrovnávání zatížení) na nasazení |1 |1 |
| Seznamy řízení přístupu (ACL) koncového bodu |50 |50 |
