---
title: Reakce na události služby Azure Media Services | Dokumentace Microsoftu
description: Přihlášení k odběru událostí služby Media Services pomocí služby Azure Event Grid.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996117"
---
# <a name="handling-event-grid-events"></a>Zpracování událostí služby Event Grid

Události služby Media Services umožňují aplikacím reagovat na různé události (například úloha události změny stavu) pomocí moderní architektury bez serveru. Dělá to bez nutnosti složité kódu nebo nákladné a neefektivní cyklického dotazování služby. Místo toho události se nasdílejí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obslužné rutiny událostí, jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo dokonce vlastní Webhook a budete platit pouze podle co můžete použít. Informace o cenách najdete v tématu [ceny služby Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Dostupnost pro události služby Media Services je vázán na služby Event Grid [dostupnosti](../../event-grid/overview.md) a bude k dispozici v jiných oblastech, stejně jako služby Event Grid.  

## <a name="media-services-events-and-schemas"></a>Služba Media Services události a schémata

Pomocí služby Event grid [odběry událostí](../../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí pro předplatitele. Media Services události obsahují všechny informace, které je potřeba reagovat na změny ve vašich datech. Události služby Media Services můžete identifikovat, protože vlastnost Typ události začíná řetězcem "Microsoft.Media.".

Další informace najdete v tématu [schémata událostí služby Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Postupy pro spotřebovávajících událostí

Aplikace, které zpracovávají události Media Services by měl postupovat podle několik doporučených postupů:

* Směrování událostí na stejný ovladač událostí dá nakonfigurovat několik předplatných, je důležité se předpokládá, že události pocházejí z určitého zdroje, ale ke kontrole tématu zprávy, která se ujistěte, že pocházejí z účtu úložiště, které jste očekávali.
* Podobně zkontrolujte, že typ události jsou připraveny k procesu, a Nepředpokládejte, že všechny události, které se zobrazí typy, které očekáváte, že bude.
* Pole, která nevíte, ignorujte.  Tento postup vám pomůže pokračovat je odolné k novým funkcím, které mohou být přidány v budoucnu.
* Shody předpony a přípony "předmět" použijte k omezení událostí k určité události.

## <a name="next-steps"></a>Další postup

[Získat události stavu úloh](job-state-events-cli-how-to.md)
