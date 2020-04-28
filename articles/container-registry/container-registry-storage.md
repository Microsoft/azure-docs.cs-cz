---
title: Úložiště image kontejneru
description: Podrobnosti o tom, jak jsou image kontejnerů Docker uložené v Azure Container Registry, včetně zabezpečení, redundance a kapacity.
ms.topic: article
ms.date: 03/21/2018
ms.openlocfilehash: f66c3dd95edfe5035c46857cb6f9aa59d8a6a0e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456205"
---
# <a name="container-image-storage-in-azure-container-registry"></a>Úložiště imagí kontejneru v Azure Container Registry

Každá služba Azure Container Registry [Basic, Standard a Premium](container-registry-skus.md) přináší výhody pokročilých funkcí úložiště Azure, jako je šifrování v klidovém režimu pro zabezpečení dat imagí a geografická redundance pro ochranu imagí dat. V následujících částech najdete popis funkcí a omezení úložiště imagí v Azure Container Registry (ACR).

## <a name="encryption-at-rest"></a>Šifrování v klidovém případě

Všechny Image kontejneru v registru jsou zašifrované v klidovém stavu. Azure automaticky zašifruje image před uložením a dešifruje je průběžně, když se nebo vaše aplikace a služby vyžádají z image.

## <a name="geo-redundant-storage"></a>Geograficky redundantní úložiště

Azure používá geograficky redundantní schéma úložiště pro ochranu před ztrátou imagí kontejneru. Azure Container Registry automaticky replikuje image kontejneru do několika geograficky vzdálených datových center a brání jejich ztrátě v případě selhání místního úložiště.

## <a name="geo-replication"></a>Geografická replikace

V případě scénářů, které vyžadují ještě větší záruku na vysokou dostupnost, zvažte použití funkce [geografické replikace](container-registry-geo-replication.md) v registrech úrovně Premium. Geografická replikace pomáhá chránit před ztrátou přístupu k vašemu registru v případě *celkové* regionální chyby, nikoli jenom při selhání úložiště. Geografická replikace poskytuje další výhody, jako je například úložiště imagí v síti, pro rychlejší nabízená oznámení a stahování v případě distribuovaných scénářů vývoje nebo nasazení.

## <a name="image-limits"></a>Omezení imagí

Následující tabulka popisuje kapacitu kontejneru a omezení úložiště pro služby Azure Container Registry.

| Prostředek | Omezení |
| -------- | :---- |
| Úložiště | Bez omezení |
| Obrázky | Bez omezení |
| Vrstvy | Bez omezení |
| Značky | Bez omezení|
| Storage | 5 TB |

Výkon vašeho registru může ovlivnit velmi vysoký počet úložišť a značek. V rámci rutiny údržby registru pravidelně odstraňujte nepoužívaná úložiště, značky a obrázky. Odstraněné prostředky registru, jako jsou úložiště, image a značky, se po odstranění *nedají* obnovit. Další informace o odstraňování prostředků registru najdete v tématu [odstranění imagí kontejneru v Azure Container Registry](container-registry-delete.md).

## <a name="storage-cost"></a>Náklady na úložiště

Úplné podrobnosti o cenách najdete v tématu [Azure Container Registry ceny][pricing].

## <a name="next-steps"></a>Další kroky

Další informace o různých Azure Container Registry SKU (Basic, Standard, Premium) najdete v tématu [Azure Container Registry SKU](container-registry-skus.md).

<!-- IMAGES -->

<!-- LINKS - External -->
[portal]: https://portal.azure.com
[pricing]: https://aka.ms/acr/pricing

<!-- LINKS - Internal -->
