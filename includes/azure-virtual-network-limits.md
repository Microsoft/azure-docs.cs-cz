---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 06/12/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: ad76046a092a37a2dc1f59459ac3c3e48bc284e5
ms.sourcegitcommit: 8659efc885be98f42edae0b516ca576e38940c97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2018
ms.locfileid: "35568122"
---
<a name="virtual-networking-limits-classic"></a>Následující omezení platí pouze pro síťové prostředky spravované přes model nasazení Classic na předplatné. Zjistěte, jak [zobrazení vašeho aktuálního využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |50 |100 |
| Místní síťové lokality |20 |kontaktovat podporu |
| Servery DNS na virtuální síť |20 |100 |
| Privátní IP adresy na virtuální síť |4 096 |4 096 |
| Souběžné TCP nebo UDP toků na síťový adaptér virtuálního počítače nebo role instance |500 000 |500 000 |
| Skupiny zabezpečení sítě (NSG) |100 |200 |
| Počet pravidel NSG na skupinu NSG |200 |400 |
| Směrovací tabulky definované uživatelem |100 |200 |
| Trasy definované uživatelem na směrovací tabulku |100 |400 |
| Veřejné IP adresy (dynamické) |5 |kontaktovat podporu |
| Vyhrazené veřejné IP adresy |20 |kontaktovat podporu |
| Veřejné virtuální IP adresy na nasazení |5 |kontaktovat podporu |
| Privátní virtuální IP adresy (ILB) na nasazení |1 |1 |
| Seznamy řízení přístupu (ACL) koncového bodu |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Síťová omezení – Azure Resource Manager
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Zjistěte, jak [zobrazení vašeho aktuálního využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |50 |1000 |
| Podsítě na virtuální síť |1000 |10000 |
| Partnerské vztahy virtuální sítě na jednu virtuální síť |10 |50 |
| Servery DNS na virtuální síť |9 |25 |
| Privátní IP adresy na virtuální síť |16384 ** |16384 |
| Privátní IP adresy na síťové rozhraní |256 |1024 |
| Souběžné TCP nebo UDP toků na síťový adaptér virtuálního počítače nebo role instance |500 000 |500 000 |
| Síťová rozhraní (NIC) |24000 ** |24000 |
| Skupiny zabezpečení sítě (NSG) |100 |5000 |
| Počet pravidel NSG na skupinu NSG |1000 ** |1000 |
| IP adresy a rozsahy, zadaný pro zdrojové nebo cílové ve skupině zabezpečení |2000 |4000 |
| Skupiny zabezpečení aplikací |200 |500 |
| Skupiny zabezpečení aplikací na konfiguraci IP adresy, na síťový adaptér |10 |20 |
| Konfigurace protokolu IP na skupinu zabezpečení aplikací |1000 |4000 |
| Skupiny zabezpečení aplikací, které lze zadat v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |50 |100 |
| Směrovací tabulky definované uživatelem |100 |200 |
| Trasy definované uživatelem na směrovací tabulku |100 |400 |
| Veřejné IP adresy - dynamické |60 (základní) |kontaktovat podporu |
| Veřejné IP adresy – statické |20 (základní) |kontaktovat podporu |
| Veřejné IP adresy – statické |20 (standardní) |kontaktovat podporu |
| Kořenové certifikáty typu Point-to-Site na službu VPN Gateway |20 |20 |

** Tyto výchozí omezení se vztahují na odběry, které předtím nebyl dříve tyto limity zvýšit prostřednictvím podpory

#### <a name="load-balancer"></a>Omezení nástroje pro vyrovnávání zatížení

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Služby vyrovnávání zatížení | 100 | 1000 |
| Pravidla na prostředek, Basic | 150 | 250 |
| Pravidla na prostředek, Standard | 1250 | 1 500 |
| Pravidla pro konfiguraci protokolu IP | 299 |299 |
| Konfigurace IP front-endu, Basic | 10 | 200 |
| Konfigurace IP front-endu, Standard | 10 | 600 |
| Fond back-end, Basic | 100, jedna skupina dostupnosti | 100, jedna skupina dostupnosti |
| Fond back-end, Standard | 1000, jedna virtuální síť | 1000, jedna virtuální síť |
| Back-endové prostředky na Vyrovnávání zatížení, Standard &ast; | 50 | 150 |
| HA porty, Standard | 1 na interní front-endu | 1 na interní front-endu |

&ast; Až 150 prostředky libovolnou kombinaci samostatné virtuální počítače, skupiny dostupnosti a sady škálování virtuálního počítače.

Pokud potřebujete navýšit výchozí omezení, [kontaktujte podporu](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

