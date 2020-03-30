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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205729"
---
Kód pro všechny funkce v aplikaci konkrétní funkce je umístěn v kořenové složce projektu, která obsahuje konfigurační soubor hostitele a jednu nebo více podsložek. Každá podsložka obsahuje kód pro samostatnou funkci. Struktura složek je zobrazena v následující reprezentaci:

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

Ve verzi 2.x a vyšší funkce runtime, všechny funkce v aplikaci funkce musí sdílet stejný jazyk zásobníku.  

Soubor [host.json](../articles/azure-functions/functions-host-json.md) obsahuje konfigurace specifické pro runtime a je v kořenové složce aplikace funkce. Složka *přihrádky* obsahuje balíčky a další soubory knihovny, které aplikace funkce vyžaduje. Podívejte se na požadavky specifické pro jazyk pro projekt aplikace funkce:

* [Knihovna tříd C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [Skript jazyka C# (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Skript F#](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [Javascript](../articles/azure-functions/functions-reference-node.md#folder-structure)
* [Python](../articles/azure-functions/functions-reference-python.md#folder-structure)
