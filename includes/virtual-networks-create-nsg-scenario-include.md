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
ms.openlocfilehash: 09c6871fc5243296da2f2defd594afb80c62ac95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
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

