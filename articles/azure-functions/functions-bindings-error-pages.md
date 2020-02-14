---
title: Průvodce zpracováním chyb Azure Functions
description: Naučte se zpracovávat chyby v Azure Functions s odkazy na konkrétní chyby vazby.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: 920b887382ca55d84c66fabb8f1464fd05041094
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198445"
---
# <a name="azure-functions-error-handling"></a>Zpracování chyb Azure Functions

Zpracování chyb v Azure Functions je důležité k tomu, abyste se vyhnuli ztrátě dat, nezmeškaným událostem a mohli monitorovat stav vaší aplikace.

Tento článek popisuje obecné strategie pro zpracování chyb spolu s odkazy na chyby specifické pro vazbu.

## <a name="handling-errors"></a>Zpracování chyb

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kódy chyb vazby

Při integraci se službami Azure mohou chyby vzcházet z rozhraní API základních služeb. Informace o chybách specifických pro vazbu jsou k dispozici v části **výjimky a návratové kódy** v následujících článcích:

+ [Databáze Azure Cosmos](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob-output.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
