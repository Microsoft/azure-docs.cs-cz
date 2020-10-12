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
ms.openlocfilehash: e2a950037aed2a8ded4d4e55920721285cbfc05c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204555"
---
Při práci s vlastními zásadami IPsec mějte na paměti následující požadavky:

* **IKE** – pro protokol IKE můžete vybrat libovolný parametr z šifrování IKE, a také libovolný parametr z integrity protokolu IKE a jakýkoli parametr ze skupiny DH.
* **Protokol IPSec** – pro protokol IPSec můžete vybrat libovolný parametr z šifrování protokolu IPSec a jakýkoli parametr z integrity protokolu IPSec a PFS. Pokud je GCM některý z parametrů šifrování protokolu IPsec nebo integrity protokolu IPsec, musí být parametry obou nastavení GCM.

>[!NOTE]
> S vlastními zásadami IPsec neexistuje koncept respondérů a iniciátoru (na rozdíl od výchozích zásad protokolu IPsec). Obě strany (místní i brána Azure VPN Gateway) budou používat stejné nastavení pro fáze 1 a IKE fáze 2 protokolu IKE. Podporují se protokoly IKEv1 i IKEv2. Azure není podporován pouze jako respondér.
>

**Dostupná nastavení a parametry**

| Nastavení | Parametry |
|--- |--- |
| Šifrování IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Integrita protokolu IKE | SHA384, SHA256 |
| Skupina DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Šifrování protokolem IPsec | GCMAES256, GCMAES128, AES256, AES128, None |
| Integrita protokolu IPsec | GCMAES256, GCMAES128, SHA256 |
| Skupina PFS | ECP384, ECP256, PFS24, PFS14, None |
