---
title: Průvodce zpracováním chyb Azure Functions
description: Naučte se zpracovávat chyby v Azure Functions s odkazy na konkrétní chyby vazby.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: befdb4a8cceaef18961c1e9297e23ed5d405ff50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77586185"
---
# <a name="azure-functions-error-handling"></a>Zpracování chyb Azure Functions

Zpracování chyb v Azure Functions je důležité k tomu, abyste se vyhnuli ztrátě dat, nezmeškaným událostem a mohli monitorovat stav vaší aplikace.

Tento článek popisuje obecné strategie pro zpracování chyb spolu s odkazy na chyby specifické pro vazbu.

## <a name="handling-errors"></a>Zpracování chyb

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kódy chyb vazby

Při integraci se službami Azure mohou chyby vzcházet z rozhraní API základních služeb. Informace o chybách specifických pro vazbu jsou k dispozici v části **výjimky a návratové kódy** v následujících článcích:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Úložiště objektů BLOB](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs-output.md#exceptions-and-return-codes)

+ [Centra IoT](functions-bindings-event-iot-output.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue-output.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus-output.md#exceptions-and-return-codes)

+ [Úložiště tabulek](functions-bindings-storage-table.md#exceptions-and-return-codes)
