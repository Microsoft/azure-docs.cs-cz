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
ms.openlocfilehash: dcfa65c655d1508510282fe66e90e4076278b29b
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949952"
---
## <a name="run-the-function-locally"></a>Místní spuštění funkce

Následující příkaz spustí aplikaci funkcí. Aplikace je spuštěna pomocí stejného modulu runtime Azure Functions, který je v Azure. Spouštěcí příkaz se liší v závislosti na jazyku projektu.

### <a name="c"></a>C\#

```command
func start --build
```

### <a name="javascript"></a>JavaScript

```command
func start
```

### <a name="typescript"></a>TypeScript

```command
npm install
npm start     
```

Jakmile se hostitel funkce spustí, zapíše něco podobného následujícímu výstupu, který byl zkrácen pro čitelnost:

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