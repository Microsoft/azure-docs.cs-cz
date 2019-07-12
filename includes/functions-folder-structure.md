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
ms.openlocfilehash: aad66a91f7de8380ac7e87f0ce8e35ed43cac4a6
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594541"
---
Kód pro všechny funkce v aplikaci konkrétní funkce je umístěn v kořenové složky projektu, který obsahuje konfigurační soubor hostitele a jeden nebo více jejích podsložkách. Každá podsložka obsahuje kód do samostatné funkce. Struktura složek je vidět v následující reprezentaci:

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

Ve verzi 2.x modul runtime služby Functions, všechny funkce do aplikace function App musí sdílet stejný zásobník jazyka.  

[Host.json](../articles/azure-functions/functions-host-json.md) soubor obsahuje konfigurace modulu runtime specifické a je v kořenové složce aplikace function app. A *bin* složka obsahuje balíčky a další soubory knihovny, které aplikace function app vyžaduje. Projděte si požadavky na konkrétní jazyk pro projekt aplikace funkcí:

* [Knihovny tříd C# (.csproj)](../articles/azure-functions/functions-dotnet-class-library.md#functions-class-library-project)
* [C# skript (.csx)](../articles/azure-functions/functions-reference-csharp.md#folder-structure)
* [F#skript](../articles/azure-functions/functions-reference-fsharp.md#folder-structure)
* [Java](../articles/azure-functions/functions-reference-java.md#folder-structure)
* [JavaScript](../articles/azure-functions/functions-reference-node.md#folder-structure)



