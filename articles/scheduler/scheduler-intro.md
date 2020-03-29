---
title: Co je Azure Scheduler?
description: Vytvoření plánu a spuštění automatizovaných úloh, které volají služby uvnitř nebo vně Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: e851da0013cf3a9ff6bb1a0fc1c073b5b796c54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898540"
---
# <a name="what-is-azure-scheduler"></a>Co je Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje Azure Scheduler, který [je vyřazen .](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Pokud chcete pokračovat v práci s úlohami, které jste nastavili v Plánovači, [migrujte co](../scheduler/migrate-from-scheduler-to-logic-apps.md) nejdříve do Azure Logic Apps. 
>
> Plánovač už není k dispozici na webu Azure Portal, ale rutiny [ROZHRANÍ REST API](/rest/api/scheduler) a Azure [Scheduler PowerShell](scheduler-powershell-reference.md) zůstávají k dispozici v tuto chvíli, takže můžete spravovat své úlohy a kolekce úloh.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomáhá vytvářet [úlohy](../scheduler/scheduler-concepts-terms.md) spouštěné v cloudu tím, že umožňuje deklarativně popisovat akce. Služba pak tyto akce automaticky plánuje a spouští. Můžete například volat služby v i mimo Azure, například koncové body HTTP nebo HTTPS, a také odesílat zprávy do front služby Azure Storage a front nebo témat služby Azure Service Bus. Úlohy můžete spouštět okamžitě nebo později. Scheduler bez problémů podporuje [komplexní plánování a pokročilé opakování](../scheduler/scheduler-advanced-complexity.md). Scheduler určuje, kdy se mají úlohy spouštět, uchovává historii výsledků úloh, kterou můžete procházet, a pak předvídatelně a spolehlivě plánuje úlohy, které se mají spustit.

Službu Scheduler používají na pozadí také další plánovací nástroje Azure, například [Azure WebJobs](../app-service/webjobs-create.md), což je funkce [Web Apps](https://azure.microsoft.com/services/app-service/web/) ve službě Azure App Service. Komunikaci pro tyto akce můžete spravovat pomocí [rozhraní REST API plánovače](https://docs.microsoft.com/rest/api/scheduler/), které vám pomůže spravovat komunikaci pro tyto akce.

Tady je několik scénářů, ve kterých vám Scheduler může pomoct:

* Spouštění opakujících se akcí aplikací: Například pravidelné shromažďování dat z Twitteru do informačního kanálu.

* Provádění každodenní údržby: Například každodenní vyřazování protokolů, zálohování a další úkoly týkající se údržby.

  Jako správce například můžete chtít každý den v 1:00 po dobu dalších devíti měsíců zálohovat databázi.

I když můžete pomocí služby Scheduler vytvářet, spravovat a spouštět plánované úlohy, Scheduler úlohy nehostuje ani nespouští kód. Tato služba pouze *volá* služby nebo kód hostované jinde, například v Azure, v místním prostředí nebo u jiného poskytovatele. Scheduler může provádět volání přes HTTP, HTTPS, frontu úložiště, frontu služby Service Bus nebo téma služby Service Bus.

Chcete-li vytvořit, naplánovat, spravovat, aktualizovat nebo odstranit [úlohy](../scheduler/scheduler-concepts-terms.md)a kolekce úloh , můžete použít kód, [rozhraní REST API plánovače](https://docs.microsoft.com/rest/api/scheduler/)nebo [rutiny Prostředí PowerShell plánovače](scheduler-powershell-reference.md).

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Plány a fakturace pro Plánovač Azure](scheduler-plans-billing.md)
* [Vytváření složitých plánů a pokročilé opakování pomocí Plánovače Azure](scheduler-advanced-complexity.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)
