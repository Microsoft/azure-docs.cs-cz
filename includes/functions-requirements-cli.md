---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: a84f0a92703d1b626710cfc4dcfa2820bc58bda6
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673119"
---
## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell"  
+ [Nástroje Azure Functions Core verze](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 nebo novější verze 2.x.
::: zone-end  
::: zone pivot="programming-language-python"
+ Python 3.6 a 3.7 vyžadují [nástroje Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) verze 2.7.1846 nebo novější verzi 2.x. Python 3.8 vyžaduje [verzi 3.x](../articles/azure-functions/functions-run-local.md#v2) základních nástrojů.
::: zone-end

+ [Azure CLI](/cli/azure/install-azure-cli) verze 2.0.76 nebo novější. 
::: zone pivot="programming-language-javascript,programming-language-typescript"
+ [Verze Node.js](https://nodejs.org/), Active LTS a Maintenance LTS (doporučeno 8.11.1 a 10.14.1).
::: zone-end

::: zone pivot="programming-language-python"
+ [Python 3.8 (64bitový)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64bitový)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64bitový)](https://www.python.org/downloads/release/python-368/), které jsou podporované funkcemi Azure. 
::: zone-end
::: zone pivot="programming-language-powershell"
+ [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-windows)

+ Sada [SDK 2.2 core .NET](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Java Developer Kit](https://aka.ms/azure-jdks), verze 8.

+ [Apache Maven](https://maven.apache.org), verze 3.0 nebo vyšší.

> [!IMPORTANT]
> Pro dokončení tohoto rychlého startu musí být proměnná prostředí JAVA_HOME nastavená na umístění instalace sady JDK.
::: zone-end