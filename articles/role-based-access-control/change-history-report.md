---
title: Zobrazení protokolů aktivit pro změny v Azure RBAC
description: Zobrazení protokolů aktivit pro řízení přístupu na základě role Azure (Azure RBAC) se v posledních 90 dnech změní na prostředky Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017, devx-track-azurecli
ms.openlocfilehash: 4ccd668fb6afa6787fadeda6ed92ebd954e2b892
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94657801"
---
# <a name="view-activity-logs-for-azure-rbac-changes"></a>Zobrazení protokolů aktivit pro změny v Azure RBAC

Někdy potřebujete informace o změnách řízení přístupu na základě role v Azure (Azure RBAC), například pro účely auditování nebo řešení potíží. Kdykoli někdo provede změny v přiřazení rolí nebo definicích rolí v rámci předplatných, změny se přihlásí do [protokolu aktivit Azure](../azure-monitor/platform/platform-logs-overview.md). V protokolech aktivit si můžete zobrazit všechny změny v rámci Azure RBAC za posledních 90 dní.

## <a name="operations-that-are-logged"></a>Operace, které jsou protokolovány

Tady jsou operace související s Azure RBAC, které se zaznamenávají do protokolu aktivit:

- Vytvořit přiřazení role
- Odstranit přiřazení role
- Vytvořit nebo aktualizovat definici vlastní role
- Odstranit definici vlastní role

## <a name="azure-portal"></a>portál Azure

Nejjednodušší způsob, jak začít, je zobrazit si protokoly aktivit přes Azure Portal. Následující snímek obrazovky ukazuje příklad operací přiřazení rolí v protokolu aktivit. Obsahuje taky možnost stahovat protokoly jako soubor CSV.

![Protokoly aktivit pomocí portálu – snímek obrazovky](./media/change-history-report/activity-log-portal.png)

Protokol aktivit na portálu má několik filtrů. Tady jsou filtry vztahující se ke službě Azure RBAC:

| Filtr | Hodnota |
| --------- | --------- |
| Kategorie události | <ul><li>Správcovské</li></ul> |
| Operace | <ul><li>Vytvořit přiřazení role</li><li>Odstranit přiřazení role</li><li>Vytvořit nebo aktualizovat definici vlastní role</li><li>Odstranit definici vlastní role</li></ul> |

Další informace o protokolech aktivit najdete v tématu [zobrazení protokolů aktivit pro monitorování akcí v prostředcích](../azure-resource-manager/management/view-activity-logs.md?toc=%252fazure%252fmonitoring-and-diagnostics%252ftoc.json).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

K zobrazení protokolů aktivit pomocí Azure PowerShell použijte příkaz [Get-AzLog](/powershell/module/Az.Monitor/Get-AzLog) .

Tento příkaz vypíše všechny změny přiřazení rolí v předplatném za posledních 7 dnů:

```azurepowershell
Get-AzLog -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleAssignments/*'}
```

Tento příkaz vypíše všechny změny definice rolí ve skupině prostředků za posledních 7 dnů:

```azurepowershell
Get-AzLog -ResourceGroupName pharma-sales -StartTime (Get-Date).AddDays(-7) | Where-Object {$_.Authorization.Action -like 'Microsoft.Authorization/roleDefinitions/*'}
```

Tento příkaz vypíše všechny změny role přiřazení rolí a definice rolí v předplatném za posledních 7 dnů a výsledky se zobrazí v seznamu:

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
                          eventCategory  : Administrative

Caller                  : alain@example.com
EventTimestamp          : 2/27/2020 9:18:05 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              :
                          requestbody    : {"Id":"22222222-2222-2222-2222-222222222222","Properties":{"PrincipalId":"33333333-3333-3333-3333-333333333333","RoleDefinitionId":"/subscriptions/00000000-0000-0000-0000-000000000000/providers
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

Pokud k vytvoření přiřazení rolí používáte instanční objekt, vlastnost volající bude ID objektu. K získání informací o instančním objektu můžete použít [příkaz Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) .

```Example
Caller                  : 44444444-4444-4444-4444-444444444444
EventTimestamp          : 6/4/2020 9:43:08 PM
$_.Authorization.Action : Microsoft.Authorization/roleAssignments/write
Properties              : 
                          statusCode     : Created
                          serviceRequestId: 55555555-5555-5555-5555-555555555555
                          category       : Administrative
```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit protokoly aktivit pomocí Azure CLI, použijte příkaz [AZ monitor Activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

Tento příkaz vypíše protokoly aktivit ve skupině prostředků z 27. února, přičemž se bude hledat sedm dní.

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2020-02-27 --offset 7d
```

Tento příkaz vypíše protokoly aktivit pro poskytovatele prostředků autorizací z 27. února, který se bude zasílat do sedmi dnů.

```azurecli
az monitor activity-log list --namespace "Microsoft.Authorization" --start-time 2020-02-27 --offset 7d
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](../azure-monitor/log-query/log-query-overview.md) jsou další nástroj, který můžete použít ke shromažďování a analýze změn ve službě Azure RBAC pro všechny prostředky Azure. Protokoly Azure Monitor mají následující výhody:

- Zápis složitých dotazů a logiky
- Integrace s výstrahami, Power BI a dalšími nástroji
- Uložení dat pro delší dobu uchování
- Křížové odkazy na jiné protokoly, jako je zabezpečení, virtuální počítač a vlastní

Zde jsou základní kroky, jak začít:

1. [Vytvořte pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Nakonfigurujte řešení Activity Log Analytics](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution) pro váš pracovní prostor.

1. [Zobrazení protokolů aktivit](../azure-monitor/platform/activity-log.md#activity-log-analytics-monitoring-solution). Rychlý způsob, jak přejít na stránku Přehled řešení Activity Log Analytics, je kliknout na možnost **protokoly** .

   ![Možnost protokolů Azure Monitor na portálu](./media/change-history-report/azure-log-analytics-option.png)

1. Volitelně můžete použít [Log Analytics Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md) k dotazování a zobrazení protokolů. Další informace najdete v tématu [Začínáme s Azure monitor dotazy protokolu](../azure-monitor/log-query/get-started-queries.md).

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
* [Zobrazení událostí v protokolu aktivit](../azure-resource-manager/management/view-activity-logs.md?toc=%252fazure%252fmonitoring-and-diagnostics%252ftoc.json)
* [Monitorování aktivit předplatného s protokolem aktivit Azure](../azure-monitor/platform/platform-logs-overview.md)