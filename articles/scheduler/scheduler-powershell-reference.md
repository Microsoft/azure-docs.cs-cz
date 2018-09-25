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
ms.openlocfilehash: a06439150ac255e7b436082ecc88702bf0c1dec1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46991092"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>Referenční informace k rutin Powershellu pro Azure Scheduler

> [!IMPORTANT]
> [Služba Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduleru, která se vyřazuje. K plánování úloh, [místo toho vyzkoušet Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

K vytváření skriptů pro vytváření a Správa úloh a kolekcí úloh Scheduleru, můžete použít rutiny prostředí PowerShell. Tento článek uvádí hlavní [rutiny Powershellu pro Azure Scheduler](/powershell/module/azurerm.scheduler) s odkazy na články odkaz. Instalace Azure Powershellu pro vaše předplatné Azure, najdete v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview). Další informace o [rutiny Azure Resource Manageru](/powershell/azure/overview), naleznete v tématu [pomocí Azure Powershellu s Azure Resource Managerem](../powershell-azure-resource-manager.md).

| Rutina | Popis |
|--------|-------------|
| [Zakázat AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Vypne kolekci úloh. |
| [Povolit AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Umožňuje kolekci úloh. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Získá Plánovač úloh. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Získá kolekci úloh. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Načte historii úlohy. |
| [Nové AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Vytvoří úlohu služby HTTP. |
| [Nové AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Vytvoří kolekci úloh. |
| [Nové AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Vytvoří úlohu fronty služby Service Bus. |
| [Nové AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Vytvoří úlohu téma služby Service Bus. |
| [Nové AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Vytvoří úlohu fronty úložiště. |
| [Odebrat AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Odebere úlohy Scheduleru. |
| [Odebrat AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Odstraní kolekci úloh. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Upraví HTTP Plánovač úloh. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Upraví kolekci úloh. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Upraví úlohy fronty služby Service Bus. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Upraví úlohu téma služby Service Bus. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Upraví úlohy fronty úložiště. |
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
* [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)
