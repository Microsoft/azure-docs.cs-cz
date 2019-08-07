---
title: Řešení VMware podle služby CloudSimple
description: Popisuje přehled služby a konceptů CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/24/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a43fbebb21be82fd751778d6fec95e0ee9c346ad
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812509"
---
# <a name="cloudsimple-service-overview"></a>Přehled služby CloudSimple

Pomocí služby CloudSimple můžete použít řešení Azure VMware podle CloudSimple. Po vytvoření služby můžete zřídit uzly, rezervovat uzly a vytvořit privátní cloudy. Službu CloudSimple vytvoříte v každé oblasti Azure, kde je dostupná služba CloudSimple. Služba definuje hraniční síť řešení Azure VMware od CloudSimple. Hraniční síť podporuje služby, které zahrnují připojení k síti VPN, Azure ExpressRoute a připojení k Internetu do privátních cloudů.

## <a name="gateway-subnet"></a>Podsíť brány

Pro každou službu CloudSimple se vyžaduje podsíť brány a je jedinečná pro oblast, ve které je vytvořená. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR/28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple. Sítě, které komunikují s CloudSimple, zahrnují místní sítě a virtuální sítě Azure. Po vytvoření nelze odstranit podsíť brány. Podsíť brány se odebere, když se služba odstraní.

## <a name="next-steps"></a>Další postup

* Naučte se [vytvořit službu CloudSimple v Azure](quickstart-create-cloudsimple-service.md).
