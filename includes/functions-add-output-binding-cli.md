---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 121f6ffa5c1a7c903e59be8a5bc3e1e1db0834fc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673338"
---
## <a name="add-an-output-binding-definition-to-the-function"></a>Přidání definice výstupní vazby do funkce

I když funkce může mít pouze jednu aktivační událost, může mít více vstupních a výstupních vazeb, které umožňují připojení k jiným službám a prostředkům Azure bez psaní vlastního integračního kódu. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Deklarujete tyto vazby v souboru *function.json* ve složce funkce. Z předchozího rychlého startu soubor *function.json* ve složce *HttpExample* obsahuje dvě vazby v kolekci: `bindings`  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
Každá vazba má alespoň typ, směr a název. Ve výše uvedeném příkladu je `httpTrigger` první `in`vazba typu se směrem . Pro `in` směr `name` určuje název vstupního parametru, který je odeslán do funkce při vyvolání aktivační událostí.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Druhá vazba v kolekci je pojmenována `res`. Tato `http` vazba je`out`výstupní vazba ( ), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapsat `out` do fronty `queue` služby `msg`Azure Storage, přidejte vazbu typu s názvem , jak je znázorněno v níže uvedeném kódu:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Druhá vazba v kolekci `http` je `out`typu se směrem `name` , `$return` v takovém případě special of označuje, že tato vazba používá vrácenou hodnotu funkce spíše než poskytnutí vstupního parametru.

Chcete-li z této funkce zapsat `out` do fronty `queue` služby `msg`Azure Storage, přidejte vazbu typu s názvem , jak je znázorněno v níže uvedeném kódu:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Druhá vazba v kolekci je pojmenována `res`. Tato `http` vazba je`out`výstupní vazba ( ), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapsat `out` do fronty `queue` služby `msg`Azure Storage, přidejte vazbu typu s názvem , jak je znázorněno v níže uvedeném kódu:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
V tomto `msg` případě je dána funkce jako výstupní argument. Pro `queue` typ musíte také zadat název fronty `queueName` a zadat *název* připojení Azure Storage (z *local.settings.json)* v . `connection` 
::: zone-end  
