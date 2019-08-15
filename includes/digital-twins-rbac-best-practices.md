---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: fa88bf7f8db9b4fea63429dbfd18f84ee84ccda1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012091"
---
Řízení přístupu na základě role je strategie zabezpečení řízená dědičností pro správu přístupu, oprávnění a rolí. Podřízené role dědí oprávnění od nadřazených rolí. Oprávnění lze také přiřadit bez dědění z nadřazené role. Můžete je také přiřadit podle potřeby a přizpůsobit roli.

Správce prostoru může například potřebovat globální přístup ke spuštění všech operací v zadaném prostoru. Přístup zahrnuje všechny uzly pod nebo v prostoru. Instalační program zařízení může potřebovat oprávnění *ke čtení* a *aktualizaci* pro zařízení a senzory.

V každém případě jsou role uděleny *přesně a nejsou k dispozici více než přístup nutný* ke splnění svých úkolů podle principu nejnižší úrovně oprávnění. V souladu s tímto principem je udělena *jenom*identita:

* Množství přístupu potřebného k dokončení jeho úlohy.
* Vhodná role a omezená na provádění svých úloh.

>[!IMPORTANT]
> Vždy postupujte podle principu nejnižší úrovně oprávnění.

Existují dva další důležité postupy řízení přístupu založené na rolích:

> [!div class="checklist"]
> * Pravidelně auditujte přiřazení rolí, abyste ověřili, že každá role má správná oprávnění.
> * Vyčištění rolí a přiřazení v případě, kdy jednotlivci mění role nebo přiřazení.