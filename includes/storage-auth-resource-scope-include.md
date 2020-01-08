---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460493"
---
Než přiřadíte roli RBAC objektu zabezpečení, určete rozsah přístupu, který má objekt zabezpečení mít. Osvědčené postupy určují, že vždy nejlépe přidělíte jenom nejužšímu možnému rozsahu.

Následující seznam popisuje úrovně, na jejichž základě můžete nastavit rozsah přístupu k prostředkům Azure Blob a Queue, počínaje nejužším rozsahem:

- **Jednotlivý kontejner.** V tomto oboru se přiřazení role vztahuje na všechny objekty BLOB v kontejneru a také na vlastnosti kontejneru a metadata.
- **Jednotlivé fronty.** V tomto oboru se přiřazení role vztahuje na zprávy ve frontě a také na vlastnosti fronty a metadata.
- **Účet úložiště.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery a jejich objekty blob nebo na všechny fronty a jejich zprávy.
- **Skupina prostředků.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru se přiřazení role vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve všech skupinách prostředků v rámci předplatného.

> [!IMPORTANT]
> Pokud vaše předplatné obsahuje obor názvů Azure datacihly, role s oborem pro předplatné neudělí přístup k datům BLOB a Queue. Místo toho můžete použít role oboru pro skupinu prostředků, účet úložiště nebo kontejner nebo frontu.     
