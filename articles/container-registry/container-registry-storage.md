---
title: Úložiště image kontejneru
description: Podrobnosti o tom, jak se image kontejnerů a jiné artefakty ukládají v Azure Container Registry, včetně zabezpečení, redundance a kapacity.
ms.topic: article
ms.date: 03/02/2021
ms.custom: references_regions
ms.openlocfilehash: 4bdffd111273e00b796e45f4e09bfac9ba6713e4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036006"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Úložiště imagí kontejneru v Azure Container Registry

Každá služba Azure Container Registry [Basic, Standard a Premium](container-registry-skus.md) přináší výhody pokročilých funkcí úložiště Azure, jako je šifrování v klidovém režimu pro zabezpečení dat imagí a geografická redundance pro ochranu imagí dat. V následujících částech najdete popis funkcí a omezení úložiště imagí v Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Šifrování v klidovém případě

Všechny Image kontejnerů a jiné artefakty v registru jsou v klidovém stavu šifrované. Azure automaticky zašifruje image před uložením a dešifruje je průběžně, když se nebo vaše aplikace a služby vyžádají z image. Volitelně můžete použít dodatečnou vrstvu šifrování s [klíčem spravovaným zákazníkem](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Pro Registry kontejnerů nasazené ve většině oblastí Azure používá geograficky redundantní schéma úložiště, které vám pomůžou chránit před ztrátou imagí kontejneru a dalších artefaktů. Azure Container Registry automaticky replikuje image kontejneru do několika geograficky vzdálených datových center a zabraňuje jejich ztrátě, pokud dojde k selhání místního úložiště.

> [!IMPORTANT]
> * Pokud dojde k selhání místního úložiště, můžete obnovit data registru jenom kontaktováním podpory Azure. 
> * S ohledem na požadavky na umístění dat v oblasti Brazílie – jih a jihovýchodní Asie se Azure Container Registry data v těchto oblastech ukládají [pouze do místního geografického](https://azure.microsoft.com/global-infrastructure/geographies/)umístění. V jihovýchodní Asie se všechna data ukládají v Singapuru. V oblasti Brazílie – jih jsou všechna data uložená v Brazílii. Když dojde ke ztrátě oblasti z důvodu významné havárie, Microsoft nebude moct obnovit data Azure Container Registry.

## <a name="geo-replication"></a>Geografická replikace

V případě scénářů, které vyžadují ještě větší záruku na vysokou dostupnost, zvažte použití funkce [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium. Geografická replikace pomáhá chránit před ztrátou přístupu k vašemu registru v případě *celkové* regionální chyby, nikoli jenom při selhání úložiště. Geografická replikace poskytuje další výhody, jako je například úložiště imagí v síti, pro rychlejší nabízená oznámení a stahování v případě distribuovaných scénářů vývoje nebo nasazení.

## <a name="zone-redundancy"></a>Zónová redundance

Pokud chcete vytvořit odolný a vysoce dostupný registr Azure Container Registry, volitelně povolte [redundanci zóny](zone-redundancy.md) v oblasti výběr oblastí Azure. Funkce služby Premium Service úrovně Premium, redundance zóny, používá [zóny dostupnosti](../availability-zones/az-overview.md) Azure k replikaci vašeho registru do minimálně tří samostatných zón v každé povolené oblasti. Kombinování geografické replikace a redundance zóny pro zvýšení spolehlivosti a výkonu registru. 

## <a name="scalable-storage"></a>Škálovatelné úložiště

Azure Container Registry vám umožňuje vytvořit libovolný počet úložišť, obrázků, vrstev nebo značek podle potřeby, a to až do [limitu úložiště registru](container-registry-skus.md#service-tier-features-and-limits). 

Vysoký počet úložišť a značek může mít vliv na výkon vašeho registru. V rámci rutiny údržby registru pravidelně odstraňujte nepoužívaná úložiště, značky a image a volitelně nastavte [zásady uchovávání informací](container-registry-retention-policy.md) pro netagované manifesty. Odstraněné prostředky registru, jako jsou úložiště, image a značky, *nelze* po odstranění obnovit. Další informace o odstraňování prostředků registru najdete v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Náklady na úložiště

Úplné podrobnosti o cenách najdete v tématu [Azure Container Registry ceny][pricing].

## <a name="next-steps"></a>Další kroky

Další informace o kontejnerech kontejneru Basic, Standard a Premium najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
