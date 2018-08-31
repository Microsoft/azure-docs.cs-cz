---
title: Úložiště obrázků ve službě Azure Container Registry
description: Podrobnosti o způsobu uložení imagí kontejnerů Dockeru v Azure Container Registry, včetně zabezpečení, redundance a kapacity.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 65ff60be992440c69e50a084b467a8efbb19574e
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307145"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Úložiště obrázků kontejneru ve službě Azure Container Registry

Každý [Basic, Standard a Premium](container-registry-skus.md) -klidové šifrování pro zabezpečení dat a geografické redundanci pro ochranu dat pomocí image, jako je Azure container registry výhody funkce Rozšířené úložiště Azure. Následující části popisují funkce a omezení úložiště obrázků v Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Všechny Image kontejneru v registru se šifrují při nečinnosti. Azure automaticky šifruje image před uložením a dešifruje ji v běhu při vám nebo vašim aplikacím a službám, stáhněte si image.

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Azure používá schéma geograficky redundantní úložiště s přístupem pro ochranu před ztrátou imagí kontejnerů. Služba Azure Container Registry automaticky replikuje imagí kontejnerů do několika geograficky vzdáleném datových center, prevenci jejich ztrát v případě selhání místního úložiště.

## <a name="geo-replication"></a>Geografická replikace

Pro scénáře vyžadující ještě větší jistotu vysokou dostupnost, zvažte použití [geografickou replikaci](container-registry-geo-replication.md) funkce registry úrovně Premium. Geografická replikace pomůže chránit proti ztrátě přístupu ke svému registru v *celkový* selhání v oblasti, ne jenom selhání úložiště. Geografická replikace přináší i další výhody, příliš, jako obrázku blízko sítě úložiště pro rychlejší nabízených oznámení a získává v distribuovaných scénářích, vývoj nebo nasazení.

## <a name="image-limits"></a>Obrázek omezení

Následující tabulka popisuje omezení úložiště a image kontejneru v místě pro Azure container Registry.

| Prostředek | Omezení |
| -------- | :---- |
| Úložiště | Bez omezení |
| Image | Bez omezení |
| Vrstvy | Bez omezení |
| Značky | Bez omezení|
| Úložiště | 5 TB |

Velmi vysoké počty značky a úložiště můžete mít vliv na výkon vašeho registru. Pravidelně jako součást vašeho registru údržby rutina Odstraňte nepoužívané úložišť, značky a obrázků. Odstranit registr prostředkům, jako jsou úložiště, obrázků a značek *nelze* po odstranění obnovit. Další informace o odstraňování registru prostředky, najdete v části [odstranit Image kontejnerů ve službě Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Náklady na úložiště

Úplné podrobnosti o cenách najdete v tématu [ceny Azure Container Registry][pricing].

## <a name="next-steps"></a>Další postup

Další informace o registru kontejneru různých Azure SKU (Basic, Standard, Premium), najdete v části [skladové jednotky Azure Container Registry](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
