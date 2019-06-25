---
title: Řešení VMware službou CloudSimple
description: Popisuje přehled CloudSimple služby a koncepty.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6a4c0bc6070d372a279b74f81ac1f84f565559c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165244"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Ve službě CloudSimple můžete použít řešení VMware Azure podle CloudSimple. Po vytvoření služby zřízení uzlů a uzly rezervovat vytváření privátních cloudů. Vytvoříte službu CloudSimple v každé oblasti Azure, kde je k dispozici služba CloudSimple. Služba definuje hraniční síť řešení VMware Azure podle CloudSimple. Hraniční síť podporuje služby, které zahrnují sítě VPN, Azure ExpressRoute a připojení k Internetu do privátních cloudů.

## <a name="gateway-subnet"></a>Podsíť brány

Podsíť brány je vyžadována na službu CloudSimple a je jedinečný pro oblast, ve kterém se vytvoří. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje o velikosti/28 blok CIDR. Adresní prostor podsítě brány musí být jedinečný. Nesmí překrývat s síti, která komunikuje s CloudSimple prostředí. Sítě, které komunikují s CloudSimple zahrnují místních sítí a virtuální sítě Azure. Podsíť brány nejde odstranit, po jeho vytvoření. Podsíť brány je odebrat, pokud je služba odstraněna.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [v Azure vytvořit službu CloudSimple](quickstart-create-cloudsimple-service.md).
