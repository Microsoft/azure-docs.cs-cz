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
ms.openlocfilehash: d166a77a0636efea3b63660fde2187e3f2ec15c0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063744"
---
Při vývoji funkce místně, můžete nainstalovat rozšíření, která je nutné pomocí nástroje Azure funkce základní z terminálu nebo z příkazového řádku. 

Po dokončení aktualizace vašeho *function.json* souboru všechny vazby, které funkce potřebuje, spusťte `func extensions install` příkazu ve složce projektu. Načte příkaz *function.json* soubor zobrazíte balíčky, které potřebujete a potom nainstaluje je.

Pokud chcete nainstalovat konkrétní verzi balíčku, nebo chcete instalovat balíčky před úpravou *function.json* soubor, použijte `func extensions install` příkaz s názvem balíčku, jak je znázorněno v následujícím příkladu:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Nahraďte `<target_version>` s určitou verzí balíčku, například `3.0.0-beta5`. Neplatná verze jsou uvedené na stránkách individuální balíčku na [NuGet.org](https://nuget.org).
