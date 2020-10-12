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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "81274851"
---
Trasy definované uživatelem s cíli 0.0.0.0/0 a skupin zabezpečení sítě v GatewaySubnet nejsou **podporovány**. Pro brány vytvořené s touto konfigurací budou zablokovány vytváření. Brány vyžadují přístup k řadičům pro správu, aby fungovaly správně. [Šíření trasy protokolu BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) by mělo být na GatewaySubnet nastaveno na povoleno, aby se zajistila dostupnost brány. Pokud je tato nastavení zakázaná, brána nebude fungovat.
