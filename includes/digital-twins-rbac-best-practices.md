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
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044961"
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