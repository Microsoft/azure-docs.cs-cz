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
ms.openlocfilehash: f77a5d482c3f8632a3d86bd8e027fbb4418168c3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205326"
---
Potřebujete zajistit, aby uživatelé ve vaší organizaci měli správnou úroveň přístupu k těmto prostředkům. Nechcete uživatelům dát neomezený přístup, ale zároveň jim potřebujete umožnit dělat svou práci. Řízení přístupu na základě role (RBAC) umožňuje spravovat, kteří mají oprávnění k provedení určité akce v oboru. Roli definuje sadu povolených akcí. Přiřazení role do oboru a určit, kteří uživatelé patří do této role pro obor.

Při plánování strategie řízení přístupu udělte uživatelům nejnižší úroveň oprávnění, kterou k práci potřebují. Následující obrázek ukazuje navrhovaný způsob přiřazování rolí RBAC.

![Scope](./media/resource-manager-governance-rbac/role-examples.png)

Existují tři role, které se vztahují na všechny prostředky – vlastník, Přispěvatel a čtenář. Všechny účty přiřazené role vlastníka by měla podléhat důkladné kontrole a zřídka se používá. Uživatelé, kteří potřebují pouze sledovat stav řešení udělení role čtenáře.

Většina uživatelů jsou udělena [rolím pro konkrétní prostředky](../articles/role-based-access-control/built-in-roles.md) nebo [vlastní role](../articles/role-based-access-control/custom-roles.md) na úrovni skupiny prostředků nebo předplatného. Pracovníci v těchto rolích úzce definovat povolených akcí. Prostřednictvím přiřazování uživatelů do těchto rolí, udělit požadovaný přístup pro uživatele bez povolení příliš velkou kontrolu. Účet můžete přiřadit k více než jednu roli a tento uživatel získá kombinované oprávnění z rolí. Udělení přístupu na úrovni prostředků je často příliš omezující pro uživatele, ale může fungovat pro automatizovaný proces určený pro konkrétní úlohu.

### <a name="who-can-assign-roles"></a>Přiřazování rolí

K vytváření a odebírání přiřazení rolí musí mít uživatelé přístup `Microsoft.Authorization/roleAssignments/*`. Tento přístup se poskytuje prostřednictvím role vlastníka nebo správce uživatelských přístupů.