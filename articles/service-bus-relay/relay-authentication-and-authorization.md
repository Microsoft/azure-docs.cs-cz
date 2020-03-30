---
title: Ověřování a autorizace azure relay | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled ověřování sdíleného přístupového podpisu (SAS) se službou Azure Relay.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514574"
---
# <a name="azure-relay-authentication-and-authorization"></a>Ověřování a autorizace Azure Relay

Aplikace se můžou ověřovat ve službě Azure Relay pomocí ověřování pomocí sdíleného přístupového podpisu (SAS). Ověřování SAS umožňuje aplikacím ověřit službu Azure Relay pomocí přístupového klíče nakonfigurovaného v oboru názvů Relay. Tento klíč pak můžete použít ke generování tokenu sdíleného přístupového podpisu, který mohou klienti použít k ověření služby přenosu.

## <a name="shared-access-signature-authentication"></a>Ověřování pomocí sdíleného přístupového podpisu

[Ověřování SAS](../service-bus-messaging/service-bus-sas.md) umožňuje udělit uživateli přístup k prostředkům Azure Relay s konkrétními právy. Ověřování SAS zahrnuje konfiguraci kryptografického klíče s přidruženými právy k prostředku. Klienti pak mohou získat přístup k tomuto prostředku předložením tokenu SAS, který se skládá z přístupu identifikátoru URI prostředku a vypršení platnosti podepsané nakonfigurovaným klíčem.

Klíče pro sas můžete nakonfigurovat v oboru názvů přenosu. Na rozdíl od zasílání zpráv service bus, [přenoshybridní připojení](relay-hybrid-connections-protocol.md) podporuje neoprávněné nebo anonymní odesílatele. Anonymní přístup entity můžete povolit při jeho vytváření, jak je znázorněno na následujícím snímku obrazovky z portálu:

![][0]

Chcete-li použít SAS, můžete nakonfigurovat objekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) v oboru názvů Relay, který se skládá z následujících:

* *KeyName,* který identifikuje pravidlo.
* *PrimaryKey* je kryptografický klíč používaný k podepisování/ověřování tokenů SAS.
* *SecondaryKey* je kryptografický klíč používaný k podepisování/ověřování tokenů SAS.
* *Práva* představující kolekci práv naslouchání, odesílání nebo správu udělených.

Autorizační pravidla nakonfigurovaná na úrovni oboru názvů mohou udělit přístup ke všem přenosovým připojením v oboru názvů klientům s tokeny podepsanými pomocí odpovídajícího klíče. Až 12 takových autorizačních pravidel lze nakonfigurovat v oboru názvů přenosu. Ve výchozím nastavení [sharedaccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy je nakonfigurován pro každý obor názvů při prvním zřízení.

Pro přístup k entitě klient vyžaduje token SAS generovaný pomocí konkrétního [sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token SAS je generován pomocí HMAC-SHA256 řetězce prostředků, který se skládá z identifikátoru URI prostředku, ke kterému je nárokován přístup, a vypršení platnosti s kryptografickým klíčem přidruženým k autorizačnímu pravidlu.

Podpora ověřování SAS pro Azure Relay je součástí sady Azure .NET SDK verze 2.0 a novější. SAS zahrnuje podporu pro [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, která přijímají připojovací řetězec jako parametr, zahrnují podporu připojovacích řetězců SAS.

## <a name="next-steps"></a>Další kroky

- Další podrobnosti o službě SAS [načtete pomocí sdílených přístupových podpisů.](../service-bus-messaging/service-bus-sas.md)
- Podrobné informace o funkci Hybridní připojení [azure relay](relay-hybrid-connections-protocol.md) představují podrobné informace o funkci hybridní připojení.
- Odpovídající informace o ověřování a autorizaci zasílání zpráv služby Service Bus naleznete v tématu [Ověřování a autorizace služby Service Bus](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png