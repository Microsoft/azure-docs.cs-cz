---
title: Pokyny pro zpracování chyb Azure Functions | Microsoft Docs
description: Obsahuje obecné pokyny pro zpracování chyb, ke kterým dojde v případě, že se vaše funkce spouštějí, a odkazy na témata s chybami týkajícími se vazby.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: gwallace
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: bc0ffec1bfe55ee81573feffc58e1adb11421eec
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667449"
---
# <a name="azure-functions-error-handling"></a>Zpracování chyb Azure Functions

V tomto tématu najdete obecné pokyny pro zpracování chyb, ke kterým dojde, když se vaše funkce spustí. Obsahuje také odkazy na témata, která popisují chyby specifické pro vazbu, ke kterým může dojít. 

## <a name="handling-errors-in-functions"></a>Zpracování chyb ve funkcích
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kódy chyb vazby

Při integraci se službami Azure možná dojde k chybám, které pocházejí z rozhraní API základních služeb. Odkazy na dokumentaci ke kódu chyby pro tyto služby najdete v části **výjimky a návratové kódy** v následujících tématech Trigger a odkazu na vazbu:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
