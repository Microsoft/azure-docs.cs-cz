---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 6a359cdd44cc0c0cfbd93bd23b69a67a641c7fbb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673292"
---
## <a name="update-the-tests"></a>Aktualizace testů

Vzhledem k tomu, že Archetype také vytvoří sadu testů, je nutné aktualizovat tyto testy pro zpracování nového `msg` parametru v `run` signatuře metody.  

Přejděte do umístění testovacího kódu v části _Src/test/Java_, otevřete soubor projektu *Function. Java* a nahraďte řádek kódu `//Invoke` následujícím kódem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::