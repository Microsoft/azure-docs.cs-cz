---
title: Předávací ověřování a autorizace Azure | Dokumentace Microsoftu
description: Přehled ověřování pomocí sdíleného přístupového podpisu (SAS) v Azure Relay
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: fff484f2ee6722083a34383b8960931cb37b8316
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700442"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay ověřování a autorizace

Aplikace lze ověřit pomocí sdíleného přístupového podpisu (SAS) ověřování Azure Relay. SAS ověřování umožňuje aplikacím na ověření ve službě Azure Relay pomocí přístupového klíče nakonfigurovaného na obor názvů služby Relay. Potom můžete tento klíč se vygenerovat token sdíleného přístupového podpisu, který můžou klienti použít k ověření ve službě relay.

## <a name="shared-access-signature-authentication"></a>Ověření pomocí sdíleného přístupového podpisu

[Ověřování SAS](../service-bus-messaging/service-bus-sas.md) vám umožní udělit přístup uživatelů k prostředkům Azure Relay s konkrétní práva. Ověřování SAS zahrnuje konfiguraci kryptografický klíč s přidružená práva na prostředek. Klienti pak získat přístup k danému prostředku nabízí ten samý token SAS, který se skládá z identifikátoru URI v přístupu k prostředku a vypršení platnosti podepsán nakonfigurovaný klíč.

Klíče můžete nakonfigurovat pro SAS na obor názvů služby Relay. Na rozdíl od zasílání zpráv Service Bus, [Relay Hybrid Connections](relay-hybrid-connections-protocol.md) podporuje neoprávněným nebo anonymní odesílatelů. Anonymní přístup pro entitu můžete povolit při vytváření, jak je znázorněno na následujícím snímku z portálu:

![][0]

Pokud chcete použít SAS, můžete nakonfigurovat [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektu na obor názvů služby Relay, která se skládá z následujících akcí:

* *Název klíče* , který identifikuje pravidlo.
* *PrimaryKey* je kryptografický klíč používaný k přihlašování a ověřování tokenů SAS.
* *Sekundární klíč* je kryptografický klíč používaný k přihlašování a ověřování tokenů SAS.
* *Práva* představující kolekci vlastností listenurimode nastavenou na odeslání nebo spravovat udělena oprávnění.

Autorizační pravidla nakonfigurována na úrovni oboru názvů může udělit přístup k přenosového připojení v oboru názvů pro klienty s tokeny, které jsou podepsány pomocí odpovídajícího klíče. Až 12 takové autorizační pravidla můžete konfigurovat na obor názvů služby Relay. Ve výchozím nastavení [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy k je nakonfigurovaný pro každý obor názvů je nejprve zřízena.

Pro přístup k entitě, klient vyžaduje tokenem SAS vygenerovaným pomocí konkrétní [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS token je generována pomocí HMAC SHA256 řetězec prostředku, který se skládá z identifikátoru URI prostředků, ke kterému je požadována přístup a vypršení platnosti kryptografickým klíčem spojené s tímto pravidlem autorizace.

Podpora ověřování SAS pro Azure Relay je součástí sady Azure .NET SDK verze 2.0 nebo novější. Zahrnuje podporu pro SAS [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, které přijímají jako parametr řetězec připojení zahrnují podporu pro funkci připojovací řetězce SAS.

## <a name="next-steps"></a>Další postup

- Pokračujte ve čtení [ověřování služby Service Bus se sdílenými přístupovými podpisy](../service-bus-messaging/service-bus-sas.md) podrobné informace o tokenu SAS.
- Zobrazit [Průvodce protokolem Azure Relay Hybrid Connections](relay-hybrid-connections-protocol.md) podrobné informace o funkci hybridní připojení.
- Odpovídající informace o zasílání zpráv Service Bus ověřování a autorizaci, naleznete v tématu [ověřování a autorizace Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png