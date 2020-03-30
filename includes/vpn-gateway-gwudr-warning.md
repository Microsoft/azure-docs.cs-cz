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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838163"
---
Uživatelem definované trasy s cílem 0.0.0.0/0 a skupiny nsg v síti GatewaySubnet **nejsou podporovány**. Brány vytvořené pomocí této konfigurace budou blokovány od vytvoření. Brány vyžadují přístup k řadičům správy, aby správně fungovaly.
