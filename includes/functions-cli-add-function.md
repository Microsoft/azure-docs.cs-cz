---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a33cc604d45d51a63a73ea6ed58abc67269437d7
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673148"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Přidejte funkci do projektu pomocí následujícího `--name` příkazu, kde argument je jedinečný název `--template` funkce (HttpExample) a argument určuje aktivační událost funkce (HTTP). 

```
func new --name HttpExample --template "HTTP trigger"
```  
::: zone-end  
::: zone pivot="programming-language-csharp"
`func new`vytvoří soubor kódu HttpExample.cs.
::: zone-end
::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
`func new`Vytvoří podsložku odpovídající názvu funkce, která obsahuje soubor kódu odpovídající zvolenému jazyku projektu a konfigurační soubor s názvem *function.json*.
::: zone-end