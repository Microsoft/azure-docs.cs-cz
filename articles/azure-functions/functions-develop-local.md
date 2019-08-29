---
title: Vývoj a spouštění Azure Functions v místním prostředí | Microsoft Docs
description: Naučte se, jak v místním počítači kódovat a testovat služby Azure Functions, než je spustíte na Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 7e0b5137355c9fd89600fa48c075c81cfb1190e3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085448"
---
# <a name="code-and-test-azure-functions-locally"></a>Azure Functions kódu a testování místně

I když máte možnost vyvíjet a testovat Azure Functions v [Azure Portal], mnoho vývojářů preferuje místní vývojové prostředí. Funkce usnadňují používání oblíbeného editoru kódu a vývojářských nástrojů k vytváření a testování funkcí v místním počítači. Místní funkce se můžou připojit k živým službám Azure a můžete je ladit na svém místním počítači pomocí modulu runtime Full Functions.

## <a name="local-development-environments"></a>Místní vývojová prostředí

Způsob, jakým vyvíjíte funkce v místním počítači, závisí na vašich [jazykových](supported-languages.md) a nástrojových preferencích. Prostředí v následující tabulce podporují místní vývoj:

|Prostředí                              |Languages         |Popis|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [](functions-create-first-function-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(knihovna tříd), skript (. csx), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Rozšíření Azure Functions pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) přidává do vs Code podporu funkcí. Vyžaduje základní nástroje. Podporuje vývoj v systémech Linux, MacOS a Windows při použití verze 2. x základních nástrojů. Další informace najdete v tématu [Vytvoření první funkce pomocí Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Příkazový řádek nebo terminál](functions-run-local.md) | [](functions-reference-powershell.md) [](functions-reference-python.md) [ C# ](functions-reference-csharp.md) [(knihovna tříd), skript (. csx), JavaScript C# ](functions-dotnet-class-library.md), PowerShell, Python [](functions-reference-node.md) | [Azure Functions Core Tools] poskytuje základní modul runtime a šablony pro tvorbu funkcí, které umožňují místní vývoj. Verze 2. x podporuje vývoj v systémech Linux, MacOS a Windows. Všechna prostředí využívají základní nástroje pro modul runtime místních funkcí. |
| [Visual Studio 2019](functions-develop-vs.md) | [C#(knihovna tříd)](functions-dotnet-class-library.md) | Nástroje pro Azure Functions jsou součástí úlohy **vývoje Azure** sady [Visual Studio 2019](https://www.visualstudio.com/vs/) a novějších verzí. Umožňuje kompilaci funkcí v knihovně tříd a publikování knihovny DLL do Azure. Zahrnuje základní nástroje pro místní testování. Další informace najdete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) Některé | [Java](functions-reference-java.md) | Integruje se základními nástroji a umožňuje vývoj funkcí Java. Verze 2. x podporuje vývoj v systémech Linux, MacOS a Windows. Další informace najdete v tématu [Vytvoření první funkce pomocí jazyků Java a Maven](functions-create-first-java-maven.md). Podporuje také vývoj pomocí [zatmění](functions-create-maven-eclipse.md) a [IntelliJ nápadu](functions-create-maven-intellij.md) . |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Každé z těchto místních vývojových prostředí umožňuje vytvářet projekty aplikací Function App a používat předdefinované šablony funkcí k vytváření nových funkcí. Každá z nich používá základní nástroje, takže můžete testovat a ladit své funkce na vlastním počítači pomocí modulu runtime Real Functions, stejně jako u jakékoli jiné aplikace. Projekt aplikace Function App můžete také publikovat z libovolného z těchto prostředí do Azure.  

## <a name="next-steps"></a>Další postup

+ Další informace o místním vývoji kompilovaných C# funkcí pomocí sady visual Studio 2019 naleznete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md).
+ Další informace o místním vývoji funkcí pomocí VS Code na počítačích se systémem Mac, Linux nebo Windows najdete v [dokumentaci k VS Code Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Další informace o vývoji funkcí z příkazového řádku nebo terminálu najdete v tématu věnovaném [práci s Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
