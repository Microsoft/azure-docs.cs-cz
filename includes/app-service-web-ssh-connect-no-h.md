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
ms.openlocfilehash: 458cd36a35ea37b2a317fe98fdeb5acc69a36ce8
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822803"
---
Pokud chcete otevřít přímou relaci SSH s kontejnerem, vaše aplikace by měla být spuštěná.

Vložte následující adresu URL do vašeho prohlížeče a `<app-name>` nahraďte názvem vaší aplikace:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Pokud ještě nejste ověření, budete se muset ověřit s vaším předplatným Azure, abyste se mohli připojit. Po ověření se vám zobrazí prostředí prohlížeče, ve kterém můžete spouště příkazy uvnitř vašeho kontejneru.

![Připojení SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
