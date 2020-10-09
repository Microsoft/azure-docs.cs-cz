---
title: Plně kvalifikovaný název domény infrastruktury pro Azure Firewall
description: Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: d4de81bad1ddda2f5b54f47c1be78925ad4cdbf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168981"
---
# <a name="infrastructure-fqdns"></a>Plně kvalifikované názvy domén infrastruktury

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. 

Do předdefinované kolekce pravidel jsou zahrnuté tyto služby:

- Výpočetní přístup k úložišti imagí platformy úložiště (PIR)
- Přístup k úložišti stavu spravovaných disků
- Azure Diagnostics a protokolování (MDS)

## <a name="overriding"></a>Přitom 

Tuto vestavěnou kolekci pravidel infrastruktury můžete přepsat vytvořením kolekce pravidel Odepřít všechny aplikace, která se zpracovává jako poslední. Vždy se zpracuje před kolekcí pravidel infrastruktury. Vše mimo kolekci pravidel infrastruktury je ve výchozím nastavení zakázané.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).