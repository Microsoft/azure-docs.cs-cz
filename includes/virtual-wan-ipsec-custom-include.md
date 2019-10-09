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
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168400"
---
Při práci s vlastními zásadami IPsec mějte na paměti následující požadavky:

* **IKE** – pro protokol IKE můžete vybrat libovolný parametr z šifrování IKE, a také libovolný parametr z integrity protokolu IKE a jakýkoli parametr ze skupiny DH.
* **Protokol IPSec** – pro protokol IPSec můžete vybrat libovolný parametr z šifrování protokolu IPSec a jakýkoli parametr z integrity protokolu IPSec a PFS. Pokud je GCM některý z parametrů šifrování protokolu IPsec nebo integrity protokolu IPsec, musí být parametry obou nastavení GCM.

>[!NOTE]
> S vlastními zásadami IPsec neexistuje koncept respondérů a iniciátoru (na rozdíl od výchozích zásad protokolu IPsec). Obě strany (místní i brána Azure VPN Gateway) budou používat stejné nastavení pro fáze 1 a IKE fáze 2 protokolu IKE. Podporují se protokoly IKEv1 i IKEv2. Azure není podporován pouze jako respondér.
>

**Dostupná nastavení a parametry**

| Nastavením | Parametry |
|--- |--- |
| Šifrování IKE | AES256, AES192, AES128 |
| Integrita protokolu IKE | SHA384, SHA256, SHA1 |
| Skupina DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Šifrování IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integrita protokolu IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Skupina PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
