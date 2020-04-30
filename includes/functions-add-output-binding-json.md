---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "78191008"
---
Atributy vazby se definují přímo v souboru Function. JSON. V závislosti na typu vazby mohou být vyžadovány další vlastnosti. [Konfigurace výstupu fronty](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) popisuje pole požadovaná pro vazbu fronty Azure Storage. Rozšíření usnadňuje přidávání vazeb do souboru Function. JSON. 

Vazbu vytvoříte tak, že `function.json` kliknete pravým tlačítkem (CTRL + kliknete na MacOS) do souboru ve složce HttpTrigger a kliknete na **Přidat vazbu...**. Podle pokynů definujte následující vlastnosti vazby pro novou vazbu:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Vyberte vazbu se směrováním...** | `Azure Queue Storage` | Vazba je vazba fronty Azure Storage. |
| **Název, který slouží k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Fronta, do které bude odeslána zpráva** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud pole *Queue* neexistuje, vytvoří ho při prvním použití. |
| **Vyberte nastavení z místní. nastavení. JSON.** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Toto `AzureWebJobsStorage` nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

Vazba je přidána do `bindings` pole ve Function. JSON, který by měl vypadat takto:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::