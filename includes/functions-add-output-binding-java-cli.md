---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673259"
---
V projektu Java jsou vazby definovány jako anotace vazby v metodě Function. *function.jsv* souboru se pak automaticky generuje na základě těchto poznámek.

Přejděte do umístění kódu funkce v části _Src/Main/Java_, otevřete soubor projektu *Function. Java* a do definice metody přidejte následující parametr `run` :

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

`msg`Parametr je [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) typ, který představuje kolekci řetězců, které jsou zapsány jako zprávy do výstupní vazby po dokončení funkce. V tomto případě je výstupem fronta úložiště s názvem `outqueue` . Připojovací řetězec pro účet úložiště je nastaven `connection` metodou. Místo samotného připojovacího řetězce předáte nastavení aplikace, které obsahuje připojovací řetězec účtu úložiště.

`run`Definice metody by teď měla vypadat jako v následujícím příkladu:  

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