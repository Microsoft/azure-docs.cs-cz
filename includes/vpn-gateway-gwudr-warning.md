---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: cf9d4c3fd96df83361e7d9aa89ba702d37265ec6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95563562"
---
Trasy definované uživatelem s cíli 0.0.0.0/0 a skupin zabezpečení sítě v GatewaySubnet nejsou **podporovány**. Pro brány vytvořené s touto konfigurací budou zablokovány vytváření. Brány vyžadují přístup k řadičům pro správu, aby fungovaly správně. [Šíření trasy protokolu BGP](../articles/virtual-network/virtual-networks-udr-overview.md#border-gateway-protocol) by mělo být na GatewaySubnet nastaveno na povoleno, aby se zajistila dostupnost brány. Pokud je tato možnost nastavená na hodnotu Zakázáno, brána nebude fungovat.