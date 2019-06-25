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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175486"
---
Ujistěte se, otevřete relaci SSH s přímým přístupem s kontejneru, měl by být spuštěný vaší aplikace.

Vložte následující adresu URL do prohlížeče a nahradit \<název aplikace > názvem vaší aplikace:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Pokud jste ještě není ověřen, budete muset ověřit pomocí vašeho předplatného Azure připojit. Po ověření, se zobrazí prostředí v prohlížeči, kde můžete spouštět příkazy uvnitř kontejneru.

![Připojení SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
