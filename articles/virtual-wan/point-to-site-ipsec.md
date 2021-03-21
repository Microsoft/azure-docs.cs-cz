---
title: Zásady protokolu IPsec typu Point-to-site pro virtuální sítě WAN
titleSuffix: Azure Virtual WAN
description: Přečtěte si o zásadách připojení IPsec typu Point-to-site pro Azure Virtual WAN.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101744988"
---
# <a name="point-to-site-ipsec-policies"></a>Zásady protokolu IPsec Point-to-site

Tento článek ukazuje podporované kombinace zásad protokolu IPsec pro připojení VPN typu Point-to-site v Azure Virtual WAN.

## <a name="default-ipsec-policies"></a>Výchozí zásady protokolu IPsec

V následující tabulce jsou uvedeny výchozí parametry protokolu IPsec pro připojení typu Point-to-site k síti VPN. Tyto parametry se při vytvoření profilu Point-to-site automaticky zvolí z virtuální sítě WAN Point-to-Site VPN Gateway.

| Nastavení | Parametry |
|--- |--- |
| Šifrování IKE fáze 1 | AES256 |
| Fáze 1 – integrita IKE |  SHA256 |
| Skupina DH | DHGroup24 |
| Fáze 2 šifrování IPsec | GCMAES256|
| Integrita protokolu IPsec fáze 2 | GCMAES25 |
| Skupina PFS |PFS24|

## <a name="custom-ipsec-policies"></a>Vlastní zásady IPsec

Při práci s vlastními zásadami IPsec mějte na paměti následující požadavky:

* **IKE** – pro fázi 1 protokolu IKE můžete vybrat libovolný parametr z šifrování protokolu IKE, a také libovolný parametr z integrity protokolu IKE a jakýkoli parametr ze skupiny DH.
* **IPSec** – pro fázi 2 protokolu IPSec můžete vybrat libovolný parametr z šifrování protokolu IPSec a jakýkoli parametr z integrity protokolu IPSec a PFS. Pokud je GCM některý z parametrů šifrování protokolu IPsec nebo integrity protokolu IPsec, musí mít šifrování a integritu protokolu IPSec stejný algoritmus. Pokud je například pro šifrování IPsec zvolena možnost GCMAES128, musí být pro integritu protokolu IPsec vybrána také možnost GCMAES128.  

Následující tabulka uvádí dostupné parametry protokolu IPsec pro připojení typu Point-to-site k síti VPN typu Point-to-site.

| Nastavení | Parametry |
|--- |--- |
| Šifrování IKE fáze 1 | AES128, AES256, GCMAES128, GCMAES256 |
| Fáze 1 – integrita IKE |  SHA256, SHA384 |
| Skupina DH | DHGroup14, DHGroup24, ECP256, ECP384 |
| Fáze 2 šifrování IPsec | GCMAES128, GCMAES256, SHA256|
| Integrita protokolu IPsec fáze 2 | GCMAES128, GCMAES256 |
| Skupina PFS |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Další kroky

Viz [Postup vytvoření připojení typu Point-to-site](virtual-wan-point-to-site-portal.md) .

Další informace o virtuální síti WAN najdete v [nejčastějších dotazech k virtuální síti WAN](virtual-wan-faq.md).
