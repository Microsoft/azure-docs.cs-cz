---
title: Co jsou zóny dostupnosti Azure?
description: Chcete-li vytvořit vysoce dostupné a odolné aplikace v Azure, zóny dostupnosti poskytují fyzicky samostatná umístění, která můžete použít ke spuštění prostředků.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 9f1b0f1885019c75252a4c5b8333f342660fac0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057274"
---
# <a name="what-are-availability-zones-in-azure"></a>Co jsou zóny dostupnosti v Azure?
Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data před selháním datového centra. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny. Fyzické oddělení zón dostupnosti v rámci oblasti chrání aplikace a data před selháním datového centra. Zónově redundantní služby replikují vaše aplikace a data napříč zónami dostupnosti, aby byly chráněny před jediným bodem selhání. Díky zónám dostupnosti nabízí Azure nejlepší 99,99% dostupnost služby SLA virtuálního počítače. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, vaše virtuální počítače se efektivně distribuují mezi tři domény selhání a tři aktualizační domény. Platforma Azure rozpoznává tuto distribuci mezi aktualizačními doménami, aby se ujistila, že virtuální počítače v různých zónách nejsou aktualizovány současně.

Zařaďte vysokou dostupnost do architektury aplikace tak, že spoluložte výpočetní, úložné, síťové a datové prostředky v rámci zóny a replikujte se v jiných zónách. Služby Azure, které podporují zóny dostupnosti, spadají do dvou kategorií:

- **Zonální služby** – připnete prostředek do určité zóny (například virtuální počítače, spravované disky, standardní IP adresy) nebo
- **Zónově redundantní služby** – platforma je automaticky replikovaná mezi zónami (například zónově redundantní úložiště, databáze SQL).

Chcete-li dosáhnout komplexní kontinuity podnikání v Azure, sestavte architekturu aplikací pomocí kombinace zón dostupnosti s dvojicemi oblastí Azure. Můžete synchronně replikovat vaše aplikace a data pomocí zón dostupnosti v rámci oblasti Azure pro vysokou dostupnost a asynchronně replikovat napříč oblastmi Azure pro ochranu zotavení po havárii.
 
![koncepční pohled na jednu zónu, která se v regionu protíná](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identifikátory zóny dostupnosti (čísla 1, 2 a 3 na obrázku výše) jsou logicky mapovány na skutečné fyzické zóny pro každé předplatné nezávisle. To znamená, že zóna dostupnosti 1 v daném předplatném může odkazovat na jinou fyzickou zónu než zónu dostupnosti 1 v jiném předplatném. V důsledku toho se doporučuje nespoléhat na ID zóny dostupnosti v různých předplatných pro umístění virtuálního počítače.

## <a name="services-support-by-region"></a>Podpora služeb podle regionů

Kombinace služeb Azure a oblastí, které podporují zóny dostupnosti jsou:


|                                 |Amerika |              |           |           | Evropa |              |          |              | Asie a Tichomoří |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |USA – střed|USA – východ|USA – východ 2|USA – západ 2|Francie – střed|Severní Evropa|Spojené království – jih|Západní Evropa|Japonsko – východ|Jihovýchodní Asie|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |
| Linux Virtual Machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Virtuální počítače s Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Škálovací sady virtuálních počítačů      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| ILB prostředí služby Azure App Service | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Úložiště**   |            |              |           |           |                |              |          |             |            |                |
| Spravované disky                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Zónově redundantní úložiště          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Síťové služby**                     |            |              |           |           |                |              |          |             |            |                |
| Standardní IP adresa        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Standardní balancer zatížení     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Brána ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Aplikační brána (V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Brána Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Databáze**                     |            |              |           |           |                |              |          |             |            |                |
| Průzkumník dat Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| Databáze SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003; (náhled)      | &#10003;       |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analýza**                       |            |              |           |           |                |              |          |             |            |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integrace**                     |            |              |           |           |                |              |          |             |            |                |
| Service Bus (pouze úroveň Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identita**                     |            |              |           |           |                |              |          |             |            |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Odolnost služeb
Všechny služby správy Azure jsou navrženy tak, aby byly odolné vůči selhání mno žahe. Ve spektru selhání má jedna nebo více selhání zóny dostupnosti v rámci oblasti menší poloměr selhání ve srovnání s celou oblastí selhání. Azure se může zotavit z selhání služeb správy na úrovni zóny v rámci oblasti nebo z jiné oblasti Azure. Azure provádí kritickou údržbu po jedné zóně v rámci oblasti, aby se zabránilo selhání majícím selháním ovlivňujícím prostředky zákazníků nasazené napříč zónami dostupnosti v rámci oblasti.

## <a name="pricing"></a>Ceny
Virtuální počítače nasazené v zóně dostupnosti se neplatí žádné další náklady. 99,99% dostupnost i v případě, že dva nebo více virtuálních počítačů se nasadí ve dvou nebo více zónách dostupnosti v rámci oblasti Azure. Budou účtovány další poplatky za přenos dat mezi zónami dostupnosti. Další informace naleznete na stránce [ceny šířky pásma.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Začínáme se zónami dostupnosti
- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidání spravovaného disku pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření zónově redundantní škálovací sady virtuálních strojů](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Virtuální aplikace pro vyrovnávání zatížení napříč zónami pomocí standardního vykladače zatížení se zónově redundantním front-endem](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Virtuální počítače s vyrovnáváním zatížení v rámci zóny pomocí standardního vykladače zatížení se zónovou frontendem](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy-zrs.md)
- [Databáze SQL](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Přidání nadbytečné oblasti zóny pro Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Začínáme azure cache pro zóny dostupnosti Redis](https://aka.ms/redis/az/getstarted)
- [Vytvoření instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Vytvoření clusteru služby Azure Kubernetes Service (AKS), který používá zóny dostupnosti](../aks/availability-zones.md)

## <a name="next-steps"></a>Další kroky
- [Šablony Rychlý start](https://aka.ms/azqs)
