---
title: Aktualizace pro Azure Notification Hubs iOS 13 | Microsoft Docs
description: Přečtěte si o nejnovějších změnách v Azure Notification Hubs v iOS 13
author: sethmanheim
ms.author: sethm
ms.date: 10/16/2019
ms.topic: article
ms.service: notification-hubs
ms.reviewer: jowargo
ms.lastreviewed: 10/16/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: df8560bec3671a9f05628ee6ed8ea95c31e9b16f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998046"
---
# <a name="azure-notification-hubs-updates-for-ios-13"></a>Aktualizace služby Azure Notification Hubs pro iOS 13

Společnost Apple nedávno provedla některé změny své veřejné nabízené služby. změny se většinou zarovnají s verzemi iOS 13 a Xcode. Tento článek popisuje vliv těchto změn na Azure Notification Hubs.

## <a name="apns-push-payload-changes"></a>Změny datové části APNS push

### <a name="apns-push-type"></a>Typ push APNS

Společnost Apple teď vyžaduje, aby vývojáři identifikovali oznámení jako výstrahu nebo oznámení na pozadí prostřednictvím nové `apns-push-type` hlavičky v rozhraní API služby APN. Podle [Dokumentace společnosti Apple](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns): "hodnota této hlavičky musí přesně odrážet obsah datové části oznámení. Pokud dojde k neshodě, nebo pokud chybí hlavička v požadovaných systémech, může APNs vrátit chybu, zpozdit doručení oznámení nebo ho úplně odstranit. "

Vývojáři musí nyní tuto hlavičku nastavit v aplikacích, které odesílají oznámení prostřednictvím služby Azure Notification Hubs. Z důvodu technického omezení musí zákazníci používat ověřování na základě tokenů pro přihlašovací údaje APNS s požadavky, které obsahují tento atribut. Pokud pro přihlašovací údaje APNS používáte ověřování na základě certifikátů, musíte přepnout na použití ověřování založeného na tokenech.

Následující ukázky kódu ukazují, jak nastavit tento atribut záhlaví v žádostech o oznámení odeslaných prostřednictvím Azure Notification Hubs.

#### <a name="template-notifications---net-sdk"></a>Oznámení šablon – sada .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var tempprop = new Dictionary<string, string> {{"message", "value"}};
var notification = new TemplateNotification(tempprop);
notification.Headers = headers;
await hub.SendNotificationAsync(notification);
```

#### <a name="native-notifications---net-sdk"></a>Nativní oznámení – sada .NET SDK

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

#### <a name="direct-rest-calls"></a>Přímá volání REST

```csharp
var request = new HttpRequestMessage(method, $"<resourceUri>?api-version=2017-04");
request.Headers.Add("Authorization", createToken(resourceUri, KEY_NAME, KEY_VALUE));
request.Headers.Add("ServiceBusNotification-Format", "apple");
request.Headers.Add("apns-push-type", "alert");
```

Pokud vám Azure Notification Hubs detekuje oznámení, které nemá nastavenou hodnotu, bude tento přechod v průběhu tohoto přechodu `apns-push-type` vyvodit z požadavku na oznámení typ push a automaticky nastaví hodnotu. Pamatujte, že je nutné nakonfigurovat Azure Notification Hubs pro použití ověřování založeného na tokenech k nastavení požadované hlavičky; Další informace najdete v tématu [ověřování založené na tokenech (http/2) pro služby APN](./notification-hubs-push-notification-http2-token-authentication.md).

## <a name="apns-priority"></a>Priorita služby APN

Další menší změna, ale ta, která vyžaduje změnu aplikace back-end, která odesílá oznámení, je požadavek na to, aby oznámení na pozadí, která má `apns-priority` záhlaví obsahovat, byla teď nastavená na 5. Řada aplikací nastavuje `apns-priority` hlavičku na 10 (indikuje okamžité doručení), nebo ji nenastaví a získá výchozí hodnotu (což je také 10).

Nastavení této hodnoty na hodnotu 10 již není povoleno pro oznámení na pozadí a je nutné nastavit hodnotu pro každý požadavek. Pokud tato hodnota chybí, Apple nebude doručovat oznámení na pozadí. Například:

```csharp
var hub = NotificationHubClient.CreateFromConnectionString(...);
var headers = new Dictionary<string, string> {{"apns-push-type", "background"}, { "apns-priority", "5" }};
var notification = new AppleNotification("notification text", headers);
await hub.SendNotificationAsync(notification);
```

## <a name="sdk-changes"></a>Změny sady SDK

Pro roky používají vývojáři pro iOS `description` atribut `deviceToken` dat odeslaných do delegáta tokenu push k extrakci tokenu push, který aplikace back-end používá k odesílání oznámení do zařízení. S Xcode 11 se tento `description` atribut změnil na jiný formát. Existující kód, který vývojářům použili pro tento atribut, je teď porušený. Aktualizovali jsme sadu Azure Notification Hubs SDK, aby odpovídala této změně, proto aktualizujte sadu SDK používané vašimi aplikacemi na verzi 2.0.4 nebo novější v [sadě Azure Notification Hubs iOS SDK](https://github.com/Azure/azure-notificationhubs-ios).
