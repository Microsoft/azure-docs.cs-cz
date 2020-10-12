---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 5f5f682c8c31c17f1a645bcdacdc78f32bd5c001
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205729"
---
Kód pro všechny funkce v konkrétní aplikaci Function App je umístěn v kořenové složce projektu, která obsahuje konfigurační soubor hostitele a jednu nebo více podsložek. Každá podsložka obsahuje kód pro samostatnou funkci. Struktura složek se zobrazuje v následujícím vyjádření:

```
FunctionApp
 | - host.json
 | - MyFirstFunction
 | | - function.json
 | | - ...  
 | - MySecondFunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Ve verzi 2. x a vyšší modulu runtime funkcí musí všechny funkce v aplikaci Function App sdílet stejnou jazykovou sadu.  

[host.jsv](../articles/azure-functions/functions-host-json.md) souboru obsahuje konfigurace specifické pro modul runtime a je v kořenové složce aplikace Function App. Složka *bin* obsahuje balíčky a jiné soubory knihovny, které aplikace Function App vyžaduje. Prohlédněte si požadavky na konkrétní jazyk pro projekt Function App:

* [Knihovna tříd C# (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Skript jazyka C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Skript F #](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
