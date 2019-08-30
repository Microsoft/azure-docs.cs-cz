---
title: Zobrazení protokolů aktivit pro změny RBAC v prostředcích Azure | Microsoft Docs
description: Zobrazení protokolů aktivit pro změny řízení přístupu na základě role (RBAC) na prostředky Azure za posledních 90 dní.
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
ms.openlocfilehash: e5758f480c9216cf71e47509682053b39f0b15bf
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172402"
---
# <a name="view-activity-logs-for-rbac-changes-to-azure-resources"></a>Zobrazení protokolů aktivit pro změny RBAC v prostředcích Azure

Někdy potřebujete informace o změnách řízení přístupu na základě role (RBAC) v prostředcích Azure, například pro účely auditování nebo řešení potíží. Pokaždé, když někdo provede změny v přiřazení rolí nebo definicích rolí v rámci předplatného, změny se přihlásí do [protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md). V protokolech aktivit můžete zobrazit všechny změny RBAC za posledních 90 dní.

## <a name="operations-that-are-logged"></a>Operace, které jsou protokolovány

Tady jsou operace související s RBAC, které se zaznamenávají do protokolu aktivit:

- Vytvořit přiřazení role
- Odstranit přiřazení role
- Vytvořit nebo aktualizovat definici vlastní role
- Odstranit definice rolí

## <a name="azure-portal"></a>portál Azure

Nejjednodušší způsob, jak začít, je zobrazit protokoly aktivit pomocí Azure Portal. Na následujícím snímku obrazovky vidíte příklad protokolu aktivit, který byl filtrován tak, aby zobrazoval operace přiřazení rolí a definice rolí. Obsahuje taky odkaz pro stažení protokolů jako souboru. CSV.

![Protokoly aktivit pomocí portálu – snímek obrazovky](./media/change-history-report/activity-log-portal.png)

Protokol aktivit na portálu má několik filtrů. Tady jsou filtry týkající se RBAC:

|Filtr  |Value  |
|---------|---------|
|Kategorie události     | <ul><li>Administrativní</li></ul>         |
|Operace     | <ul><li>Vytvořit přiřazení role</li> <li>Odstranit přiřazení role</li> <li>Vytvořit nebo aktualizovat definici vlastní role</li> <li>Odstranit definice rolí</li></ul>      |


Další informace o protokolech aktivit najdete [v tématu Zobrazení událostí v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json).

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
                          /Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c","Scope":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"}}

```

## <a name="azure-cli"></a>Azure CLI

Pokud chcete zobrazit protokoly aktivit pomocí Azure CLI, použijte příkaz [AZ monitor Activity-log list](/cli/azure/monitor/activity-log#az-monitor-activity-log-list) .

Tento příkaz vypíše protokoly aktivit ve skupině prostředků od počátečního času:

```azurecli
az monitor activity-log list --resource-group pharma-sales --start-time 2018-04-20T00:00:00Z
```

Tento příkaz vypíše protokoly aktivit pro poskytovatele prostředků autorizace od počátečního času:

```azurecli
az monitor activity-log list --resource-provider "Microsoft.Authorization" --start-time 2018-04-20T00:00:00Z
```

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor

[Protokoly Azure monitor](../log-analytics/log-analytics-overview.md) jsou další nástroj, který můžete použít ke shromažďování a analýze změn RBAC pro všechny prostředky Azure. Protokoly Azure Monitor mají následující výhody:

- Zápis složitých dotazů a logiky
- Integrace s výstrahami, Power BI a dalšími nástroji
- Uložení dat pro delší dobu uchování
- Křížové odkazy na jiné protokoly, jako je zabezpečení, virtuální počítač a vlastní

Zde jsou základní kroky, jak začít:

1. [Vytvořte pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

1. [Nakonfigurujte řešení Activity Log Analytics](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution) pro váš pracovní prostor.

1. [Zobrazení protokolů aktivit](../azure-monitor/platform/activity-log-collect.md#activity-logs-analytics-monitoring-solution). Rychlý způsob, jak přejít na stránku Přehled řešení Activity Log Analytics, je kliknout na možnost **Log Analytics** .

   ![Možnost protokolů Azure Monitor na portálu](./media/change-history-report/azure-log-analytics-option.png)

1. Volitelně můžete k dotazování a zobrazení protokolů použít stránku [prohledávání protokolu](../log-analytics/log-analytics-log-search.md) nebo [portál pro pokročilou analýzu](../azure-monitor/log-query/get-started-portal.md) . Další informace o těchto dvou možnostech najdete na [stránce hledání v protokolu nebo na portálu pro pokročilou analýzu](../azure-monitor/log-query/portals.md).

Tady je dotaz, který vrací přiřazení nových rolí uspořádaná podle poskytovatele cílových prostředků:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments/write" and ActivityStatus == "Succeeded"
| parse ResourceId with * "/providers/" TargetResourceAuthProvider "/" *
| summarize count(), makeset(Caller) by TargetResourceAuthProvider
```

Tady je dotaz, který vrací změny přiřazení rolí zobrazené v grafu:

```
AzureActivity
| where TimeGenerated > ago(60d) and OperationNameValue startswith "Microsoft.Authorization/roleAssignments"
| summarize count() by bin(TimeGenerated, 1d), OperationNameValue
| render timechart
```

![Protokoly aktivit pomocí portálu pro pokročilou analýzu – snímek obrazovky](./media/change-history-report/azure-log-analytics.png)

## <a name="next-steps"></a>Další kroky
* [Zobrazení událostí v protokolu aktivit](/azure/azure-resource-manager/resource-group-audit?toc=%2fazure%2fmonitoring-and-diagnostics%2ftoc.json)
* [Monitorování aktivit předplatného s protokolem aktivit Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
