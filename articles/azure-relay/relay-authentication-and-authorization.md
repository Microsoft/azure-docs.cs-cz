---
title: Ověřování a autorizace Azure Relay | Microsoft Docs
description: Tento článek poskytuje přehled ověřování pomocí protokolu SAS (Shared Access Signature) ve službě Azure Relay.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 4b0a5c7a092155a006419eedd170a63abed42bb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87033373"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay ověřování a autorizace

Aplikace se můžou ověřit, aby Azure Relay používali ověřování pomocí sdíleného přístupového podpisu (SAS). Ověřování SAS umožňuje aplikacím ověřování ve službě Azure Relay pomocí přístupového klíče nakonfigurovaného v oboru názvů Relay. Potom můžete tento klíč použít k vygenerování tokenu sdíleného přístupového podpisu, který můžou klienti použít k ověřování ve službě Relay.

## <a name="shared-access-signature-authentication"></a>Ověřování sdíleného přístupového podpisu

[Ověřování SAS](../service-bus-messaging/service-bus-sas.md) umožňuje uživateli udělit přístup k prostředkům Azure Relay s konkrétními právy. Ověřování SAS zahrnuje konfiguraci kryptografického klíče s přidruženými právy k prostředku. Klienti pak mohou získat přístup k tomuto prostředku předložením tokenu SAS, který se skládá z přistupového identifikátoru URI prostředku a jeho vypršení platnosti podepsaného pomocí konfigurovaného klíče.

Klíče pro SAS můžete nakonfigurovat v oboru názvů přenosu. Na rozdíl od Service Bus zasílání zpráv [přenosová Hybrid Connections](relay-hybrid-connections-protocol.md) podporuje neautorizované nebo anonymní odesílatele. Anonymní přístup pro entitu můžete povolit při jejím vytváření, jak je znázorněno na následujícím snímku obrazovky na portálu:

![Dialogové okno s názvem "vytvořit hybridní připojení" má textové pole "název" a zaškrtávací políčko s názvem vyžaduje ověření klienta, které je zaškrtnuté.][0]

Chcete-li použít SAS, můžete nakonfigurovat objekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) v oboru názvů Relay, který se skládá z následujících:

* *KeyName* , který identifikuje pravidlo.
* *PrimaryKey* je kryptografický klíč, který slouží k podepsání a ověření tokenů SAS.
* *SecondaryKey* je kryptografický klíč, který slouží k podepisování nebo ověřování tokenů SAS.
* *Práva* představující shromažďování udělených, odesílaných nebo spravovaných práv.

Autorizační pravidla konfigurovaná na úrovni oboru názvů můžou udělit přístup ke všem připojením přenosu v oboru názvů pro klienty s tokeny podepsanými pomocí odpovídajícího klíče. V oboru názvů Relay lze nakonfigurovat až 12 takových autorizačních pravidel. Ve výchozím nastavení se [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy nakonfigurují pro každý obor názvů při prvním zřízení.

Pro přístup k entitě vyžaduje klient token SAS generovaný pomocí konkrétní [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token SAS se vygeneruje pomocí HMAC-SHA256 řetězce prostředku, který se skládá z identifikátoru URI prostředku, ke kterému se přistupuje, a vypršení platnosti kryptografického klíče přidruženého k autorizačnímu pravidlu.

Podpora ověřování SAS pro Azure Relay je obsažená v sadě Azure .NET SDK verze 2,0 a novější. SAS zahrnuje podporu pro [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, která přijímají připojovací řetězec jako parametr, zahrnují podporu připojovacích řetězců SAS.

## <a name="next-steps"></a>Další kroky

- Další podrobnosti o SAS najdete dál v tématu čtení [Service Bus ověřování pomocí podpisů sdíleného přístupu](../service-bus-messaging/service-bus-sas.md) .
- Podrobné informace o funkci Hybrid Connections najdete v [Průvodci protokolem Hybrid Connections Azure Relay](relay-hybrid-connections-protocol.md) .
- Příslušné informace o ověřování a autorizaci zpráv Service Bus najdete v tématu [Service Bus ověřování a autorizaci](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png