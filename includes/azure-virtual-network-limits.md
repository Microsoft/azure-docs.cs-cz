---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 08/16/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: d1c5c9a8709467c9f9ca87c841cffcf77a5b5f0b
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219895"
---
<a name="virtual-networking-limits-classic"></a>Následující omezení platí pouze pro síťové prostředky spravované přes model nasazení Classic na předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |50 |100 |
| Místní síťové lokality |20 |kontaktovat podporu |
| Servery DNS na virtuální síť |20 |20 |
| Privátní IP adresy na virtuální síť |4 096 |4 096 |
| Souběžné TCP nebo UDP toky pro síťové rozhraní virtuálního počítače nebo role instance |500 000 |500 000 |
| Skupiny zabezpečení sítě (NSG) |100 |200 |
| Počet pravidel NSG na skupinu NSG |200 |1000 |
| Směrovací tabulky definované uživatelem |100 |200 |
| Trasy definované uživatelem na směrovací tabulku |100 |400 |
| Veřejné IP adresy (dynamické) |5 |kontaktovat podporu |
| Vyhrazené veřejné IP adresy |20 |kontaktovat podporu |
| Veřejné virtuální IP adresy na nasazení |5 |kontaktovat podporu |
| Privátní virtuální IP adresy (ILB) na nasazení |1 |1 |
| Seznamy řízení přístupu (ACL) koncového bodu |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Síťová omezení – Azure Resource Manager
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Zvětšili jsme nedávno všechny výchozí omezení maximální mezí. Pokud není žádný **maximální Limit** sloupec a pak prostředku nemá nastavitelná omezení. Pokud jste využili tyto limity zvýšit podpora v minulosti a nezobrazí aktualizované omezení, jak je uvedeno níže, můžete [otevřete žádost o podporu online zákazníků bez poplatků](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Prostředek | Výchozí omezení | 
| --- | --- |
| Virtuální sítě |1000 |
| Podsítě na virtuální síť |3000 |
| Partnerské vztahy virtuálních sítí na virtuální síť |100 |
| Servery DNS na virtuální síť |25 |
| Privátní IP adresy na virtuální síť |65536 |
| Privátní IP adresy na síťové rozhraní |256 |
| Souběžné TCP nebo UDP toky pro síťové rozhraní virtuálního počítače nebo role instance |500 000 |
| Síťová rozhraní (NIC) |24000 |
| Skupiny zabezpečení sítě (NSG) |5000 |
| Počet pravidel NSG na skupinu NSG |1000 |
| IP adresy a rozsahy zadané pro zdroj nebo cíl ve skupině zabezpečení |4000 |
| Skupiny zabezpečení aplikací |3000 |
| Skupiny zabezpečení aplikace podle konfigurace protokolu IP pro síťové rozhraní |20 |
| Konfigurace IP na skupiny zabezpečení aplikací |4000 |
| Skupiny zabezpečení aplikací, které se dá nastavit v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Směrovací tabulky definované uživatelem |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |
| Kořenové certifikáty typu Point-to-Site na službu VPN Gateway |20 |
| Klepne virtuální sítě |100 |
| Klepněte na konfiguraci síťového rozhraní na virtuální síť, klepněte na |100 |

#### <a name="publicip-address"></a>Veřejné IP adresy omezení

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy – dynamická |200 (základní) |kontaktovat podporu |
| Veřejné IP adresy – statické |200 (základní) |kontaktovat podporu |
| Veřejné IP adresy – statické |200 (standard) |kontaktovat podporu |

#### <a name="load-balancer"></a>Omezení nástroje pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md)

| Prostředek | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Nástroje pro vyrovnávání zatížení | 100 | 1000 |
| Pravidla pro každý prostředek, Basic | 250 | 250 |
| Pravidla pro každý prostředek, Standard | 1 500 | 1 500 |
| Pravidla na konfiguraci IP adresy | 299 |299 |
| Konfigurace protokolu IP front-endu, Basic | 10 | 200 |
| Konfigurace protokolu IP front-endu, Standard | 10 | 600 |
| Back-endový fond Basic | 100, jedna skupina dostupnosti | 100, jedna skupina dostupnosti |
| Back-endový fond Standard | 1 000, jedna virtuální síť | 1 000, jedna virtuální síť |
| Back-Endovým prostředkům za nástroj pro vyrovnávání zatížení, standardní * | 150 | 150 |
| HA portů a Standard | 1 za interní front-endu | 1 za interní front-endu |

** Až 150 zdrojů libovolnou kombinaci samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů.

Pokud potřebujete navýšit výchozí omezení, [kontaktujte podporu](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

