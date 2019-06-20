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
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174889"
---
* **PolicyBased:** PolicyBased označovaly jako brány se statickým směrováním v modelu nasazení classic. Sítě VPN založené na zásadách šifrují pakety a směrují do tunelových propojení IPsec podle zásad IPsec nakonfigurovaných pomocí kombinace předpon adres mezi vaší místní sítí a virtuální sítě Azure. Zásada (nebo selektor provozu) se většinou určuje v konfiguraci zařízení VPN jako přístupový seznam. Hodnota pro síť VPN typu PolicyBased *PolicyBased*. Při použití sítě VPN PolicyBased, mějte na paměti následující omezení:
  
  * Sítě VPN PolicyBased mohou **pouze** použít pro základní SKU brány. Tento typ sítě VPN není kompatibilní s další SKU brány.
  * Při použití sítě VPN PolicyBased, může mít pouze 1 tunel.
  * Můžete použít pouze sítě VPN PolicyBased pro připojení S2S a jenom pro některé konfigurace. Většina konfigurací brány VPN vyžaduje VPN typu RouteBased.
* **RouteBased**: RouteBased označovaly jako brány s dynamickým směrováním v modelu nasazení classic. Sítí VPN RouteBased použít IP předávání nebo směrovací tabulce ke směrování paketů do svých příslušných rozhraní tunelových propojení "trasy". Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásada (nebo selektor provozu) pro sítě VPN typu RouteBased jsou nakonfigurované jako any-to-any (nebo zástupné znaky). Hodnota pro typ sítě VPN RouteBased *RouteBased*.
