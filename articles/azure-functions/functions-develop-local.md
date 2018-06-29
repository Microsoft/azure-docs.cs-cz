---
title: Vývoj a spustit místně na Azure functions | Microsoft Docs
description: Zjistěte, jak kód a testovat Azure functions v místním počítači před spuštěním na Azure Functions.
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
ms.openlocfilehash: 89f94be4cf624914183480362ae23c62c363622f
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37088606"
---
# <a name="code-and-test-azure-functions-locally"></a>Kód a testovat místně na Azure Functions

Zatímco vy budete schopní vývoj a testování v Azure Functions [Azure Portal], celá řada vývojářů raději místní vývojové prostředí. Funkce usnadňuje pomocí Oblíbené kód editoru a vývoj nástroje vytvořit a otestovat funkce v místním počítači. Lokální funkce se může připojit k Azure služby live, a můžete ladit, je v místním počítači pomocí úplné funkce runtime.

## <a name="local-development-environments"></a>Místní vývojové prostředí

Způsob, ve kterém můžete vyvíjet funkce v místním počítači závisí na vaší [jazyk](supported-languages.md) a tooling předvolby. Prostředí v následující tabulce podporují místní vývoj:

|Prostředí                              |Jazyky         |Popis|
|-----------------------------------------|------------|---|
| [Příkazový řádek nebo Terminálové](functions-run-local.md) | [C# skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Azure Functions základní nástroje] poskytuje základní runtime a šablony pro vytvoření funkce, která umožňuje místní vývoj. Verze 2.x podporu vývoje Windows, Linux a systému MacOS. Všechna prostředí závisí na základní nástroje pro místní runtime Functions.|
|[Visual Studio Code](https://code.visualstudio.com/tutorials/functions-extension/getting-started)| [C# skript (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Azure Functions rozšíření pro VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) přidá funkce podporují VS Code. Vyžaduje základní nástroje. Podporuje vývoj na Linuxu, systému MacOS a systému Windows, při použití verze 2.x nástroje jádra. Další informace najdete v tématu [nasadit do Azure pomocí Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).  |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (knihovna tříd)](functions-dotnet-class-library.md) | Jsou součástí nástroje Azure Functions **Azure development** úlohy, které obsahují [Visual Studio 2017 verze 15,5](https://www.visualstudio.com/vs/) a novějších verzích. Umožňuje kompilace funkcí v knihovně tříd a publikovat soubor knihovny DLL do Azure. Obsahuje nástroje jádra pro místní testování. Další informace najdete v tématu [Azure Functions vývoj pomocí sady Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) | [Java](functions-reference-java.md) | Se integruje s jádra nástroje umožňující vývoj Java funkcí. Verze 2.x podporuje vývoj v systému Windows, Linux a systému MacOS. Další informace najdete v tématu [vytvoření první funkce s Java a Maven](functions-create-first-java-maven.md).|

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Každý z těchto místním vývojovém prostředí vám umožní vytvořit funkce projekty aplikací a pomocí předdefinovaných šablon funkcí můžete vytvořit nové funkce. Každá používá základní nástroje, aby mohli testování a ladění funkcí proti skutečné runtime Functions na vlastním počítači stejně jako kterákoli jiná aplikace. Můžete také publikovat, můžete funkce projekt aplikace z jakéhokoli z těchto prostředí do Azure.  

## <a name="next-steps"></a>Další postup

+ Další informace o místní vývoj kompilované funkce jazyka C# pomocí Visual Studio 2017, najdete v části [Azure Functions vývoj pomocí sady Visual Studio](functions-develop-vs.md).
+ Další informace o místní vývoj funkcí pomocí VS kódu na počítači Mac, Linux nebo Windows, najdete v článku [VS Code dokumentaci pro Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Další informace o vývoji funkce z příkazového řádku nebo terminálu, najdete v části [práci s nástroji pro základní funkce Azure](functions-run-local.md).

<!-- LINKS -->

[Azure Functions základní nástroje]: https://www.npmjs.com/package/azure-functions-core-tools
[Azure Portal]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
