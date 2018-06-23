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
ms.openlocfilehash: 5de227e5de5ef9b41f6e0f64db86b7195259f7d6
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "30196757"
---
* **PolicyBased:** sítě VPN PolicyBased označovaly jako brány se statickým směrováním v modelu nasazení classic. Sítě VPN založené na zásadách šifrují pakety a směrují je do tunelových propojení IPsec na základě zásad IPsec nakonfigurovaných pomocí kombinace předpon adres mezi vaší místní sítí a virtuální síť Azure. Zásada (nebo selektor provozu) se většinou určuje v konfiguraci zařízení VPN jako přístupový seznam. Hodnota parametru typu Policybased *PolicyBased*. Při použití sítě VPN PolicyBased, mějte na paměti následující omezení:
  
  * Sítě VPN PolicyBased může **pouze** použije na základní SKU brány. Tento typ sítě VPN není kompatibilní s SKU jiné brány.
  * Při použití sítě VPN PolicyBased, může mít pouze 1 tunelové propojení.
  * Sítě VPN PolicyBased můžete použít pouze pro připojení S2S a jenom pro některé konfigurace. Většinu konfiguraci brány VPN vyžadují sítě VPN RouteBased.
* **RouteBased**: sítě VPN RouteBased označovaly jako brány s dynamickým směrováním v modelu nasazení classic. Sítě VPN RouteBased používají IP předávání nebo směrovací tabulce ke směrování paketů do svých příslušných rozhraní tunelových propojení "trasy". Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásada (nebo selektor provozu) pro sítě VPN RouteBased jsou nakonfigurované jako any-to-any (nebo zástupné znaky). Hodnota pro typ sítě VPN RouteBased je *RouteBased*.
