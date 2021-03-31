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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67174866"
---
Pokud máte potíže s připojením, podívejte se na následující položky:

- Pokud jste exportovali certifikát klienta pomocí **Průvodce exportem certifikátu**, ujistěte se, že jste ho exportovali jako soubor. pfx a vybrali **Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné**. Při exportu s touto hodnotou jsou exportovány také informace o kořenovém certifikátu. Po instalaci certifikátu do klientského počítače je také nainstalován kořenový certifikát v souboru. pfx. Pokud chcete ověřit, jestli je nainstalovaný kořenový certifikát, otevřete **spravovat certifikáty uživatelů** a vyberte **Důvěryhodné kořenové certifikační autority Authorities\Certificates**. Ověřte, že je v seznamu uveden kořenový certifikát, který musí být k dispozici, aby ověřování fungovalo.

- Pokud jste použili certifikát vydaný podnikovou certifikační autoritou a nemůžete ověřit, ověřte pořadí ověřování klientského certifikátu. V pořadí seznamu ověřování klikněte dvakrát na certifikát klienta, vyberte kartu **Podrobnosti** a pak vyberte **rozšířené použití klíče**. Ujistěte se, že je *ověření klienta* první položkou v seznamu. Pokud ne, vydejte klientský certifikát založený na šabloně uživatele, která má *ověřování klienta* jako první položku v seznamu.

- Další informace o řešení potíží s P2S najdete v tématu [Řešení potíží s připojeními P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).