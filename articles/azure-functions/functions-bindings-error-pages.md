---
title: Průvodce zpracováním chyb Azure Functions
description: Naučte se zpracovávat chyby v Azure Functions s odkazy na konkrétní chyby vazby.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: cshoe
ms.openlocfilehash: d301940998175adb6469e46cff2de918b5cae9df
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155291"
---
# <a name="azure-functions-error-handling"></a>Zpracování chyb Azure Functions

Zpracování chyb v Azure Functions je důležité k tomu, abyste se vyhnuli ztrátě dat, nezmeškaným událostem a mohli monitorovat stav vaší aplikace.

Tento článek popisuje obecné strategie pro zpracování chyb spolu s odkazy na chyby specifické pro vazbu.

## <a name="handling-errors"></a>Zpracování chyb

[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

## <a name="binding-error-codes"></a>Kódy chyb vazby

Při integraci se službami Azure mohou chyby vzcházet z rozhraní API základních služeb. Informace o chybách specifických pro vazbu jsou k dispozici v části **výjimky a návratové kódy** v následujících článcích:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
