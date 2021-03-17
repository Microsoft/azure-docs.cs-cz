---
title: Zobrazení protokolů aktivit pro změny v Azure RBAC
description: Zobrazení protokolů aktivit pro změny řízení přístupu na základě role Azure (Azure RBAC) za posledních 90 dní.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 03/01/2021
ms.author: rolyon
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: d9b39bc9a2f00fe83cae0ff78c6346042967e8bf
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042115"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Zobrazení protokolů aktivit pro změny v Azure RBAC

Někdy potřebujete informace o změnách řízení přístupu na základě role v Azure (Azure RBAC), například pro účely auditování nebo řešení potíží. Kdykoli někdo provede změny v přiřazení rolí nebo definicích rolí v rámci předplatných, změny se přihlásí do [protokolu aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md). V protokolech aktivit si můžete zobrazit všechny změny v rámci Azure RBAC za posledních 90 dní.

## <a name="operations-that-are-logged"></a>Operace, které jsou protokolovány

Tady jsou operace související s Azure RBAC, které se zaznamenávají do protokolu aktivit:

- Vytvořit přiřazení role
- Odstranit přiřazení role
- Vytvořit nebo aktualizovat definici vlastní role
- Odstranit definici vlastní role

## <a name="azure-portal"></a>portál Azure

Nejjednodušší způsob, jak začít, je zobrazit si protokoly aktivit přes Azure Portal. Následující snímek obrazovky ukazuje příklad operací přiřazení rolí v protokolu aktivit. Obsahuje taky možnost stahovat protokoly jako soubor CSV.

![Protokoly aktivit pomocí portálu – snímek obrazovky](./media/change-history-report/activity-log-portal.png)

Chcete-li získat další informace, klikněte na položku a otevřete podokno Souhrn. Kliknutím na kartu **JSON** získáte Podrobný protokol.

![Protokoly aktivit pomocí portálu s oknem souhrn otevřít – snímek obrazovky](./media/change-history-report/activity-log-summary-portal.png)

Protokol aktivit na portálu má několik filtrů. Tady jsou filtry vztahující se ke službě Azure RBAC:

| Filtrovat | Hodnota |
| --------- | --------- |
| Kategorie události | <ul><li>Správcovské</li></ul> |
| Operace | <ul><li>Vytvořit přiřazení role</li><li>Odstranit přiřazení role</li><li>Vytvořit nebo aktualizovat definici vlastní role</li><li>Odstranit definici vlastní role</li></ul> |

Další informace o protokolech aktivit najdete v tématu [zobrazení protokolů aktivit pro monitorování akcí v prostředcích](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).


## <a name="interpret-a-log-entry"></a>Interpretace položky protokolu

Výstup protokolu z karty JSON, Azure PowerShell nebo rozhraní příkazového řádku Azure CLI může obsahovat spoustu informací. Tady jsou některé z klíčových vlastností, které se mají najít při pokusu o interpretaci položky protokolu. Způsoby, jak filtrovat výstup protokolu pomocí Azure PowerShell nebo Azure CLI, najdete v následujících částech.

> [!div class="mx-tableFixed"]
> | Vlastnost | Příklady hodnot | Popis |
> | --- | --- | --- |
> | autorizace: akce | Microsoft.Authorization/roleAssignments/write | Vytvořit přiřazení role |
> |  | Microsoft. Authorization/roleAssignments/DELETE | Odstranit přiřazení role |
> |  | Microsoft. Authorization/roleDefinitions/Write | Vytvořit nebo aktualizovat definici role |
> |  | Microsoft. Authorization/roleDefinitions/DELETE | Odstranit definici role |
> | autorizace: obor | /subscriptions/{subscriptionId}<br/>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId} | Rozsah akce |
> | volající | admin@example.com<br/>Objektu | Uživatel zahájil akci. |
> | eventTimestamp | 2021-03-01T22:07:41.126243 Z | Čas, kdy došlo k akci |
> | stav: hodnota | Zahájeno<br/>Úspěšný<br/>Neúspěšný | Stav akce |

## <a name="azure-powershell"></a>Azure PowerShell

