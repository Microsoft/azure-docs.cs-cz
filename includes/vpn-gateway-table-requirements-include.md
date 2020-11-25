---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96010798"
---
V následující tabulce jsou uvedené požadavky na brány VPN PolicyBased a RouteBased. Tato tabulka platí pro model nasazení Resource Manager a pro model nasazení Classic. Pro klasický model jsou brány VPN PolicyBased stejné jako statické brány a brány založené na trasách jsou stejné jako dynamické brány.

|  | **PolicyBased Basic VPN Gateway** | **RouteBased Basic VPN Gateway** | **RouteBased Standard VPN Gateway** | **RouteBased vysoký výkon VPN Gateway** |
| --- | --- | --- | --- | --- |
| **Připojení typu site-to-site… (S2S)** |Konfigurace sítě VPN PolicyBased |Konfigurace sítě VPN RouteBased |Konfigurace sítě VPN RouteBased |Konfigurace sítě VPN RouteBased |
| **Připojení typu point-to-site (P2S**) |Nepodporováno |Podporuje se (může existovat vedle připojení S2S) |Podporuje se (může existovat vedle připojení S2S) |Podporuje se (může existovat vedle připojení S2S) |
| **Metoda ověřování** |Předsdílený klíč |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |
| **Maximální počet připojení S2S** |1 |10 |10 |30 |
| **Maximální počet připojení P2S** |Nepodporováno |128 |128 |128 |
| **Podpora aktivních směrování (BGP)** (*) |Nepodporováno |Nepodporováno |Podporováno |Podporováno |

  (*) Protokol BGP není pro klasický model nasazení podporován.
