---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: jimdial
ms.service: networking
ms.topic: include
ms.date: 08/03/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 9dbd230e431b2bc3041cfc854c0161ce8d453870
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "40133495"
---
<a name="virtual-networking-limits-classic"></a>Následující omezení platí pouze pro síťové prostředky spravované přes model nasazení Classic na předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

| Zdroj | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |50 |100 |
| Místní síťové lokality |20 |Kontaktujte podporu |
| Servery DNS na virtuální síť |20 |100 |
| Privátní IP adresy na virtuální síť |4096 |4096 |
| Souběžné TCP nebo UDP toky pro síťové rozhraní virtuálního počítače nebo role instance |500 000 |500 000 |
| Skupiny zabezpečení sítě (NSG) |100 |200 |
| Počet pravidel NSG na skupinu NSG |200 |1000 |
| Směrovací tabulky definované uživatelem |100 |200 |
| Trasy definované uživatelem na směrovací tabulku |100 |400 |
| Veřejné IP adresy (dynamické) |5 |Kontaktujte podporu |
| Vyhrazené veřejné IP adresy |20 |Kontaktujte podporu |
| Veřejné virtuální IP adresy na nasazení |5 |Kontaktujte podporu |
| Privátní virtuální IP adresy (ILB) na nasazení |1 |1 |
| Seznamy řízení přístupu (ACL) koncového bodu |50 |50 |

#### <a name="azure-resource-manager-virtual-networking-limits"></a>Síťová omezení – Azure Resource Manager
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Zvětšili jsme nedávno všechny výchozí omezení maximální mezí. Pokud není žádný **maximální Limit** sloupec a pak prostředku nemá nastavitelná omezení. Pokud jste využili tyto limity zvýšit podpora v minulosti a nezobrazí aktualizované omezení, jak je uvedeno níže, můžete [otevřete žádost o podporu online zákazníků bez poplatků](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Zdroj | Výchozí omezení | 
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
| Skupiny zabezpečení aplikace |3000 |
| Skupiny zabezpečení aplikace podle konfigurace protokolu IP pro síťové rozhraní |20 |
| Konfigurace IP na skupiny zabezpečení aplikací |4000 |
| Skupiny zabezpečení aplikací, které se dá nastavit v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Směrovací tabulky definované uživatelem |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |
| Kořenové certifikáty typu Point-to-Site na službu VPN Gateway |20 |

#### <a name="publicip-address"></a>Veřejné IP adresy omezení

| Zdroj | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy – dynamická |200 (základní) |Kontaktujte podporu |
| Veřejné IP adresy – statické |200 (základní) |Kontaktujte podporu |
| Veřejné IP adresy – statické |200 (standard) |Kontaktujte podporu |

#### <a name="load-balancer"></a>Omezení nástroje pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md)

| Zdroj | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Služby vyrovnávání zatížení | 100 | 1000 |
| Pravidla pro každý prostředek, Basic | 150 | 250 |
| Pravidla pro každý prostředek, Standard | 1250 | 1500 |
| Pravidla na konfiguraci IP adresy | 299 |299 |
| Konfigurace protokolu IP front-endu, Basic | 10 | 200 |
| Konfigurace protokolu IP front-endu, Standard | 10 | 600 |
| Back-endový fond Basic | 100, jedna skupina dostupnosti | 100, jedna skupina dostupnosti |
| Back-endový fond Standard | 1 000, jedna virtuální síť | 1 000, jedna virtuální síť |
| Back-Endovým prostředkům za nástroj pro vyrovnávání zatížení, Standard &ast; | 50 | 150 |
| HA portů a Standard | 1 za interní front-endu | 1 za interní front-endu |

&ast; Až 150 zdrojů libovolnou kombinaci samostatné virtuální počítače, skupiny dostupnosti a škálovací sady virtuálních počítačů.

Pokud potřebujete navýšit výchozí omezení, [kontaktujte podporu](../articles/azure-supportability/resource-manager-core-quotas-request.md ).

