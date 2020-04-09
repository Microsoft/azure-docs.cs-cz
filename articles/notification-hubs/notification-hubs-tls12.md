---
title: Aktualizace TLS centra oznámení
description: Přečtěte si o podpoře TLS v Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 4da96df50e961f4291029a37e883fdcf88c6c87f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885748"
---
# <a name="transport-layer-security-tls"></a>Protokol TLS (Transport Layer Security)

Chcete-li zajistit vyšší úroveň zabezpečení, notification hubs zakáže podporu pro TLS verze 1.0 a 1.1 na 31 května 2020 (prodloužena od dubna 30, 2020). Tyto starší protokoly poskytují slabou kryptografii a jsou náchylné k útokům BEAST a POODLE. Aplikace nasazené do zařízení se systémem Android verze 5 nebo vyšší nebo iOS verze 5 nebo vyšší nejsou touto změnou ovlivněny, protože tyto operační systémy podporují Protokol TLS 1.2 a klient a server budou při připojení vyjednat nejvyšší vzájemně podporovanou verzi protokolu.

Doporučujeme zkontrolovat všechny vaše aplikace, které používají Azure Notification Hubs, abyste zajistili, že používají nejpoužitelnější knihovny a balíčky TLS, které podporují TLS 1.2.

## <a name="update-apps"></a>Aktualizace aplikací

Můžete zajistit, aby vaše aplikace pro iOS používaly TLS 1.2 pomocí funkce zabezpečení sítě společnosti Apple s názvem App Transport Security (ATS). Ats nelze použít pro sady SDK starší než iOS 9.0 nebo macOS 10.11 a můžete si přečíst dále z [dokumentace společnosti Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Pro aplikace pro Android, které používají instance SSLSocket, nastavte povolené protokoly na každé instanci SSLSocket, jak je uvedeno v [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabulka na stránce podpory [kompatibility protokolu TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) pomáhá mapovat operační systémy s kompatibilními verzemi TLS.

Další informace naleznete v přehledu [podpory protokolů TLS v systému Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Další kroky

- [Přehled center oznámení](notification-hubs-push-notification-overview.md)