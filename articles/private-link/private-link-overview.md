---
title: Co je privátní propojení Azure?
description: Naučte se používat privátní propojení Azure k přístupu ke službám Azure PaaS (například Azure Storage a SQL Database) a službám Azure Hosted Customer/Partnerská služba prostřednictvím privátního koncového bodu ve vaší virtuální síti.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 11/04/2019
ms.author: allensu
ms.openlocfilehash: 127807a57598fcfe239db39cb9874bca955da44e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647352"
---
# <a name="what-is-azure-private-link-preview"></a>Co je privátní propojení Azure? (Preview)
Privátní odkaz Azure vám umožní přístup ke službám Azure PaaS (například Azure Storage, Azure Cosmos DB a SQL Database) a hostovaným zákazníkům a partnerským službám Azure prostřednictvím [privátního koncového bodu](private-endpoint-overview.md) ve vaší virtuální síti. Provoz mezi vaší virtuální sítí a službou prochází přes páteřní síť Microsoftu a eliminuje rizika vystavení na veřejném internetu. Ve své virtuální síti můžete také vytvořit vlastní [službu privátního propojení](private-link-service-overview.md) a poskytnout ji soukromým uživatelům. Prostředí pro nastavení a spotřebu pomocí privátního propojení Azure je konzistentní v rámci služeb Azure PaaS, Customer a Shared partnered.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Známá omezení najdete v tématu [privátní koncový bod](private-endpoint-overview.md#limitations) a [Služba privátního propojení](private-link-service-overview.md#limitations).


![Přehled privátního koncového bodu](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Klíčové výhody
Privátní propojení Azure přináší následující výhody:  
- **Služby soukromého přístupu na platformě Azure**: Připojte virtuální síť ke službám běžícím v Azure Private bez nutnosti veřejné IP adresy ve zdroji nebo cíli. Poskytovatelé služeb můžou své služby vykreslovat soukromě ve své vlastní virtuální síti a příjemci mají přístup k těmto službám soukromě ve své místní virtuální síti. Platforma privátního propojení bude zpracovávat připojení mezi příjemcem a službami přes páteřní síť Azure. 
 
- **Místní a partnerské sítě**: přístup ke službám běžícím v Azure z místního prostředí přes tunely privátního partnerského vztahu/sítě VPN ExpressRoute (z místního) a partnerských virtuálních sítí s použitím privátních koncových bodů. Pro přístup ke službě není nutné nastavovat veřejný partnerský vztah ani procházet internetem. Tato možnost poskytuje zabezpečený způsob migrace úloh do Azure.
 
- **Ochrana proti exfiltrace dat**: pomocí privátního propojení Azure se privátní koncový bod ve virtuální síti namapuje na konkrétní instanci prostředku PaaS zákazníka, a to na rozdíl od celé služby. Použití příjemců privátního koncového bodu se může připojit pouze ke konkrétnímu prostředku a nikoli k žádnému jinému prostředku ve službě. V sestavách mechanismu je zajištěna ochrana před riziky exfiltrace dat. 
 
- **Globální dosah**: Připojte soukromě ke službám běžícím v jiných oblastech. To znamená, že virtuální síť příjemce může být v oblasti a a může se připojit ke službám za soukromým odkazem v oblasti B.  
 
- **Přihlaste se ke svým vlastním službám**: Využijte stejné možnosti a funkce, abyste svým uživatelům mohli soukromě vykreslit vlastní službu pro uživatele v Azure. Umístěním služby za Standard Load Balancer můžete povolit pro privátní propojení. Příjemce se pak může připojit přímo k vaší službě pomocí privátního koncového bodu ve své vlastní virtuální síti. Tyto požadavky na připojení můžete spravovat pomocí jednoduchého toku volání schválení. Privátní propojení Azure funguje i pro zákazníky a služby patřící do různých tenantů služby Active Directory. 

## <a name="availability"></a>Dostupnost 
 V následující tabulce jsou uvedeny služby privátních odkazů a oblasti, kde jsou k dispozici. 

|Scénář  |Podporované služby  |Dostupné oblasti | Stav  |
|:---------|:-------------------|:-----------------|:--------|
|Soukromý odkaz pro služby vlastněné zákazníkem|Služby privátních odkazů za Standard Load Balancer | Všechny veřejné oblasti  | Preview  |
|Privátní odkaz pro služby Azure PaaS   | Azure Storage        |  Všechny veřejné oblasti      | Preview <br/> [Další informace](/azure/storage/common/storage-private-endpoints).  |
|  | Azure Data Lake Storage Gen2        |  Všechny veřejné oblasti      | Preview <br/> [Další informace](/azure/storage/common/storage-private-endpoints).  |
|  |  Databáze SQL Azure         | Všechny veřejné oblasti      |   Preview      |
||Azure SQL Data Warehouse| Všechny veřejné oblasti |Preview|
||Azure Cosmos DB| Středozápadní USA, WestUS, Střed USA – sever |Preview|

Nejaktuálnější oznámení najdete na [stránce s aktualizacemi služby Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Protokolování a monitorování

Privátní propojení Azure je integrované s Azure Monitor, což umožňuje archivaci protokolů do účtu úložiště, streamování událostí do centra událostí nebo jejich odeslání do protokolů Azure Monitor. Na Azure Monitor můžete získat přístup k následujícím informacím: 
- **Privátní koncový bod**: data zpracovaná privátním koncovým bodem (v/v)
 
- **Služba privátního propojení**:
    - Data zpracovaná službou privátního propojení (v/v)
    - Dostupnost portu NAT  
 
## <a name="pricing"></a>Ceny   
Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure](https://azure.microsoft.com/pricing/details/private-link/).
 
## <a name="faqs"></a>Nejčastější dotazy  
Nejčastější dotazy najdete v tématu [Nejčastější dotazy k privátním linkám Azure](private-link-faq.md).
 
## <a name="limits"></a>Omezení  
Omezení najdete v tématu [omezení privátních propojení Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits).

## <a name="next-steps"></a>Další kroky
- [Vytvoření privátního koncového bodu pro SQL Database Server pomocí portálu](create-private-endpoint-portal.md)
- [Vytvoření privátního koncového bodu pro SQL Database Server pomocí PowerShellu](create-private-endpoint-powershell.md)
- [Vytvoření privátního koncového bodu pro SQL Database Server pomocí rozhraní příkazového řádku](create-private-endpoint-cli.md)
- [Vytvoření privátního koncového bodu pro účet úložiště pomocí portálu](create-private-endpoint-storage-portal.md)
- [Vytvoření privátního koncového bodu pro účet Azure Cosmos pomocí portálu](../cosmos-db/how-to-configure-private-endpoints.md)
- [Vytvoření vlastní služby privátního propojení pomocí Azure PowerShell](create-private-link-service-powershell.md)


 
