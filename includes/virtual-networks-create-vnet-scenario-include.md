---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: 2b1f9990985951a4e6ef260954968c0e1466c298
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174923"
---
## <a name="scenario"></a>Scénář

V tomto dokumentu si ukážeme, jak vytvořit virtuální síť a podsítě, používá následující scénář:

![Scénář sítě VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

V tomto scénáři vytvoříte virtuální síť s názvem **TestVNet**, s vyhrazeným blokem CIDR **192.168.0.0./16**. Virtuální síť obsahuje následující podsítě: 

* **FrontEnd** s blokem CIDR **192.168.1.0/24**
* **BackEnd** s blokem CIDR **192.168.2.0/24**

