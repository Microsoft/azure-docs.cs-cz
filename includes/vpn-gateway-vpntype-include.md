---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b3907882df09bfae1d6453fbffbd3e7562554f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174889"
---
* **Zásady založené na:** Sítě virtuálních sítí založené na zásadách se dříve nazývaly statické brány směrování v klasickém modelu nasazení. Sítě virtuálnísítě založené na zásadách šifrují a směrují pakety prostřednictvím tunelových propojení IPsec na základě zásad IPsec nakonfigurovaných pomocí kombinací předpon adres mezi místní sítí a virtuální sítí Azure. Zásada (nebo selektor provozu) se většinou určuje v konfiguraci zařízení VPN jako přístupový seznam. Hodnota pro typ sítě VPN založené na zásadách je *PolicyBased*. Při použití sítě VPN založené na zásadách mějte na paměti následující omezení:
  
  * Sítě virtuálních sítě založené na zásadách lze použít **pouze** ve skladové jednotce základní brány. Tento typ VPN není kompatibilní s jinými virtuálními zařízeními brány.
  * Při použití sítě VPN založené na zásadách můžete mít pouze 1 tunelové propojení.
  * Sítě VIRTUÁLNÍSÍTĚ založené na zásadách lze použít pouze pro připojení S2S a pouze pro určité konfigurace. Většina konfigurací brány VPN vyžaduje síť VPN routebased.
* **RouteBased**: Sítě VIRTUÁLNÍ Sítě RouteBased se dříve nazývaly dynamické brány směrování v klasickém modelu nasazení. Virtuální ny RouteBased používají "trasy" v tabulce předávání nebo směrování IP k přímému směrování paketů do odpovídajících rozhraní tunelového propojení. Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásady (nebo volič provozu) pro sítě VIRTUÁLNÍ SÍTĚ RouteBased jsou nakonfigurovány jako libovolné (nebo zástupné znaky). Hodnota pro typ RouteBased VPN je *RouteBased*.
