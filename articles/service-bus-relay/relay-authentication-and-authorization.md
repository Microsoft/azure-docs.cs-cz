---
title: Azure předávací ověřování a autorizace | Microsoft Docs
description: Přehled sdíleného přístupového podpisu (SAS) ověřování v Azure předávání
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: 86a9cf2c1106180ba5c8c65849042784bfd2afcd
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2018
ms.locfileid: "28018113"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure předávací ověřování a autorizace

Aplikace, můžete ověřovat pomocí sdíleného přístupového podpisu (SAS) ověřování Azure (Relay). SAS ověřování umožňuje aplikacím k ověření služby předávání přes Azure pomocí přístupový klíč nakonfigurovaný na předávání oboru názvů. Pak můžete tento klíč k vygenerování tokenu sdíleného přístupového podpisu, který můžou klienti používat k ověření služby předávání.

## <a name="shared-access-signature-authentication"></a>Ověření pomocí sdíleného přístupového podpisu

[Ověřování SAS](../service-bus-messaging/service-bus-sas.md) vám umožní udělit přístup uživatelů k prostředkům Azure předávání s konkrétní práva. SAS ověřování zahrnuje konfiguraci kryptografického klíče s přidružená práva na prostředku. Klienti mohou získat přístup k danému prostředku pak prezentací token SAS, který se skládá z identifikátoru URI přistupuje prostředku a vypršení platnosti podepsaný pomocí nakonfigurovaný klíč.

Klíče pro SAS můžete nakonfigurovat na předávání názvů. Na rozdíl od zasílání zpráv Service Bus, [předávání hybridní připojení](relay-hybrid-connections-protocol.md) podporuje odesílatelé neoprávněným nebo anonymní. Anonymní přístup pro entitu můžete povolit při vytváření, jak je znázorněno na následujícím snímku z portálu obrazovky:

![][0]

Chcete-li použít SAS, můžete konfigurovat [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt na předávání obor názvů, který se skládá z následujících akcí:

* *KeyName* identifikující pravidlo.
* *PrimaryKey* je kryptografický klíč používaný k přihlášení nebo ověření tokeny SAS.
* *Sekundární klíč* je kryptografický klíč používaný k přihlášení nebo ověření tokeny SAS.
* *Práva* představující kolekci naslouchání, odesílat, nebo spravovat udělena oprávnění.

Autorizační pravidla nakonfigurována na úrovni oboru názvů můžete udělit přístup k předávání připojení v oboru názvů pro klienty s tokenů podepsaných pomocí odpovídající klíče. Až 12 toto povolení pravidla lze nastavit u oboru názvů předávání. Ve výchozím nastavení [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy je nakonfigurován pro každý obor názvů při první je zřízený.

Pro přístup k entity, klient vyžaduje token SAS, který je generována pomocí konkrétní [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS token je generována pomocí SHA256 HMAC řetězec prostředku, který se skládá z identifikátoru URI prostředku, na kterou je požadována přístupu a vypršení platnosti se kryptografický klíč přidružený k pravidlu autorizace.

Podpora ověřování SAS pro předávání přes Azure je zahrnutý ve verzích sady Azure .NET SDK 2.0 nebo novější. Zahrnuje podporu pro SAS [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, které přijímají připojovací řetězec jako parametr zahrnují podporu pro SAS připojovací řetězce.

## <a name="next-steps"></a>Další postup

- Materiály [ověření sběrnice s podpisy sdíleného přístupu](../service-bus-messaging/service-bus-sas.md) další podrobnosti o tokenu SAS.
- Najdete v článku [Azure předávání hybridní připojení protokolu Průvodce](relay-hybrid-connections-protocol.md) podrobné informace o funkcích pro hybridní připojení.
- Odpovídající informace o zasílání zpráv Service Bus ověřování a autorizace najdete v tématu [Service Bus ověřování a autorizace](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png