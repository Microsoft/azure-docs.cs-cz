---
title: Řešení Azure VMware od CloudSimple – služba
description: Obsahuje přehled cloudových služeb a konceptů.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d128a248c2e6e1e2e35e3b633975ba081e77f028
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024946"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvoření služby umožňuje zakoupit uzly, rezervovat uzly a vytvořit privátní cloudy.  Službu CloudSimple vytvoříte v každé oblasti Azure, kde je služba CloudSimple dostupná. Služba definuje hraniční síť řešení Azure VMware podle CloudSimple. Hraniční síť podporuje služby, které zahrnují VPN, ExpressRoute a připojení k internetu do privátních cloudů.

## <a name="gateway-subnet"></a>Podsíť brány

Podsíť brány je vyžadována pro službu CloudSimple a je jedinečná pro oblast, ve které je vytvořena. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR /28.  Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple patří místní sítě a virtuální sítě Azure.  Podsíť brány nelze po vytvoření odstranit.  Podsíť brány je odebrána při odstranění služby.

## <a name="next-steps"></a>Další kroky

* Přečtěte si, jak [vytvořit službu CloudSimple v Azure](quickstart-create-cloudsimple-service.md).
