---
title: Co jsou zóny dostupnosti Azure? | Dokumenty Microsoft
description: Zóny dostupnosti v Azure vytvořit vysoce dostupné a odolné aplikace, poskytují fyzicky oddělená umístění, které lze použít ke spuštění vašich prostředků.
services: ''
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 92ef494efa62a7f4e2d0a7c88ef8efd950dd2472
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786088"
---
# <a name="what-are-availability-zones-in-azure"></a>Co jsou zóny dostupnosti v Azure?
Zóny dostupnosti je vysoká dostupnost služeb, které chrání vaše aplikace a data z datacenter selhání. Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. K zajištění odolnosti proti chybám, je minimálně tří samostatných zón ve všech oblastech, povolené. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Zónově redundantní služby replikaci vašich aplikací a dat napříč zónami dostupnosti pro zajištění ochrany z jednoho body z chyby. Zóny dostupnosti Azure nabízí odvětví nejlepší 99,99 % doby provozu SLA k virtuálním počítačům. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Zónu dostupnosti, do oblasti Azure je kombinace doména selhání a aktualizační doména. Například pokud vytvoříte tři nebo více virtuálních počítačů napříč zónami tři v oblasti Azure, vaše virtuální počítače jsou účinně rozloženy na tři domény selhání a aktualizačních doménách tři. Platforma Azure rozpoznává této distribuce napříč aktualizační domény, abyste měli jistotu, že virtuální počítače v různých oblastech nejsou aktualizovány ve stejnou dobu.

Sestavte vysokou dostupnost do vaší aplikace architektury společně umístěných prostředky výpočetní prostředky, úložiště, sítě a dat v rámci zóny a replikace v jiné zóně. Služby Azure, které podporují zóny dostupnosti se dělí do dvou kategorií:

- **Oblastmi služby** – Připnutí prostředků pro konkrétní zónu (například virtuální počítače, spravované disky, IP adresy), nebo
- **Zónově redundantní služby** – platforma se automaticky replikuje napříč zónami (například zónově redundantní úložiště, databáze SQL).

Pokud chcete dosáhnout komplexní obchodní kontinuity podnikových procesů v Azure, sestavení architektury aplikace pomocí kombinace zón dostupnosti s párování oblastí Azure. Můžete synchronní replikace aplikací a dat s využitím zón dostupnosti v rámci oblasti Azure pro vysokou dostupnost a asynchronní replikace v různých oblastech Azure pro ochranu zotavení po havárii.
 
![koncepční zobrazení jednu zónu směrem dolů v oblasti](./media/az-overview/az-graphic-two.png)

## <a name="services-support-by-region"></a>Podpora služeb podle oblastí

Kombinace služeb Azure a oblasti, které podporují zóny dostupnosti jsou:


|                                 |Amerika |              |           |           | Evropa |              |          |              | Asie a Tichomoří |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |USA – střed|USA – východ|Východní USA 2|Západní USA 2|Francie – střed|Severní Evropa|Velká Británie – jih|Západní Evropa|Japonsko – východ|Jihovýchodní Asie|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Linux Virtual Machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Windows Virtual Machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |
| Spravované disky                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zónově redundantní úložiště          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Sítě**                     |            |              |           |           |                |              |          |             |            |                |
| Standardní IP adresu        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Load Balancer úrovně Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway                     | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| ExpressRoute                    | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| Application Gateway   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    | &#10003;       | &#10003;       |
| **Databáze**                     |            |              |           |           |                |              |          |             |            |                |
| SQL Database                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |            | &#10003;       |
| **Analýzy**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |
| **Integrace**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (pouze úroveň Premium) | &#10003;   |              | &#10003;  | &#10003;  | &#10003;       | &#10003;     |          | &#10003;    |            | &#10003;       |



## <a name="services-resiliency"></a>Odolnost proti chybám služby
Všech služeb Azure pro správu dokáže odolné proti chybám v případě selhání úrovni oblasti. V celé spektrum od selhání mají nejmíň jeden chyby zóna dostupnosti v rámci oblasti menší radius chyby ve srovnání s selhání celé oblasti. Azure můžete obnovit v případě selhání zóny úrovně služeb pro správu v rámci oblasti nebo z jiné oblasti Azure. Azure provádí po jednom v rámci oblasti, aby se zabránilo chybám vliv na prostředky zákazníků nasazení napříč zónami dostupnosti v rámci oblasti kritické údržby jednu zónu.

## <a name="pricing"></a>Ceny
Se neúčtují žádné další poplatky pro virtuální počítače nasazené v zóně dostupnosti. 99,99 % dostupnosti virtuálního počítače nabízíme smlouvy SLA při nasazení dvou nebo více virtuálních počítačů napříč zónami dostupnosti dva nebo více v rámci oblasti Azure. Bude existovat další mezi dostupnost zóny virtuálních počítačů VM poplatky za přenos dat. Další informace najdete v článku [cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/) stránky.


## <a name="get-started-with-availability-zones"></a>Začínáme se zónami dostupnosti
- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidat spravovaný Disk pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření zóny redundantní virtuálního počítače škálovací sady](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Nástroj pro vyrovnávání zatížení virtuálních počítačů napříč zónami zónově redundantních front-endu pomocí Load balanceru úrovně Standard](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Nástroj pro vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Load balanceru úrovně Standard s oblastmi front-endu](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Další postup
- [Šablony Rychlý start](https://aka.ms/azqs)
