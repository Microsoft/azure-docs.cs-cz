---
title: Chyba služby Azure Functions zpracování pokyny | Dokumentace Microsoftu
description: Obsahuje obecné pokyny pro zpracování chyb, ke kterým dochází ve spuštění vašich funkcí a odkazy na témata chyby specifické pro vazbu.
services: functions
cloud: ''
documentationcenter: ''
author: craigshoemaker
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: cshoe
ms.openlocfilehash: bf54d312de5625a7fa44cea4d5107e83cf15583c
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50248247"
---
# <a name="azure-functions-error-handling"></a>Zpracování chyb Azure Functions

Toto téma obsahuje obecné pokyny pro zpracování chyb, ke kterým dochází, když vaše funkce se mohou spouštět. Obsahuje také odkazy na témata popisující vazbu konkrétních chyb, které mohou nastat. 

## <a name="handing-errors-in-functions"></a>Zpracování chyb ve funkcích
[!INCLUDE [bindings errors intro](../../includes/functions-bindings-errors-intro.md)]

 
## <a name="binding-error-codes"></a>Kódy chyb vazeb

Při integraci se službami Azure, můžete mít chyb, které vyvolala, které pocházejí z rozhraní API základní služby. Odkazy na chybu kódu dokumentaci pro tyto služby najdete v **výjimky a návratové kódy** najdete následující aktivační události a vazby referenční témata:

+ [Azure Cosmos DB](functions-bindings-cosmosdb.md#exceptions-and-return-codes)

+ [Blob Storage](functions-bindings-storage-blob.md#exceptions-and-return-codes)

+ [Event Hubs](functions-bindings-event-hubs.md#exceptions-and-return-codes)

+ [Notification Hubs](functions-bindings-notification-hubs.md#exceptions-and-return-codes)

+ [Queue Storage](functions-bindings-storage-queue.md#exceptions-and-return-codes)

+ [Service Bus](functions-bindings-service-bus.md#exceptions-and-return-codes)

+ [Table Storage](functions-bindings-storage-table.md#exceptions-and-return-codes)
