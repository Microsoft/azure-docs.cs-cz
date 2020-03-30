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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460493"
---
Před přiřazením role RBAC k objektu zabezpečení určete rozsah přístupu, který by měl mít zaregistrovaný objekt zabezpečení. Osvědčené postupy určují, že je vždy nejlepší udělit pouze nejužší možný rozsah.

Následující seznam popisuje úrovně, na kterých můžete obor přístup k prostředkům objektů blob Azure a fronty, počínaje nejužším rozsahem:

- **Samostatný kontejner.** V tomto oboru přiřazení role platí pro všechny objekty BLOB v kontejneru, jakož i vlastnosti kontejneru a metadata.
- **Individuální fronta.** V tomto oboru přiřazení role se vztahuje na zprávy ve frontě, jakož i vlastnosti fronty a metadata.
- **Účet úložiště.** V tomto oboru přiřazení role platí pro všechny kontejnery a jejich objekty BLOB nebo pro všechny fronty a jejich zprávy.
- **Požadovaná skupina prostředků.** V tomto oboru přiřazení role platí pro všechny kontejnery nebo fronty ve všech účtech úložiště ve skupině prostředků.
- **Předplatné.** V tomto oboru přiřazení role se vztahuje na všechny kontejnery nebo fronty ve všech účtech úložiště ve všech skupin ách prostředků v předplatném.

> [!IMPORTANT]
> Pokud vaše předplatné obsahuje obor názvů Azure DataBricks, role, které jsou vymezeny na předplatné neudělí přístup k datům objektů blob a fronty. Obor role do skupiny prostředků, účtu úložiště nebo kontejneru nebo fronty místo.     
