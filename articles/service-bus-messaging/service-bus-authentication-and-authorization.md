---
title: Ověřování a autorizace azure service bus | Dokumenty společnosti Microsoft
description: Ověřování aplikací do služby Service Bus pomocí ověřování se sdíleným přístupovým podpisem (SAS).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70013249"
---
# <a name="service-bus-authentication-and-authorization"></a>Ověřování a autorizace Service Bus

Aplikace získají přístup k prostředkům Azure Service Bus pomocí ověřování tokenu sdíleného přístupového podpisu (SAS). S SAS aplikace představují token service bus, který byl podepsán s symetrickým klíčem známý jak vydavatel tokenu a Service Bus (tedy "sdílené") a tento klíč je přímo spojena s pravidlem udělujícím konkrétní přístupová práva, jako je oprávnění přijímat nebo poslouchat nebo odesílat zprávy. Pravidla SAS jsou konfigurována v oboru názvů nebo přímo na entitách, jako je fronta nebo téma, což umožňuje jemně odstupňovanou kontrolu přístupu.

Tokeny SAS mohou být generovány přímo klientem služby Service Bus nebo mohou být generovány některým zprostředkujícím koncovým bodem, se kterým klient komunikuje. Systém může například vyžadovat, aby klient zavolal koncový bod webové služby chráněné autorizací služby Active Directory, aby prokázal svou identitu a přístupová práva k systému, a webová služba pak vrátí příslušný token služby Service Bus. Tento token SAS lze snadno generovat pomocí zprostředkovatele tokenu Service Bus zahrnuté v Azure SDK. 

> [!IMPORTANT]
> Pokud používáte Azure Active Directory Access Control (označované také jako služba řízení přístupu nebo ACS) s Service Bus, všimněte si, že podpora pro tuto metodu je nyní omezená a měli byste migrovat aplikaci používat SAS. Další informace naleznete v [tomto příspěvku blogu](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) a [v tomto článku](service-bus-migrate-acs-sas.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Integrace Azure Active Directory (Azure AD) pro prostředky service bus poskytuje řízení přístupu na základě rolí (RBAC) pro jemně odstupňovanou kontrolu nad přístupem klienta k prostředkům. Pomocí řízení přístupu na základě rolí (RBAC) můžete udělit oprávnění k zaregistrovaný objekt zabezpečení, který může být uživatel, skupina nebo instanční objekt služby aplikace. Objekt zabezpečení je ověřen službou Azure AD, aby vrátil token OAuth 2.0. Token lze použít k autorizaci požadavku na přístup k prostředku služby Service Bus (fronta, téma atd.).

Další informace o ověřování pomocí služby Azure AD najdete v následujících článcích:

- [Ověřování pomocí spravovaných identit](service-bus-managed-service-identity.md)
- [Ověření z aplikace](authenticate-application.md)

> [!IMPORTANT]
> Autorizace uživatelů nebo aplikací pomocí tokenu OAuth 2.0 vráceného službou Azure AD poskytuje vynikající zabezpečení a snadné použití oproti sdíleným přístupovým podpisům (SAS). S Azure AD, není nutné ukládat tokeny ve vašem kódu a riskovat potenciální ohrožení zabezpečení. Doporučujeme používat Azure AD s aplikacemi Azure Service Bus, pokud je to možné. 


## <a name="shared-access-signature"></a>Sdílený přístupový podpis
[Ověřování SAS](service-bus-sas.md) umožňuje udělit uživateli přístup k prostředkům služby Service Bus se specifickými právy. Ověřování SAS v service bus zahrnuje konfiguraci kryptografického klíče s přidruženými právy na prostředek služby Service Bus. Klienti pak mohou získat přístup k tomuto prostředku předložením tokenu SAS, který se skládá z přístupu identifikátoru URI prostředku a vypršení platnosti podepsané nakonfigurovaným klíčem.

Klíče pro SAS můžete nakonfigurovat v oboru názvů service bus. Klíč platí pro všechny entity zasílání zpráv v rámci tohoto oboru názvů. Můžete také nakonfigurovat klíče ve frontách a tématech služby Service Bus. SAS je také podporována na [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Chcete-li použít SAS, můžete nakonfigurovat objekt [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) v oboru názvů, frontě nebo tématu. Toto pravidlo se skládá z následujících prvků:

* *KeyName*: identifikuje pravidlo.
* *PrimaryKey*: kryptografický klíč používaný k podepisování/ověřování tokenů SAS.
* *SecondaryKey*: kryptografický klíč používaný k podepisování/ověřování tokenů SAS.
* *Práva*: představuje kolekci **práv naslouchání**, **odesílání**nebo **správy.**

Autorizační pravidla nakonfigurovaná na úrovni oboru názvů mohou udělit přístup všem entitám v oboru názvů klientům s tokeny podepsanými pomocí odpovídajícího klíče. Můžete nakonfigurovat až 12 takových pravidel autorizace v oboru názvů service bus, frontě nebo tématu. Ve výchozím nastavení [sharedaccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy je nakonfigurován pro každý obor názvů při prvním zřízení.

Pro přístup k entitě klient vyžaduje token SAS generovaný pomocí konkrétního [sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Token SAS je generován pomocí HMAC-SHA256 řetězce prostředků, který se skládá z identifikátoru URI prostředku, ke kterému je nárokován přístup, a vypršení platnosti s kryptografickým klíčem přidruženým k autorizačnímu pravidlu.

Podpora ověřování SAS pro Service Bus je součástí sady Azure .NET SDK verze 2.0 a novější. SAS zahrnuje podporu pro [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, která přijímají připojovací řetězec jako parametr, zahrnují podporu připojovacích řetězců SAS.

## <a name="next-steps"></a>Další kroky

- Další podrobnosti o službě SAS [načtete pomocí sdílených přístupových podpisů.](service-bus-sas.md)
- Jak [migrovat z Azure Active Directory Access Control (ACS) na autorizaci sdíleného přístupového podpisu](service-bus-migrate-acs-sas.md).
- [Změny oborů názvů enabled služby ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Odpovídající informace o ověřování a autorizaci Azure Relay najdete v tématu [Ověřování a autorizace Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md). 

