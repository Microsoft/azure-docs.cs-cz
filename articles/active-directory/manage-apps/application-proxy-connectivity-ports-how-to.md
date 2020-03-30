---
title: Jak otevřít porty brány firewall potřebné pro aplikaci Proxy aplikace
description: Zjistěte, jaké porty se mají otevřít pro proxy aplikace Azure AD, aby fungoval správně
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275547"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Jak otevřít porty brány firewall potřebné pro aplikaci Proxy aplikace

Úplný seznam požadovaných portů a funkce každého portu naleznete v části požadavky [v dokumentaci k proxy aplikaci](application-proxy-add-on-premises-application.md). Všimněte si, že proxy aplikace používá pouze odchozí porty.

Můžete také zkontrolovat, zda máte všechny požadované porty otevřené otevřením [nástroje Connector Ports Test Tool](https://aadap-portcheck.connectorporttest.msappproxy.net/) z místní sítě. Více zelených značek znamená větší odolnost proti chybám. 

## <a name="app-proxy-regions"></a>Oblasti proxy aplikace

Pracujeme na způsobu, jak vám dát vědět, který z těchto regionů pro vás musí být zelený. Prozatím se ujisti, že jsou všichni. Centrální USA je také nutné bez ohledu na to, ve kterém regionu se nacházíte.

Chcete-li se ujistit, že nástroj poskytuje správné výsledky, ujistěte se, že:

-   Otevřete nástroj v prohlížeči ze serveru, na kterém jste nainstalovali konektor.

-   Ujistěte se, že všechny proxy servery nebo brány firewall, které se vztahují k konektoru jsou také použity na této stránce. To lze provést v aplikaci Internet Explorer najdete **v nastavení**  - &gt; **Nastavení nastavení připojení**  - &gt; **k síti**  - &gt; **LAN .** Na této stránce se zobrazí pole "Použít proxy server pro vaši síť LAN". Toto políčko zaškrtněte a adresu proxy vložte do pole Adresa.

## <a name="next-steps"></a>Další kroky
[Principy konektorů proxy aplikací Azure AD](application-proxy-connectors.md)
