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
ms.date: 09/19/2018
ms.author: juliako
ms.openlocfilehash: 143fec2ddb168b0fff0e419fa5767e9718637241
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465533"
---
# <a name="reacting-to-media-services-events"></a>Reakce na události služby Media Services

Události služby Media Services umožňují aplikacím reagovat na různé události (například úloha události změny stavu) pomocí moderní architektury bez serveru. Dělá to bez nutnosti složité kódu nebo nákladné a neefektivní cyklického dotazování služby. Místo toho události se nasdílejí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) do obslužné rutiny událostí, jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo dokonce vlastní Webhook a budete platit pouze podle co můžete použít. Informace o cenách najdete v tématu [ceny služby Event Grid](https://azure.microsoft.com/pricing/details/event-grid/).

Dostupnost pro události služby Media Services je vázán na služby Event Grid [dostupnosti](../../event-grid/overview.md) a bude k dispozici v jiných oblastech, stejně jako služby Event Grid.  

## <a name="available-media-services-events"></a>K dispozici událostí Media Services

Pomocí služby Event grid [odběry událostí](../../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí pro předplatitele.  Odběry událostí Media Services v současné době může obsahovat následující události:  

|Název události|Popis|
|----------|-----------|
| Microsoft.Media.JobStateChange| Vyvoláno, když stav úlohy změny. |
| Microsoft.Media.LiveEventConnectionRejected | Pokus o připojení kodéru byl odmítnut. |
| Microsoft.Media.LiveEventEncoderConnected | Kodér naváže připojení se živá událost. |
| Microsoft.Media.LiveEventEncoderDisconnected | Kodér odpojí. |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | Server mediálních datových zahodí datové dávky, protože je příliš pozdě nebo obsahuje překrývající se časové razítko (timestamp nové datové dávky je menší než koncový čas předchozího bloku dat). |
| Microsoft.Media.LiveEventIncomingStreamReceived | Média server přijímá prvního bloku dat pro každou sledovat ve službě stream ani připojení. |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | Server mediálních datových zjistí zvuk a video streamy nejsou synchronizované. Použít jako varování, protože nemusí mít dopad na činnost koncového uživatele. |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | Server mediálních datových zjistí, že některý dvě videa datové proudy z externí kodér nejsou synchronizované. Použít jako varování, protože nemusí mít dopad na činnost koncového uživatele. |
| Microsoft.Media.LiveEventIngestHeartbeat | Publikuje každých 20 sekund pro každé stopu, když běží živá událost. Poskytuje ingestování shrnutí stavu. |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | Server mediálních datových detekuje diskontinuitu v příchozí směr. |

## <a name="event-schema"></a>Schéma událostí

Media Services události obsahují všechny informace, které je potřeba reagovat na změny ve vašich datech.  Události služby Media Services můžete identifikovat, protože vlastnost Typ události začíná řetězcem "Microsoft.Media.".

Další informace najdete v tématu [schémata událostí služby Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Postupy pro spotřebovávajících událostí

Aplikace, které zpracovávají události Media Services by měl postupovat podle několik doporučených postupů:

* Směrování událostí na stejný ovladač událostí dá nakonfigurovat několik předplatných, je důležité se předpokládá, že události pocházejí z určitého zdroje, ale ke kontrole tématu zprávy, která se ujistěte, že pocházejí z účtu úložiště, které jste očekávali.
* Podobně zkontrolujte, že typ události jsou připraveny k procesu, a Nepředpokládejte, že všechny události, které se zobrazí typy, které očekáváte, že bude.
* Pole, která nevíte, ignorujte.  Tento postup vám pomůže pokračovat je odolné k novým funkcím, které mohou být přidány v budoucnu.
* Shody předpony a přípony "předmět" použijte k omezení událostí k určité události.

## <a name="next-steps"></a>Další postup

[Získat události stavu úloh](job-state-events-cli-how-to.md)
