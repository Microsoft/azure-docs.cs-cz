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
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501765"
---
Uživatelem definované trasy s cílem 0.0.0.0/0 a skupiny nsg v síti GatewaySubnet **nejsou podporovány**. Brány vytvořené pomocí této konfigurace budou blokovány od vytvoření. Brány vyžadují přístup k řadičům správy, aby správně fungovaly. [Šíření trasy Protokolu BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) by mělo být nastaveno na možnost "Povoleno" v síti GatewaySubnet, aby byla zajištěna dostupnost brány. Pokud je tato funkce zakázána, brána nebude fungovat.
