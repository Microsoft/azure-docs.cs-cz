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
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336022"
---
Od 1. července 2018, je právě odebírán podpora protokolu TLS 1.0 a 1.1 z Azure VPN Gateway. Brána sítě VPN bude podporovat jenom TLS 1.2. Pokud chcete zachovat podporu protokolu TLS a připojení pro vaši klienti point-to-site Windows 7 a Windows 8, které používají TLS, doporučujeme nainstalovat následující aktualizace:

• [Aktualizace pro implementaci Microsoft EAP, která umožňuje použití protokolu TLS](https://support.microsoft.com/help/2977292/microsoft-security-advisory-update-for-microsoft-eap-implementation-th)

• [Aktualizace k povolení TLS 1.1 a TLS 1.2 jako výchozí zabezpečené protokolů v WinHTTP](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-a-default-secure-protocols-in)

Tyto algoritmy starší verze také přestanou TLS na 1. července 2018:

* RC4 (Rivest šifrovací 4)
* DES (Data šifrovací algoritmus)
* Algoritmus 3DES (Triple šifrovací algoritmus)
* Algoritmus MD5 (Message Digest 5)
