---
title: Řešení Azure VMware podle CloudSimple-Service
description: Seznamte se s jeho přehledem o službě CloudSimple. Vytvořením služby umožníte nákup uzlů, rezervace uzlů a vytváření privátních cloudů.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8f32197eda4fc7632e883fd21dd6e1caa0dbd24b
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898738"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvořením služby umožníte nákup uzlů, rezervace uzlů a vytváření privátních cloudů.  Službu CloudSimple vytvoříte v každé oblasti Azure, kde je dostupná služba CloudSimple. Služba definuje hraniční síť řešení Azure VMware od CloudSimple. Hraniční síť podporuje služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům.

## <a name="gateway-subnet"></a>Podsíť brány

Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28.  Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple, zahrnují místní sítě a Azure Virtual Network.  Po vytvoření nelze odstranit podsíť brány.  Podsíť brány se odebere, když se služba odstraní.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvořit službu CloudSimple v Azure](quickstart-create-cloudsimple-service.md).
