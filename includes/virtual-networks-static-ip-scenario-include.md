---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015912"
---
## <a name="scenario"></a>Scenario

Pro lepší ilustraci, jak nakonfigurovat statickou IP adresu pro virtuální počítač, tento dokument používá tento scénář:

![Scénář virtuální sítě: front-endové a back-endové podsítě se statickou IP adresou pro front-end podsíť](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

V tomto scénáři vytvoříte virtuální počítač s názvem *DNS01* v podsíti *front-endu* a pak ho nastavíte tak, aby používal statickou IP adresu *192.168.1.101*.
