---
title: zahrnout soubor
description: zahrnout soubor
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854117"
---
| Prostředek                                | Omezení        |
|-----------------------------------------|------------------------------|
| Předpony adres virtuální sítě                   | 600 na bránu VPN          |
| Agregované trasy protokolu BGP                    | 4 000 na bránu VPN        |
| Předpony adresy brány místní sítě  | 1000 na bránu místní sítě               |
| Připojení S2S                         | [Závisí na SKU brány.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Připojení P2S                         | [Závisí na SKU brány.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Limit trasy P2S – IKEv2                 | 256 pro jiný systém než Windows **/** 25 pro Windows           |
| Limit trasy P2S – OpenVPN               | 1000                         |
| Max. flows                              | 100 tisíc for VpnGw1/AZ  **/**  512K for VpnGw2-4/AZ|