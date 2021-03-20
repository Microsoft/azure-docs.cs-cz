---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93425010"
---
Spuštěním následujícího příkazu zobrazíte [protokoly streamování](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)téměř v reálném čase:

```console
func azure functionapp logstream <APP_NAME> 
```

V samostatném okně terminálu nebo v prohlížeči zavolejte vzdálenou funkci znovu. Podrobný protokol provádění funkce v Azure je zobrazený v terminálu. 