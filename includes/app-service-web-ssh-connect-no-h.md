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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175486"
---
Aby bylo možné otevřít přímou relaci SSH s vaším kontejnerem, měla by být vaše aplikace spuštěná.

Vložte následující adresu URL do prohlížeče a nahraďte ji \<app-name> názvem vaší aplikace:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Pokud ještě nejste ověřeni, budete se muset ověřit s vaším předplatným Azure, abyste se mohli připojit. Po ověření se zobrazí prostředí v prohlížeči, ve kterém můžete spouštět příkazy uvnitř svého kontejneru.

![Připojení SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
