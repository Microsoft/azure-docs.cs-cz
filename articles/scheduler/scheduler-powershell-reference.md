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
ms.openlocfilehash: 4b179c50af8b1ffc4313a49da978f178915ec9cc
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470810"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referenční informace k rutin Powershellu pro Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje službu Azure Scheduler, která se vyřazuje z provozu. K plánování úloh [místo ní zkuste použít Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

K vytváření skriptů pro vytváření a Správa úloh a kolekcí úloh Scheduleru, můžete použít rutiny prostředí PowerShell. Tento článek uvádí hlavní rutiny Powershellu pro Azure Scheduler s odkazy na články odkaz. Instalace Azure Powershellu pro vaše předplatné Azure, najdete v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview). Další informace o [rutiny Azure Resource Manageru](/powershell/azure/overview), naleznete v tématu [pomocí Azure Powershellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

| Rutina | Popis |
|--------|-------------|
| [Disable-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Vypne kolekci úloh. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Umožňuje kolekci úloh. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Získá Plánovač úloh. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Získá kolekci úloh. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Načte historii úlohy. |
| [New-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Vytvoří úlohu služby HTTP. |
| [New-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Vytvoří kolekci úloh. |
| [New-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Vytvoří úlohu fronty služby Service Bus. |
| [New-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Vytvoří úlohu téma služby Service Bus. |
| [New-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Vytvoří úlohu fronty úložiště. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Odebere úlohy Scheduleru. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Odstraní kolekci úloh. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Upraví HTTP Plánovač úloh. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Upraví kolekci úloh. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Upraví úlohy fronty služby Service Bus. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Upraví úlohu téma služby Service Bus. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Upraví úlohy fronty úložiště. |
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
