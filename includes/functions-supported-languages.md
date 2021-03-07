---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d09d69db33cadee1b5a214e9cb2f09a508f37242
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431838"
---
|Jazyk                                 |verze         |2.x| 3.x |
|-----------------------------------------|------------|---| --- |
|[C#](../articles/azure-functions/functions-reference-csharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1)<br/>[Verze Preview (.NET 5,0)](../articles/azure-functions/dotnet-isolated-process-guide.md) |
|[JavaScript](../articles/azure-functions/functions-reference-node.md#node-version)|GA (uzel 6)|GA (uzel 10 & 8)| GA (uzel 12 & 10)<br />Náhled (uzel 14) |
|[F#](../articles/azure-functions/functions-reference-fsharp.md)|GA (.NET Framework 4,7)|GA (.NET Core 2,2<sup>1</sup>)| GA (.NET Core 3,1) |
|[Java](../articles/azure-functions/functions-reference-java.md)|–|GA (Java 8)| GA (Java 11 & 8)|
|[PowerShell](../articles/azure-functions/functions-reference-powershell.md) |–|GA (PowerShell Core 6)| GA (PowerShell 7 & Core 6)|
|[Python](../articles/azure-functions/functions-reference-python.md#python-version)|–|GA (Python 3,7 & 3,6)| GA (Python 3,8, 3,7, & 3,6) <br/> Verze Preview (Python 3,9)|
|[TypeScript](../articles/azure-functions/functions-reference-node.md#typescript) |–|GA<sup>2</sup>| GA<sup>2</sup> |


<sup>1</sup> aplikace knihovny tříd .NET, které cílí na modul runtime verze 2. x, se teď dají spouštět na .net Core 3,1 v režimu kompatibility .NET Core 2. x. Další informace najdete v tématu [informace o funkcích v2. x](../articles/azure-functions/functions-dotnet-class-library.md#functions-v2x-considerations).  
<sup>2</sup> podporováno prostřednictvím Transpiling do JavaScriptu.

Další podrobnosti o podporovaných jazykových verzích najdete v článku Příručka pro vývojáře konkrétního jazyka.   
Informace o plánovaných změnách jazykové podpory najdete v tématu [plán Azure](https://azure.microsoft.com/roadmap/?tag=functions).
