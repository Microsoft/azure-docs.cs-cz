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
ms.openlocfilehash: 9a17f34333503436d3da340670abdde154e45ef6
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38727522"
---
Od 1. července 2018 se začíná rušit podpora protokolu TLS 1.0 a 1.1 ve službě Azure VPN Gateway. Služba VPN Gateway bude podporovat pouze protokol TLS 1.2. Pokud chcete pro své klienty Point-to-Site s Windows 7 a Windows 8 využívající protokol TLS zachovat podporu a možnosti připojení protokolu TLS, doporučujeme nainstalovat následující aktualizace:

• [Aktualizace pro implementaci protokolu EAP od Microsoftu umožňující použití protokolu TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

• [Aktualizace pro povolení protokolů TLS 1.1 a TLS 1.2 jako výchozích zabezpečených protokolů ve službě WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

1. července 2018 se také přestanou používat následující starší verze algoritmů pro protokol TLS:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)
