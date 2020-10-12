---
title: Řešení Azure VMware podle CloudSimple-Service
description: Seznamte se s jeho přehledem o službě CloudSimple. Vytvořením služby umožníte nákup uzlů, rezervace uzlů a vytváření privátních cloudů.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ec77209c0995bf5aec0957c8d4d2269f27d743bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88142124"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvořením služby umožníte nákup uzlů, rezervace uzlů a vytváření privátních cloudů.  Službu CloudSimple vytvoříte v každé oblasti Azure, kde je dostupná služba CloudSimple. Služba definuje hraniční síť řešení Azure VMware od CloudSimple. Hraniční síť podporuje služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům.

## <a name="gateway-subnet"></a>Podsíť brány

Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28.  Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple, zahrnují místní sítě a Azure Virtual Network.  Po vytvoření nelze odstranit podsíť brány.  Podsíť brány se odebere, když se služba odstraní.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvořit službu CloudSimple v Azure](quickstart-create-cloudsimple-service.md).
