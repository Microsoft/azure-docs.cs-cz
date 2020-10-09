---
title: Úložiště image kontejneru
description: Podrobnosti o tom, jak jsou image kontejnerů Docker uložené v Azure Container Registry, včetně zabezpečení, redundance a kapacity.
ms.topic: article
ms.date: 06/18/2020
ms.openlocfilehash: d51014e9e0769091aba42682cce3a6a01cfa19de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85214056"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Úložiště imagí kontejneru v Azure Container Registry

Každá služba Azure Container Registry [Basic, Standard a Premium](container-registry-skus.md) přináší výhody pokročilých funkcí úložiště Azure, jako je šifrování v klidovém režimu pro zabezpečení dat imagí a geografická redundance pro ochranu imagí dat. V následujících částech najdete popis funkcí a omezení úložiště imagí v Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Šifrování v klidovém případě

Všechny Image kontejneru v registru jsou zašifrované v klidovém stavu. Azure automaticky zašifruje image před uložením a dešifruje je průběžně, když se nebo vaše aplikace a služby vyžádají z image. Volitelně můžete použít další vrstvu šifrování s [klíčem spravovaným zákazníkem](container-registry-customer-managed-keys.md).

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Azure používá geograficky redundantní schéma úložiště pro ochranu před ztrátou imagí kontejneru. Azure Container Registry automaticky replikuje image kontejneru do několika geograficky vzdálených datových center a brání jejich ztrátě v případě selhání místního úložiště.

## <a name="geo-replication"></a>Geografickou replikací

V případě scénářů, které vyžadují ještě větší záruku na vysokou dostupnost, zvažte použití funkce [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium. Geografická replikace pomáhá chránit před ztrátou přístupu k vašemu registru v případě *celkové* regionální chyby, nikoli jenom při selhání úložiště. Geografická replikace poskytuje další výhody, jako je například úložiště imagí v síti, pro rychlejší nabízená oznámení a stahování v případě distribuovaných scénářů vývoje nebo nasazení.

## <a name="scalable-storage"></a>Škálovatelné úložiště

Azure Container Registry vám umožňuje vytvořit libovolný počet úložišť, obrázků, vrstev nebo značek podle potřeby, a to až do [limitu úložiště registru](container-registry-skus.md#service-tier-features-and-limits). 

Výkon vašeho registru může ovlivnit velmi vysoký počet úložišť a značek. V rámci rutiny údržby registru pravidelně odstraňujte nepoužívaná úložiště, značky a image a volitelně nastavte [zásady uchovávání informací](container-registry-retention-policy.md) pro netagované manifesty. Odstraněné prostředky registru, jako jsou úložiště, image a značky, *nelze* po odstranění obnovit. Další informace o odstraňování prostředků registru najdete v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Náklady na úložiště

Úplné podrobnosti o cenách najdete v tématu [Azure Container Registry ceny][pricing].

## <a name="next-steps"></a>Další kroky

Další informace o kontejnerech kontejneru Basic, Standard a Premium najdete v tématu [Azure Container Registry úrovně služeb](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
