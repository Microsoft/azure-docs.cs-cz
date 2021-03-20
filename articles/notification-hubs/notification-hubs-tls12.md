---
title: Aktualizace Notification Hubs TLS
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
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87084241"
---
# <a name="transport-layer-security-tls"></a>Protokol TLS (Transport Layer Security)

Aby se zajistila vyšší úroveň zabezpečení, Notification Hubs zakáže podporu TLS verze 1,0 a 1,1 **31. prosince 2020** (rozšířené od 30. dubna 2020). Tyto starší protokoly poskytují slabou kryptografii a jsou zranitelné z útoků TOUCHDOWN a POODLE. Aplikace nasazené na zařízení s Androidem verze 5 nebo novějším nebo iOS verze 5 nebo vyšší nejsou touto změnou ovlivněné, protože tyto operační systémy podporují protokol TLS 1,2 a klient a server vyjednávají nejvyšší vzájemně podporovanou verzi protokolu při připojení.

Doporučujeme zkontrolovat všechny aplikace, které používají Azure Notification Hubs, a zajistit tak, aby používaly nejvhodnější knihovny a zásobníky TLS, které podporují protokol TLS 1,2.

## <a name="update-apps"></a>Aktualizace aplikací

Můžete zajistit, aby vaše aplikace pro iOS používaly protokol TLS 1,2 pomocí funkce zabezpečení sítě od společnosti Apple označované jako ATS (App Transport Security). ATS nelze použít pro sady SDK starší než iOS 9,0 nebo macOS 10,11. Další informace o této dokumentaci získáte z [dokumentace od společnosti Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

U aplikací pro Android, které používají instance SSLSocket, nastavte u každé instance SSLSocket Povolené protokoly, jak je uvedeno v [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabulka na stránce podpory [kompatibility protokolu TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) pomáhá mapovat operační systémy s kompatibilními verzemi TLS.

Další informace najdete v tématu Přehled [podpory protokolů TLS ve Windows](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Další kroky

- [Přehled Notification Hubs](notification-hubs-push-notification-overview.md)
