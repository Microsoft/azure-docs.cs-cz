---
title: Zobrazení protokolů aktivit pro RBAC změny v Azure | Dokumentace Microsoftu
description: Zobrazit protokoly aktivit pro řízení přístupu na základě role (RBAC) změny za posledních 90 dnů.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ff09647dc3271c947b3037afcfacc33951ddb07
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117644"
---
# <a name="view-activity-logs-for-rbac-changes"></a>Zobrazení protokolů aktivit pro RBAC změny

Někdy potřebujete informace o změnách přístupu na základě role (RBAC) ovládacího prvku, například pro auditování a odstraňování potíží. Kdykoli někdo provádí změny v přiřazení role nebo definice rolí v rámci vašich předplatných, změny budou protokolovat [protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md). Můžete zobrazit protokoly aktivit zobrazíte všechny RBAC změny za posledních 90 dnů.

## <a name="operations-that-are-logged"></a>Operace, které se protokolují

Tady jsou operace související s RBAC, které se zaznamenávají v protokolu aktivit:

- Vytvořit přiřazení role
- Odstranit přiřazení role
- Vytvořit nebo aktualizovat vlastní definici role
- Odstranit definice rolí

## <a name="azure-portal"></a>portál Azure

Chcete-li zobrazit protokoly aktivit pomocí webu Azure portal je nejjednodušší způsob, jak začít pracovat. Následující snímek obrazovky ukazuje příklad, který je filtrován. Chcete-li zobrazit přiřazení role a role definice operace protokol aktivit. Zahrnuje také odkaz ke stažení protokolů jako soubor CSV.

![Protokoly aktivit na portálu – snímek obrazovky](./media/change-history-report/activity-log-portal.png)

Protokol aktivit na portálu má několik filtrů. Tady jsou filtry související RBAC:

|Filtr  |Hodnota  |
|---------|---------|
|Kategorie události     | <ul><li>Správa</li></ul>         |
|Operace     | <ul><li>Vytvořit přiřazení role</li> <li>Odstranit přiřazení role</li> <li>Vytvořit nebo aktualizovat vlastní definici role</li> <li>Odstranit definice rolí</li></ul>      |


Další informace o protokolech aktivit najdete v tématu [zobrazení událostí v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Chcete-li zobrazit protokoly aktivit pomocí Azure Powershellu, použijte [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) příkazu.

Tento příkaz vypíše všechny změny přiřazení role v rámci předplatného za posledních sedm dnů:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Tento příkaz vypíše všechny změny definice rolí ve skupině prostředků za posledních sedm dnů:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Tento příkaz jsou uvedeny všechny přiřazení role a role definition změny v rámci předplatného za posledních sedm dnů a zobrazí výsledky v seznamu:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 4/20/2018 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales-projectforecast"}}

```

## <a name="azure-cli"></a>Azure CLI

Chcete-li zobrazit protokoly aktivit pomocí Azure CLI, použijte [az monitor protokolu aktivit seznamu](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) příkazu.

Tento příkaz vypíše protokoly aktivit ve skupině prostředků od počáteční čas:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Tento příkaz vypíše protokolů aktivit pro poskytovatele prostředků autorizace od počáteční čas:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Azure Log Analytics](../log-analytics/log-analytics-overview.md) je jiný nástroj, můžete použít ke shromažďování a analýze RBAC změny provedené u všech vašich prostředků Azure. Log Analytics má následující výhody:

- Zápis složitých dotazů a logiku
- Integrujte upozornění, Power BI a další nástroje
- Ukládání dat pro delších dob uchování
- Křížový odkaz s jiné protokoly, jako je zabezpečení, virtuální počítač a vlastní

Tady jsou základní kroky, abyste mohli začít:

1. [Vytvoření pracovního prostoru Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Konfigurovat řešení Activity Log Analytics](../azure-monitor/platform/collect-activity-logs.md#configuration) pro váš pracovní prostor.

1. [Zobrazení protokolů aktivit](../azure-monitor/platform/collect-activity-logs.md#using-the-solution). Rychlý způsob, jak přejít na stránku přehled Activity Log Analytics je kliknout **Log Analytics** možnost.

   ![Možnost log Analytics na portálu](./media/change-history-report/azure-log-analytics-option.png)

1. Volitelně můžete použít [prohledávání protokolů](../log-analytics/log-analytics-log-search.md) stránky nebo [portálu pro pokročilou analýzu](../azure-monitor/log-query/get-started-portal.md) pro dotazování a zobrazení protokolů. Další informace o těchto dvou možnostech naleznete v tématu [stránku prohledávání protokolů nebo portálu pro pokročilou analýzu](../azure-monitor/log-query/portals.md).

Tady je dotaz, který vrátí uspořádané podle poskytovatele prostředků cílového nové přiřazení rolí:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Tady je dotaz, který vrátí změny v přiřazení role zobrazí v grafu:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Protokoly aktivit pomocí portálu pro pokročilou analýzu – snímek obrazovky](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Další postup
* [Zobrazení událostí v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorování aktivit předplatného s protokolem aktivit Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
