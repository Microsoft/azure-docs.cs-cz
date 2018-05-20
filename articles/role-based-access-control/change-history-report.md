---
title: Zobrazit protokoly aktivity pro RBAC změny v Azure | Microsoft Docs
description: Zobrazit protokoly aktivity pro změny řízení přístupu na základě rolí za posledních 90 dnů.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2017
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e48ea2293c186bbc337f9d70464df374d64b5e61
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="view-activity-logs-for-role-based-access-control-changes"></a>Zobrazit protokoly aktivity pro změny řízení přístupu na základě rolí

Vždy, když někdo provede změny definice rolí nebo přiřazení rolí v rámci vašich předplatných, změny se budou protokolovat [protokol činnosti Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) v administrativní kategorie. Můžete zobrazit protokoly aktivity zobrazíte všechny změny přístupu na základě rolí k řízení (RBAC) za posledních 90 dnů.

## <a name="operations-that-are-logged"></a>Operace, které se protokolují

Zde jsou operací souvisejících s RBAC, které se zaznamenávají v protokolu aktivit:

- Vytvořit nebo aktualizovat vlastní definici role
- Odstranit definice rolí
- Vytvořit přiřazení role
- Odstranit přiřazení role

## <a name="azure-portal"></a>Azure Portal

Nejjednodušší způsob, jak začít pracovat se má zobrazit protokoly aktivity pomocí portálu Azure. Následující snímek obrazovky ukazuje příklad protokol činnosti filtrovanou zobrazíte **správy** kategorie spolu s definice role a operace přiřazení role. Zahrnuje také odkaz ke stažení protokolů do souboru CSV.

![Protokoly aktivity portálu – snímek obrazovky](./media/change-history-report/activity-log-portal.png)

Další informace najdete v tématu [zobrazit události v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

Chcete-li zobrazit protokoly aktivity s prostředím Azure PowerShell, použijte [Get-AzureRmLog](/powershell/module/azurerm.insights/get-azurermlog) příkaz.

Tento příkaz vypíše všechny změny v přiřazení role v rámci předplatného pro posledních sedmi dnech:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Tento příkaz vypíše všechny změny definice role ve skupině prostředků pro posledních sedmi dnech:

```azurepowershell
Get-AzureRmLog -ResourceGroupName pharma-sales-projectforecast -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Tento příkaz zobrazí všechny změny definice role v rámci předplatného pro posledních sedmi dnech a přiřazení role a zobrazí výsledky v seznamu:

```azurepowershell
Get-AzureRmLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
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

Chcete-li zobrazit protokoly aktivity pomocí Azure CLI, použijte [az monitorování protokol aktivit seznamu](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) příkaz.

Tento příkaz vypíše protokoly aktivit ve skupině prostředků od čas spuštění:

```azurecli
az monitor activity-log list --resource-group pharma-sales-projectforecast --start-time 2018-04-20T00:00:00Z
```

Tento příkaz vypíše protokoly aktivity pro poskytovatele prostředků autorizace od čas spuštění:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-log-analytics"></a>Azure Log Analytics

[Azure Log Analytics](../log-analytics/log-analytics-overview.md) je jiný nástroj, můžete shromažďovat a analyzovat změny řízení přístupu na základě rolí pro všechny prostředky Azure. Analýzy protokolů má následující výhody:

- Zápis složitých dotazů a logiku
- Integrovat výstrahy, Power BI a další nástroje
- Uložení dat po delší dobu uchovávání dat
- Vytvořit křížový odkaz s další protokoly, jako je například zabezpečení, virtuální počítač a vlastní

Zde jsou základní kroky, abyste mohli začít:

1. [Vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

1. [Nakonfigurujte řešení, analýzy protokolů aktivity](../log-analytics/log-analytics-activity.md#configuration) vašeho pracovního prostoru.

1. [Zobrazit protokoly aktivity](../log-analytics/log-analytics-activity.md#using-the-solution). Rychlý způsob, jak přejít na stránku přehled Analytics protokolu aktivit je kliknout na odkaz **analýzy protokolů** možnost.

   ![Log Analytics možnost portálu](./media/change-history-report/azure-log-analytics-option.png)

1. Volitelně můžete použít [hledání protokolů](../log-analytics/log-analytics-log-search.md) stránky nebo [pokročilé analýzy portál](https://docs.loganalytics.io/docs/Learn) pro dotazování a zobrazení protokolů. Další informace o těchto dvou možností najdete v tématu [stránky hledání protokolu nebo portálu pokročilé analýzy](../log-analytics/log-analytics-log-search-portals.md).

Zde je dotaz, který vrátí nové přiřazení role uspořádané podle zprostředkovatel prostředků cíle:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Zde je dotaz, který vrátí změny v přiřazení role v grafu zobrazí:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationName startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Protokoly aktivity pomocí portálu Advanced Analytics – snímek obrazovky](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Další postup
* [Zobrazení událostí v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Sledování aktivity předplatné s protokol činnosti Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
