---
title: Plně kvalifikovaný název domény infrastruktury pro Azure Firewall
description: Další informace o plně kvalifikovaném názvu domény infrastruktury v Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 09/20/2019
ms.author: victorh
ms.openlocfilehash: 5b134058e1924bae030338411226a9a6aff46557
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130202"
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