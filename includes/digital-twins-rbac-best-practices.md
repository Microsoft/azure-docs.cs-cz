---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 11/13/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 42aa275e692e4e2e9b7ca38825c828c1f56247fb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51628114"
---
Řízení přístupu na základě role je strategie zabezpečení na základě dědičnosti pro správu přístupu, oprávnění a rolí. Podřízený prvek role dědit oprávnění z nadřazené role. Oprávnění také může být přiřazena, aniž by se dědí z nadřazené role. Jsou také můžete přiřadit role podle potřeby upravte.

Například místo správce potřebovat globální přístup ke spouštění všech operací pro zadané místo. Přístup zahrnuje všechny uzly pod nebo v oboru. Instalační program, který zařízení může být nutné jenom *čtení* a *aktualizovat* oprávnění pro zařízení a senzorů.

V každém případě role mají přidělena *přesně a více než přístup požadovaný* ke splnění svých úkolů na princip nejnižších oprávnění. Podle této zásady je udělen identitě *pouze*:

* Takový přístup potřebný k dokončení jejich úloh.
* Role vhodné a omezené na provádění svých úloh.

>[!IMPORTANT]
> Vždy použijte Princip nejnižších oprávnění.

Dvě další důležité role řízení přístupu podle postupy:

> [!div class="checklist"]
> * Pravidelným auditem přiřazení rolí ověřte, že každá role má správná oprávnění.
> * Vyčištění při změně jednotlivce role nebo přiřazení role a přiřazení.