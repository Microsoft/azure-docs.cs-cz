---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genli
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 29b2ac1a5dc128028dbd40e683c1b45e6208d124
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
## <a name="scenario"></a>Scénář

Tento dokument k ukazují, jak vytvořit síť VNet a podsítě, používá následující scénář:

![Scénář sítě VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

V tomto scénáři vytvoříte virtuální síť s názvem **TestVNet**, s vyhrazeným blokem CIDR **192.168.0.0./16**. Virtuální sítě obsahuje následující podsítě: 

* **FrontEnd** s blokem CIDR **192.168.1.0/24**
* **BackEnd** s blokem CIDR **192.168.2.0/24**

