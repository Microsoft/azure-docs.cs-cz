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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010826"
---
* **PolicyBased:** Sítě VPN PolicyBased se dřív nazývaly brány statického směrování v modelu nasazení Classic. Sítě VPN založené na zásadách šifrují a směrují pakety prostřednictvím tunelů IPsec na základě zásad IPsec nakonfigurovaných s kombinacemi předpon adres mezi vaší místní sítí a virtuální sítí Azure. Zásada (nebo selektor provozu) se většinou určuje v konfiguraci zařízení VPN jako přístupový seznam. Hodnota pro typ VPN PolicyBased je *PolicyBased*. Při použití PolicyBased VPN mějte na paměti následující omezení:
  
  * Sítě VPN PolicyBased se dají použít **jenom** na základní skladové jednotce brány. Tento typ sítě VPN není kompatibilní s jinými SKU brány.
  * Při použití PolicyBased VPN můžete mít jenom jeden tunel.
  * Pro připojení S2S můžete použít jenom sítě VPN PolicyBased a jenom pro určité konfigurace. Většina konfigurací VPN Gateway vyžaduje RouteBased VPN.
* **RouteBased**: v modelu nasazení Classic se dříve používaly sítě VPN brány RouteBased. Sítě VPN RouteBased používají "trasy" v tabulce předávání IP nebo směrovací tabulky k přímému směrování paketů do příslušných rozhraní tunelového propojení. Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásady (nebo selektor provozu) pro VPN RouteBased jsou nakonfigurovány jako any (nebo zástupné karty). Hodnota pro typ VPN RouteBased je *RouteBased*.
