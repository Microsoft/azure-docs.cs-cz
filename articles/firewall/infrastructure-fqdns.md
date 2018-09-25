---
title: Infrastruktura plně kvalifikovaný název domény pro Azure bránu Firewall
description: Další informace o infrastruktuře plně kvalifikovaných názvů domén v Brána Firewall služby Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 1369a82829b2c80768d746ba92daf2482c1fd7f8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994135"
---
# <a name="infrastructure-fqdns"></a>Infrastruktura plně kvalifikovaných názvů domén

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. 

Tyto služby jsou součástí kolekce předdefinovaných pravidel:

- COMPUTE přístup k úložišti úložiště Image platformy (PIR)
- Spravovat přístup k úložišti disků stav
- Protokolování (MDS) a diagnostiky Azure
- Azure Active Directory

## <a name="overriding"></a>Přepsání 

Tuto kolekci pravidel integrované infrastruktury můžete přepsat vytvořením Odepřít všechny kolekce pravidel aplikace, která je zpracována jako poslední. Vždy se zpracuje před kolekcí pravidel infrastruktury. Vše mimo kolekci pravidel infrastruktury je ve výchozím nastavení zakázané.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nasazení a konfiguraci brány Firewall Azure](tutorial-firewall-deploy-portal.md).