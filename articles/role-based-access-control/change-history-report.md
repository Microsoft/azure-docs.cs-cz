---
title: Zobrazení protokolů aktivit pro změny Azure RBAC
description: Zobrazení protokolů aktivit pro azure role-based řízení přístupu (Azure RBAC) změny prostředků Azure za posledních 90 dní.
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
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2024bd14241184338195ed635039bae774da816
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78161760"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Zobrazení protokolů aktivit pro změny Azure RBAC

Někdy potřebujete informace o změnách řízení přístupu na základě rolí Azure (Azure RBAC), jako je například pro účely auditování nebo řešení potíží. Kdykoli někdo provede změny přiřazení rolí nebo definice rolí v rámci vašich předplatných, změny získat zaznamenány v [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md). Můžete zobrazit protokoly aktivit zobrazíte všechny změny Azure RBAC za posledních 90 dní.

## <a name="operations-that-are-logged"></a>Operace, které jsou protokolovány

Tady jsou operace související s Azure RBAC, které jsou protokolovány v protokolu aktivit:

- Vytvořit přiřazení role
- Odstranit přiřazení role
- Vytvoření nebo aktualizace vlastní definice role
- Odstranit vlastní definici role

## <a name="azure-portal"></a>portál Azure

Nejjednodušší způsob, jak začít, je zobrazit si protokoly aktivit přes Azure Portal. Následující snímek obrazovky ukazuje příklad operací přiřazení rolí v protokolu aktivit. Obsahuje také možnost stáhnout protokoly jako soubor CSV.

![Protokoly aktivit pomocí portálu - snímek obrazovky](./media/change-history-report/activity-log-portal.png)

Protokol aktivit na portálu má několik filtrů. Tady jsou filtry související s Azure RBAC:

| Filtr | Hodnota |
| --------- | --------- |
| Kategorie události | <ul><li>Správa</li></ul> |
| Operace | <ul><li>Vytvořit přiřazení role</li><li>Odstranit přiřazení role</li><li>Vytvoření nebo aktualizace vlastní definice role</li><li>Odstranit vlastní definici role</li></ul> |

Další informace o protokolech aktivit naleznete v [tématu Zobrazení protokolů aktivit ke sledování akcí na prostředcích](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Chcete-li zobrazit protokoly aktivit pomocí Azure PowerShellu, použijte příkaz [Get-AzLog.](/powershell/module/Az.Monitor/Get-AzLog)

Tento příkaz uvádí všechny změny přiřazení rolí v předplatném za posledních sedm dní:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Tento příkaz obsahuje seznam všech změn definice role ve skupině prostředků za posledních sedm dní:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Tento příkaz zobrazí seznam všech změn přiřazení rolí a definice role v předplatném za posledních sedm dní a zobrazí výsledky v seznamu:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

```Example
Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:07 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: 11111111-1111-1111-1111-111111111111

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Chcete-li zobrazit protokoly aktivit pomocí příkazového příkazu k onomu Azure CLI, použijte příkaz [seznamu protokolu aktivit monitorování az.](/cli/azure/monitor/activity-log#az-monitor-activity-log-list)

Tento příkaz zobrazí seznam protokolů aktivit ve skupině zdrojů od 27.

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Tento příkaz zobrazí seznam protokolů aktivit pro poskytovatele autorizačních prostředků od 27.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure Monitor je](../log-analytics/log-analytics-overview.md) další nástroj, který můžete použít ke shromažďování a analýze změny Azure RBAC pro všechny prostředky Azure. Protokoly Azure Monitor má následující výhody:

- Psaní složitých dotazů a logiky
- Integrace s výstrahami, Power BI a dalšími nástroji
- Ukládání dat na delší dobu uchovávání
- Křížový odkaz s jinými protokoly, jako je zabezpečení, virtuální počítač a vlastní

Zde jsou základní kroky, jak začít:

1. [Vytvořte pracovní prostor Analýzy protokolů](../azure-monitor/learn/quick-create-workspace.md).

1. [Nakonfigurujte řešení Analýzy protokolů aktivit](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) pro váš pracovní prostor.

1. [Zobrazení protokolů aktivit](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Rychlý způsob, jak přejít na stránku Přehled řešení Protokol protokolu aktivit, je kliknout na možnost **Protokoly.**

   ![Možnost protokolů Azure Monitoru na portálu](./media/change-history-report/azure-log-analytics-option.png)

1. Volitelně použijte [Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md) k dotazování a zobrazení protokolů. Další informace najdete [v tématu Začínáme s dotazy protokolu Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

Tady je dotaz, který vrací nová přiřazení rolí uspořádaná podle cílového poskytovatele prostředků:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Tady je dotaz, který vrací změny přiřazení rolí zobrazené v grafu:

```Kusto
AzureActivity
| where TimeGenerated > ago(60d) and Authorization contains "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationName
| render timechart
```

![Protokoly aktivit pomocí portálu Advanced Analytics – snímek obrazovky](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Další kroky
* [Zobrazení událostí v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorování aktivit předplatného s protokolem aktivit Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
