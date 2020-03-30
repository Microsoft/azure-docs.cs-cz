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
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "69520826"
---
Následující konfigurace byla použita pro následující kroky:

  | | |
  |---|---|
  |Počítač| Ubuntu Server 18.04|
  |Závislosti| silnýLaupan |


K instalaci požadované konfigurace strongSwan použijte následující příkazy:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

K instalaci rozhraní příkazového řádku Azure použijte následující příkaz:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Další pokyny k instalaci příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
