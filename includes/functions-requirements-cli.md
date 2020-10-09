---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: e969520e226999bc2b104fb4f757619832eaa165
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88944413"
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
+ [Sada Java Developer Kit](https://aka.ms/azure-jdks), verze 8. 

    > [!IMPORTANT]
    > + Podpora funkcí pro Java 11 je momentálně ve verzi Preview a Maven Archetype ve výchozím nastavení vytvoří nasazení Java 8. Chcete-li místo toho spustit aplikaci Function App na Java 11, je nutné ručně aktualizovat soubor pom.xml s hodnotami jazyka Java 11. Další informace najdete v tématu [verze Java](../articles/azure-functions/functions-reference-java.md#java-versions). 
    > + `JAVA_HOME`Aby se tento rychlý Start dokončil, musí být proměnná prostředí nastavená na umístění instalace správné verze JDK.

+ [Apache Maven](https://maven.apache.org)verze 3,0 nebo vyšší.

::: zone-end
