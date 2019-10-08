---
title: Reakce na události Azure Media Services | Microsoft Docs
description: K přihlášení k odběru událostí Media Services použijte Azure Event Grid.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: 07ad39bdcec6e4b00d3b47553930fc05694ca2af
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026618"
---
# <a name="handling-event-grid-events"></a>Zpracování událostí služby Event Grid

Media Services události umožňují aplikacím reagovat na různé události (například událost změny stavu úlohy) pomocí moderních architektur bez serveru. Je to bez nutnosti složitosti složitého kódu nebo nákladných a neefektivních služeb cyklického dotazování. Místo toho se události přijdou prostřednictvím [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obslužných rutin událostí, jako je [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce z vlastního Webhooku, a platíte jenom za to, co využijete. Informace o cenách najdete v tématu [Event Grid ceny](https://azure.microsoft.com/pricing/details/event-grid/).

Dostupnost pro události Media Services je vázána na Event Grid [dostupnost](../../event-grid/overview.md) a v jiných oblastech bude k dispozici jako Event Grid.  

## <a name="media-services-events-and-schemas"></a>Media Services události a schémata

Event Grid používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Media Services události obsahují všechny informace, které potřebujete k reakci na změny ve vašich datech. Můžete identifikovat událost Media Services, protože vlastnost eventType začíná na "Microsoft. Media".

Další informace najdete v tématu [Media Services schématech událostí](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají Media Services události, by měly dodržovat několik doporučených postupů:

* Vzhledem k tomu, že je možné nakonfigurovat více předplatných pro směrování událostí ke stejné obslužné rutině události, je důležité, abyste nepředpokládali události z konkrétního zdroje, ale pokud chcete zkontrolovat téma zprávy, abyste měli jistotu, že pochází z účtu úložiště, který očekáváte.
* Podobně ověřte, zda je typ eventType, který je připraven ke zpracování, a nepředpokládá se, že všechny události, které obdržíte, budou takové typy, které očekáváte.
* Ignorujte pole, která nerozumíte.  Tento postup vám pomůže zajistit odolný přístup k novým funkcím, které se v budoucnu můžou přidat.
* Pokud chcete omezit události na konkrétní událost, použijte předponu a příponu "Subject".

> [!NOTE]
> Na události se vztahují Event Grid [smlouva SLA (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/). Pokud chcete dostávat oznámení o událostech pomocí rozhraní API, přečtěte si příklady o tom, jak zpracovávat události, pomocí [sady .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) nebo [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Další kroky

* [Monitorování událostí – portál](monitor-events-portal-how-to.md)
* [Monitorování událostí – rozhraní příkazového řádku](job-state-events-cli-how-to.md)
