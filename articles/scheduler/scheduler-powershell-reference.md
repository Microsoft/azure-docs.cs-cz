---
title: Reference rutin prostředí PowerShell – Azure Scheduleru
description: Další informace o rutinách prostředí PowerShell pro Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 5c80e86699d671994a0989b99c0f97ebe2680592
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044993"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referenční informace k rutin Powershellu pro Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje službu Azure Scheduler, která se vyřazuje z provozu. K plánování úloh [místo ní zkuste použít Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K vytváření skriptů pro vytváření a Správa úloh a kolekcí úloh Scheduleru, můžete použít rutiny prostředí PowerShell. Tento článek uvádí hlavní [rutiny Powershellu pro Azure Scheduler](/powershell/module/azurerm.scheduler) s odkazy na články odkaz. Instalace Azure Powershellu pro vaše předplatné Azure, najdete v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview). Další informace o [rutiny Azure Resource Manageru](/powershell/azure/overview), naleznete v tématu [pomocí Azure Powershellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

| Rutina | Popis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/az.scheduler/disable-azschedulerjobcollection) |Vypne kolekci úloh. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/az.scheduler/enable-azschedulerjobcollection) |Umožňuje kolekci úloh. |
| [Get-AzSchedulerJob](/powershell/module/az.scheduler/get-azschedulerjob) |Získá Plánovač úloh. |
| [Get-AzSchedulerJobCollection](/powershell/module/az.scheduler/get-azschedulerjobcollection) |Získá kolekci úloh. |
| [Get-AzSchedulerJobHistory](/powershell/module/az.scheduler/get-azschedulerjobhistory) |Načte historii úlohy. |
| [New-AzSchedulerHttpJob](/powershell/module/az.scheduler/new-azschedulerhttpjob) |Vytvoří úlohu služby HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/az.scheduler/new-azschedulerjobcollection) |Vytvoří kolekci úloh. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/new-azschedulerservicebusqueuejob) | Vytvoří úlohu fronty služby Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/new-azschedulerservicebustopicjob) |Vytvoří úlohu téma služby Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/new-azschedulerstoragequeuejob) |Vytvoří úlohu fronty úložiště. |
| [Remove-AzSchedulerJob](/powershell/module/az.scheduler/remove-azschedulerjob) |Odebere úlohy Scheduleru. |
| [Remove-AzSchedulerJobCollection](/powershell/module/az.scheduler/remove-azschedulerjobcollection) |Odstraní kolekci úloh. |
| [Set-AzSchedulerHttpJob](/powershell/module/az.scheduler/set-azschedulerhttpjob) |Upraví HTTP Plánovač úloh. |
| [Set-AzSchedulerJobCollection](/powershell/module/az.scheduler/set-azschedulerjobcollection) |Upraví kolekci úloh. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/az.scheduler/set-azschedulerservicebusqueuejob) |Upraví úlohy fronty služby Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/az.scheduler/set-azschedulerservicebustopicjob) |Upraví úlohu téma služby Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/az.scheduler/set-azschedulerstoragequeuejob) |Upraví úlohy fronty úložiště. |
||| 

Další podrobnosti můžete spustit některý z těchto rutin: 

```
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Další informace najdete v tématech

* [Co je Azure Scheduler?](scheduler-intro.md)
* [Koncepty, terminologie a hierarchie entit](scheduler-concepts-terms.md)
* [Vytvoření a naplánování první úlohy – Azure portal](scheduler-get-started-portal.md)
* [REST API služby Azure Scheduler – referenční informace](https://msdn.microsoft.com/library/mt629143)
