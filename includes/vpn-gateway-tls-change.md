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
ms.openlocfilehash: 2ed141847f923a847443d2293e850519357cdae2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174898"
---
Od 1. července 2018 se začíná rušit podpora protokolu TLS 1.0 a 1.1 ve službě Azure VPN Gateway. Služba VPN Gateway bude podporovat pouze protokol TLS 1.2. Ovlivněny jsou pouze připojení bodu k lokalitě. Připojení mezi lokalitami nebudou ovlivněna. Pokud používáte TLS pro sítě VIRTUÁLNÍ SÍTĚ s bodem na web v klientech windows 10, nemusíte provádět žádnou akci. Pokud používáte protokol TLS pro připojení z bodu na pracoviště v klientech se systémem Windows 7 a Windows 8, přečtěte si pokyny k aktualizaci v nejčastějších dotazech k [bráně VPN.](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)