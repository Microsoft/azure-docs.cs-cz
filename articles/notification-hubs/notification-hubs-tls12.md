---
title: Aktualizace TLS centra oznámení
description: Přečtěte si o podpoře TLS v Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908524"
---
# <a name="transport-layer-security-tls"></a>Protokol TLS (Transport Layer Security)

Chcete-li zajistit vyšší úroveň zabezpečení, centra oznámení zakáže podporu pro TLS verze 1.0 a 1.1 v dubnu 30, 2020. Tyto starší protokoly poskytují slabou kryptografii a jsou náchylné k útokům BEAST a POODLE. Aplikace nasazené do zařízení se systémem Android verze 5 nebo vyšší nebo iOS verze 5 nebo vyšší nejsou touto změnou ovlivněny, protože tyto operační systémy podporují TLS 1.2 a klient a server vyjedná nejvyšší vzájemně podporovanou verzi protokolu při připojení.

Doporučujeme zkontrolovat všechny vaše aplikace, které používají Azure Notification Hubs, abyste zajistili, že používají nejpoužitelnější knihovny a balíčky TLS, které podporují TLS 1.2.

## <a name="update-apps"></a>Aktualizace aplikací

Můžete zajistit, aby vaše aplikace pro iOS používaly TLS 1.2 pomocí funkce zabezpečení sítě společnosti Apple s názvem App Transport Security (ATS). Ats nelze použít pro sady SDK starší než iOS 9.0 nebo macOS 10.11 a můžete si přečíst dále z [dokumentace společnosti Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Pro aplikace pro Android, které používají instance SSLSocket, nastavte povolené protokoly na každé instanci SSLSocket, jak je uvedeno v [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabulka na stránce podpory [kompatibility protokolu TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) pomáhá mapovat operační systémy s kompatibilními verzemi TLS.

Další informace naleznete v přehledu [podpory protokolů TLS v systému Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Další kroky

- [Přehled center oznámení](notification-hubs-push-notification-overview.md)