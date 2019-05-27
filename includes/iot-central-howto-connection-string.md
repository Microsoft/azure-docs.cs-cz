---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 04/09/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: eae4b1e919270413d4ca6627964fad9c7f5bd9bf
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151966"
---
1. Použití `dps-keygen` nástroj příkazového řádku k vygenerování připojovacího řetězce:

    K instalaci [klíče nástroj Generátor](https://github.com/Azure/dps-keygen), spusťte následující příkaz:

    ```cmd/sh
    npm i -g dps-keygen
    ```

1. Pokud chcete generovat připojovací řetězec, spusťte následující příkaz pomocí podrobné informace o připojení, které jste si poznamenali dříve:

    ```cmd/sh
    dps-keygen -di:<Device ID> -dk:<Primary or Secondary Key> -si:<Scope ID>
    ```

1. Zkopírujte připojovací řetězec z `dps-keygen` výstup pro použití ve vašem kódu zařízení.