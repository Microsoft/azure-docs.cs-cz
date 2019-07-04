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
ms.openlocfilehash: 3e66bf61caf786473d89eab9a6567bb05aff0d19
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457283"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Síťová omezení – Azure Resource Manageru následující omezení platí pouze pro síťové prostředky spravované přes **Azure Resource Manageru** na oblast a předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Zvětšili jsme nedávno všechny výchozí omezení maximální mezí. Pokud neexistuje žádný sloupec maximálního limitu, nemá zdroj nastavitelná omezení. Pokud jste tyto limity zvýšit podpora v minulosti a nezobrazí aktualizované omezení v následujících tabulkách [otevřete žádost o podporu online zákazníků bez poplatků](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Výchozí/maximální limit | 
| --- | --- |
| Virtuální sítě |1 000 |
| Podsítě na virtuální síť |3 000 |
| Partnerské vztahy virtuálních sítí na virtuální síť |500 |
| Servery DNS na virtuální síť |20 |
| Privátní IP adresy na virtuální síť |65,536 |
| Privátní IP adresy na síťové rozhraní |256 |
| Privátní IP adresy na virtuální počítač |256 |
| Souběžné TCP nebo UDP toky pro síťové rozhraní virtuálního počítače nebo role instance |500,000 |
| Síťové karty |65,536 |
| Network Security Groups (Skupiny zabezpečení sítě) |5 000 |
| Počet pravidel NSG na skupinu NSG |1 000 |
| IP adresy a rozsahy zadané pro zdroj nebo cíl ve skupině zabezpečení |4,000 |
| Skupiny zabezpečení aplikací |3 000 |
| Skupiny zabezpečení aplikace podle konfigurace protokolu IP pro síťové rozhraní |20 |
| Konfigurace IP na skupiny zabezpečení aplikací |4,000 |
| Skupiny zabezpečení aplikací, které se dá nastavit v rámci všech pravidel zabezpečení skupiny zabezpečení sítě |100 |
| Uživatelem definovaná trasa tabulky |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |
| Point-to-site kořenových certifikátů na službu Azure VPN Gateway |20 |
| Klepne virtuální sítě |100 |
| Klepněte na konfiguraci síťového rozhraní na virtuální síť, klepněte na |100 |

#### <a name="publicip-address"></a>Veřejné IP adresy omezení
| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Veřejné IP adresy – dynamická | 1000 pro jazyk Basic. |Kontaktujte podporu. |
| Veřejné IP adresy – statické | 1000 pro jazyk Basic. |Kontaktujte podporu. |
| Veřejné IP adresy – statické | 200 pro úroveň Standard.|Kontaktujte podporu. |
| Délka předpony veřejných IP | /28 | Kontaktujte podporu. |

#### <a name="load-balancer"></a>Omezení nástroje pro vyrovnávání zatížení
Následující omezení platí pouze pro síťové prostředky spravované přes Azure Resource Manager na oblast a předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

| Resource | Výchozí/maximální limit |
| --- | --- |
| Nástroje pro vyrovnávání zatížení | 1 000 | 
| Pravidla pro každý prostředek, Basic | 250 |
| Pravidla pro každý prostředek, Standard | 1,500 | 
| Pravidla na konfiguraci IP adresy | 299 |
| Pravidla pro síťové rozhraní | 300 |
| Front-endové konfigurace protokolu IP, Basic | 200 |
| Front-endové konfigurace protokolu IP, Standard | 600 |
| Back endový fond Basic | 100, jedné skupiny dostupnosti |
| Back endový fond Standard | 1 000, jedné virtuální sítě |
| Back endové prostředky za nástroj pro vyrovnávání zatížení, standardní<sup>1</sup> | 150 |
| Vysoké dostupnosti portů, standardní | 1 za interní front-endu |

<sup>1</sup>až 150 prostředky v libovolné kombinace prostředků samostatný virtuální počítač je limit, skupinu dostupnosti, prostředky a prostředky škálovací sady virtuálních počítačů.

#### <a name="virtual-networking-limits-classic"></a>Následující omezení platí pouze pro síťové prostředky spravované přes **classic** model nasazení na předplatné. Zjistěte, jak [zobrazit aktuální využití prostředků pro vaše předplatné omezení](../articles/networking/check-usage-against-limits.md).

| Resource | Výchozí omezení | Maximální omezení |
| --- | --- | --- |
| Virtuální sítě |100 |100 |
| Místní síťové lokality |20 |Kontaktujte podporu. |
| Servery DNS na virtuální síť |20 |20 |
| Privátní IP adresy na virtuální síť |4,096 |4,096 |
| Souběžné TCP nebo UDP toky pro síťové rozhraní virtuálního počítače nebo role instance |500 000 až 1 000 000 pro nejmíň dva síťové adaptéry. |500 000 až 1 000 000 pro nejmíň dva síťové adaptéry. |
| Skupiny zabezpečení sítě (Nsg) |200 |200 |
| Počet pravidel NSG na skupinu NSG |1 000 |1 000 |
| Uživatelem definovaná trasa tabulky |200 |200 |
| Trasy definované uživatelem na směrovací tabulku |400 |400 |
| Veřejné IP adresy (dynamické) |500 |500 |
| Vyhrazené veřejné IP adresy |500 |500 |
| Veřejné virtuální IP adresy na nasazení |5 |Kontaktujte podporu |
| Privátní virtuální IP adresy (interní Vyrovnávání zatížení) na nasazení |1 |1 |
| Seznamy koncový bod řízení přístupu (ACL) |50 |50 |
