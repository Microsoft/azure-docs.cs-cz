---
title: Zpracování pokyny Azure funkce chyb | Microsoft Docs
description: Obsahuje obecné pokyny pro zpracování chyb vzniklých v při spuštění funkce a odkazy na témata vazby konkrétních chyb.
services: functions
cloud: ''
documentationcenter: ''
author: ggailey777
manager: cfowler
ms.assetid: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/01/2018
ms.author: glenga; cfowler
ms.openlocfilehash: 82cdc62b3070811186583fdf1ce5e6ce421ebc34
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118486"
---
# <a name="azure-functions-error-handling"></a>Zpracování chyb Azure funkce

Toto téma obsahuje obecné pokyny pro zpracování chyb vzniklých při spuštění funkcí. Obsahuje taky odkazy na témata, která popisují vazby konkrétních chyb, které mohou nastat. 

## <a name="handing-errors-in-functions"></a>Blokováním chyby ve funkcích
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kódy chyb vazby

Při integraci se službami Azure, můžete mít vyvolá chyby, které pocházejí z rozhraní API základní služby. Odkazy na chybu code dokumentaci pro tyto služby najdete v **výjimky a návratové kódy** části následující aktivační události a vazba referenční témata:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
