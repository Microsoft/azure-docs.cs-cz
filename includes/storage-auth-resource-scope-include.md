---
title: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/10/2021
ms.author: tamram
ms.openlocfilehash: 483f5853c321eee4ac6d10543f0e360a0a5e54b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373742"
---
Než přiřadíte roli Azure RBAC k objektu zabezpečení, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že vždy nejlépe přidělíte jenom nejužšímu možnému rozsahu. Role Azure RBAC definované v širším oboru jsou děděny prostředky, které jsou pod nimi.

Následující seznam popisuje úrovně, na jejichž základě můžete nastavit rozsah přístupu k prostředkům Azure Blob a Queue, počínaje nejužším rozsahem:

- **Jednotlivý kontejner.** V tomto oboru se přiřazení role vztahuje na všechny objekty BLOB v kontejneru a také na vlastnosti kontejneru a metadata.
- **Jednotlivé fronty.** V tomto oboru se přiřazení role vztahuje na zprávy ve frontě a také na vlastnosti fronty a metadata.
- **Účet úložiště.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery a jejich objekty blob nebo na všechny fronty a jejich zprávy.
- **Požadovaná skupina prostředků.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve všech skupinách prostředků v rámci předplatného.
- **Skupina pro správu.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve všech skupinách prostředků ve všech předplatných ve skupině pro správu.

Další informace o přiřazení a rozsahu rolí Azure najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../articles/role-based-access-control/overview.md).
