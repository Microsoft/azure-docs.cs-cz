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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174894"
---
V následující tabulce jsou uvedeny požadavky na brány VPN Založené na zásadách a RouteBased. Tato tabulka platí pro model nasazení Resource Manager a pro model nasazení Classic. Pro klasický model zásady založené na vpn brány jsou stejné jako statické brány a brány založené na směrování jsou stejné jako dynamické brány.

|  | **Základní brána VPN založená na zásadách** | **Základní brána VPN založená na route** | **Standardní brána VPN založená na routebased** | **Směrová vysoce výkonná brána VPN** |
| --- | --- | --- | --- | --- |
| **Připojení typu site-to-site… (S2S)** |Konfigurace sítě VPN založené na zásadách |Konfigurace sítě VPN routebased |Konfigurace sítě VPN routebased |Konfigurace sítě VPN routebased |
| **Připojení typu point-to-site (P2S**) |Nepodporuje se |Podporuje se (může existovat vedle připojení S2S) |Podporuje se (může existovat vedle připojení S2S) |Podporuje se (může existovat vedle připojení S2S) |
| **Metoda ověřování** |Předsdílený klíč |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |
| **Maximální počet připojení S2S** |1 |10 |10 |30 |
| **Maximální počet připojení P2S** |Nepodporuje se |128 |128 |128 |
| **Aktivní podpora směrování (BGP)** (*) |Nepodporuje se |Nepodporuje se |Podporuje se |Podporuje se |

  (*) Protokol BGP není podporován pro klasický model nasazení.
