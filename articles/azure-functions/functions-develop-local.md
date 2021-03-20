---
title: Vývoj a spouštění Azure Functions místně
description: Naučte se, jak nasměrovat a testovat Azure Functions v místním počítači, než je spustíte v Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c37d51abcc8d612b777b845515cf07666369d4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96168130"
---
# <a name="code-and-test-azure-functions-locally"></a>Místní psaní kódu a testování funkcí Azure Functions

I když máte možnost vyvíjet a testovat Azure Functions v [Azure Portal], mnoho vývojářů preferuje místní vývojové prostředí. Funkce usnadňují používání oblíbeného editoru kódu a vývojářských nástrojů k vytváření a testování funkcí v místním počítači. Místní funkce se můžou připojit k živým službám Azure a můžete je ladit na svém místním počítači pomocí modulu runtime Full Functions.

## <a name="local-development-environments"></a>Místní vývojová prostředí

Způsob, jakým vyvíjíte funkce v místním počítači, závisí na vašich [jazykových](supported-languages.md) a nástrojových preferencích. Prostředí v následující tabulce podporují místní vývoj:

|Prostředí                              |Jazyky         |Description|
|-----------------------------------------|------------|---|
|[Visual Studio Code](functions-develop-vs-code.md)| [C# (knihovna tříd)](functions-dotnet-class-library.md), [skript c# (. csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](./create-first-function-vs-code-powershell.md), [Python](functions-reference-python.md) | [Rozšíření Azure Functions pro vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) přidává do vs Code podporu funkcí. Vyžaduje základní nástroje. Podporuje vývoj v systémech Linux, MacOS a Windows při použití verze 2. x základních nástrojů. Další informace najdete v tématu [Vytvoření první funkce pomocí Visual Studio Code](./create-first-function-vs-code-csharp.md). |
| [Příkazový řádek nebo terminál](functions-run-local.md) | [C# (knihovna tříd)](functions-dotnet-class-library.md), [skript c# (. csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Azure Functions Core Tools] poskytuje základní modul runtime a šablony pro tvorbu funkcí, které umožňují místní vývoj. Verze 2. x podporuje vývoj v systémech Linux, MacOS a Windows. Všechna prostředí využívají základní nástroje pro modul runtime místních funkcí. |
| [Visual Studio 2019](functions-develop-vs.md) | [Jazyk C# (knihovna tříd)](functions-dotnet-class-library.md) | Nástroje pro Azure Functions jsou součástí úlohy **vývoje Azure** sady [Visual Studio 2019](https://www.visualstudio.com/vs/) a novějších verzí. Umožňuje kompilaci funkcí v knihovně tříd a publikování knihovny DLL do Azure. Zahrnuje základní nástroje pro místní testování. Další informace najdete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md). |
| [Maven](./create-first-function-cli-java.md) (různé) | [Java](functions-reference-java.md) | Integruje se základními nástroji a umožňuje vývoj funkcí Java. Verze 2. x podporuje vývoj v systémech Linux, MacOS a Windows. Další informace najdete v tématu [Vytvoření první funkce pomocí jazyků Java a Maven](./create-first-function-cli-java.md). Podporuje také vývoj pomocí [zatmění](functions-create-maven-eclipse.md) a [IntelliJ nápadu](functions-create-maven-intellij.md) . |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Každé z těchto místních vývojových prostředí umožňuje vytvářet projekty aplikací Function App a používat předdefinované šablony funkcí k vytváření nových funkcí. Každá z nich používá základní nástroje, takže můžete testovat a ladit své funkce na vlastním počítači pomocí modulu runtime Real Functions, stejně jako u jakékoli jiné aplikace. Projekt aplikace Function App můžete také publikovat z libovolného z těchto prostředí do Azure.

## <a name="next-steps"></a>Další kroky

+ Další informace o místním vývoji kompilovaných funkcí jazyka C# pomocí sady Visual Studio 2019 naleznete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md).
+ Další informace o místním vývoji funkcí pomocí VS Code na počítačích se systémem Mac, Linux nebo Windows najdete v tématu [nasazení Azure Functions z vs Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01).
+ Další informace o vývoji funkcí z příkazového řádku nebo terminálu najdete v tématu věnovaném [práci s Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Azure Functions Core Tools]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows