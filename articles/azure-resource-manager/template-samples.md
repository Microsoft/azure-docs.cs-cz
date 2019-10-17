---
title: Ukázky šablon Azure Resource Manager
description: Příklady šablon Azure Resource Manager pro nasazování funkcí správy, jako jsou role a zámky.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 11/16/2018
ms.author: tomfitz
ms.openlocfilehash: e342507b584a405637fc6728dfcd6e49199a154f
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390181"
---
# <a name="azure-resource-manager-templates-for-management-features"></a>Šablony Azure Resource Manager pro funkce správy

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro funkce poskytované Správce prostředků.

| | |
|-|-|
|**Přiřazení rolí**||
| [Přiřadit roli pro skupinu prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-resourcegroup)| Přiřadí uživateli předdefinovanou roli pro existující skupinu prostředků. |
| [Přiřadit roli pro existující virtuální počítač](https://github.com/Azure/azure-quickstart-templates/tree/master/101-rbac-builtinrole-virtualmachine)| Přiřadí uživateli předdefinovanou roli pro existující virtuální počítač. |
| [Přiřazení role pro několik virtuálních počítačů](https://github.com/Azure/azure-quickstart-templates/tree/master/201-rbac-builtinrole-multipleVMs)| Přiřadí uživatelům předdefinované role pro více než jeden virtuální počítač. |
| [Přiřazení role k předplatnému Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/subscription-role-assigment)| Přiřadí uživateli roli pro předplatné Azure. |
|**Definice role**||
| [Vytvořit definici vlastní role](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-role-def)| Vytvoří novou definici role v rámci předplatného Azure. |
|**Zámek prostředků**||
| [Zamknout skupinu prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment)| Vytvoří skupinu prostředků a použije **DoNotDelete** zámek pro skupinu prostředků. Přiřadí roli přispěvatele uživateli. |
| | |
