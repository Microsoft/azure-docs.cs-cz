---
title: Co jsou Zóny dostupnosti Azure?
description: Pokud chcete vytvářet vysoce dostupné a odolné aplikace v Azure, Zóny dostupnosti poskytovat fyzicky samostatná umístění, která můžete použít ke spouštění svých prostředků.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: e7ba62a96a6fc76ad63960cfe57f5b1f85589c32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085038"
---
# <a name="what-are-availability-zones-in-azure"></a>Co jsou v Azure Zóny dostupnosti?
Zóny dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny. Fyzické oddělení Zóny dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Redundantní služby v zóně replikují aplikace a data napříč Zóny dostupnosti, aby se chránily před jednotlivými chybami. Díky Zóny dostupnosti Azure nabízí nejlepší smlouvu SLA 99,99% provozu virtuálního počítače. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, budou vaše virtuální počítače efektivně distribuovány mezi tři domény selhání a tři aktualizační domény. Platforma Azure tuto distribuci rozpoznává mezi aktualizačními doménami, aby se zajistilo, že se virtuální počítače v různých zónách neaktualizují současně.

Společné umístění výpočetních operací, úložiště, sítě a datových prostředků v rámci zóny a replikace v jiných zónách vám může vytvořit vysokou dostupnost architektury aplikace. Služby Azure, které podporují zóny dostupnosti, spadají do dvou kategorií:

- **Služby** oblastí – připnutí prostředku ke konkrétní zóně (například virtuální počítače, spravované disky, standardní IP adresy) nebo
- **Zónově redundantní služby** – platforma je automaticky replikovaná mezi zónami (například zónově redundantní úložiště, databáze SQL).

Pro zajištění komplexní provozní kontinuity v Azure Sestavte architekturu aplikace pomocí kombinace Zóny dostupnosti s páry oblastí Azure. Můžete synchronně replikovat aplikace a data pomocí Zóny dostupnosti v oblasti Azure pro zajištění vysoké dostupnosti a asynchronní replikace napříč oblastmi Azure pro ochranu proti havárii.
 
![koncepční zobrazení jedné zóny v oblasti](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identifikátory zóny dostupnosti (čísla 1, 2 a 3 na obrázku výše) jsou logicky mapovány na skutečné fyzické zóny pro každé předplatné nezávisle na sobě. To znamená, že dostupnost Zóna 1 v daném předplatném může odkazovat na jinou fyzickou zónu než Zóna 1 dostupnosti v jiném předplatném. V důsledku toho se doporučuje nespoléhat na ID zón dostupnosti napříč různými předplatnými pro umístění virtuálního počítače.

## <a name="services-support-by-region"></a>Podpora služeb podle oblasti

Kombinace služeb a oblastí Azure, které podporují Zóny dostupnosti:


|                                 |Amerika |              |           |           | Evropa |              |          |              | Asie a Tichomoří |                 |                |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
|          |USA – střed|USA – východ|USA – východ 2|USA – západ 2|Francie – střed|Severní Evropa|Spojené království – jih|Západní Evropa|Japonsko – východ|Jihovýchodní Asie|Austrálie – východ|
| **Compute**                         |            |              |           |           |                |              |          |             |            |                |                |
| Linux Virtual Machines          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Windows Virtual Machines        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Virtual Machine Scale Sets      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| INTERNÍHO nástroje prostředí Azure App Service | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Azure Kubernetes Service        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Spravované disky                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Zóna – redundantní úložiště          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Sítě**                     |            |              |           |           |                |              |          |             |            |                |                |
| Standardní IP adresa        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| VPN Gateway            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Brána ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Application Gateway (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |                |
| Brána Azure Firewall           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Databáze**                     |            |              |           |           |                |              |          |             |            |                |                |
| Průzkumník dat Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Databáze SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (Preview) | &#10003;       | &#10003; (Preview)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Azure Cache for Redis           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |                |
| **Analýzy**                       |            |              |           |           |                |              |          |             |            |                |                |
| Event Hubs                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |                |
| **Integrace**                     |            |              |           |           |                |              |          |             |            |                |                |
| Service Bus (jenom úroveň Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| Event Grid | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identita**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

## <a name="services-resiliency"></a>Odolnost služeb
Všechny služby správy Azure jsou navržené tak, aby byly odolné proti selháním na úrovni jednotlivých oblastí. V případě selhání má jedna nebo více selhání zóny dostupnosti v rámci oblasti menší poloměr selhání v porovnání s selháním celé oblasti. Azure se může zotavit z neúspěšného selhání služeb správy v rámci oblasti nebo z jiné oblasti Azure. Azure provádí kritickou údržbu jedné zóny v čase v rámci určité oblasti, aby nedocházelo k chybám, které mají vliv na prostředky zákazníka nasazené napříč Zóny dostupnosti v rámci oblasti.

## <a name="pricing"></a>Ceny
Pro virtuální počítače nasazené v zóně dostupnosti se neúčtují žádné další náklady. 99,99% doba provozu virtuálního počítače se nabízí, když se v rámci jedné nebo více Zóny dostupnosti v oblasti Azure nasadí nejméně dva virtuální počítače. Budou se účtovat další poplatky za přenos dat mezi virtuálními počítači v zóně dostupnosti. Další informace najdete na stránce s [cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/) .


## <a name="get-started-with-availability-zones"></a>Začínáme s Zóny dostupnosti
- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidání spravovaného disku pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření sady škálování virtuálních počítačů v zóně redundantní](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Vyrovnávání zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer se zónou redundantního front-endu](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Přidat redundantní oblast zóny pro Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Začínáme Azure cache pro Zóny dostupnosti Redis](https://aka.ms/redis/az/getstarted)
- [Vytvoření instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Vytvoření clusteru služby Azure Kubernetes (AKS), který používá Zóny dostupnosti](../aks/availability-zones.md)

## <a name="next-steps"></a>Další kroky
- [Šablony Rychlý start](https://aka.ms/azqs)
