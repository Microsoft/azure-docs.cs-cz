---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: b118da6b751bc7a1e29ceef10c91dc5e9e3659c2
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77474208"
---
Atributy vazby se definují přímo v souboru Function. JSON. V závislosti na typu vazby mohou být vyžadovány další vlastnosti. [Konfigurace výstupu fronty](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) popisuje pole požadovaná pro vazbu fronty Azure Storage. Rozšíření usnadňuje přidávání vazeb do souboru Function. JSON. 

Vazbu vytvoříte tak, že kliknete pravým tlačítkem (CTRL + kliknete na macOS) do souboru `function.json` ve složce HttpTrigger a kliknete na **Přidat vazbu...** . Podle pokynů definujte následující vlastnosti vazby pro novou vazbu:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Vyberte vazbu se směrováním...** | `Azure Queue Storage` | Vazba je vazba fronty Azure Storage. |
| **Název, který slouží k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Fronta, do které bude odeslána zpráva** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud pole *Queue* neexistuje, vytvoří ho při prvním použití. |
| **Vyberte nastavení z místní. nastavení. JSON.** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Nastavení `AzureWebJobsStorage` obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

Do pole `bindings` v souboru Function. JSON se přidá vazba, která by teď měla vypadat jako v následujícím příkladu:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```