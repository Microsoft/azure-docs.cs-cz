---
title: zahrnout soubor
description: zahrnout soubor
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: ea7396117935c13698a8c6cc6ef4029cc82b90bc
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76279401"
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
* [C# skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#pravidel](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)
