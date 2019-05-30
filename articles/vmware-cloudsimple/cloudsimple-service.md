---
title: Řešení VMware podle CloudSimple – služby
description: Popisuje přehled CloudSimple služby a koncepty.
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/24/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f7b4be0ff3997e27dd5b5321dd44b5006ae52102
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358105"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Služba CloudSimple umožňuje využívat řešení VMware Azure podle CloudSimple.  Vytváření služby umožňuje koupit uzly, rezerva uzly a vytvoření privátních Cloudů.  Vytvoříte službu CloudSimple v každé oblasti Azure, kde je k dispozici služba CloudSimple.  Služba definuje hraniční síť řešení VMware Azure podle CloudSimple.  Hraniční síť podporuje služby, které zahrnují sítě VPN, ExpressRoute a připojení k Internetu k privátním Cloudům.

## <a name="gateway-subnet"></a>Podsíť brány

Podsíť brány je vyžadována na službu CloudSimple a je jedinečný pro oblast, ve kterém se vytvoří. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje o velikosti/28 blok CIDR.  Adresní prostor podsítě brány musí být jedinečný. Nesmí překrývat s síti, která komunikuje s CloudSimple prostředí. Sítě, které komunikují s CloudSimple zahrnují místními sítěmi a Azure virtual network.  Podsíť brány nejde odstranit, jakmile ho vytvoříte.  Podsíť brány je odebrat, pokud je služba odstraněna.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvoření CloudSimple služby v Azure](quickstart-create-cloudsimple-service.md)