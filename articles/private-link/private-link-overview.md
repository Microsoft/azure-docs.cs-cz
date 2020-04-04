---
title: Co je privátní propojení Azure?
description: Přehled funkcí Azure Private Link, architektury a implementace. Zjistěte, jak azure privátní koncové body a služba Azure Private Link funguje a jak je používat.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 1bef4e5f4129ddc8300d61d609392ce0b07b74b8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656255"
---
# <a name="what-is-azure-private-link"></a>Co je privátní propojení Azure? 
Azure Private Link umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám Azure hostovaným zákazníkům nebo partnerským službám prostřednictvím [privátního koncového bodu](private-endpoint-overview.md) ve vaší virtuální síti.

Provoz mezi vaší virtuální sítí a službou putuje páteřní sítí Microsoftu. Vystavení vaší služby veřejnému internetu již není nutné. Můžete vytvořit vlastní [službu privátního propojení](private-link-service-overview.md) ve virtuální síti a doručit ji svým zákazníkům. Nastavení a využití pomocí Privátního propojení Azure je konzistentní napříč Azure PaaS, službami vlastněnými zákazníky a sdílenými partnerskými službami.

> [!IMPORTANT]
> Azure Private Link je teď obecně dostupné. Služba Private Endpoint a Private Link (služba za standardním vyrovnáváním zatížení) jsou obecně dostupné. Různé Azure PaaS bude na palubě Azure Private Link v různých plánech. Přesný stav Azure PaaS na private link najdete v části [dostupnosti](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) níže. Známá omezení naleznete [v tématu Private Endpoint](private-endpoint-overview.md#limitations) and [Private Link Service](private-link-service-overview.md#limitations). 

![Soukromý koncový bod – přehled](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Klíčové výhody
Azure Private Link poskytuje následující výhody:  
- **Soukromě přístup ke službám na platformě Azure:** Připojte svou virtuální síť ke službám v Azure bez veřejné IP adresy u zdroje nebo cíle. Poskytovatelé služeb mohou své služby vykreslovat ve své vlastní virtuální síti a spotřebitelé k nim mají přístup ve své místní virtuální síti. Platforma Private Link bude zpracovávat připojení mezi spotřebitelem a službami přes páteřní síť Azure. 
 
- **Místní a partnerské sítě:** Přístup ke službám spuštěným v Azure z místního soukromého partnerského vztahu, tunelových propojení VPN a partnerských virtuálních sítí pomocí privátních koncových bodů. Není třeba nastavit veřejný partnerský vztah nebo procházet internetem, abyste se dostali ke službě. Private Link poskytuje bezpečný způsob migrace úloh do Azure.
 
- **Ochrana proti úniku dat**: Soukromý koncový bod je mapován na instanci prostředku PaaS namísto celé služby. Spotřebitelé se mohou připojit pouze k určitému prostředku. Přístup k jinému prostředku ve službě je blokován. Tento mechanismus poskytuje ochranu před riziky úniku dat. 
 
- **Globální dosah**: Soukromě se připojte ke službám spuštěným v jiných oblastech. Virtuální síť spotřebitele může být v oblasti A a může se připojit ke službám za private link v oblasti B.  
 
- **Rozšířit na své vlastní služby**: Povolte stejné prostředí a funkce a vykreslete své služby soukromě spotřebitelům v Azure. Umístěním služby za standardní Azure Balancer, můžete povolit pro private link. Příjemce se pak může připojit přímo k vaší službě pomocí privátního koncového bodu ve vlastní virtuální síti. Požadavky na připojení můžete spravovat pomocí toku volání schválení. Azure Private Link funguje pro spotřebitele a služby patřící do různých klientů Služby Azure Active Directory. 

## <a name="availability"></a>Dostupnost 
 V následující tabulce jsou uvedeny služby Private Link a oblasti, kde jsou k dispozici. 

|Scénář  |Podporované služby  |Dostupné oblasti | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Privátní odkaz pro služby vlastněné zákazníky |Privátní linkové služby za standardním službou Azure Load Balancer | Všechny veřejné regiony  | GA <br/> [Další informace](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Privátní propojení pro služby Azure PaaS   | Azure Storage        |  Všechny veřejné regiony      | GA <br/> [Další informace](/azure/storage/common/storage-private-endpoints)  |
|  | Azure Data Lake Storage Gen2        |  Všechny veřejné regiony      | GA <br/> [Další informace](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Všechny veřejné regiony      |   GA <br/> [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Azure Synapse Analytics (DATOVÝ SKLAD SQL)| Všechny veřejné regiony |GA <br/> [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  Všechny veřejné regiony |GA <br/> [Další informace](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Databáze Azure pro PostgreSQL – jeden server         | Všechny veřejné regiony      |   GA <br/> [Další informace](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Všechny veřejné regiony      |   GA <br/> [Další informace](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Všechny veřejné regiony      |   GA <br/> [Další informace](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Všechny veřejné regiony      |   GA   <br/> [Další informace](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Služba Azure Kubernetes – rozhraní API Kubernetes | Všechny veřejné regiony      |   GA   <br/> [Další informace](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | VÝCHODNÍ USA, ZÁPADNÍ USA 2, JIŽNÍ STŘED USA |   Preview    |
|  |Azure Container Registry | Všechny veřejné regiony      |   Preview   |
|  |Azure App Configuration | Všechny veřejné regiony      |   Preview   |
|  |Azure Backup | VÝCHODNÍ USA, ZÁPADNÍ USA 2, JIŽNÍ STŘED USA     |   Preview   |
|  |Azure Event Hub | Všechny veřejné regiony      |   Preview    |
|  |Azure Service Bus | Všechny veřejné regiony      |   Preview   |
|  |Azure Relay | Všechny veřejné regiony      |   Preview   |
|  |Azure Event Grid| VÝCHODNÍ USA, ZÁPADNÍ USA 2, JIŽNÍ STŘED USA      |   Preview   <br/> [Další informace](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | VÝCHODNÍ USA, ZÁPADNÍ USA 2, JIŽNÍ STŘED USA      |   Preview   <br/> [Další informace](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|  |Azure Machine Learning | VÝCHODNÍ USA, ZÁPADNÍ USA 2, JIŽNÍ STŘED USA      |   Preview   <br/> [Další informace](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |

Nejaktuálnější oznámení najdete na stránce [aktualizace virtuální sítě Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Azure Private Link má integraci s Azure Monitor. Tato kombinace umožňuje:

 - Archivace protokolů do účtu úložiště.
 - Streamování událostí do centra událostí.
 - Protokolování Azure Monitor.

Na Azure Monitoru máte přístup k následujícím informacím: 
- **Soukromý koncový bod**: 
    - Data zpracovaná privátním koncovým bodem (IN/OUT)
 
- **Služba Private Link**:
    - Data zpracovávaná službou Private Link (IN/OUT)
    - Dostupnost portu NAT  
 
## <a name="pricing"></a>Ceny   
Podrobnosti o cenách najdete v článku [Ceny Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Nejčastější dotazy  
Nejčastější dotazy najdete v [tématu Nejčastější dotazy k privátním uponekm ům v Azure](private-link-faq.md).
 
## <a name="limits"></a>Omezení  
Omezení najdete v tématu [limity privátního propojení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>Smlouva o úrovni služeb
SLA najdete v článku [SLA pro Privátní propojení Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka: Vytvoření privátního koncového bodu pomocí portálu Azure](create-private-endpoint-portal.md)
- [Úvodní příručka: Vytvoření služby Private Link pomocí portálu Azure](create-private-link-service-portal.md)


 
