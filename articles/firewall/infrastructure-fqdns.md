---
title: Podrobný než faktická správa infrastruktury pro Azure Firewall
description: Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168981"
---
# <a name="infrastructure-fqdns"></a>Plně kvalifikované názvy domén infrastruktury

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. 

Do kolekce předdefinovaných pravidel jsou zahrnuty následující služby:

- Výpočetní přístup k úložišti image platformy úložiště (PIR)
- Přístup ke stavu spravovaných disků
- Diagnostika a protokolování Azure (MDS)

## <a name="overriding"></a>Přepsání 

Tuto kolekci pravidel integrované infrastruktury můžete přepsat vytvořením kolekce pravidel odepřít všechny aplikace, která je zpracována jako poslední. Vždy se zpracuje před kolekcí pravidel infrastruktury. Vše mimo kolekci pravidel infrastruktury je ve výchozím nastavení zakázané.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat bránu Azure Firewall](tutorial-firewall-deploy-portal.md).