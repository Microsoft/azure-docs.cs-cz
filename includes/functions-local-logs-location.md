---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739369"
---
Když funkce hostiteli běží místně, zapisuje protokoly do následujícího umístění:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

Na Windows `<DefaultTempDirectory>` je první nalezené hodnoty USERPROFILE TMP, TEMP, proměnné prostředí, nebo adresář Windows.
V systému MacOS nebo Linux `<DefaultTempDirectory>` je proměnná prostředí TMPDIR.

> [!NOTE]
> Při spuštění hostitele funkce přepíše existující strukturu souborů v adresáři.