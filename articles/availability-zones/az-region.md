---
title: Služby Azure, které podporují zóny dostupnosti
description: Pokud chcete vytvářet vysoce dostupné a odolné aplikace v Azure, Zóny dostupnosti poskytovat fyzicky samostatná umístění, která můžete použít ke spouštění svých prostředků.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 7aff8320186ada53772fc4ff2232e8cf55585a77
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573026"
---
# <a name="azure-services-that-support-availability-zones"></a>Služby Azure, které podporují zóny dostupnosti

Microsoft Azure globální infrastruktura je navržená a vytvořená na všech úrovních k zajištění nejvyšší úrovně redundance a odolnosti vůči zákazníkům. Infrastruktura Azure se skládá z geografických oblastí, oblastí a Zóny dostupnosti, které omezují poloměr vysokého poloměru selhání, a proto omezují potenciální dopad na aplikace a data zákazníků. Zóny dostupnosti Azure konstrukce byla vyvinuta tak, aby poskytovala softwarové a síťové řešení pro ochranu před chybami datového centra a poskytovala zákazníkům větší vysokou dostupnost (HA).

Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každá zóna se skládá z jednoho nebo více datových center s nezávisle napájením, chlazením a sítí. Fyzické oddělení Zóny dostupnosti v rámci oblasti omezuje dopad na aplikace a data před selháním zóny, jako je například zahlcení velkých objemů, velké množství a přenásobení a další události, které by mohly přerušit přístup k webu, bezpečný průchod, rozšířené nástroje v době provozu a dostupnost prostředků. Zóny dostupnosti a jejich přidružená datová centra jsou navržena tak, aby v případě ohrožení jedné zóny byly služby, kapacity a dostupnost podporovány jinými Zóny dostupnosti v oblasti.

Všechny služby správy Azure jsou navržené tak, aby byly odolné proti selháním na úrovni jednotlivých oblastí. V případě selhání má jedna nebo více selhání zóny dostupnosti v rámci oblasti menší poloměr selhání v porovnání s selháním celé oblasti. Azure se může zotavit z neúspěšného selhání služeb správy v rámci oblasti. Azure provádí kritickou údržbu jedné zóny v čase v rámci určité oblasti, aby nedocházelo k chybám, které mají vliv na prostředky zákazníka nasazené napříč Zóny dostupnosti v rámci oblasti.


![koncepční zobrazení oblasti Azure se 3 zónami](./media/az-region/azure-region-availability-zones.png)


Služby Azure, které podporují Zóny dostupnosti spadají do tří kategorií: **oblast**, **redundantní zóna** a **neregionální** služby. Úlohy zákazníků je možné kategorizovat tak, aby využívaly některé z těchto scénářů architektury ke splnění výkonu a odolnosti aplikací.

- Hraniční **služby** – prostředek se dá nasadit na konkrétní, samostatně vybranou zónu dostupnosti, abyste dosáhli přísnějších požadavků na latenci nebo výkon.  Odolnost proti chybám je navržená replikací aplikací a dat do jedné nebo více zón v rámci oblasti.  Prostředky je možné připnout ke konkrétní zóně. Například virtuální počítače, spravované disky nebo standardní IP adresy je možné připnout ke konkrétní zóně, což umožňuje zvýšit odolnost díky tomu, že se jedna nebo víc instancí prostředků rozloží v různých zónách.

- **Redundantní služby v zóně** – platforma Azure replikuje prostředky a data napříč zónami.  Microsoft spravuje doručování vysoké dostupnosti, protože Azure automaticky replikuje a distribuuje instance v rámci dané oblasti.  ZRS například replikuje data ve třech zónách tak, že selhání zóny nemá vliv na HA dat. 

- **Neregionální služby** – služby jsou vždycky dostupné z geografických oblastí Azure a jsou odolné vůči výpadkům v rámci zóny a také výpadkům v rámci oblastí. 


