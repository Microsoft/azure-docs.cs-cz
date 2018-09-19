---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 09/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 2808264b4641bda49a53677ebe216a3b53b7d0d9
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46293682"
---
Kód pro všechny funkce v aplikaci konkrétní funkce je umístěn v kořenové složky projektu, který obsahuje konfigurační soubor hostitele a jeden nebo více jejích podsložkách. Každá podsložka obsahuje kód pro samostatnou funkci, jako v následující reprezentaci:

```
FunctionApp
 | - host.json
 | - Myfirstfunction
 | | - function.json
 | | - ...  
 | - mysecondfunction
 | | - function.json
 | | - ...  
 | - SharedCode
 | - bin
```

Ve verzi 2.x modul runtime služby Functions, všechny funkce do aplikace function App musí sdílet stejný jazyk pracovního procesu.  

[Host.json](../articles/azure-functions/functions-host-json.md) soubor, který obsahuje některé konfigurace specifické pro modul runtime, je v kořenové složce aplikace function app. A `bin` složka obsahuje balíčky a další soubory knihovny vyžadují aplikace function app. Projděte si požadavky na konkrétní jazyk pro projekt aplikace funkcí:

* [Knihovny tříd C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [Skript F #](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



