---
title: Tom, jak otevřít porty brány firewall, vyžaduje se pro aplikaci Proxy aplikací | Dokumentace Microsoftu
description: Zjistěte, jaké porty otevřete pro Azure AD Application Proxy fungovat správně
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 710abfd8dd56d3f8276accec397b21d8d60fd3c5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55203313"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Tom, jak otevřít porty brány firewall, vyžaduje se pro aplikaci Proxy aplikací

Pokud chcete zobrazit úplný seznam požadované porty a funkce každý z portů, naleznete v části Požadavky [Proxy aplikací dokumentaci](application-proxy-add-on-premises-application.md). Všimněte si, že Proxy aplikace používá pouze odchozí porty.

Můžete také zkontrolovat, zda mají všechny požadované porty otevřete tak, že otevřete [nástroj pro testování konektoru porty](https://aadap-portcheck.connectorporttest.msappproxy.net/) z vaší místní sítě. Další zelené zaškrtnutí znamená větší odolnost proti chybám. 

## <a name="app-proxy-regions"></a>Proxy aplikací oblastí

Pracujeme na způsob, jak vám oznámíme, které z těchto oblastí musí být zelená za vás. Teď Ujistěte se, že všechny jsou. Také je vyžadována bez ohledu na to, jakou oblast v USA (střed).

Pokud chcete mít jistotu, že nástroj poskytuje správných výsledků, nezapomeňte na následující:

-   Spusťte nástroj v prohlížeči ze serveru, kam jste nainstalovali konektor.

-   Ujistěte se, že všechny proxy nebo brány firewall pro váš konektor jsou použita také na této stránce. To můžete udělat v aplikaci Internet Explorer, tak, že přejdete do **nastavení**  - &gt; **Možnosti Internetu**  - &gt; **připojení**  - &gt; **Nastavení místní sítě**. Na této stránce se zobrazí pole "Použití Proxy serveru pro svůj LAN". Zaškrtněte toto políčko a vložte adresu proxy serveru do pole "Address".

## <a name="next-steps"></a>Další postup
[Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)
