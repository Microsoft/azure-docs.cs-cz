---
title: Řešení Azure VMware (AVS) – služba
description: V této části najdete přehled služby a konceptů služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d09c8c34093e7d33122f934138ff9fdf4842508e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024946"
---
# <a name="avs-service-overview"></a>Služba AVS – přehled služby

Služba AVS umožňuje využívat řešení Azure VMware pomocí služby AVS. Vytvořením služby můžete koupit uzly, rezervovat uzly a vytvořit privátní cloudy služby AVS. Službu AVS můžete vytvořit v každé oblasti Azure, kde je k dispozici služba AVS. Služba prostřednictvím služby AVS definuje hraniční síť řešení Azure VMware. Hraniční síť podporuje služby, které zahrnují připojení VPN, ExpressRoute a Internet k privátním cloudům služby AVS.

## <a name="gateway-subnet"></a>Podsíť brány

Pro každou službu AVS se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím AVS. Sítě, které komunikují se službou AVS, zahrnují místní sítě a Azure Virtual Network. Po vytvoření nelze odstranit podsíť brány. Podsíť brány se odebere, když se služba odstraní.

## <a name="next-steps"></a>Další kroky

* Naučte se [vytvořit službu AVS v Azure](quickstart-create-cloudsimple-service.md).
