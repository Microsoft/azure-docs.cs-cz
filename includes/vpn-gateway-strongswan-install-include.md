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
ms.openlocfilehash: 2d0e171807985deaebe8faa625d6b767c2a3efd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86218402"
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

[Další pokyny k instalaci rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
