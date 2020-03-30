---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/11/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0ce4e882f270f5e0c789a608aaada5c6c9cba92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67174866"
---
Pokud máte potíže s připojením, zkontrolujte následující položky:

- Pokud jste exportovali klientský certifikát pomocí **Průvodce exportem certifikátu**, ujistěte se, že jste jej exportovali jako soubor PFX a pokud možno vybrali **zahrnout všechny certifikáty do cesty certifikace**. Při exportu s touto hodnotou se exportují také informace o kořenovém certifikátu. Po instalaci certifikátu do klientského počítače je nainstalován také kořenový certifikát v souboru PFX. Chcete-li ověřit, zda je kořenový certifikát nainstalován, otevřete **možnost Spravovat uživatelské certifikáty** a vyberte **možnost Důvěryhodné kořenové certifikační autority\Certifikáty**. Ověřte, zda je v seznamu uveden kořenový certifikát, který musí být k dispozici pro ověřování, aby fungoval.

- Pokud jste použili certifikát vydaný řešením certifikační autority rozlehlé sítě a nemůžete jej ověřit, ověřte pořadí ověřování na klientském certifikátu. Poklepáním na klientský certifikát, výběrem na kartu **Podrobnosti** a výběrem **možnosti Rozšířené použití klíče**zaškrtněte pořadí seznamu ověřování . Ujistěte se, že *ověření klienta* je první položkou v seznamu. Pokud tomu tak není, vystavte klientský certifikát založený na uživatelské šabloně, která má *ověřování klienta* jako první položku v seznamu.

- Další informace o řešení potíží s P2S najdete v tématu [Řešení potíží s připojeními P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).