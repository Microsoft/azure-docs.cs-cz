---
title: Referenční informace k rutinám PowerShellu – Azure Scheduler
description: Další informace o rutinách PowerShellu pro Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 1ad1d6b9f59fa51b9e27fe5b70ce4e2a5d36f3ad
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300882"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referenční informace k rutinám PowerShellu pro Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud chcete vytvářet skripty pro vytváření a správu úloh a kolekcí úloh Scheduleru, můžete použít rutiny PowerShellu. V tomto článku jsou uvedené hlavní rutiny PowerShellu pro Azure Scheduler s odkazy na jejich referenční články. Pokud chcete nainstalovat Azure PowerShell pro předplatné Azure, přečtěte si téma [instalace a konfigurace Azure PowerShell](/powershell/azure/overview). Další informace o [rutinách Azure Resource Manager](/powershell/azure/overview)najdete v tématu [použití Azure PowerShell s Azure Resource Manager](../powershell-azure-resource-manager.md).

| Rutiny | Popis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Zakáže kolekci úloh. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Povolí kolekci úloh. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Získá úlohy plánovače. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Načte kolekce úloh. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Získá historii úlohy. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Vytvoří úlohu HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Vytvoří kolekci úloh. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Vytvoří úlohu Service Bus Queue. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Vytvoří úlohu Service Bus tématu. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Vytvoří úlohu fronty úložiště. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Odebere úlohu Scheduleru. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Odebere kolekci úloh. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Upraví úlohu protokolu HTTP Scheduleru. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Upraví kolekci úloh. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Upraví úlohu Service Bus Queue. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Upraví úlohu Service Bus tématu. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Upraví úlohu fronty úložiště. |
||| 

Další podrobnosti můžete spustit některou z těchto rutin: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Viz také:

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit](scheduler-concepts-terms.md)
* [Vytvoření a naplánování první úlohy – Azure Portal](scheduler-get-started-portal.md)
* [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)
