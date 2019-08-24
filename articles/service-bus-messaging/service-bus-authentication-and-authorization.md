---
title: Ověřování a autorizace Azure Service Bus | Microsoft Docs
description: Ověřování aplikací pro Service Bus s ověřováním pomocí sdíleného přístupového podpisu (SAS).
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/22/2019
ms.author: aschhab
ms.openlocfilehash: 7234e33c04e742c77630f8d87481c7831fb00bf2
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70013249"
---
# <a name="service-bus-authentication-and-authorization"></a>Ověřování a autorizace Service Bus

Aplikace získávají přístup k Azure Service Bus prostředkům pomocí ověřování pomocí tokenu sdíleného přístupového podpisu (SAS). V případě SAS aplikace prezentují token, který Service Bus podepsaný symetrickým klíčem, který je známý jak pro vystavitele tokenu, tak Service Bus (tedy "Shared") a tento klíč je přímo přidružený k pravidlu, které uděluje specifická přístupová práva, jako je oprávnění k přijímání, naslouchat nebo posílání zpráv. Pravidla SAS jsou nakonfigurovaná buď v oboru názvů, nebo přímo na entitách, jako je například fronta nebo téma, což umožňuje jemně odstupňované řízení přístupu.

Tokeny SAS mohou být buď generovány přímo klientem Service Bus, nebo mohou být vygenerovány pomocí nějakého koncového bodu vydávajícího zprostředkujícího tokenu, se kterým klient komunikuje. Systém může například vyžadovat, aby klient zavolal koncový bod webové služby Active Directory Authorization Protected, aby prokáže jeho identitu a přístupová práva k systému, a webová služba pak vrátí příslušný token Service Bus. Tento token SAS se dá snadno vygenerovat pomocí poskytovatele Service Busho tokenu, který je zahrnutý v sadě Azure SDK. 

> [!IMPORTANT]
> Pokud používáte Azure Active Directory Access Control (označované také jako Access Control Service nebo ACS) se Service Bus, pamatujte, že podpora této metody je teď omezená a že byste měli svoji aplikaci migrovat na použití SAS. Další informace najdete v [tomto blogovém příspěvku](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) a v [tomto článku](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Integrace služby Azure Active Directory (Azure AD) pro prostředky Service Bus poskytuje řízení přístupu na základě role (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům. Řízení přístupu na základě role (RBAC) můžete použít k udělení oprávnění objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt. Služba Azure AD ověřuje objekt zabezpečení, aby vrátil token OAuth 2,0. Token se dá použít k autorizaci žádosti o přístup k prostředku Service Bus (frontě, tématu atd.).

Další informace o ověřování ve službě Azure AD najdete v následujících článcích:

- [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
- [Ověřování z aplikace](authenticate-application.md)

> [!IMPORTANT]
> Ověřování uživatelů nebo aplikací pomocí tokenu OAuth 2,0 vráceného službou Azure AD poskytuje vynikající zabezpečení a usnadňuje použití přes sdílené přístupové podpisy (SAS). V případě Azure AD není nutné ukládat tokeny do kódu a ohrozit potenciální ohrožení zabezpečení. Pokud je to možné, doporučujeme používat Azure AD s aplikacemi Azure Service Bus. 


## <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Ověřování SAS](service-bus-sas.md) umožňuje udělit uživateli přístup k prostředkům Service Bus s konkrétními právy. Ověřování SAS v Service Bus zahrnuje konfiguraci kryptografického klíče s přidruženými právy pro prostředek Service Bus. Klienti pak mohou získat přístup k tomuto prostředku předložením tokenu SAS, který se skládá z přistupového identifikátoru URI prostředku a jeho vypršení platnosti podepsaného pomocí konfigurovaného klíče.

Klíče pro SAS můžete nakonfigurovat na Service Bus oboru názvů. Klíč se vztahuje na všechny entity zasílání zpráv v rámci tohoto oboru názvů. Můžete také nakonfigurovat klíče pro Service Bus fronty a témata. SAS je také podporován v [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Chcete-li použít SAS, můžete nakonfigurovat objekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) v oboru názvů, ve frontě nebo tématu. Toto pravidlo se skládá z následujících prvků:

* *KeyName*: identifikuje pravidlo.
* *PrimaryKey*: kryptografický klíč, který slouží k podepisování/ověřování tokenů SAS.
* *SecondaryKey*: kryptografický klíč, který slouží k podepisování/ověřování tokenů SAS.
* *Práva*: představuje shromažďování udělenýchoprávnění k naslouchání, **odesílání**nebo **správě** .

Autorizační pravidla konfigurovaná na úrovni oboru názvů můžou udělit přístup ke všem entitám v oboru názvů pro klienty s tokeny podepsanými pomocí odpovídajícího klíče. Pro Service Bus obor názvů, frontu nebo téma můžete nakonfigurovat až 12 těchto autorizačních pravidel. Ve výchozím nastavení se [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy nakonfigurují pro každý obor názvů při prvním zřízení.

Pro přístup k entitě vyžaduje klient token SAS generovaný pomocí konkrétní [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token SAS se vygeneruje pomocí HMAC-SHA256 řetězce prostředku, který se skládá z identifikátoru URI prostředku, ke kterému se přistupuje, a vypršení platnosti kryptografického klíče přidruženého k autorizačnímu pravidlu.

Podpora ověřování SAS pro Service Bus je obsažená v sadě Azure .NET SDK verze 2,0 a novější. SAS zahrnuje podporu pro [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, která přijímají připojovací řetězec jako parametr, zahrnují podporu připojovacích řetězců SAS.

## <a name="next-steps"></a>Další kroky

- Další podrobnosti o SAS najdete dál v tématu čtení [Service Bus ověřování pomocí podpisů sdíleného přístupu](service-bus-sas.md) .
- Jak [migrovat z Azure Active Directory Access Control (ACS) na autorizaci sdíleného přístupového podpisu](service-bus-migrate-acs-sas.md).
- [Změny v oborech názvů s povoleným ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Příslušné informace o Azure Relay ověřování a autorizaci najdete v tématu [ověřování Azure Relay a autorizaci](../service-bus-relay/relay-authentication-and-authorization.md). 

