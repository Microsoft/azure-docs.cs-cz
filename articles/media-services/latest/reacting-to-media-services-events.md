---
title: Reaktivní události Azure Media Services | Microsoft Docs
description: Přihlásit k odběru událostí Media Services pomocí Azure událostí mřížky.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 969957d53824bd70440e5529b83bc830bb5d9cc4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788148"
---
# <a name="reacting-to-media-services-events"></a>Reaktivní události Media Services

Služba Media Services události umožňují aplikacím reagovat na různé událostí (například úlohy události změny stavu) pomocí architektury moderní bez serveru. Dělá to tak, bez nutnosti složitý kód nebo neefektivní a drahé dotazování služby. Místo toho jsou události instaluje [mřížky událostí Azure](https://azure.microsoft.com/services/event-grid/) do obslužné rutiny událostí jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo i vlastního Webhooku a jste jediným platím co můžete použít. Informace o cenách najdete v tématu [událostí mřížky ceny](https://azure.microsoft.com/pricing/details/event-grid/).

Dostupnost pro události služby Media Services je vázaný na události mřížky [dostupnosti](../../event-grid/overview.md) a bude dostupná v jiných oblastech, stejně jako událost mřížky.  

## <a name="available-media-services-events"></a>K dispozici události Media Services

Událost mřížky používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí odběratelům.  V současné době odběry událostí Media Services může zahrnovat následující typ události:  

|Název události|Popis|
|----------|-----------|
| Microsoft.Media.JobStateChange| Vyvolá, když stav úlohy změny. |

## <a name="event-schema"></a>Schématu události

Služba Media Services události obsahují všechny informace, které je potřeba reagovat na změny v datech.  Událost Media Services můžete určit, protože vlastnost eventType začíná "Microsoft.Media.".

Další informace najdete v tématu [Media Services událostí schémata](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Postupy pro použití události

Aplikace, které zpracování událostí Media Services postupujte několik doporučených postupů:

* Události trasy na stejné obslužné rutiny události je možné nakonfigurovat více předplatných, je důležité se předpokládá, že události jsou z určitého zdroje, ale ke kontrole tématu zprávu, která se ujistěte, že pocházejí z účtu úložiště, které očekáváte.
* Podobně zkontrolujte, jestli typ události je jedním jsou připravené informace o procesu a Nepředpokládejte, že všechny události, které vám bude typy, které očekáváte.
* Pole, která nerozumíte ignorujte.  Tento postup vám pomůže, abychom vás odolné nové funkce, které se může v budoucnu přidat.
* Použijte předponu "předmět" a příponu odpovídá k omezení událostí k určité události.

## <a name="next-steps"></a>Další postup

[Získat události stavu úlohy](job-state-events-cli-how-to.md)
