---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673443"
---
## <a name="update-the-tests"></a>Aktualizace testů

Vzhledem k tomu, že archetyp také vytvoří sadu testů, `msg` je `run` třeba aktualizovat tyto testy zpracovat nový parametr v podpisu metody.  

Přejděte do umístění testovacího kódu pod _src/test/java_, otevřete soubor projektu *Function.java* a nahraďte řádek kódu pod `//Invoke` následujícím kódem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
