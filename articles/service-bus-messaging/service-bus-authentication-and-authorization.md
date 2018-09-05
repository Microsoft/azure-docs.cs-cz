---
title: Azure Service Bus ověřování a autorizace | Dokumentace Microsoftu
description: Ověření aplikace ke službě Service Bus pomocí sdíleného přístupového podpisu (SAS) ověřování.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: spelluru
ms.openlocfilehash: e98d980747edfb9987430a635ff3118cdd455828
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702309"
---
# <a name="service-bus-authentication-and-authorization"></a>Ověřování a autorizace Service Bus

Aplikace získáte přístup k prostředkům Azure Service Bus pomocí ověřování pomocí tokenu sdíleného přístupového podpisu (SAS). Pomocí SAS, aplikace k dispozici token ke službě Service Bus, která byla podepsána s symetrický klíč známý obojí, aby vydavatel tokenu a Service Bus (tedy "sdílené") a tento klíč je přímo spojené s pravidlem konkrétní přístupová práva, jako jsou oprávnění k poskytnutí Zobrazit/naslouchání nebo odesílání zpráv. SAS pravidla jsou že buď nakonfigurovaná v oboru názvů nebo přímo u entit, jako jsou fronty nebo tématu, umožňující řízení přístupu potřebujete jemněji odstupňované.

Tokeny SAS může být generována buď klientem služby Service Bus přímo, nebo mohou být generovány některé zprostředkující token vystavení koncového bodu, se kterým klient komunikuje. Systém například může vyžadovat klientovi umožní zavolat Active Directory autorizace chráněné koncový bod webové služby k prokázání své identity a systému přístupová práva a webová služba poté vrátí odpovídající služby Service Bus token. Tento token SAS snadno vygenerujete pomocí poskytovatel tokenů Service Bus součástí sady Azure SDK. 

> [!IMPORTANT]
> Pokud používáte Azure Active Directory Access Control (označované také jako Access Control Service nebo ACS) pomocí služby Service Bus, mějte na paměti, že podpora pro tuto metodu je teď omezená a vy byste migrovat aplikace pomocí SAS. Další informace najdete v tématu [tento příspěvek na blogu](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/) a [v tomto článku](service-bus-migrate-acs-sas.md).

## <a name="shared-access-signature-authentication"></a>Ověření pomocí sdíleného přístupového podpisu

[Ověřování SAS](service-bus-sas.md) vám umožní udělit přístup uživatelů k prostředkům služby Service Bus se konkrétní práva. SAS ověřování ve službě Service Bus zahrnuje konfiguraci kryptografický klíč s přidružená práva na prostředek služby Service Bus. Klienti pak získat přístup k danému prostředku nabízí ten samý token SAS, který se skládá z identifikátoru URI v přístupu k prostředku a vypršení platnosti podepsán nakonfigurovaný klíč.

Klíče můžete nakonfigurovat pro SAS oboru názvů Service Bus. Klíč se vztahuje na všechny entity zasílání zpráv v daném oboru názvů. Klíče můžete nakonfigurovat také u front a témat Service Bus. SAS je také podporována na [Azure Relay](../service-bus-relay/relay-authentication-and-authorization.md).

Pokud chcete použít SAS, můžete nakonfigurovat [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objekt v oboru názvů, frontu nebo téma. Toto pravidlo se skládá z následujících elementů:

* *Název klíče*: identifikuje pravidlo.
* *PrimaryKey*: kryptografický klíč používaný k přihlašování a ověřování tokenů SAS.
* *Sekundární klíč*: kryptografický klíč používaný k přihlašování a ověřování tokenů SAS.
* *Práva*: představuje kolekci **naslouchání**, **odeslat**, nebo **spravovat** udělena oprávnění.

Autorizační pravidla nakonfigurována na úrovni oboru názvů můžete udělit přístup ke všem entitám v oboru názvů pro klienty s tokeny, které jsou podepsány pomocí odpovídajícího klíče. Až 12 takové autorizační pravidla můžete nakonfigurovat na obor názvů služby Service Bus, fronta nebo téma. Ve výchozím nastavení [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) se všemi právy k je nakonfigurovaný pro každý obor názvů je nejprve zřízena.

Pro přístup k entitě, klient vyžaduje tokenem SAS vygenerovaným pomocí konkrétní [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). SAS token je generována pomocí HMAC SHA256 řetězec prostředku, který se skládá z identifikátoru URI prostředků, ke kterému je požadována přístup a vypršení platnosti kryptografickým klíčem spojené s tímto pravidlem autorizace.

Podpora ověřování SAS služby Service Bus je součástí sady Azure .NET SDK verze 2.0 nebo novější. Zahrnuje podporu pro SAS [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Všechna rozhraní API, které přijímají jako parametr řetězec připojení zahrnují podporu pro funkci připojovací řetězce SAS.

## <a name="next-steps"></a>Další postup

- Pokračujte ve čtení [ověřování služby Service Bus se sdílenými přístupovými podpisy](service-bus-sas.md) podrobné informace o tokenu SAS.
- Jak [migrovat z Azure Active Directory Access Control (ACS) k povolení sdíleného přístupového podpisu](service-bus-migrate-acs-sas.md).
- [Obory názvů změny k povoleným ACS](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/).
- Odpovídající informace o Azure Relay ověřování a autorizaci, naleznete v tématu [Azure Relay ověřování a autorizace](../service-bus-relay/relay-authentication-and-authorization.md). 