K zobrazení protokolů aktivit pomocí Azure PowerShell použijte příkaz [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Tento příkaz vypíše všechny změny přiřazení rolí v předplatném za posledních 7 dnů:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Tento příkaz vypíše všechny změny definice rolí ve skupině prostředků za posledních 7 dnů:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

### <a name="filter-log-output"></a>Filtrovat výstup protokolu

Výstup protokolu může obsahovat spoustu informací. Tento příkaz vypíše všechny změny role přiřazení rolí a definice rolí v předplatném za posledních 7 dní a vyfiltruje výstup:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/role*'} | Format-List Caller,EventTimestamp,{$_.Authorization.Action},Properties
```

V následujícím příkladu je uveden příklad filtrovaného výstupu protokolu při vytváření přiřazení role:

```azurepowershell
Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:42 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

Caller                  : admin@example.com
EventTimestamp          : 3/1/2021 10:07:41 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"{roleAssignmentId}","Properties":{"PrincipalId":"{principalId}","PrincipalType":"User","RoleDefinitionId":"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64","Scope":"/subscriptions/
                          {subscriptionId}/resourceGroups/example-group"}}
                          eventCategory  : Administrative
                          entity         : /subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}
                          message        : Microsoft.Authorization/roleAssignments/write
                          hierarchy      : {tenantId}/{subscriptionId}

```

Pokud k vytvoření přiřazení rolí používáte instanční objekt, vlastnost volající bude ID instančního objektu služby. K získání informací o instančním objektu můžete použít [příkaz Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) .

```Example
Caller                  : {objectId}
EventTimestamp          : 3/1/2021 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: {serviceRequestId}
                          eventCategory  : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit protokoly aktivit pomocí Azure CLI, použijte příkaz [AZ monitor Activity-log list](/cli/azure/monitor/activity-log#az_monitor_activity_log_list) .

Tento příkaz vypíše protokoly aktivit ve skupině prostředků od 1. března, přičemž se bude hledat sedm dní.

```azurecli
az monitor activity-log list --resource-group example-group --start-time 2021-03-01 --offset 7d
```

Tento příkaz vypíše protokoly aktivit pro poskytovatele prostředků autorizace z 1. března, přičemž se bude hledat sedm dní.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d
```

### <a name="filter-log-output"></a>Filtrovat výstup protokolu

Výstup protokolu může obsahovat spoustu informací. Tento příkaz vypíše všechny změny role přiřazení rolí a definice rolí v rámci předplatného, které hledají sedm dní a filtrují výstup:

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2021-03-01 --offset 7d --query '[].{authorization:authorization, caller:caller, eventTimestamp:eventTimestamp, properties:properties}'
```

V následujícím příkladu je uveden příklad filtrovaného výstupu protokolu při vytváření přiřazení role:

```azurecli
[
 {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:42.456241+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "serviceRequestId": "{serviceRequestId}",
      "statusCode": "Created"
    }
  },
  {
    "authorization": {
      "action": "Microsoft.Authorization/roleAssignments/write",
      "role": null,
      "scope": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}"
    },
    "caller": "admin@example.com",
    "eventTimestamp": "2021-03-01T22:07:41.126243+00:00",
    "properties": {
      "entity": "/subscriptions/{subscriptionId}/resourceGroups/example-group/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
      "eventCategory": "Administrative",
      "hierarchy": "{tenantId}/{subscriptionId}",
      "message": "Microsoft.Authorization/roleAssignments/write",
      "requestbody": "{\"Id\":\"{roleAssignmentId}\",\"Properties\":{\"PrincipalId\":\"{principalId}\",\"PrincipalType\":\"User\",\"RoleDefinitionId\":\"/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64\",\"Scope\":\"/subscriptions/{subscriptionId}/resourceGroups/example-group\"}}"
    }
  }
]
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](../azure-monitor/logs/log-query-overview.md) jsou další nástroj, který můžete použít ke shromažďování a analýze změn ve službě Azure RBAC pro všechny prostředky Azure. Protokoly Azure Monitor mají následující výhody:

- Zápis složitých dotazů a logiky
- Integrace s výstrahami, Power BI a dalšími nástroji
- Uložení dat pro delší dobu uchování
- Křížové odkazy na jiné protokoly, jako je zabezpečení, virtuální počítač a vlastní

Zde jsou základní kroky, jak začít:

1. [Vytvořte pracovní prostor Log Analytics](../azure-monitor/logs/quick-create-workspace.md).

1. [Nakonfigurujte řešení Activity Log Analytics](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution) pro váš pracovní prostor.

1. [Zobrazení protokolů aktivit](../azure-monitor/essentials/activity-log.md#activity-log-analytics-monitoring-solution). Rychlý způsob, jak přejít na stránku Přehled řešení Activity Log Analytics, je kliknout na možnost **protokoly** .

   ![Možnost protokolů Azure Monitor na portálu](./media/change-history-report/azure-log-analytics-option.png)

1. Volitelně můžete použít [Log Analytics Azure monitor](../azure-monitor/logs/log-analytics-tutorial.md) k dotazování a zobrazení protokolů. Další informace najdete v tématu [Začínáme s dotazy protokolu v Azure monitor](../azure-monitor/logs/get-started-queries.md).

Tady je dotaz, který vrací přiřazení nových rolí uspořádaná podle poskytovatele cílových prostředků:

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

![Protokoly aktivit pomocí portálu pro pokročilou analýzu – snímek obrazovky](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Další kroky
* [Zobrazení protokolů aktivit pro monitorování akcí u prostředků](../azure-resource-manager/management/view-activity-logs.md?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorování aktivity předplatného pomocí protokolu aktivit Azure](../azure-monitor/essentials/platform-logs-overview.md)