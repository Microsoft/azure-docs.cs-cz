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
ms.openlocfilehash: a50eb45291ada23f55057f3c440c5b8b23cc4bce
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58622633"
---
Předtím, než přiřadíte roli RBAC k objektu zabezpečení, určete obor přístupu, který by měl mít objekt zabezpečení. Osvědčené postupy určují, že je vždy vhodné udělit pouze nejužší možná obor.

Následující seznam popisuje úrovně, ve kterém můžete upřesnit rozsah přístupu k prostředkům Azure blob a fronty, počínaje zpomalit:

- **Kontejner.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem objektům BLOB v kontejneru, jakož i kontejner vlastností a metadat.
- **Jednotlivé fronty.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup k zprávy ve frontě, jakož i fronty vlastností a metadat.
- **Účet úložiště.** V tomto oboru objekt zabezpečení má přístup do všech kontejnerů a jeho objekty BLOB, nebo všechny fronty a jejich zprávy.
- **Skupina prostředků.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem kontejnery nebo fronty ve všech účtů úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru zaregistrovaný objekt zabezpečení má přístup ke všem kontejnery nebo fronty ve všech účtů úložiště ve všech skupinách prostředků v předplatném.

> [!IMPORTANT]
> Pokud vaše předplatné obsahuje obor názvů služby Azure DataBricks, nepůjdou udělení přístupu k datům objektu blob a fronty rolí v oboru předplatného.