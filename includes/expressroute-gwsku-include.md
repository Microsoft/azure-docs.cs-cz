---
title: zahrnout soubor
description: zahrnout soubor
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: include
ms.date: 04/05/2021
ms.author: duau
ms.custom: include file
ms.openlocfilehash: 27f5755ce8b7d204cad6cdc2281d7992bf86615a
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504673"
---
Při vytváření brány virtuální sítě musíte určit SKU brány, které chcete použít. Pokud vyberete vyšší SKU brány, přidělí se pro bránu víc procesorů a šířky pásma sítě, a v důsledku toho může brána podporovat vyšší propustnost sítě k virtuální síti. 

Brány virtuální sítě ExpressRoute můžou používat následující SKU:

|     | Brána sítě VPN a ExpressRoute existují vedle sebe | FastPath | Maximální počet připojení okruhů |
| --- | --- | --- | --- |
| **Standard** | Yes | No | 4 |
| **Vysokovýkonné** | Yes | No | 4 |
| **UltraPerformance** | Yes | Yes | 16 |