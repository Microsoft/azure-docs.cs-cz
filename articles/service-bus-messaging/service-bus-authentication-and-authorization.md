---
title: Ověřování a autorizace Azure Service Bus | Microsoft Docs
description: Ověřování aplikací pro Service Bus s ověřováním pomocí sdíleného přístupového podpisu (SAS).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: d5d0ed03c869bd574e4cfaa52ac7b62e8cb7fb98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066857"
---
# <a name="service-bus-authentication-and-authorization"></a>Ověřování a autorizace Service Bus
Existují dva způsoby, jak ověřit a autorizovat přístup k prostředkům Azure Service Bus: Azure Activity Directory (Azure AD) a signatury sdíleného přístupu (SAS). Tento článek obsahuje podrobné informace o použití těchto dvou typů mechanismů zabezpečení. 

## <a name="azure-active-directory"></a>Azure Active Directory
Integrace Azure AD pro prostředky Service Bus poskytuje řízení přístupu na základě role (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům. Řízení přístupu na základě role (RBAC) můžete použít k udělení oprávnění objektu zabezpečení, který může být uživatel, skupina nebo instanční objekt služby. Služba Azure AD ověřuje objekt zabezpečení, aby vrátil token OAuth 2,0. Token se dá použít k autorizaci žádosti o přístup k prostředku Service Bus (frontě, tématu atd.).

Další informace o ověřování ve službě Azure AD najdete v následujících článcích:

- [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
- [Ověření z aplikace](authenticate-application.md)

> [!NOTE]
> [Service Bus REST API](/rest/api/servicebus/) podporuje ověřování OAuth pomocí Azure AD.

> [!IMPORTANT]
> Ověřování uživatelů nebo aplikací pomocí tokenu OAuth 2,0 vráceného službou Azure AD poskytuje vynikající zabezpečení a usnadňuje použití přes sdílené přístupové podpisy (SAS). V případě Azure AD není nutné ukládat tokeny do kódu a ohrozit potenciální ohrožení zabezpečení. Pokud je to možné, doporučujeme používat Azure AD s aplikacemi Azure Service Bus. 

## <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Ověřování SAS](service-bus-sas.md) umožňuje udělit uživateli přístup k prostředkům Service Bus s konkrétními právy. Ověřování SAS v Service Bus zahrnuje konfiguraci kryptografického klíče s přidruženými právy pro prostředek Service Bus. Klienti pak mohou získat přístup k tomuto prostředku předložením tokenu SAS, který se skládá z přistupového identifikátoru URI prostředku a jeho vypršení platnosti podepsaného pomocí konfigurovaného klíče.

Klíče pro SAS můžete nakonfigurovat na Service Bus oboru názvů. Klíč se vztahuje na všechny entity zasílání zpráv v rámci tohoto oboru názvů. Můžete také nakonfigurovat klíče pro Service Bus fronty a témata. SAS je také podporován v [Azure Relay](../azure-relay/relay-authentication-and-authorization.md).

Chcete-li použít SAS, můžete nakonfigurovat objekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) v oboru názvů, ve frontě nebo tématu. Toto pravidlo se skládá z následujících prvků:

* *KeyName*: identifikuje pravidlo.
* *PrimaryKey*: kryptografický klíč, který slouží k podepisování/ověřování tokenů SAS.
* *SecondaryKey*: kryptografický klíč, který slouží k podepisování/ověřování tokenů SAS.
* *Práva*: představuje shromažďování udělených oprávnění k **naslouchání**, **odesílání**nebo **správě** .

Autorizační pravidla konfigurovaná na úrovni oboru názvů můžou udělit přístup ke všem entitám v oboru názvů pro klienty s tokeny podepsanými pomocí odpovídajícího klíče. Pro Service Bus obor názvů, frontu nebo téma můžete nakonfigurovat až 12 těchto autorizačních pravidel. Ve výchozím nastavení se [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy nakonfigurují pro každý obor názvů při prvním zřízení.

Pro přístup k entitě vyžaduje klient token SAS generovaný pomocí konkrétní [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token SAS se vygeneruje pomocí HMAC-SHA256 řetězce prostředku, který se skládá z identifikátoru URI prostředku, ke kterému se přistupuje, a vypršení platnosti kryptografického klíče přidruženého k autorizačnímu pravidlu.

Podpora ověřování SAS pro Service Bus je obsažená v sadě Azure .NET SDK verze 2,0 a novější. SAS zahrnuje podporu pro [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, která přijímají připojovací řetězec jako parametr, zahrnují podporu připojovacích řetězců SAS.

> [!IMPORTANT]
> Pokud používáte Azure Active Directory Access Control (označované také jako Access Control Service nebo ACS) se Service Bus, pamatujte, že podpora této metody je nyní omezená a že byste měli [aplikaci migrovat na používání SAS](service-bus-migrate-acs-sas.md) nebo ověřování pomocí protokolu OAuth 2,0 s Azure AD (doporučeno). Další informace o vyřazení služby ACS najdete v [tomto blogovém příspěvku](/archive/blogs/servicebus/upcoming-changes-to-acs-enabled-namespaces).

## <a name="next-steps"></a>Další kroky
Další informace o ověřování ve službě Azure AD najdete v následujících článcích:

- [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
- [Ověřování z aplikace](authenticate-application.md)

Další informace o ověřování pomocí SAS najdete v následujících článcích:

- [Ověřování pomocí SAS](service-bus-sas.md)