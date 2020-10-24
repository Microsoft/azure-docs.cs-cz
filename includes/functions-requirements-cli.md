---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7ddceb2808874e531cfda81e5a9ce74be3a4fab4
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517071"
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
+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Sada Java Developer Kit](https://aka.ms/azure-jdks), verze 8 nebo 11. 

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

::: zone-end
