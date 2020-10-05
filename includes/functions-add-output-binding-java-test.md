---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: eb54439f89cc2443eeed2d3b63dfbe7fedb4bf17
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "80673443"
---
## <a name="update-the-tests"></a>Aktualizace testů

Vzhledem k tomu, že Archetype také vytvoří sadu testů, je nutné aktualizovat tyto testy pro zpracování nového `msg` parametru v `run` signatuře metody.  

Přejděte do umístění testovacího kódu v části _Src/test/Java_, otevřete soubor projektu *Function. Java* a nahraďte řádek kódu `//Invoke` následujícím kódem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
