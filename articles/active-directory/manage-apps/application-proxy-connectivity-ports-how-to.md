---
title: Tom, jak otevřít porty brány firewall, vyžaduje se pro aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Zjistěte, jaké porty otevřete pro Azure AD Application Proxy fungovat správně
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: af43b1dd0a6ccb60ba6911bce8dd540aed5c0db4
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356370"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Tom, jak otevřít porty brány firewall, vyžaduje se pro aplikaci Proxy aplikací

Pokud chcete zobrazit úplný seznam požadované porty a funkce každý z portů, naleznete v části Požadavky [Proxy aplikací dokumentaci](application-proxy-enable.md). Všimněte si, že Proxy aplikace používá pouze odchozí porty.

Můžete také zkontrolovat, zda mají všechny požadované porty otevřete tak, že otevřete [nástroj pro testování konektoru porty](https://aadap-portcheck.connectorporttest.msappproxy.net/) z vaší místní sítě. Další zelené zaškrtnutí znamená větší odolnost proti chybám. 

## <a name="app-proxy-regions"></a>Proxy aplikací oblastí

Pracujeme na způsob, jak vám oznámíme, které z těchto oblastí musí být zelená za vás. Teď Ujistěte se, že všechny jsou. Také je vyžadována bez ohledu na to, jakou oblast v USA (střed).

Pokud chcete mít jistotu, že nástroj poskytuje správných výsledků, nezapomeňte na následující:

-   Spusťte nástroj v prohlížeči ze serveru, kam jste nainstalovali konektor.

-   Ujistěte se, že všechny proxy nebo brány firewall pro váš konektor jsou použita také na této stránce. To můžete udělat v aplikaci Internet Explorer, tak, že přejdete do **nastavení**  - &gt; **Možnosti Internetu**  - &gt; **připojení**  - &gt; **Nastavení místní sítě**. Na této stránce se zobrazí pole "Použití Proxy serveru pro svůj LAN". Zaškrtněte toto políčko a vložte adresu proxy serveru do pole "Address".

## <a name="next-steps"></a>Další postup
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
