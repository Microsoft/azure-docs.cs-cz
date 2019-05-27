---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 10/20/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 74e14e36b1ac0979da31203a2d16e2396ed821d0
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66132190"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Následující příkaz spustí aplikaci funkcí. Aplikace je spuštěna pomocí stejného modulu runtime Azure Functions, který je v Azure.

```bash
func host start --build
```

Ke kompilování projektů jazyka C# je vyžadována možnost `--build`. Tuto možnost pro projekt jazyka JavaScript nepotřebujete.

Při spuštění funkce hostitele se vám zobrazí něco, co se podobá následujícímu výstupu, který jsme kvůli čitelnosti zkrátili:

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

Zkopírujte adresu URL vaší funkce `HttpTrigger` z výstupu modulu runtime a vložte do panelu Adresa vašeho prohlížeče. K této adrese URL připojte řetězec dotazu `?name=<yourname>` a proveďte požadavek. Následuje ukázka odezvy na požadavek GET vrácené místní funkcí v prohlížeči:

![Místní test v prohlížeči](./media/functions-run-function-test-local/functions-test-local-browser.png)

Teď, když jste spustili funkci místně, můžete vytvořit aplikaci aplikace funkcí a ostatní požadované prostředky v Azure.