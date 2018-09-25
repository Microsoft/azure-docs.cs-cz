---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: functions
ms.topic: include
ms.date: 09/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f1b53c53b1e5fb089eb9b8a9b816b11a1eea126d
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47044505"
---
Při vývoji funkcí místně, můžete nainstalovat rozšíření, která je třeba pomocí nástrojů Azure Functions Core z terminálu nebo z příkazového řádku.

Po aktualizaci vašich *function.json* soubor zahrnout všechny vazby, které vaše funkce vyžaduje, spusťte následující příkaz ve složce projektu.

```bash
func extensions install
```

Tento příkaz načte *function.json* souboru a zjistěte jaké balíčky, které potřebujete, nainstaluje je a znovu sestaví projekt rozšíření. Přidá všechny nové vazby na aktuální verzi, ale neaktualizuje existující vazby. Použití `--force` možnost aktualizovat existující vazby na nejnovější verzi při instalaci nové značky.

Pokud chcete nainstalovat konkrétní verzi balíčku, nebo chcete provést instalaci balíčků před úpravou *function.json* souboru, použijte `func extensions install` příkaz s názvem balíčku, jak je znázorněno v následujícím příkladu:

```bash
func extensions install --package Microsoft.Azure.WebJobs.ServiceBus --version <target_version>
```

Nahraďte `<target_version>` s určitou verzí balíčku, jako například `3.0.0-beta5`. Platná verze jsou uvedeny v nějakém balíčku stránkách v [NuGet.org](https://nuget.org).
