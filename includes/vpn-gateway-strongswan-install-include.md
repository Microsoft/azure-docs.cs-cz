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
ms.openlocfilehash: 089ad704a466590a9649107fef245a88d6a4d6e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102244632"
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

[Další pokyny k instalaci rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli-apt)