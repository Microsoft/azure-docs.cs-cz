---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673259"
---
V projektu Java jsou vazby definovány jako vazby poznámky na metodu funkce. Soubor *function.json* je pak automaticky generován na základě těchto anotací.

Přejděte do umístění kódu funkce pod _src/main/java_, otevřete soubor projektu *Function.java* a přidejte do definice `run` metody následující parametr:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Parametr `msg` je [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ, který představuje kolekci řetězců, které jsou zapsány jako zprávy do výstupní vazby po dokončení funkce. V tomto případě je výstupem `outqueue`fronta úložiště s názvem . Připojovací řetězec pro účet `connection` úložiště je nastaven metodou. Spíše než samotný připojovací řetězec, předáte nastavení aplikace, která obsahuje připojovací řetězec účtu úložiště.

Definice `run` metody by nyní měla vypadat jako následující příklad:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```