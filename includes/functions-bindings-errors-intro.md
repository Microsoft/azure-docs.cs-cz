---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 9fb462b83eccde3d2b486d491517973fe6bac4f1
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50132971"
---
Služba Azure Functions [aktivačními událostmi a vazbami](..\articles\azure-functions\functions-triggers-bindings.md) komunikovat s různými službami Azure. Při integraci s těmito službami, pravděpodobně vyvolá chyby, které pocházejí z rozhraní API podpůrné služby Azure. Může také dojít k chybám při pokusu o komunikaci s dalšími službami v kódu funkce pomocí REST nebo klientských knihoven. Pokud chcete nedošlo ke ztrátě dat a zajistit správné chování funkcí, je důležité pro zpracování chyb, buď z zdroje.

Následující aktivační události mají podporu předdefinován opakovaný pokus:

* [Azure Blob Storage](../articles/azure-functions/functions-bindings-storage-blob.md)
* [Azure Queue storage](../articles/azure-functions/functions-bindings-storage-queue.md)
* [Azure Service Bus (fronty nebo tématu)](../articles/azure-functions/functions-bindings-service-bus.md)

Ve výchozím nastavení jsou tyto triggery opakována až pět x. Po páté opakování tyto triggery zapsat zprávu do speciální [nezpracovatelných fronty](..\articles\azure-functions\functions-bindings-storage-queue.md#trigger---poison-messages). 

Pro jiné funkce triggery neexistuje žádný předdefinován opakovaný pokus při výskytu chyby během provádění funkce. Aby se zabránilo ztrátě informací o aktivační události musí dojít k chybě ve funkci, doporučujeme použít try-catch – bloky v kódu funkce zachytit všechny chyby. Pokud dojde k chybě, zapisovat informace předaného do funkce má aktivační procedura do fronty zvláštní zpráva "nezpracovatelných". Tento přístup je stejný jako ten používané [aktivační událost objektů Blob storage](..\articles\azure-functions\functions-bindings-storage-blob.md#trigger---poison-blobs). 

Tímto způsobem můžete zachytit aktivační události, které může být ztraceno v důsledku chyby a opakujte později použití jiné funkce pro zpracování zpráv z fronty nezpracovatelných pomocí uložené informace.  
