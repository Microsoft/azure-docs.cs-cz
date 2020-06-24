---
title: Postup otevření portů brány firewall vyžadovaných pro aplikaci proxy aplikace
description: Zjistěte, jaké porty se mají otevřít, aby mohla Proxy aplikací služby AD Azure fungovat správně.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 192e721e25612019649fed86b44135fb6bfef89c
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2020
ms.locfileid: "84764702"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Postup otevření portů brány firewall vyžadovaných pro aplikaci proxy aplikace

Úplný seznam požadovaných portů a funkce jednotlivých portů naleznete v části požadavky v [dokumentaci k proxy aplikací](application-proxy-add-on-premises-application.md). Všimněte si, že proxy aplikací používá pouze odchozí porty.

Můžete taky zkontrolovat, jestli máte všechny požadované porty otevřené, a to tak, že otevřete [Nástroj pro testování portů konektoru](https://aadap-portcheck.connectorporttest.msappproxy.net/) z místní sítě. Více zelených značek znamená větší odolnost. 

## <a name="app-proxy-regions"></a>Oblasti proxy aplikací

Pracujeme na tom, abychom vám věděli, které z těchto oblastí musí být pro vás zelené. Prozatím se ujistěte, že jsou všechny. Střed USA je také vyžadován bez ohledu na to, ve které oblasti se nacházíte.

Abyste se ujistili, že vám nástroj poskytne správné výsledky, nezapomeňte:

-   Otevřete nástroj v prohlížeči ze serveru, na který jste nainstalovali konektor.

-   Zajistěte, aby na této stránce platily i všechny proxy a brány firewall, které platí pro váš konektor. To se dá udělat v Internet Exploreru tak, že na **Nastavení**  - &gt; **Možnosti Internetu**  - &gt; **připojení**  - &gt; **nastavení sítě LAN**. Na této stránce se zobrazí pole "použít proxy server pro vaši síť LAN". Zaškrtněte toto políčko a adresu proxy umístěte do pole adresa.

## <a name="next-steps"></a>Další kroky
[Vysvětlení konektorů Azure Proxy aplikací služby AD](application-proxy-connectors.md)
