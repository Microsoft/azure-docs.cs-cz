---
title: Úložiště image kontejneru
description: Podrobnosti o tom, jak se image kontejnerů a jiné artefakty ukládají v Azure Container Registry, včetně zabezpečení, redundance a kapacity.
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: references_regions
ms.openlocfilehash: a9c8ec877ddb17603e82b763223278a2e5e36714
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047741"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Úložiště imagí kontejneru v Azure Container Registry

Každá služba Azure Container Registry [Basic, Standard a Premium](container-registry-skus.md) přináší výhody pokročilých funkcí úložiště Azure, včetně šifrování v klidovém režimu. V následujících částech najdete popis funkcí a omezení úložiště imagí v Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Šifrování v klidovém případě

Všechny Image kontejnerů a jiné artefakty v registru jsou v klidovém stavu šifrované. Azure automaticky zašifruje image před uložením a dešifruje je průběžně, když se nebo vaše aplikace a služby vyžádají z image. Volitelně můžete použít dodatečnou vrstvu šifrování s [klíčem spravovaným zákazníkem](container-registry-customer-managed-keys.md).

## <a name="regional-storage"></a>Místní úložiště

Azure Container Registry ukládá data v oblasti, ve které je registr vytvořený, aby zákazníci mohli splnit požadavky na zaregistrování dat a dodržování předpisů. Ve všech oblastech s výjimkou Brazílie – jih a jihovýchodní Asie může Azure také ukládat data registru do spárované oblasti ve stejné geografické oblasti. V oblasti Brazílie – jih a jihovýchodní Asie jsou data registru vždycky omezená na oblast, aby se pro tyto oblasti vešly požadavky na data o sídle.

Pokud dojde k oblastnímu výpadku, data registru můžou být nedostupná a automaticky se neobnoví. Zákazníci, kteří chtějí mít svá data registru uložená v několika oblastech pro zajištění lepšího výkonu napříč různými geografickými oblastmi nebo kteří chtějí mít odolnost v případě regionálního výpadku, by měla umožňovat [geografickou replikaci](container-registry-geo-replication.md).

## <a name="geo-replication"></a>Geografická replikace

V případě scénářů vyžadujících zajištění vysoké dostupnosti zvažte použití funkce [geografické replikace](container-registry-geo-replication.md) pro Registry úrovně Premium. Geografická replikace pomáhá chránit před ztrátou přístupu k vašemu registru v případě regionálního selhání. Geografická replikace poskytuje další výhody, jako je například úložiště imagí v síti, pro rychlejší nabízená oznámení a stahování v případě distribuovaných scénářů vývoje nebo nasazení.

## <a name="zone-redundancy"></a>Zónová redundance

Pokud chcete vytvořit odolný a vysoce dostupný registr Azure Container Registry, volitelně povolte [redundanci zóny](zone-redundancy.md) v rámci výběru oblastí Azure. Funkce služby Premium Service úrovně Premium, redundance zóny, používá [zóny dostupnosti](../availability-zones/az-overview.md) Azure k replikaci vašeho registru do minimálně tří samostatných zón v každé povolené oblasti. Kombinování geografické replikace a redundance zóny pro zvýšení spolehlivosti a výkonu registru. 

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
