---
title: Vývoj a spouštění Azure functions místně | Dokumentace Microsoftu
description: Zjistěte, jak pro kódování a testování Azure functions na místním počítači před spuštěním ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/27/2018
ms.author: glenga
ms.openlocfilehash: e82981471306c9a9d09751da15256e85b770eb1a
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125035"
---
# <a name="code-and-test-azure-functions-locally"></a>Kódování a testování Azure Functions místně

Když budete moct vyvíjet a testovat v Azure Functions [Azure Portal], celá řada vývojářů raději místní vývojové prostředí. Funkce usnadňuje použít kód v oblíbeném editoru a vývojářské nástroje pro vytváření a testování funkce v místním počítači. Lokální funkce se můžete připojit k za provozu služeb Azure a můžete ladit v místním počítači pomocí úplné modul runtime služby Functions.

## <a name="local-development-environments"></a>Místní vývojové prostředí

Způsob, ve kterém můžete vyvíjet funkce v místním počítači, závisí na vaší [jazyk](supported-languages.md) a předvolby nástrojů. Prostředí v tabulce následujících podporují místní vývoj:

|Prostředí                              |Jazyky         |Popis|
|-----------------------------------------|------------|---|
| [Příkazový řádek nebo terminálu](functions-run-local.md) | [C# (knihovna tříd)](functions-dotnet-class-library.md), [skript jazyka C# (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md), [Java](functions-reference-java.md) | [Nástroje Azure Functions Core] poskytuje core runtime a šablony pro vytváření funkcí, která umožňuje místní vývoj. Verze 2.x podporuje vývoj pro Linux, MacOS a Windows. Všechna prostředí závisí na základní nástroje pro místní modul runtime služby Functions. |
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [Skript jazyka C# (.csx)](functions-reference-csharp.md), [jazyka JavaScript](functions-reference-node.md) | [Azure Functions rozšíření pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) přidá podporu funkcí VS Code. Vyžaduje základní nástroje. Podporuje vývoj pro Linux, MacOS a Windows, pokud používá verzi 2.x základní nástroje. Další informace najdete v tématu [nasadit do Azure pomocí služby Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (knihovna tříd)](functions-dotnet-class-library.md) | Nástroje Azure Functions jsou součástí **vývoj pro Azure** zatížení [Visual Studio 2017 verze 15.5](https://www.visualstudio.com/vs/) a novějších verzích. Umožňuje zkompilovat funkce v knihovně tříd a publikovat soubor .dll do Azure. Zahrnuje základní nástroje pro místní testování. Další informace najdete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Se integruje s základní nástroje pro vývoj funkcí v Javě. Verze 2.x podporuje vývoj pro Linux, MacOS a Windows. Další informace najdete v tématu [vytvoření první funkce pomocí Javy a Mavenu](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Každá z těchto místní vývojové prostředí umožňuje vytvářet funkce projekty aplikací a použít předdefinované šablony funkce vytvořit nové funkce. Každý používá základní nástroje, takže můžete testovat a ladit vaše funkce proti skutečné modul runtime služby Functions na svém počítači stejně jako jakoukoli jinou aplikaci. Můžete také publikovat projekt aplikace funkcí z některého z těchto prostředí do Azure.  

## <a name="next-steps"></a>Další postup

+ Další informace o místním vývoji kompilované funkcí jazyka C# pomocí sady Visual Studio 2017 najdete v tématu [vývoj Azure Functions pomocí sady Visual Studio](functions-develop-vs.md).
+ Další informace o místním vývoji funkcí pomocí VS Code na počítači Mac, Linux nebo Windows, najdete v článku [dokumentaci k nástroji VS Code pro službu Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Další informace o vývoji funkcí z terminálu nebo příkazového řádku najdete v tématu [pracovat s Azure Functions Core Tools](functions-run-local.md).

<!-- LINKS -->

[Nástroje Azure Functions Core]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
