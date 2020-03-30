---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168400"
---
Při práci s vlastními zásadami IPsec mějte na paměti následující požadavky:

* **IKE** - Pro IKE můžete vybrat libovolný parametr z IKE Encryption, plus libovolný parametr z Integrity Protokolu IKE a libovolný parametr ze skupiny DH.
* **IPsec** - Pro IPsec můžete vybrat libovolný parametr z iPsec encryption, plus libovolný parametr z Integrity Protokolu IPsec a PFS. Pokud některý z parametrů pro šifrování IPsec nebo Integrita Protokolu IPsec je GCM, pak parametry pro obě nastavení musí být GCM.

>[!NOTE]
> S vlastní zásady IPsec neexistuje žádný koncept respondéru a iniciátora (na rozdíl od výchozích zásad IPsec). Obě strany (místní a brána Azure VPN) budou používat stejné nastavení pro IKE fáze 1 a IKE fáze 2. Protokoly IKEv1 a IKEv2 jsou podporovány. Neexistuje žádná podpora pro Azure jako pouze respondér.
>

**Dostupná nastavení a parametry**

| Nastavení | Parametry |
|--- |--- |
| Šifrování ike | AES256, AES192, AES128 |
| Integrita ike | SHA384, SHA256, SHA1 |
| Skupina DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integrita protokolu IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Skupina PFS | PFS24, ECP384, ECP256, PF2048, PFS2 |
