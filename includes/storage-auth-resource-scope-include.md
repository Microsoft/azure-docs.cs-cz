---
title: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 644d58c3d1c60611b0d22d2757da089313fa12b6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423697"
---
Než přiřadíte roli RBAC objektu zabezpečení, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že vždy nejlépe přidělíte jenom nejužšímu možnému rozsahu.

Následující seznam popisuje úrovně, na jejichž základě můžete nastavit rozsah přístupu k prostředkům Azure Blob a Queue, počínaje nejužším rozsahem:

- **Jednotlivý kontejner.** V tomto oboru se přiřazení role vztahuje na všechny objekty BLOB v kontejneru a také na vlastnosti kontejneru a metadata.
- **Jednotlivé fronty.** V tomto oboru se přiřazení role vztahuje na zprávy ve frontě a také na vlastnosti fronty a metadata.
- **Účet úložiště.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery a jejich objekty blob nebo na všechny fronty a jejich zprávy.
- **Požadovaná skupina prostředků.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve všech skupinách prostředků v rámci předplatného.
- **Skupina pro správu.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve všech skupinách prostředků ve všech předplatných ve skupině pro správu.

Další informace o přiřazení a rozsahu rolí Azure najdete v tématu [co je řízení přístupu na základě role Azure (Azure RBAC)?](../articles/role-based-access-control/overview.md).
