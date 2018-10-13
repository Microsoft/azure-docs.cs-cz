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
ms.date: 08/31/2018
ms.author: cynthn
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 2a119e110d9d3687f24ebf7f72b2b4506674d4e3
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310119"
---
# <a name="what-are-availability-zones-in-azure"></a>Co jsou zóny dostupnosti v Azure?
Zóny dostupnosti je vysoká dostupnost služeb, které chrání vaše aplikace a data z datacenter selhání. Zóny dostupnosti jsou jedinečná fyzická umístění v rámci oblasti Azure. Každá zóna se skládá z jednoho nebo více datových Center vybavených nezávislým napájením, chlazením a sítí. K zajištění odolnosti proti chybám, je minimálně tří samostatných zón ve všech oblastech, povolené. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Zónově redundantní služby replikaci vašich aplikací a dat napříč zónami dostupnosti pro zajištění ochrany z jednoho body z chyby. Zóny dostupnosti Azure nabízí odvětví nejlepší 99,99 % doby provozu SLA k virtuálním počítačům. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Zónu dostupnosti, do oblasti Azure je kombinace doména selhání a aktualizační doména. Například pokud vytvoříte tři nebo více virtuálních počítačů napříč zónami tři v oblasti Azure, vaše virtuální počítače jsou účinně rozloženy na tři domény selhání a aktualizačních doménách tři. Platforma Azure rozpoznává této distribuce napříč aktualizační domény, abyste měli jistotu, že virtuální počítače v různých oblastech nejsou aktualizovány ve stejnou dobu.

Sestavte vysokou dostupnost do vaší aplikace architektury společně umístěných prostředky výpočetní prostředky, úložiště, sítě a dat v rámci zóny a replikace v jiné zóně. Služby Azure, které podporují zóny dostupnosti se dělí do dvou kategorií:

- **Oblastmi služby** – Připnutí prostředků pro konkrétní zónu (například virtuální počítače, spravované disky, IP adresy), nebo
- **Zónově redundantní služby** – platforma se automaticky replikuje napříč zónami (například zónově redundantní úložiště, databáze SQL).

Pokud chcete dosáhnout komplexní obchodní kontinuity podnikových procesů v Azure, sestavení architektury aplikace pomocí kombinace zón dostupnosti s párování oblastí Azure. Můžete synchronní replikace aplikací a dat s využitím zón dostupnosti v rámci oblasti Azure pro vysokou dostupnost a asynchronní replikace v různých oblastech Azure pro ochranu zotavení po havárii.
 
![koncepční zobrazení jednu zónu směrem dolů v oblasti](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Oblasti, které podporují zóny dostupnosti

- USA – střed
- USA – východ 2 (Preview)
- Francie – střed
- Severní Evropa
- Jihovýchodní Asie (Preview)
- Západní Evropa
- USA – západ 2



## <a name="services-that-support-availability-zones"></a>Služby, které podporují zóny dostupnosti
Služby Azure, které podporují zóny dostupnosti jsou:

- Linux Virtual Machines
- Windows Virtual Machines
- Virtual Machine Scale Sets
- Managed Disks
- Load Balancer
- Veřejná IP adresa
- Zónově redundantní úložiště
- SQL Database
- Event Hubs
- Service Bus
- VPN Gateway
- ExpressRoute
- Služba Application Gateway (preview)


## <a name="pricing"></a>Ceny
Se neúčtují žádné další poplatky pro virtuální počítače nasazené v zóně dostupnosti. 99,99 % dostupnosti virtuálního počítače nabízíme smlouvy SLA při nasazení dvou nebo více virtuálních počítačů napříč zónami dostupnosti dva nebo více v rámci oblasti Azure. Bude existovat další mezi dostupnost zóny virtuálních počítačů VM poplatky za přenos dat. Další informace najdete v článku [cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/) stránky.


## <a name="get-started-with-availability-zones"></a>Začínáme se zónami dostupnosti
- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidat spravovaný Disk pomocí Powershellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření zóny redundantní virtuálního počítače škálovací sady](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Nástroj pro vyrovnávání zatížení virtuálních počítačů napříč zónami zónově redundantních front-endu pomocí Load balanceru úrovně Standard](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Nástroj pro vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Load balanceru úrovně Standard s oblastmi front-endu](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Další postup
- [Šablony Rychlý start](http://aka.ms/azqs)
