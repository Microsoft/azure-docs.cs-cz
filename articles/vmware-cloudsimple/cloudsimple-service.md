---
title: Řešení Azure VMware podle CloudSimple-Service
description: Poskytuje přehled služby a konceptů CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877666"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvořením služby umožníte nákup uzlů, rezervace uzlů a vytváření privátních cloudů.  Službu CloudSimple vytvoříte v každé oblasti Azure, kde je dostupná služba CloudSimple. Služba definuje hraniční síť řešení Azure VMware od CloudSimple. Hraniční síť podporuje služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům.

## <a name="gateway-subnet"></a>Podsíť brány

Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28.  Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple, zahrnují místní sítě a Azure Virtual Network.  Po vytvoření nelze odstranit podsíť brány.  Podsíť brány se odebere, když se služba odstraní.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvořit službu CloudSimple v Azure](quickstart-create-cloudsimple-service.md).
