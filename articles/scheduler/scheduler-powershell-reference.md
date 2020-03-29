---
title: Odkaz na rutiny prostředí PowerShell
description: Další informace o rutinách Prostředí PowerShell pro Plánovač Azure
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 577e2128cf7e4e9f914ec5504917053acb3c19d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898485"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Odkaz na rutiny prostředí PowerShell pro Plánovač Azure

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

Chcete-li vytvářet skripty pro vytváření a správu úloh plánovače a kolekcí úloh, můžete použít rutiny prostředí PowerShell. Tento článek uvádí hlavní rutiny prostředí PowerShell pro Plánovač Azure s odkazy na jejich referenční články. Pokud chcete nainstalovat Azure PowerShell pro předplatné Azure, přečtěte [si, jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview). Další informace o [rutinách Azure Resource Manageru](/powershell/azure/overview)najdete [v tématu Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Rutina | Popis |
|--------|-------------|
| [Zakázat azschedulerjobcollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Zakáže kolekci úloh. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Umožňuje kolekci úloh. |
| [Získat-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Získá úlohy plánovače. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Získá kolekce úloh. |
| [Historie get-azschedulerjob](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Získá historii práce. |
| [Nová azschedulerhttpjob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Vytvoří úlohu HTTP. |
| [Nová kolekce azschedulerjob](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Vytvoří kolekci úloh. |
| [Nová-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Vytvoří úlohu fronty služby Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Vytvoří úlohu tématu služby Service Bus. |
| [Nová-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Vytvoří úlohu fronty úložiště. |
| [Odebrat-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Odebere úlohu plánovače. |
| [Odebrat azSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Odebere kolekci úloh. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Upraví úlohu HTTP plánovače. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Upraví kolekci úloh. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Upraví úlohu fronty služby Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Upraví úlohu tématu služby Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Upraví úlohu fronty úložiště. |
||| 

Další podrobnosti můžete spustit některou z těchto rutin: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)