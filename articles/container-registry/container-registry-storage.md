---
title: Úložiště obrázků kontejneru
description: Podrobnosti o tom, jak jsou vaše image kontejneru Dockeru uloženy v registru kontejnerů Azure, včetně zabezpečení, redundance a kapacity.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456205"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Úložiště image kontejneru v registru kontejnerů Azure

Každý [základní, standardní a prémiový](container-registry-skus.md) registr kontejnerů Azure těží z pokročilých funkcí úložiště Azure, jako je šifrování v klidovém stavu pro zabezpečení obrazových dat a geografickou redundanci pro ochranu obrazových dat. Následující části popisují funkce i omezení úložiště bitových obrázků v Registru kontejnerů Azure (ACR).

## <a name="encryption-at-rest"></a>Šifrování v klidovém stavu

Všechny image kontejneru v registru jsou šifrovány v klidovém stavu. Azure automaticky zašifruje bitovou kopii před uložením a dešifruje ji průběžně, když ji vy nebo vaše aplikace a služby vytáhnete.

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Azure používá geograficky redundantní úložiště schéma ochrany proti ztrátě iblbitů kontejneru. Azure Container Registry automaticky replikuje ibi kontejnerů do více geograficky vzdálených datových center, aby se zabránilo jejich ztrátě v případě selhání místního úložiště.

## <a name="geo-replication"></a>Geografická replikace

U scénářů, které vyžadují ještě větší záruku vysoké dostupnosti, zvažte použití funkce [geografické replikace](container-registry-geo-replication.md) privátních registrů. Geografická replikace pomáhá chránit před ztrátou přístupu k registru v případě *úplného* selhání místní oblasti, nikoli pouze selhání úložiště. Geografická replikace poskytuje také další výhody, jako je úložiště bitových obrázků zavření sítě pro rychlejší nabízení a vytahování ve scénářích distribuovaného vývoje nebo nasazení.

## <a name="image-limits"></a>Omezení obrázků

Následující tabulka popisuje limity image kontejneru a úložiště na místě pro registry kontejnerů Azure.

| Prostředek | Omezení |
| -------- | :---- |
| Úložiště | Bez omezení |
| Obrázky | Bez omezení |
| Vrstvy | Bez omezení |
| Značky | Bez omezení|
| Úložiště | 5 TB |

Velmi vysoký počet úložišť a značek může ovlivnit výkon registru. Pravidelně odstraňujte nepoužívané repozitáře, značky a bitové kopie jako součást rutiny údržby registru. Odstraněné prostředky registru, jako jsou úložiště, obrázky a značky, *nelze* po odstranění obnovit. Další informace o odstranění prostředků registru najdete v tématu [Odstranění ibi kontejnerů v registru kontejnerů Azure](container-registry-delete.md).

## <a name="storage-cost"></a>Náklady na úložiště

Podrobné informace o cenách najdete v tématu [Ceny registru kontejnerů Azure][pricing].

## <a name="next-steps"></a>Další kroky

Další informace o různých supoložkách registru kontejnerů Azure (Basic, Standard, Premium) najdete v [tématu Azure Container Registry SKU](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
