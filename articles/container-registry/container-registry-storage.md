---
title: Úložiště bitové kopie v registru kontejner Azure
description: Podrobnosti o tom, jak vaše Docker kontejneru Image jsou uložené v registru kontejner Azure, včetně zabezpečení, redundance a kapacity.
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 03/21/2018
ms.author: marsma
ms.openlocfilehash: 92e60b4213cb80d193a7c35f68b8f9fd099481d7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="container-image-storage-in-azure-container-registry"></a>Kontejner úložiště bitové kopie v registru kontejner Azure

Každý [Basic, Standard a Premium](container-registry-skus.md) výhody registru kontejner Azure z funkce Rozšířené úložiště Azure, jako třeba šifrování v rest pro zabezpečení dat bitové kopie a geografická redundance pro ochranu dat bitové kopie. Následující části popisují, jak funkce a limity úložiště bitové kopie v Azure Container registru (ACR).

## <a name="encryption-at-rest"></a>Šifrování na rest

Všechny Image kontejneru v registru jsou zašifrovaná přinejmenším. Azure automaticky šifruje image před jejich uložením a dešifruje ji na průběžně při vám nebo vaší aplikace a služby pro vyžádání obsahu bitovou kopii.

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Azure používá schéma geograficky redundantní úložiště pro ochranu proti ztrátě kontejneru obrázků. Azure registru kontejneru automaticky replikuje obrázků kontejneru do několika geograficky vzdáleným datových centrech, brání jejich ztrátě v případě selhání místní úložiště.

## <a name="geo-replication"></a>Geografická replikace

Pro scénáře, které vyžadují i další zajištění vysoké dostupnosti, zvažte použití [geografická replikace](container-registry-geo-replication.md) funkce registrech Premium. Geografická replikace pomáhá chránit proti ztrátě přístupu k vaší registru v případě *celkový* místní selhání, ne jenom selhání úložiště. Geografická replikace přináší i další výhody, příliš, jako síť zavřít image úložiště pro rychlejší nabízených oznámení a vrátí v distribuované nasazení nebo vývoj scénáře.

## <a name="image-limits"></a>Omezení bitové kopie

Následující tabulka popisuje kontejner bitové kopie a úložiště omezení nastavené pro kontejner Azure registrech.

| Prostředek | Omezení |
| -------- | :---- |
| Úložiště | Bez omezení |
| Image | Bez omezení |
| Vrstev. | Bez omezení |
| Značky | Bez omezení|
| Úložiště | 5 TB |

Velmi vysoké počty úložiště a značky může mít dopad na výkon vaší registru. Pravidelně odstranit nepoužívané úložiště, značky a bitové kopie pomocí [rozhraní příkazového řádku Azure](/cli/azure/acr), ACR [REST API](/rest/api/containerregistry/), nebo [portál Azure] [ portal] jako část vaší rutiny údržby registru. Odstranit registru prostředkům, jako jsou úložiště, Image a značky *nelze* obnovit po jeho odstranění.

## <a name="storage-cost"></a>Náklady na úložiště

Úplné podrobnosti o cenách najdete v tématu [registru kontejner Azure ceny][pricing].

## <a name="next-steps"></a>Další postup

Další informace o registru kontejneru různých Azure SKU (Basic, Standard, Premium) najdete v tématu [SKU registru kontejner Azure](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: http://aka.ms/acr/pricing

<!-- LINKS - Internal -->
