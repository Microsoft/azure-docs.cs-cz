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
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175486"
---
Pokud chcete otevřít přímou relaci SSH s kontejnerem, vaše aplikace by měla být spuštěná.

Vložte následující adresu URL do vašeho prohlížeče a \<app-name> nahraďte názvem vaší aplikace:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Pokud ještě nejste ověření, budete se muset ověřit s vaším předplatným Azure, abyste se mohli připojit. Po ověření se vám zobrazí prostředí prohlížeče, ve kterém můžete spouště příkazy uvnitř vašeho kontejneru.

![Připojení SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
