---
title: Co je privátní propojení Azure?
description: Přehled funkcí, architektury a implementace privátních odkazů Azure Přečtěte si, jak fungují privátní koncové body Azure a služba privátního propojení Azure a jak je používat.
services: private-link
author: asudbring
ms.service: private-link
ms.topic: overview
ms.date: 03/15/2021
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 6a85bfe7b3390b32fc220000b0c710b5a4e35067
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496486"
---
# <a name="what-is-azure-private-link"></a>Co je privátní propojení Azure? 
Privátní odkaz Azure vám umožňuje přístup ke službám Azure PaaS (například Azure Storage a SQL Database) a službám, které hostuje zákaznická/Partnerská služba Azure, prostřednictvím [privátního koncového bodu](private-endpoint-overview.md) ve vaší virtuální síti.

Provoz mezi vaší virtuální sítí a službou přenáší páteřní síť Microsoftu. Vystavení vaší služby pro veřejný Internet již není nutné. Ve virtuální síti můžete vytvořit vlastní [službu privátního propojení](private-link-service-overview.md) a předat ji zákazníkům. Nastavení a spotřeba pomocí privátního propojení Azure jsou konzistentní v rámci služeb Azure PaaS, Customer a Shared partnered.

> [!IMPORTANT]
> Privátní propojení Azure je teď všeobecně dostupné. K dispozici jsou všeobecně dostupné služby privátního i privátního koncového bodu (služba za službu Load Balancer úrovně Standard). Různé služby Azure PaaS se budou připojovat k privátnímu propojení Azure v různých plánech. Přesný stav služby Azure PaaS na privátním odkazu najdete v tématu [dostupnost privátních odkazů](availability.md) . Známá omezení najdete v tématu [privátní koncový bod](private-endpoint-overview.md#limitations) a [Služba privátního propojení](private-link-service-overview.md#limitations). 

:::image type="content" source="./media/private-link-overview/private-link-center.png" alt-text="Centrum privátních odkazů Azure v Azure Portal" border="false":::

## <a name="key-benefits"></a>Klíčové výhody
Privátní propojení Azure přináší následující výhody:  
- **Služby soukromého přístupu na platformě Azure**: Připojte svoji virtuální síť ke službám v Azure bez veřejné IP adresy ve zdroji nebo cíli. Poskytovatelé služeb mohou vykreslovat své služby ve své vlastní virtuální síti a příjemci mají přístup k těmto službám ve své místní virtuální síti. Platforma privátního propojení bude zpracovávat připojení mezi příjemcem a službami přes páteřní síť Azure. 
 
- **Místní a partnerské sítě**: přístup ke službám, které běží v Azure, prostřednictvím privátního partnerského vztahu ExpressRoute, tunelových propojení VPN a partnerských virtuálních sítí s použitím privátních koncových bodů. Pro přístup k této službě není nutné konfigurovat ExpressRoute partnerský vztah Microsoftu ani procházet internetem. Privátní odkaz poskytuje zabezpečený způsob migrace úloh do Azure.
 
- **Ochrana před únikem dat**: privátní koncový bod je namapován na instanci prostředku PaaS namísto celé služby. Příjemci se můžou připojit jenom ke konkrétnímu prostředku. Přístup k jakémukoli jinému prostředku ve službě je blokovaný. Tento mechanismus zajišťuje ochranu před riziky úniku dat. 
 
- **Globální dosah**: Připojte soukromě ke službám běžícím v jiných oblastech. Virtuální síť příjemce může být v oblasti A a může se připojit ke službám za soukromým odkazem v oblasti B.  
 
- **Rozšiřování na vlastní služby**: umožňuje stejné prostředí a funkce pro vlastní vygenerování služby pro uživatele v Azure. Umístěním služby za standardní Azure Load Balancer můžete povolit pro privátní propojení. Příjemce se pak může připojit přímo k vaší službě pomocí privátního koncového bodu ve své vlastní virtuální síti. Žádosti o připojení můžete spravovat pomocí toku volání schválení. Privátní propojení Azure funguje pro zákazníky a služby patřící různým klientům Azure Active Directory. 

## <a name="availability"></a>Dostupnost 

Informace o službách Azure, které podporují privátní propojení, najdete v tématu [dostupnost privátního propojení Azure](availability.md).

Nejaktuálnější oznámení najdete na [stránce s aktualizacemi privátních odkazů Azure](https://azure.microsoft.com/updates/?product=private-link).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Privátní propojení Azure má integraci s Azure Monitor. Tato kombinace umožňuje:

 - Archivace protokolů do účtu úložiště.
 - Streamování událostí do centra událostí.
 - Protokolování Azure Monitor

Na Azure Monitor můžete získat přístup k následujícím informacím: 
- **Privátní koncový bod**: 
    - Data zpracovaná privátním koncovým bodem (v/v)
 
- **Služba privátního propojení**:
    - Data zpracovaná službou privátního propojení (v/v)
    - Dostupnost portu NAT  
 
## <a name="pricing"></a>Ceny   
Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Nejčastější dotazy  
Nejčastější dotazy najdete v tématu [Nejčastější dotazy k privátním linkám Azure](private-link-faq.md).
 
## <a name="limits"></a>Omezení  
Omezení najdete v tématu [omezení privátních propojení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="service-level-agreement"></a>smlouva SLA
Pro smlouvu SLA si přečtěte téma věnované [smlouvě SLA pro privátní propojení Azure](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/).

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: Vytvoření privátního koncového bodu pomocí Azure Portal](create-private-endpoint-portal.md)
- [Rychlý Start: vytvoření služby privátního propojení pomocí Azure Portal](create-private-link-service-portal.md)
