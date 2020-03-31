---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175486"
---
Chcete-li otevřít přímou relaci SSH s kontejnerem, vaše aplikace by měla být spuštěná.

Vložte do prohlížeče následující \<adresu URL a nahraďte název aplikace> názvem aplikace názvem aplikace:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Pokud ještě nejste ověřeni, musíte se ověřit pomocí předplatného Azure pro připojení. Po ověření se zobrazí prostředí v prohlížeči, kde můžete spouštět příkazy uvnitř kontejneru.

![Připojení SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
