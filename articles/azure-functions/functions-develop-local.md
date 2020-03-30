---
title: Vývoj a spouštění funkcí Azure místně
description: Zjistěte, jak kódovat a testovat funkce Azure v místním počítači, než je spustíte ve službě Azure Functions.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 835edcb94b294d93cab41ea51b88ac38db71d95e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74230633"
---
# <a name="code-and-test-azure-functions-locally"></a>Místní psaní kódu a testování funkcí Azure Functions

I když jste schopni vyvíjet a testovat funkce Azure na [webu Azure Portal], mnoho vývojářů preferuje prostředí pro místní vývoj. Funkce usnadňují použití oblíbeného editoru kódu a vývojových nástrojů k vytváření a testování funkcí v místním počítači. Místní funkce se můžou připojit k živým službám Azure a můžete je ladit v místním počítači pomocí úplného běhu Funkce.

## <a name="local-development-environments"></a>Prostředí místního rozvoje

Způsob vývoje funkcí v místním počítači závisí na [jazyku](supported-languages.md) a předvolbách nástrojů. Prostředí v následující tabulce podporují místní rozvoj:

|Prostředí                              |Jazyky         |Popis|
|-----------------------------------------|------------|---|
|[Kód visual studia](functions-develop-vs-code.md)| [C# (knihovna tříd)](functions-dotnet-class-library.md), [Skript Jazyka C # (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-create-first-function-powershell.md), [Python](functions-reference-python.md) | [Rozšíření Funkce Azure pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) přidává podporu funkcí v VS Code. Vyžaduje základní nástroje. Podporuje vývoj v systémech Linux, MacOS a Windows při použití verze 2.x základních nástrojů. Další informace najdete v [tématu Vytvoření první funkce pomocí kódu sady Visual Studio](functions-create-first-function-vs-code.md). |
| [Příkazový řádek nebo terminál](functions-run-local.md) | [C# (knihovna tříd)](functions-dotnet-class-library.md), [Skript Jazyka C # (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [PowerShell](functions-reference-powershell.md), [Python](functions-reference-python.md) | [Nástroje Azure Functions Core Tools] poskytují základní runtime a šablony pro vytváření funkcí, které umožňují místní vývoj. Verze 2.x podporuje vývoj v systémech Linux, MacOS a Windows. Všechna prostředí spoléhají na základní nástroje pro místní funkce runtime. |
| [Visual Studio 2019](functions-develop-vs.md) | [Jazyk C# (knihovna tříd)](functions-dotnet-class-library.md) | Nástroje Azure Functions jsou součástí vývojové úlohy **Azure** [visual studia 2019](https://www.visualstudio.com/vs/) a novějších verzí. Umožňuje kompilovat funkce v knihovně tříd a publikovat knihovnu DLL do Azure. Obsahuje základní nástroje pro místní testování. Další informace najdete [v tématu Vývoj funkcí Azure pomocí Sady Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (různé) | [Java](functions-reference-java.md) | Integruje se s Core Tools, které umožňují vývoj funkcí Java. Verze 2.x podporuje vývoj v systémech Linux, MacOS a Windows. Další informace najdete v [tématu Vytvoření první funkce pomocí Javy a Mavenu](functions-create-first-java-maven.md). Také podporuje vývoj pomocí [Eclipse](functions-create-maven-eclipse.md) a [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Každé z těchto prostředí místního vývoje umožňuje vytvářet projekty aplikací funkcí a používat předdefinované šablony funkcí k vytváření nových funkcí. Každý používá základní nástroje, takže můžete testovat a ladit své funkce proti skutečné funkce runtime na vlastním počítači stejně jako u jakékoli jiné aplikace. Projekt aplikace funkce můžete také publikovat z libovolného z těchto prostředí do Azure.  

## <a name="next-steps"></a>Další kroky

+ Další informace o místním vývoji zkompilovaných funkcí C# pomocí Visual Studia 2019 najdete [v tématu Vývoj funkcí Azure pomocí Sady Visual Studio](functions-develop-vs.md).
+ Další informace o místním vývoji funkcí pomocí Kódu VS na počítači Mac, Linux nebo Windows najdete v [tématu Nasazení funkcí Azure z kódu VS](/azure/javascript/tutorial-vscode-serverless-node-01).
+ Další informace o vývoji funkcí na příkazovém řádku nebo terminálu najdete v [tématu Práce s nástroji Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Základní nástroje Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Portál Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
