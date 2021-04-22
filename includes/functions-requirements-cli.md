---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 7a390c0d19a37ea18f9eac8636683ec35ecbc844
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107881432"
---
## <a name="configure-your-local-environment"></a>Konfigurace místního prostředí

Než začnete, musíte mít následující:

+ Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-java,programming-language-other"  
+ Verze [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#v2) 2.7.1846 nebo novější.
::: zone-end  
::: zone pivot="programming-language-python"
+ Verze Azure Functions Core Tools, která odpovídá nainstalované verzi Pythonu:

   | Python version (Verze Pythonu) | Verze základních nástrojů |
   | -------------- | ------------------ |
   | Python 3.8     | [verze 3. x](../articles/azure-functions/functions-run-local.md#v2) |
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
+ [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download)
::: zone-end
::: zone pivot="programming-language-java"  
+ [Sada Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), verze 8 nebo 11. 

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

::: zone-end
::: zone pivot="programming-language-other"
+ Vývojové nástroje pro jazyk, který používáte. V tomto kurzu se jako příklad používá [programovací jazyk R](https://www.r-project.org/) .
::: zone-end