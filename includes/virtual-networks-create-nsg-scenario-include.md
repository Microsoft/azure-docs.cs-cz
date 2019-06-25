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
ms.openlocfilehash: 588aa260f2ece543445bfd4da7ef4682dab8334c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174928"
---
## <a name="scenario"></a>Scénář
Abychom vám lépe předvedli vytvoření skupin zabezpečení sítě, tento dokument používá následující scénář:

![Scénář sítě VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

V tomto scénáři vytvoříte skupinu zabezpečení sítě pro každou podsíť v **TestVNet** virtuální sítě, následujícím způsobem: 

* **Skupina zabezpečení sítě front-endu**. Front-endu skupina NSG použije na *front-endu* podsítě a obsahuje dvě pravidla:    
  * **rdp-rule**. Umožňuje provoz protokolu RDP *front-endu* podsítě.
  * **pravidlo webové**. Umožňuje přenos HTTP *front-endu* podsítě.
* **Skupina zabezpečení sítě back-endu**. Back-end skupina NSG použije na *back-endu* podsítě a obsahuje dvě pravidla:    
  * **sql-rule**. SQL provoz pouze z *front-endu* podsítě.
  * **pravidlo webové**. Odepře všechny internetové vázán provoz z *back-endu* podsítě.

Kombinace tato pravidla vytvořit scénář jako v hraniční síti, kde lze pouze přijímání příchozího provozu pro SQL z front-endové podsítě back endové podsítě a nemá přístup k Internetu, zatímco front-endové podsítě můžete komunikovat s Internetem a přijímat příchozí požadavky HTTP pouze.

