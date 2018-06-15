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
ms.openlocfilehash: 873549442284ede2e9f020bd90879f721b9c1a18
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31805202"
---
## <a name="scenario"></a>Scénář
Chcete-li lépe předvedli, jak vytvořit skupiny Nsg, tento dokument používá následující scénář:

![Scénář sítě VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

V tomto scénáři vytvoříte skupinu NSG pro každou podsíť v **TestVNet** virtuální sítě, a to následovně: 

* **Skupina NSG front-endu**. Front-endu NSG se použije na *front-endu* podsítě a obsahuje dvě pravidla:    
  * **pravidlo protokolu RDP**. Umožňuje provoz protokolu RDP *front-endu* podsítě.
  * **pravidlo webové**. Umožňuje provoz protokolu HTTP *front-endu* podsítě.
* **Skupina NSG back-end**. Back-end NSG se použije na *back-end* podsítě a obsahuje dvě pravidla:    
  * **Pravidlo SQL**. Umožňuje přenos SQL pouze z *front-endu* podsítě.
  * **pravidlo webové**. Odmítne všechny internet vázaný provoz z *back-end* podsítě.

Kombinace tato pravidla vytvořit jako zóna DMZ scénář, kde back-end podsíť může přijímat pouze příchozí provoz pro SQL z front-endu podsítě a nemá přístup k Internetu, zatímco front-end podsíť komunikují po Internetu a přijímat příchozí požadavky HTTP jenom.

