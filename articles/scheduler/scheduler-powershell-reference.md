---
title: Reference rutin Powershellu pro Scheduler
description: Reference rutin Powershellu pro Scheduler
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: 141919ab4506b3de4c4a69670dcf54c60ee6409c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23866048"
---
# <a name="scheduler-powershell-cmdlets-reference"></a>Reference rutin Powershellu pro Scheduler
Následující tabulka popisuje a obsahuje odkazy na stránce odkaz každé hlavní rutiny ve službě Azure Scheduler.

Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). 

Další informace o [rutiny Azure Resource Manager](/powershell/azure/overview), najdete v části [použití Azure Powershellu s Azure Resource Manager](../powershell-azure-resource-manager.md).

| Rutina | Popis rutiny |
| --- | --- |
| [Zakázat AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |Zakáže kolekce úloh. |
| [Povolit AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Umožňuje kolekce úloh. |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Získá plánovače úloh. |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Získá kolekce úloh. |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Získá historie úlohy. |
| [Nové AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Vytvoří úlohu HTTP. |
| [Nové AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Vytvoří kolekci úloh. |
| [Nové AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) |Vytvoří úlohu fronty service bus. |
| [Nové AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Vytvoří úlohu tématu service bus. |
| [Nové AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Vytvoří úlohu fronty úložiště. |
| [Odebrat AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Odebere plánovače úloh. |
| [Odebrat AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Odebere kolekce úloh. |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Upravuje HTTP plánovače úloh. |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |Upravuje kolekce úloh. |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Upravuje úlohy fronty sběrnice služby. |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Upravuje úlohu tématu service bus. |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Upravuje úlohy fronty úložiště. |

Podrobnější informace můžete spustit některý z následujících rutin: 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>Viz také
 [Co je Scheduler?](scheduler-intro.md)

 [Koncepty, terminologie a hierarchie entit Azure Scheduleru](scheduler-concepts-terms.md)

 [Úvod do používání Scheduleru na portálu Azure Portal](scheduler-get-started-portal.md)

 [Plány a fakturace v Azure Scheduleru](scheduler-plans-billing.md)

 [REST API Azure Scheduleru – referenční informace](https://msdn.microsoft.com/library/mt629143)

 [Vysoká dostupnost a spolehlivost Azure Scheduleru](scheduler-high-availability-reliability.md)

 [Omezení, výchozí hodnoty a chybové kódy Azure Scheduleru](scheduler-limits-defaults-errors.md)

 [Odchozí ověření Azure Scheduleru](scheduler-outbound-authentication.md)

