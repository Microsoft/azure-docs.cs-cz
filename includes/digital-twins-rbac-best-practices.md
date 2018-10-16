---
title: zahrnout soubor
description: zahrnout soubor
services: azure-digital-twins
author: adamgerard
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/27/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 075587df445b46c8b03c5448cebf8cd8b12f1179
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324045"
---
Řízení přístupu na základě role je strategie zabezpečení na základě dědičnosti pro správu přístupu, oprávnění a rolí. Podřízený prvek role dědit oprávnění z nadřazené role. Oprávnění může být přiřazen také bez zděděna od nadřazené role. Také mohly být přiřazeny k roli podle potřeby upravte.

Například **místo správce** možná bude nutné globální přístup ke spouštění všech operací pro zadané místo (včetně všechny uzly pod nebo v něm) zatímco **instalační program zařízení** potřebovat pouze *čtení* a *aktualizovat* oprávnění pro zařízení a senzorů.

V každém případě by měla být poskytnuta role **přesně a více než přístup požadovaný** ke splnění svých úkolů na **Princip nejnižších oprávnění**, která uděluje identitu *pouze*:

* Takový přístup potřebný k dokončení jejich úloh.
* Role vhodné a omezené na provádění svých úloh.

>[!IMPORTANT]
> **Vždy použijte Princip nejnižších oprávnění**.

Dvě další důležité řízení přístupu na základě Role postupy:

> [!div class="checklist"]
> * Pravidelným auditem přiřazení rolí ověřte, že každá role má správná oprávnění.
> * Role a přiřazení by měla být vyčištěna na více systémů podle jednotlivce změnit role nebo přiřazení.