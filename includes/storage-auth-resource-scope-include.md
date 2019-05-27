---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115565"
---
Předtím, než přiřadíte roli RBAC k objektu zabezpečení, určete obor přístupu, který by měl mít objekt zabezpečení. Osvědčené postupy určují, že je vždy vhodné udělit pouze nejužší možná obor.

Následující seznam popisuje úrovně, ve kterém můžete upřesnit rozsah přístupu k prostředkům Azure blob a fronty, počínaje zpomalit:

- **Kontejner.** V tomto oboru přiřazení role se vztahuje na všechny objekty BLOB v kontejneru, jakož i kontejner vlastností a metadat.
- **Jednotlivé fronty.** V tomto oboru přiřazení role se vztahuje na zprávy ve frontě, jakož i fronty vlastností a metadat.
- **Účet úložiště.** V tomto oboru přiřazení role se týká všech kontejnerů a jeho objekty BLOB, všechny fronty a jejich zprávy.
- **Skupina prostředků.** V tomto oboru přiřazení role se vztahuje na všechny kontejnery nebo fronty ve všech účtů úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru přiřazení role se vztahuje na všechny kontejnery nebo fronty ve všech účtů úložiště ve všech skupinách prostředků v předplatném.

> [!IMPORTANT]
> Pokud vaše předplatné obsahuje obor názvů služby Azure DataBricks, nepůjdou udělení přístupu k datům objektu blob a fronty rolí v oboru předplatného.
