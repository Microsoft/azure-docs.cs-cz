---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 9b3859d854b3900cc30eda6a95b8425da6763e59
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94340978"
---
Atributy vazby jsou definovány přímo v function.jsv souboru. V závislosti na typu vazby mohou být vyžadovány další vlastnosti. [Konfigurace výstupu fronty](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) popisuje pole požadovaná pro vazbu fronty Azure Storage. Rozšíření usnadňuje přidávání vazeb do function.jssouborů. 

Vazbu vytvoříte tak, že kliknete pravým tlačítkem (CTRL + kliknete na macOS) do `function.json` souboru ve složce HttpTrigger a kliknete na **Přidat vazbu...**. Podle pokynů definujte následující vlastnosti vazby pro novou vazbu:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Vyberte vazbu se směrováním...** | `Azure Queue Storage` | Vazba je vazba fronty Azure Storage. |
| **Název, který slouží k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Fronta, do které bude odeslána zpráva** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud pole *Queue* neexistuje, vytvoří ho při prvním použití. |
| **Vyberte nastavení z local.setting.jszapnuto.** | `AzureWebJobsStorage` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Toto `AzureWebJobsStorage` nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

Vazba je přidána do `bindings` pole v function.jsv, což by mělo vypadat takto:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::
