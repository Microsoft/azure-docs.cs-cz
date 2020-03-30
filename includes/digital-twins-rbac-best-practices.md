---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 893beb0800af0eece4d69e727e427c3e92b79121
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044961"
---
Řízení přístupu založené na rolích je strategie zabezpečení řízená dědičností pro správu přístupu, oprávnění a rolí. Potome role dědí oprávnění z nadřazených rolí. Oprávnění lze také přiřadit, aniž by byla zděděna z nadřazené role. Mohou být také přiřazeny k přizpůsobení role podle potřeby.

Správce prostoru může například potřebovat globální přístup ke spuštění všech operací pro zadané místo. Aplikace Access zahrnuje všechny uzly pod nebo v prostoru. Instalační program zařízení může potřebovat pouze oprávnění *ke čtení* a *aktualizaci* zařízení a senzorů.

V každém případě jsou role udělována *přesně a ne více než přístup potřebný* ke splnění jejich úkolů podle zásady nejmenšího oprávnění. Podle této zásady je identita udělena *pouze*:

* Množství přístupu potřebné k dokončení své úlohy.
* Úloha, která je vhodná a omezená na výkon své práce.

>[!IMPORTANT]
> Vždy dodržujte zásadu nejmenšího privilegia.

Dva další důležité postupy řízení přístupu založené na rolích následovat:

> [!div class="checklist"]
> * Pravidelně auditovat přiřazení rolí k ověření, že každá role má správná oprávnění.
> * Vyčištění rolí a přiřazení při změně rolí nebo přiřazení jednotlivci.