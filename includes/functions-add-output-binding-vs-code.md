---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99493937"
---
Visual Studio Code umožňuje přidat do souboru function.jsvazby pomocí praktické sady výzev. 

Chcete-li přidat vazbu, otevřete příkazová paleta (F1) a zadejte **Azure Functions: Přidat vazbu...**, zvolte funkci pro novou vazbu a pak postupujte podle pokynů, které se liší v závislosti na typu vazby přidávané do funkce. 

Níže jsou uvedené příklady výzev k definování nové výstupní vazby úložiště:

| Výzva | Hodnota | Popis |
| -------- | ----- | ----------- |
| **Vybrat směr vazby** | `out` | Vazba je výstupní vazba. |
| **Výběr vazby se směrováním** | `Azure Queue Storage` | Vazba je vazba fronty Azure Storage. |
| **Název, který slouží k identifikaci této vazby v kódu** | `msg` | Název, který identifikuje parametr vazby, na který se odkazuje v kódu. |
| **Fronta, do které bude odeslána zpráva** | `outqueue` | Název fronty, do které vazba zapisuje. Pokud pole *Queue* neexistuje, vytvoří ho při prvním použití. |
| **Vyberte nastavení z local.settings.jszapnuto.** | `MyStorageConnection` | Název nastavení aplikace, které obsahuje připojovací řetězec pro účet úložiště. Toto `AzureWebJobsStorage` nastavení obsahuje připojovací řetězec pro účet úložiště, který jste vytvořili pomocí aplikace Function App. |

Můžete také kliknout pravým tlačítkem (Ctrl + kliknout na macOS) přímo na **function.jsv** souboru ve složce Functions, vybrat **Přidat vazbu** a postupovat podle stejných výzev.

V tomto příkladu je do `bindings` pole v function.jsdo souboru přidána následující vazba:

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```