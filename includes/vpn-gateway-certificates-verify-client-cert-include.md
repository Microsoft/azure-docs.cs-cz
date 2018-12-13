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
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323655"
---
Pokud máte potíže s připojením, zkontrolujte následující položky:

- Pokud jste exportovali klientský certifikát s **Průvodce exportem certifikátu**, ujistěte se, že jste ho exportovali jako soubor .pfx a vybrali **zahrnout všechny certifikáty cestě k certifikátu, pokud je to možné**. Při exportu s touto hodnotou, se exportují také informace o kořenovém certifikátu. Po instalaci certifikátu v klientském počítači se nainstaluje i kořenový certifikát v souboru .pfx. Chcete-li ověřit, že se nainstaluje kořenový certifikát, otevřete **správu uživatelských certifikátů** a vyberte **důvěryhodné kořenové certifikační autority\Certifikáty**. Ověřte, zda je uveden kořenový certifikát, který musí být k dispozici, aby ověřování fungovalo.

- Pokud jste použili certifikát vydaný řešením CA organizace a nemůže ověřit, zkontrolujte pořadí ověřování na klientském certifikátu. Zkontrolujte pořadí seznamu ověřování tak, že poklikáte klientský certifikát, vyberete **podrobnosti** kartu a pak vyberete **rozšířené použití klíče**. Ujistěte se, že *ověření klienta* představuje první položku v seznamu. Pokud tomu tak není, vydat klientský certifikát založený na šabloně uživatele, který má *ověření klienta* jako první položku v seznamu.

- Další informace o řešení potíží s P2S najdete v tématu [Řešení potíží s připojeními P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).