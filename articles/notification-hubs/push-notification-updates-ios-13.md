---
title: Aktualizace Centra oznámení Azure iOS 13 | Dokumenty společnosti Microsoft
description: Informace o průlomových změnách iOS 13 v Centru oznámení Azure
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 697e8ba9c9f27e8d5644e3a78950ff006290efe7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228141"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Aktualizace Centra oznámení Azure pro iOS 13

Apple nedávno provedl některé změny ve své veřejné push službě; změny většinou v souladu s verzemi iOS 13 a Xcode. Tento článek popisuje dopad těchto změn na Centra oznámení Azure.

## <a name="apns-push-payload-changes"></a>Změny užitečného zatížení nabízená vypočtení APNS

### <a name="apns-push-type"></a>Typ nabízení APNS

Apple nyní vyžaduje, aby vývojáři identifikovali oznámení jako `apns-push-type` upozornění nebo oznámení na pozadí prostřednictvím nové hlavičky v rozhraní APNS API. Podle [dokumentace společnosti Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "Hodnota této hlavičky musí přesně odrážet obsah datové části oznámení. Pokud dojde k neshodě nebo pokud záhlaví chybí v požadovaných systémech, mohou kódy APN vrátit chybu, zpozdit doručení oznámení nebo jej úplně vynechat."

Vývojáři teď musí nastavit tuto hlavičku v aplikacích, které odesílají oznámení prostřednictvím Centra oznámení Azure. Z důvodu technického omezení musí zákazníci používat ověřování na základě tokenů pro pověření APNS s požadavky, které obsahují tento atribut. Pokud pro pověření APNS používáte ověřování na základě certifikátu, musíte přepnout na použití ověřování založeného na tokenech.

Následující ukázky kódu ukazují, jak nastavit tento atribut záhlaví v požadavcích na oznámení odeslaných prostřednictvím centra oznámení Azure.

#### <a name="template-notifications---net-sdk"></a>Oznámení šablon - sada .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Nativní oznámení - sada .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Přímé volání REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Abychom vám pomohli během tohoto přechodu, když Azure Notification Hubs zjistí oznámení, které nemá nastavenou, `apns-push-type` služba odvodí typ push z žádosti o oznámení a automaticky nastaví hodnotu. Nezapomeňte, že je nutné nakonfigurovat Centra oznámení Azure tak, aby používala ověřování založené na tokenech k nastavení požadované hlavičky. Další informace naleznete [v tématu Ověřování na základě tokenu (HTTP/2) pro apns](notification-hubs-push-notification-http2-token-authentification.md).

## <a name="apns-priority"></a>Priorita APNS

Další menší změna, ale ten, který vyžaduje změnu back-endové aplikace, která `apns-priority` odesílá oznámení, je požadavek, že pro oznámení na pozadí záhlaví musí být nyní nastavena na 5. Mnoho aplikací `apns-priority` nastaví záhlaví na 10 (označující okamžité doručení) nebo ji nenastaví a nezískáte výchozí hodnotu (což je také 10).

Nastavení této hodnoty na 10 již není povoleno pro oznámení na pozadí a je nutné nastavit hodnotu pro každý požadavek. Společnost Apple nebude doručovat oznámení na pozadí, pokud tato hodnota chybí. Například:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Změny sady SDK

Vývojáři iOS používali `description` atribut `deviceToken` dat odeslaných delegátovi tokenu nabízených oznámení k extrahování nabízeného tokenu, který back-endová aplikace používá k odesílání oznámení do zařízení. S Xcode 11 `description` se tento atribut změnil na jiný formát. Existující kód, který vývojáři pro tento atribut použili, je nyní přerušený. Aktualizovali jsme sdk centra oznámení Azure tak, aby vyhovovala této změně, proto aktualizujte sadu SDK používanou vašimi aplikacemi na verzi 2.0.4 nebo novější sady [Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
