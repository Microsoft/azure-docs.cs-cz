---
title: Přístup k vytváření sestav - Azure RBAC | Microsoft Docs
description: Vygenerujte sestavu, která uvádí všechny změny v přístupu k vašemu předplatnému Azure pomocí řízení přístupu na základě rolí za posledních 90 dnů.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: deef89e62dcec3141be46549cc0fb34b33a6335a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="create-an-access-report-for-role-based-access-control"></a>Vytvoření sestavy přístup k řízení přístupu na základě rolí
Vždy, když někdo uděluje nebo odvolá přístup v rámci vašich předplatných, změny se budou protokolovat do Azure událostí. Můžete vytvořit sestavy historie změn přístupu zobrazíte všechny změny za posledních 90 dnů.

## <a name="create-a-report-with-azure-powershell"></a>Vytvoření sestavy pomocí prostředí Azure PowerShell
K vytvoření sestavy historie změn přístupu v prostředí PowerShell, použijte [Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog) příkaz.

Při volání tento příkaz můžete určit kterou vlastnost tohoto přiřazení uveden v seznamu, včetně následujících:

| Vlastnost | Popis |
| --- | --- |
| **Akce** |Jestli byl přístup udělen nebo odebrán |
| **volající** |Vlastník zodpovědná za změnu přístup |
| **principalId** | Jedinečný identifikátor uživatele, skupiny nebo aplikace, kterému byla přiřazena role |
| **PrincipalName** |Jméno uživatele, skupiny nebo aplikace |
| **PrincipalType** |Jestli přiřazení byla pro uživatele, skupiny nebo aplikace |
| **hodnoty vlastnosti roleDefinitionId** |Identifikátor GUID role, který byl udělen nebo odebrán |
| **RoleName** |Role, který byl udělen nebo odebrán |
| **Rozsah** | Jedinečný identifikátor předplatné, skupinu prostředků nebo prostředek, který se vztahuje na přiřazení | 
| **ScopeName** |Název předplatné, skupinu prostředků nebo prostředek |
| **ScopeType** |Jestli přiřazení byl v předplatné, skupinu prostředků nebo prostředek oboru |
| **časové razítko** |Datum a čas, která byla změněna přístup |

Příkaz v tomto příkladu jsou uvedeny všechny změny v přístupu v rámci předplatného pro posledních sedmi dnech:

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![Prostředí PowerShell Get-AzureRMAuthorizationChangeLog – snímek obrazovky](./media/change-history-report/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>Vytvoření sestavy pomocí rozhraní příkazového řádku Azure
K vytvoření sestavy historie změn přístupu v rozhraní příkazového řádku Azure (CLI), použijte `azure role assignment changelog list` příkaz.

## <a name="export-to-a-spreadsheet"></a>Export do tabulky
Pokud chcete sestavu uložit nebo manipulovat s daty, exportujte přístup změny do souboru CSV. Potom můžete zobrazit zprávu v tabulce ke kontrole.

![Protokol změn zobrazit jako tabulku – snímek obrazovky](./media/change-history-report/change-history-spreadsheet.png)

## <a name="next-steps"></a>Další postup
* Práce s [vlastní role v Azure RBAC](custom-roles.md)
* Naučte se spravovat [RBAC Azure pomocí prostředí powershell](role-assignments-powershell.md)

