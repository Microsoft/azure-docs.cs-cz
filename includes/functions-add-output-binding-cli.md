---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ef3bd0f401ba54d56ed42df34cd2e761681dbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96904059"
---
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-an-output-binding-definition-to-the-function"></a>Přidání definice výstupní vazby do funkce

I když funkce může mít jenom jednu Trigger, může mít víc vstupních a výstupních vazeb, které vám umožní připojit se k dalším službám a prostředkům Azure bez nutnosti psát vlastní kód pro integraci. 
::: zone-end
::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
Tyto vazby deklarujete v *function.js* souboru ve složce Functions. Z předchozího rychlého startu vaše *function.js* v souboru ve složce *HttpExample* obsahují dvě vazby v `bindings` kolekci:  
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
Každá vazba má alespoň typ, směr a název. V předchozím příkladu je první vazba typu `httpTrigger` s směrem `in` . Pro `in` směr `name` Určuje název vstupního parametru, který je odeslán funkci při vyvolání triggerem.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Druhá vazba v kolekci je pojmenována `res` . Tato `http` vazba je výstupní vazba ( `out` ), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazbu typu `queue` s názvem `msg` , jak je znázorněno v následujícím kódu:

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
Druhá vazba v kolekci je typu `http` s směrem `out` . v takovém případě `name` `$return` to znamená, že tato vazba používá návratovou hodnotu funkce namísto zadání vstupního parametru.

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazbu typu `queue` s názvem `msg` , jak je znázorněno v následujícím kódu:

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
Druhá vazba v kolekci je pojmenována `res` . Tato `http` vazba je výstupní vazba ( `out` ), která se používá k zápisu odpovědi HTTP. 

Chcete-li z této funkce zapisovat do fronty Azure Storage, přidejte `out` vazbu typu `queue` s názvem `msg` , jak je znázorněno v následujícím kódu:

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
V tomto případě `msg` je funkce předána funkci jako výstupní argument. Pro `queue` typ musíte zadat také název fronty v `queueName` a zadat *název* Azure Storageho připojení (od *local.settings.jszapnuto*) v `connection` . 
::: zone-end  
