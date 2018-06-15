---
title: zahrnout soubor
description: zahrnout soubor
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/16/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: b4b06119b9d46781b967fc8d98808c60d2b41ccb
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31433245"
---
Chcete Ujistěte se, že uživatelé ve vaší organizaci mají správnou úroveň přístupu na tyto prostředky. Nechcete, aby uživatelům udělit neomezený přístup, ale musíte také zkontrolujte, zda že mohou provádět práci. Řízení přístupu na základě role (RBAC) umožňuje spravovat uživatele, kteří mají oprávnění k dokončení určitých akcí u oboru. Role definuje sadu povolených akcí. Obor přiřazení role a zadat uživatele, kteří patří do této role pro obor.

Při plánování strategie řízení přístupu, udělte uživatelům nejnižší oprávnění k práci. Následující obrázek znázorňuje navrhované vzor přiřazení RBAC.

![Rozsah](./media/resource-manager-governance-rbac/role-examples.png)

Existují tři role, které platí pro všechny prostředky - vlastník, Přispěvatel a čtečky. Všechny účty přiřazené k roli vlastníka by měl být důkladné kontrole a je používána zřídka. Uživatelům, kteří potřebují jenom sledovat stav řešení, je třeba poskytnout role Čtenář.

Většina uživatelů jsou udělena [rolím pro konkrétní prostředky](../articles/role-based-access-control/built-in-roles.md) nebo [vlastní role](../articles/role-based-access-control/custom-roles.md) na úrovni skupiny předplatné nebo prostředek. Tyto role úzce definovat povolené akce. Přiřadit uživatele k tyto role, udělíte požadovaný přístup pro uživatele bez umožňující příliš mnoho ovládacího prvku. Účet můžete přiřadit více než jedné role, a tento uživatel získá kombinované oprávnění rolí. Udělení přístupu na úrovni prostředků je často příliš omezující pro uživatele, ale může fungovat pro automatizovaný proces určené pro konkrétní úlohu.

### <a name="who-can-assign-roles"></a>Přiřazování rolí

Vytvoření a odeberte přiřazení rolí, uživatelé musí mít `Microsoft.Authorization/roleAssignments/*` přístup. Tento přístup je poskytována prostřednictvím role vlastníka nebo správce uživatelského přístupu.