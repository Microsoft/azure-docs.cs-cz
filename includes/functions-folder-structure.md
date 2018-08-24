---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 08/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cbe634d862682a5f6b06c2cfc77a4d3b03954f9
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811605"
---
Kód pro všechny funkce v aplikaci konkrétní funkce je umístěn v kořenové složce (`wwwroot`), která obsahuje konfigurační soubor hostitele a jeden nebo více podsložky. Každá podsložka obsahuje kód pro samostatnou funkci, jako v následujícím příkladu:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
 | - bin
 | | - mycompiledcsharp.dll
```

Host.json souboru obsahuje některé konfigurace specifické pro modul runtime a je umístěn v kořenové složce aplikace function app. Informace o nastavení, které jsou k dispozici, najdete v článku [referenční materiály k host.json](../articles/azure-functions/functions-host-json.md).

Každá funkce má složku, která obsahuje jeden nebo více souborů kódu, konfigurace function.json a dalších závislostí. Pro C# projekt knihovny tříd, je nasazen zkompilovaný soubor knihovny tříd (.dll) na `bin` podsložky.

