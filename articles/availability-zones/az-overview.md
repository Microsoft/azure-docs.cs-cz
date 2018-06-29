---
title: Jaké jsou zóny dostupnosti Azure? | Dokumenty Microsoft
description: K vytvoření vysoce dostupné a odolné aplikací v Azure, zadejte dostupnost zóny fyzicky oddělené umístění, které můžete použít ke spuštění vaše prostředky.
services: ''
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: iainfou
ms.custom: mvc I am an ITPro and application developer, and I want to protect (use Availability Zones) my applications and data against data center failure (to build Highly Available applications).
ms.openlocfilehash: 6a4dcc2cd3b196221b881783c79ddb0adaa6f38b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063659"
---
# <a name="what-are-availability-zones-in-azure"></a>Jaké jsou dostupnost zóny v Azure?
Dostupnost zóny je vysoká dostupnost nabízející, které chrání vaše aplikace a data před selháním datového centra. Dostupnost zóny jsou jedinečné fyzické umístění v rámci oblasti Azure. Každé zóny se skládá z jedné nebo více datových centrech vybaven nezávislé napájení, chlazení a práci v síti. K zajištění odolnosti, je minimálně tři samostatné zóny ve všech oblastech povoleno. Fyzické oddělení dostupnost zóny v rámci oblasti chrání aplikace a data před selháním datového centra. Zónově redundantní služby replikaci mezi zón dostupnosti pro ochranu před jedním bodů z selháním aplikacím a datům. Dostupnost zón Azure nabízí odvětví nejlepší 99,99 % virtuálních počítačů smlouva SLA. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Dostupnosti zónu v oblasti Azure je kombinací domény selhání a aktualizaci domény. Například pokud vytvoříte tři nebo více virtuálních počítačů mezi tři zóny v oblasti Azure, virtuální počítače jsou efektivně rozloženy na tři domén selhání a tři domény aktualizace. Platformy Azure rozpozná této distribuce napříč doménami update a ujistěte se, že virtuální počítače v různých oblastech neaktualizují ve stejnou dobu.

Sestavte vysokou dostupností do vaší aplikace architektury společné umísťování prostředkům výpočty, úložiště, sítě a data v rámci zóny a replikuje v jiných oblastech. Služby Azure, které podporují dostupnost zóny rozdělit do dvou kategorií:

- **Oblastmi služby** – prostředek pro konkrétní zónu (například virtuální počítače, spravované disky, IP adresy), připnete nebo
- **Zónově redundantní služby** – platforma automaticky replikuje přes zóny (například zónově redundantní úložiště, databáze SQL).

Zajistit komplexní provozní kontinuitu v Azure vytvořte architektuře aplikace pomocí kombinace dostupnost zóny s páry oblast Azure. Můžete replikovat synchronně vaší aplikace a data pomocí dostupnost zóny v rámci oblasti Azure pro vysokou dostupnost a asynchronně replikovat mezi oblastmi Azure pro ochranu pro zotavení po havárii.
 
![koncepční zobrazení jednu zónu směrem dolů v oblasti.](./media/az-overview/az-graphic-two.png)

## <a name="regions-that-support-availability-zones"></a>Oblasti, které podporují dostupnost zóny

- Střed USA
- Francie – střed
- Východní USA 2 (Preview)
- Západní Evropa
- Asie a Tichomoří – jihovýchod (Preview)


## <a name="services-that-support-availability-zones"></a>Služby, které podporují dostupnost zóny
Služby Azure, které podporují dostupnost zóny jsou:

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


## <a name="pricing"></a>Ceny
Není k dispozici pro virtuální počítače nasazené v zóně dostupnosti bez dalších nákladů. 99,99 % dostupnost virtuálních počítačů se SLA nabízí při nasazení dvou nebo více virtuálních počítačů mezi dva nebo víc zón dostupnosti v rámci oblasti Azure. Budou existovat další poplatky přenos dat mezi dostupnost zóny virtuálních počítačů VM. Další informace najdete v článku [šířky pásma ceny](https://azure.microsoft.com/pricing/details/bandwidth/) stránky.


## <a name="get-started-with-availability-zones"></a>Začínáme s dostupnost zóny
- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidejte Disk spravované pomocí prostředí PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření sady škálování zóny redundantní virtuální počítač](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Nástroj pro vyrovnávání zatížení virtuálních počítačů mezi různými pásmy pomocí standardní Vyrovnávání zatížení s front-end zónově redundantní](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Nástroj pro vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí standardní Vyrovnávání zatížení s front-end oblastmi](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration-preview)
- [Obnovení geograficky havárii centra událostí](../event-hubs/event-hubs-geo-dr.md#availability-zones-preview)
- [Geograficky havárii Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones-preview)
- [Vytvoření brány virtuální sítě zónově redundantní](../vpn-gateway/create-zone-redundant-vnet-gateway.md)


## <a name="next-steps"></a>Další postup
- [Šablony Rychlý start](http://aka.ms/azqs)
