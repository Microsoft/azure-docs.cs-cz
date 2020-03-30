---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174831"
---
>[!NOTE]
>Od 1. července 2018 se začíná rušit podpora protokolu TLS 1.0 a 1.1 ve službě Azure VPN Gateway. Služba VPN Gateway bude podporovat pouze protokol TLS 1.2. Chcete-li zachovat podporu, podívejte [se na aktualizace povolit podporu pro TLS1.2](#tls1).

Kromě toho následující starší algoritmy budou také zastaralé pro TLS v červenci 1, 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Jak povolím podporu TLS 1.2 ve Windows 7 a Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]
