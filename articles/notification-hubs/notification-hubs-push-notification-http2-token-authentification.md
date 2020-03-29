---
title: Ověřování na základě tokenu (HTTP/2) pro APNS v centru oznámení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat nové ověřování tokenu pro apns.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263808"
---
# <a name="token-based-http2-authentication-for-apns"></a>Ověřování založené na tokenech (HTTP/2) pro apns

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak používat nový protokol HTTP/2 APNS s ověřováním na základě tokenu.

Mezi hlavní výhody použití nového protokolu patří:

* Generování tokenu je poměrně jednoduché (ve srovnání s certifikáty)
* Žádné další data vypršení platnosti – máte kontrolu nad vašimi ověřovacími tokeny a jejich odvoláním
* Užitečné zatížení může být nyní až 4 KB
* Synchronní zpětná vazba
* Nacházíte se na nejnovějším protokolu společnosti Apple – certifikáty stále používají binární protokol, který je označen pro vyřazení

Pomocí tohoto nového mechanismu lze provést ve dvou krocích:

* Potřebné informace získáte z portálu účtu Apple Developer.
* Nakonfigurujte centrum oznámení s novými informacemi.

Centra oznámení jsou nyní nastavena tak, aby používala nový ověřovací systém s protokolem APNS.

Všimněte si, že pokud jste migrovali z použití pověření certifikátu pro APNS, vlastnosti tokenu přepsat certifikát v našem systému, ale vaše aplikace i nadále přijímat oznámení bez problémů.

## <a name="obtaining-authentication-information-from-apple"></a>Získání ověřovacích informací od společnosti Apple

K povolení ověřování pomocí tokenů potřebujete z účtu Apple Developer následující vlastnosti:

### <a name="key-identifier"></a>Identifikátor klíče

Identifikátor klíče lze získat na stránce **Klíče** v části **Certifikáty, identifikátory & profily**ve vašem účtu Apple Developer:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identifikátor aplikace a název aplikace

Název a identifikátor aplikace jsou také k dispozici na stránce **Certifikáty, Identifikátory & profily** v účtu vývojáře:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurace prostřednictvím sady .NET SDK nebo portálu Azure

Centrum můžete nakonfigurovat tak, aby používalo ověřování na základě tokenů pomocí naší [nejnovější sady SDK klienta](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)nebo na webu Azure Portal. Pokud chcete povolit ověřování na základě tokenů na portálu, přihlaste se na portál Azure portal a přejděte na panel **Nastavení > Apple (APNS)** v centru oznámení. Vyberte **token** z vlastnosti **Režim ověřování** a aktualizujte rozbočovač se všemi příslušnými vlastnostmi tokenu.

![Konfigurace tokenu](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Zadejte vlastnosti, které jste získali z účtu Apple Developer.
* Zvolte režim aplikace **(Výroba** nebo **Pískoviště).**
* Klepnutím na tlačítko **Uložit** aktualizujte přihlašovací údaje apns.

Pověření založená na tokenech se skládají z následujících polí:

* **ID klíče**: Identifikátor soukromého klíče generovaného na portálu Pro vývojáře Apple; například `2USFGKSKLT`.
* **ID týmu**: Také se nazývá "Prefix" nebo "Předpona aplikace." Toto je identifikátor organizace na portálu Pro vývojáře Apple; například `S4V3D7CHJR`.
* **ID balíčku**: Také se nazývá "ID aplikace". Toto je identifikátor balíčku pro aplikaci; například `com.microsoft.nhubsample2019`. Všimněte si, že můžete použít jeden klíč pro mnoho aplikací. Tato hodnota se `apns-topic` mapuje na hlavičku PROTOKOLU HTTP při odesílání oznámení a slouží k cílení na konkrétní aplikaci.
* **Token**: Také se nazývá "Klíč" nebo "Soukromý klíč" . To se získá ze souboru .p8 generovaného na portálu Apple Developer. Klíč musí mít povolen apns (který je vybrán na portálu Apple Developer při generování klíče). Hodnota musí mít záhlaví PEM nebo zápatí odstraněny z něj při dodání nh portálu / rozhraní API.
* **Koncový bod**: Toto je přepínač v okně portálu Centra oznámení a pole řetězce v rozhraní API. Platné hodnoty `https://api.push.apple.com` `https://api.sandbox.push.apple.com`jsou nebo . Centra oznámení používá tuto hodnotu pro produkční prostředí nebo prostředí izolovaného prostoru pro odesílání oznámení. To musí `aps-environment` odpovídat nároku v aplikaci, jinak generované tokeny zařízení APNS neodpovídají prostředí a oznámení se nepodaří odeslat.

Tady je ukázka kódu ilustrující správné použití:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md)
* [Konfigurace centra oznámení na webu Azure Portal](create-notification-hub-portal.md)
