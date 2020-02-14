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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
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

Soubor [Host. JSON](../articles/azure-functions/functions-host-json.md) obsahuje konfigurace specifické pro modul runtime a je v kořenové složce aplikace Function App. Složka *bin* obsahuje balíčky a jiné soubory knihovny, které aplikace Function App vyžaduje. Prohlédněte si požadavky na konkrétní jazyk pro projekt Function App:

* [C#Knihovna tříd (. csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C#skript (. csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#pravidel](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
