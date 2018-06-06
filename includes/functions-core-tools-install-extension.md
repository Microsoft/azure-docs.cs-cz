---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 04/06/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6fb497a5b6da00dece43c7f41ea3c411f385a2ba
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34726883"
---
Při vývoji funkce místně, můžete nainstalovat rozšíření, která je nutné pomocí nástroje Azure funkce základní z terminálu nebo z příkazového řádku. 

Po dokončení aktualizace vašeho *function.json* souboru všechny vazby, které funkce potřebuje, spusťte `func extensions install` příkazu ve složce projektu. Načte příkaz *function.json* soubor zobrazíte balíčky, které potřebujete a potom nainstaluje je.

Pokud chcete nainstalovat konkrétní verzi balíčku, nebo chcete instalovat balíčky před úpravou *function.json* soubor, použijte `func extensions install` příkaz s názvem balíčku, jak je znázorněno v následujícím příkladu:

```
func extensions install --package Microsoft.Azure.WebJobs.Extensions.CosmosDB --version <target_version>
```

Nahraďte `<target_version>` s určitou verzí balíčku. Neplatná verze jsou uvedené na stránkách individuální balíčku na [NuGet.org](https://nuget.org).
