---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/18/2020
ms.author: glenga
ms.openlocfilehash: 179ae760f146a5ac3041a54065ae12147f3f9bf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97739806"
---
Vzhledem k tomu, že Archetype také vytvoří sadu testů, je nutné aktualizovat tyto testy pro zpracování nového `msg` parametru v `run` signatuře metody.  

Přejděte do umístění testovacího kódu v části _Src/test/Java_, otevřete soubor projektu *Function. Java* a nahraďte řádek kódu `//Invoke` následujícím kódem.

:::code language="java" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/src/test/java/com/function/FunctionTest.java" range="48-50":::
