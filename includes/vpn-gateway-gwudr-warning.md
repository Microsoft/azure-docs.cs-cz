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
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274851"
---
Uživatelem definované trasy s cílem 0.0.0.0/0 a skupiny nsg v síti GatewaySubnet **nejsou podporovány**. Brány vytvořené pomocí této konfigurace budou blokovány od vytvoření. Brány vyžadují přístup k řadičům správy, aby správně fungovaly. [Šíření trasy Protokolu BGP](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) by mělo být nastaveno na možnost "Povoleno" v síti GatewaySubnet, aby byla zajištěna dostupnost brány. Pokud je tato funkce zakázána, brána nebude fungovat.
