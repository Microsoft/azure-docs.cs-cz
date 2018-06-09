---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0377548a3b026657ae3282801523b1c0c6731265
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35236677"
---
Od 1. července 2018, je právě odebírán podpora protokolu TLS 1.0 a 1.1 z Azure VPN Gateway. Brána sítě VPN bude podporovat jenom TLS 1.2. Připojení point-to-site pouze dopad; připojení Site-to-site nebude mít vliv. Pokud používáte TLS pro sítě point-to-site VPN na klientů s Windows 10, nemusíte provádět žádnou akci. Pokud používáte TLS pro připojení point-to-site v klientech Windows 7 a Windows 8, přečtěte si téma [VPN Gateway – nejčastější dotazy](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S) pokyny aktualizace.