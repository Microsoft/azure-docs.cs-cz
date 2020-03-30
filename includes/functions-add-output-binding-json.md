---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191008"
---
Atributy vazby jsou definovány přímo v souboru function.json. V závislosti na typu vazby mohou být vyžadovány další vlastnosti. [Konfigurace výstupu fronty](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) popisuje pole požadovaná pro vazbu fronty Azure Storage. Rozšíření usnadňuje přidání vazeb do souboru function.json. 

Chcete-li vytvořit vazbu, klepněte pravým tlačítkem `function.json` myši (Ctrl+klikněte na macOS) na soubor ve složce HttpTrigger a zvolte **Přidat vazbu...**. Postupujte podle pokynů k definování následujících vlastností vazby pro novou vazbu:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Vyberte vazbu se směrem...** | `Azure Queue Storage` | Vazba je vazby fronty Azure Storage. |
| **Název použitý k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby odkazovaný ve vašem kódu. |
| **Fronta, do které bude zpráva odeslána** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud *queueName* neexistuje, vazba vytvoří při prvním použití. |
| **Vybrat nastavení z "local.setting.json"** | `AzureWebJobsStorage` | Název nastavení aplikace, která obsahuje připojovací řetězec pro účet úložiště. Toto `AzureWebJobsStorage` nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace funkce. |

Vazba je přidána `bindings` do pole ve vašem function.json, který by měl vypadat takto:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::