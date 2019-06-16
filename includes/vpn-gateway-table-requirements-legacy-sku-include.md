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
ms.openlocfilehash: 5b9e036816aa532d32b1b4305ef6ae646ae05bae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159264"
---
V následující tabulce jsou uvedeny požadavky pro PolicyBased a RouteBased VPN Gateway. Tato tabulka platí pro model nasazení Resource Manager i pro klasický model. V případě klasického modelu brány PolicyBased VPN jsou stejné jako statické brány a trasové brány jsou stejné jako odpovídají dynamickým branám.

|  | **Brány sítě VPN PolicyBased Basic** | **RouteBased Basic VPN Gateway** | **RouteBased Standard VPN Gateway** | **RouteBased vysoce výkonná brána sítě VPN** |
| --- | --- | --- | --- | --- |
| **Připojení Site-to-Site (S2S)** |Konfigurace sítě VPN PolicyBased |Konfigurace VPN typu RouteBased |Konfigurace VPN typu RouteBased |Konfigurace VPN typu RouteBased |
| **Připojení typu point-to-site (P2S**) |Nepodporuje se |Podporuje se (může existovat vedle připojení S2S) |Podporuje se (může existovat vedle připojení S2S) |Podporuje se (může existovat vedle připojení S2S) |
| **Metoda ověřování** |Předsdílený klíč |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |Předsdílený klíč pro připojení S2S, certifikáty pro připojení P2S |
| **Maximální počet připojení S2S** |1 |10 |10 |30 |
| **Maximální počet připojení P2S** |Nepodporuje se |128 |128 |128 |
| **Podpora aktivního směrování (BGP)** |Nepodporuje se |Nepodporuje se |Podporováno |Podporováno |
