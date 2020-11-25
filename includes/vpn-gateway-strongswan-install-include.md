---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025779"
---
Pro následující kroky se použila tato konfigurace:

- Počítač: Ubuntu Server 18,04
- Závislosti: klient strongswan


Pomocí následujících příkazů nainstalujte požadovanou konfiguraci klient strongswan:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Pomocí následujícího příkazu nainstalujte rozhraní příkazového řádku Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Další pokyny k instalaci rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)