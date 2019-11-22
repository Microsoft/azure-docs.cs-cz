---
title: Postup otevření portů brány firewall vyžadovaných pro aplikaci proxy aplikace
description: Zjistěte, jaké porty otevřete pro Azure AD Application Proxy fungovat správně
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275547"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Tom, jak otevřít porty brány firewall, vyžaduje se pro aplikaci Proxy aplikací

Pokud chcete zobrazit úplný seznam požadované porty a funkce každý z portů, naleznete v části Požadavky [Proxy aplikací dokumentaci](application-proxy-add-on-premises-application.md). Všimněte si, že Proxy aplikace používá pouze odchozí porty.

Můžete taky zkontrolovat, jestli máte všechny požadované porty otevřené, a to tak, že otevřete [Nástroj pro testování portů konektoru](https://aadap-portcheck.connectorporttest.msappproxy.net/) z místní sítě. Další zelené zaškrtnutí znamená větší odolnost proti chybám. 

## <a name="app-proxy-regions"></a>Proxy aplikací oblastí

Pracujeme na způsob, jak vám oznámíme, které z těchto oblastí musí být zelená za vás. Teď Ujistěte se, že všechny jsou. Také je vyžadována bez ohledu na to, jakou oblast v USA (střed).

Pokud chcete mít jistotu, že nástroj poskytuje správných výsledků, nezapomeňte na následující:

-   Spusťte nástroj v prohlížeči ze serveru, kam jste nainstalovali konektor.

-   Ujistěte se, že všechny proxy nebo brány firewall pro váš konektor jsou použita také na této stránce. To lze provést v aplikaci Internet Explorer tak, že v **nastavení** -&gt; **možnosti Internetu** -&gt; **připojení** -&gt; **Nastavení LAN**. Na této stránce se zobrazí pole "Použití Proxy serveru pro svůj LAN". Zaškrtněte toto políčko a vložte adresu proxy serveru do pole "Address".

## <a name="next-steps"></a>Další kroky
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
