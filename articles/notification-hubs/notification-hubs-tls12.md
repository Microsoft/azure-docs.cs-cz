---
title: Aktualizace Notification Hubs TLS
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
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908524"
---
# <a name="transport-layer-security-tls"></a>TLS (Transport Layer Security)

Aby se zajistila vyšší úroveň zabezpečení, Notification Hubs zakáže podporu TLS verze 1,0 a 1,1 do 30. dubna 2020. Tyto starší protokoly poskytují slabou kryptografii a jsou zranitelné z útoků TOUCHDOWN a POODLE. Aplikace nasazené do zařízení se systémem Android verze 5 nebo vyšší nebo iOS verze 5 nebo vyšší nemají vliv na tuto změnu, protože tyto operační systémy podporují protokol TLS 1,2 a klient a server vyjednávají nejvyšší vzájemně podporovanou verzi protokol při připojení.

Doporučujeme zkontrolovat všechny aplikace, které používají Azure Notification Hubs, a zajistit tak, aby používaly nejvhodnější knihovny a zásobníky TLS, které podporují protokol TLS 1,2.

## <a name="update-apps"></a>Aktualizovat aplikace

Můžete zajistit, aby vaše aplikace pro iOS používaly protokol TLS 1,2 pomocí funkce zabezpečení sítě od společnosti Apple označované jako ATS (App Transport Security). ATS nelze použít pro sady SDK starší než iOS 9,0 nebo macOS 10,11. Další informace o této dokumentaci získáte z [dokumentace od společnosti Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

U aplikací pro Android, které používají instance SSLSocket, nastavte u každé instance SSLSocket Povolené protokoly, jak je uvedeno v [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

Tabulka na stránce podpory [kompatibility protokolu TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) pomáhá mapovat operační systémy s kompatibilními verzemi TLS.

Další informace najdete v tématu Přehled [podpory protokolů TLS ve Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Další kroky

- [Přehled Notification Hubs](notification-hubs-push-notification-overview.md)