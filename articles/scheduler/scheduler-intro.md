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
ms.openlocfilehash: 66ec285554299214122a4093837d3506bf642b13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368124"
---
# <a name="what-is-azure-scheduler"></a>Co je Azure Scheduler?

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) nahrazuje [vyřazení](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)Azure Scheduleru. Pokud chcete pokračovat v práci s úlohami, které jste nastavili v plánovači, [migrujte prosím na Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) co nejdříve. 
>
> Plánovač již není v Azure Portal k dispozici, ale [rutiny prostředí PowerShell](scheduler-powershell-reference.md) [REST API](/rest/api/scheduler) a Azure Scheduler jsou v tuto chvíli dostupné, abyste mohli spravovat úlohy a kolekce úloh.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) pomáhá vytvářet [úlohy](../scheduler/scheduler-concepts-terms.md) spouštěné v cloudu tím, že umožňuje deklarativně popisovat akce. Služba pak tyto akce automaticky plánuje a spouští. Můžete například volat služby v i mimo Azure, například koncové body HTTP nebo HTTPS, a také odesílat zprávy do front služby Azure Storage a front nebo témat služby Azure Service Bus. Úlohy můžete spouštět okamžitě nebo později. Scheduler bez problémů podporuje [komplexní plánování a pokročilé opakování](../scheduler/scheduler-advanced-complexity.md). Scheduler určuje, kdy se mají úlohy spouštět, uchovává historii výsledků úloh, kterou můžete procházet, a pak předvídatelně a spolehlivě plánuje úlohy, které se mají spustit.

Službu Scheduler používají na pozadí také další plánovací nástroje Azure, například [Azure WebJobs](../app-service/webjobs-create.md), což je funkce [Web Apps](https://azure.microsoft.com/services/app-service/web/) ve službě Azure App Service. Komunikaci pro tyto akce můžete spravovat pomocí [REST API Scheduleru](/rest/api/scheduler/), která vám pomůže se správou komunikace pro tyto akce.

Tady je několik scénářů, ve kterých vám Scheduler může pomoct:

* Spouštění opakujících se akcí aplikací: Například pravidelné shromažďování dat z Twitteru do informačního kanálu.

* Provádění každodenní údržby: Například každodenní vyřazování protokolů, zálohování a další úkoly týkající se údržby.

  Jako správce například můžete chtít každý den v 1:00 po dobu dalších devíti měsíců zálohovat databázi.

I když můžete pomocí služby Scheduler vytvářet, spravovat a spouštět plánované úlohy, Scheduler úlohy nehostuje ani nespouští kód. Tato služba pouze *volá* služby nebo kód hostované jinde, například v Azure, v místním prostředí nebo u jiného poskytovatele. Scheduler může provádět volání přes HTTP, HTTPS, frontu úložiště, frontu služby Service Bus nebo téma služby Service Bus.

Pokud chcete vytvořit, naplánovat, spravovat, aktualizovat nebo odstranit úlohy a [kolekce úloh](../scheduler/scheduler-concepts-terms.md), můžete použít kód, [REST API plánovače](/rest/api/scheduler/)nebo [rutiny PowerShellu pro Azure Scheduler](scheduler-powershell-reference.md).

## <a name="next-steps"></a>Další kroky

* [Koncepty, terminologie a hierarchie entit služby Azure Scheduler](scheduler-concepts-terms.md)
* [Plány a fakturace pro Azure Scheduler](scheduler-plans-billing.md)
* [Vytváření komplexních plánů a pokročilé opakování pomocí Azure Scheduleru](scheduler-advanced-complexity.md)
* [REST API Azure Scheduleru – referenční informace](/rest/api/scheduler)
* [Rutiny PowerShellu pro Azure Scheduler – referenční informace](scheduler-powershell-reference.md)