Pro zajištění komplexní provozní kontinuity v Azure Sestavte architekturu aplikace pomocí kombinace Zóny dostupnosti s páry oblastí Azure. Můžete synchronně replikovat aplikace a data pomocí Zóny dostupnosti v oblasti Azure pro zajištění vysoké dostupnosti a asynchronní replikace napříč oblastmi Azure pro ochranu proti havárii. Pokud se chcete dozvědět víc, přečtěte si téma [vytváření řešení pro zajištění vysoké dostupnosti pomocí zóny dostupnosti](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Služby Azure podporující Zóny dostupnosti

 - Virtuální počítače starší generace nejsou uvedeny. Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md).
 - Jak je uvedeno v [oblastech a zóny dostupnosti v Azure](az-overview.md), některé služby jsou jiné než regionální. Tyto služby nemají závislost na konkrétní oblasti Azure, protože jsou odolné vůči výpadkům v rámci zóny a také k výpadkům v rámci oblastí.  Seznam neoblastních služeb najdete v [produktech dostupných v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Oblasti Azure s Zóny dostupnosti


| Amerika           | Evropa               | Afrika              | Asie a Tichomoří   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brazílie – jih       | Francie – střed       | Jihoafrická Jižní Afrika – sever * | Japonsko – východ     |
| Střední Kanada     | Německo – středozápad |                     | Southeast Asia |
| USA – střed         | Severní Evropa         |                     | Austrálie – východ |
| East US            | Spojené království – jih             |                     |                |
| USA – východ 2          | West Europe          |                     |                |
| Střed USA – jih |                      |                     |                |
| USA (Gov) – Virginia     |                      |                     |                |
| Západní USA 2        |                      |                     |                |


\* Další informace o podpoře Zóny dostupnosti a dostupných služeb v těchto oblastech vám poskytne zástupce Microsoftu pro prodej nebo zákazníky. Informace o nadcházejících oblastech, které budou podporovat Zóny dostupnosti, najdete v tématu geografické oblasti [Azure](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Služby Azure podporující Zóny dostupnosti

- Virtuální počítače starší generace nejsou uvedeny níže. Další informace najdete v tématu [předchozí generace velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md).

- Některé služby jsou neregionální, další informace najdete v tématu [oblasti a zóny dostupnosti v Azure](az-overview.md) . Tyto služby nemají závislost na konkrétní oblasti Azure, protože jsou odolné vůči výpadkům v rámci zóny a výpadkům v rámci oblastí.  Seznam neoblastních služeb najdete v [produktech dostupných v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Odolné služby zóny 

: globe_with_meridians: neregionální služby jsou vždycky dostupné z geografických oblastí Azure a jsou odolné vůči výpadkům v rámci zóny a také výpadkům v rámci oblastí.

: large_blue_diamond: odolný vůči výpadkům v rámci zóny 

**Základní služby**

|     Produkty                                                    | Odolnost             |
|-----------------------------------------------------------------|:----------------------------:|
|     Účet úložiště                                           | : large_blue_diamond:  |
|     Application Gateway (v2)                                  | : large_blue_diamond:  |
|     Azure Backup                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage Gen 2                             | : large_blue_diamond:  |
|     Trasa Azure Express                                       | : large_blue_diamond:  |
|     Veřejná IP adresa Azure                                           | : large_blue_diamond:  |
|     Azure SQL Database (Pro obecné účely vrstva)                 | : large_blue_diamond:  |
|     Azure SQL Database (úroveň Premium & Pro důležité obchodní informace)     | : large_blue_diamond:  |
|     Disk Storage                                                | : large_blue_diamond:  |
|     Event Hubs                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Service Bus                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Storage: horká a studená Blob Storage vrstva                      | : large_blue_diamond:  |
|     Úložiště: Managed Disks                                    | : large_blue_diamond:  |
|     Virtual Machines škálování sad                               | : large_blue_diamond:  |
|     Virtual Machines                                          | : large_blue_diamond:  |
|     Virtual Machines: Av2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Bs-Series                               | : large_blue_diamond:  |
|     Virtual Machines: DSv2-Series                             | : large_blue_diamond:  |
|     Virtual Machines: DSv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Dv2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Dv3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: ESv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Ev3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: řada F-Series                                | : large_blue_diamond:  |
|     Virtual Machines: FS-Series                               | : large_blue_diamond:  |
|     Virtual Network                                           | : large_blue_diamond:  |
|     VPN Gateway                                                 | : large_blue_diamond:  |


**Běžné služby**

| Produkty                                        | Odolnost |
|-------------------------------------------------|:------------:|
| Prostředí App Service                        |      : large_blue_diamond:  |
| Azure Active Directory Domain Services          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Azure Cache for Redis                           |      : large_blue_diamond:  |
| Azure Cognitive Services: Analýza textu        |      : large_blue_diamond:  |
| Průzkumník dat Azure                             |      : large_blue_diamond:  |
| Azure Database for MySQL – flexibilní Server      |      : large_blue_diamond:  |
| Azure Database for PostgreSQL – flexibilní Server |      : large_blue_diamond:  |
| Azure DDoS Protection                           |      : large_blue_diamond:  |
| Azure Disk Encryption                           |      : large_blue_diamond:  |
| Azure Firewall                                  |      : large_blue_diamond:  |
| Azure Firewall Manager                          |      : large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      : large_blue_diamond:  |
| Azure Private Link                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Azure SQL: virtuální počítač                      |      : large_blue_diamond:  |
| Azure Search                                    |      : large_blue_diamond:  |
| Azure Web Application Firewall                  |      : large_blue_diamond:  |
| Cognitive Services: Analýza textu              |      : large_blue_diamond:  |
| Container Registry                              |      : large_blue_diamond:  |
| Event Grid                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Network Watcher: Analýza provozu              |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Blob Storage úrovně Premium                            |      : large_blue_diamond:  |
| Storage: soubory Azure Premium                    |      : large_blue_diamond:  |
| Virtual Machines: vyhrazený hostitel Azure          |      : large_blue_diamond:  |
| Virtual Machines: Ddsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Ddv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dsv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dv4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Edsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Edv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Esv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Ev4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Fsv2-Series                   |      : large_blue_diamond:  |
| Virtual Machines: řada M-Series                      |      : large_blue_diamond:  |
| Virtuální síť WAN                                     |      : large_blue_diamond:  |
| Virtuální síť WAN: ExpressRoute                       |      : large_blue_diamond:  |
| Virtuální síť WAN: připojení typu Point-to-Site VPN Gateway          |      : large_blue_diamond:  |
| Virtuální síť WAN: VPN Gateway typu Site-to-site           |      : large_blue_diamond:  |


**Bez regionu**

|     Produkty                                  |     Odolnost    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Advanced Threat Protection          |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Azure Blueprints                          |     : globe_with_meridians:             |
|     Azure Bot Services                        |     : globe_with_meridians:             |
|     Azure Front Door                          |     : globe_with_meridians:             |
|     Azure Defender pro IoT                  |     : globe_with_meridians:             |
|     Azure Front Door                           |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Lighthouse Azure                        |     : globe_with_meridians:             |
|     Azure Managed Applications              |     : globe_with_meridians:             |
|     Azure Maps                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Graf prostředků Azure                    |     : globe_with_meridians:             |
|     Azure Sentinel                            |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack Edge                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Content Delivery Network                  |     : globe_with_meridians:             |
|     Správa nákladů                           |     : globe_with_meridians:             |
|     Customer Lockbox pro Microsoft Azure    |     : globe_with_meridians:             |
|     Intune                                    |     : globe_with_meridians:             |
|     Služba partnerského vztahu Microsoft Azure         |     : globe_with_meridians:             |
|     portál Microsoft Azure                  |     : globe_with_meridians:             |
|     Microsoft Cloud App Security              |     : globe_with_meridians:             |
|     Microsoft Graph                           |     : globe_with_meridians:             |
|     Security Center                         |     : globe_with_meridians:             |
|     Traffic Manager                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Ceny pro virtuální počítače v Zóny dostupnosti

Pro virtuální počítače nasazené v zóně dostupnosti se neúčtují žádné další náklady. Další informace najdete na stránce s [cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Začínáme s Zóny dostupnosti

- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidání spravovaného disku pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření sady škálování virtuálních počítačů v zóně redundantní](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Vyrovnávání zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer se zónou redundantního front-endu](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy.md)
- [SQL Database úroveň pro obecné účely](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Přidat redundantní oblast zóny pro Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Začínáme Azure cache pro Zóny dostupnosti Redis](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Vytvoření instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Vytvoření clusteru služby Azure Kubernetes (AKS), který používá Zóny dostupnosti](../aks/availability-zones.md)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Oblasti a zóny dostupnosti v Azure](az-overview.md)
