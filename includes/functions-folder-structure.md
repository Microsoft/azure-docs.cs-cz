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
ms.openlocfilehash: 27dc1b1315a8e33b8ac13b34d4a86ad0343388b4
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131525"
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
* [F#skript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



