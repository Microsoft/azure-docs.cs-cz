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
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
Když hostitel funkce běží místně, zapisuje protokoly do následujícího umístění:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

V systému Windows `<DefaultTempDirectory>` je první nalezené hodnoty USERPROFILE TMP TEMP, proměnné prostředí nebo adresáři Windows.
V systému MacOS nebo Linux `<DefaultTempDirectory>` je proměnná prostředí TMPDIR.

> [!NOTE]
> Při spuštění funkce hostitele, přepíše stávající strukturu souborů v adresáři.