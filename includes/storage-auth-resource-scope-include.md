---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/21/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ad4b244b58d741ad45463297df5bd358f3ae9918
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449924"
---
Předtím, než přiřadíte roli RBAC k objektu zabezpečení, určete obor přístupu, který by měl mít objekt zabezpečení. Osvědčené postupy určují, že je vždy vhodné udělit pouze nejužší možná obor.

Následující seznam popisuje úrovně, ve kterém můžete upřesnit rozsah přístupu k prostředkům Azure blob a fronty, počínaje zpomalit:

- **Kontejner.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem objektům BLOB v kontejneru, jakož i kontejner vlastností a metadat.
- **Jednotlivé fronty.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup k zprávy ve frontě, jakož i fronty vlastností a metadat.
- **Účet úložiště.** V tomto oboru objekt zabezpečení má přístup do všech kontejnerů a jeho objekty BLOB, nebo všechny fronty a jejich zprávy.
- **Skupina prostředků.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem kontejnery nebo fronty ve všech účtů úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem kontejnery nebo fronty ve všech účtů úložiště ve všech skupinách prostředků v předplatném.
