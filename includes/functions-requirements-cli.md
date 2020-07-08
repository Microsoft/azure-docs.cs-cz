---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 9e9f9c71701ceb1c76bc162f22e166b4565e731b
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86062663"
---
## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java"  
+ Verze [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 nebo novější verze 2. x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Verze Azure Functions Core Tools, která odpovídá nainstalované verzi Pythonu:

   | Verze Pythonu | Verze základních nástrojů |
   | -------------- | ------------------ |
   | Python 3,8     | [verze 3. x](../articles/azure-functions/functions-run-local.md#v2) |
   | Python 3,6<br/>Python 3.7 | [Verze 2.7.1846 nebo novější](../articles/azure-functions/functions-run-local.md#v2) |
  
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) verze 2,4 nebo novější. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Node.js](https://nodejs.org/), aktivní verze LTS pro LTS a údržbu (Doporučené 8.11.1 a 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3,8 (64)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bit)](https://www.python.org/downloads/release/python-375/), [Python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), které jsou podporovány nástrojem Azure Functions. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ [.NET Core SDK 2.2 +](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Sada Java Developer Kit](https://aka.ms/azure-jdks), verze 8.

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.
::: zone-end
