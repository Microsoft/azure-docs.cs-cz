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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38944424"
---
Při vývoji funkcí místně, můžete nainstalovat rozšíření, která je třeba pomocí nástrojů Azure Functions Core z terminálu nebo z příkazového řádku. 

Po aktualizaci vašich *function.json* soubor zahrnout všechny vazby, které potřebuje, vaše funkce běží `func extensions install` příkazu ve složce projektu. Tento příkaz načte *function.json* soubor. zobrazí balíčky, které potřebujete a nainstaluje je.

Pokud chcete nainstalovat konkrétní verzi balíčku, nebo chcete provést instalaci balíčků před úpravou *function.json* souboru, použijte `func extensions install` příkaz s názvem balíčku, jak je znázorněno v následujícím příkladu:

```
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Nahraďte `<target_version>` s určitou verzí balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org).
