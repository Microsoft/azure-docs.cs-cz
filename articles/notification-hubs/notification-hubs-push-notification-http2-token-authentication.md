---
title: Ověřování založené na tokenech (HTTP/2) pro službu APN v Azure Notification Hubs | Microsoft Docs
description: Naučte se používat nové ověřování tokenu pro APNS.
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 4f8de6389a04448579672b84e91f0bb4dd0f4ce2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460470"
---
# <a name="token-based-http2-authentication-for-apns"></a>Ověřování založené na tokenech (HTTP/2) na APNS

## <a name="overview"></a>Přehled

Tento článek vysvětluje, jak používat nový protokol HTTP/2 služby APN s ověřováním na základě tokenu.

Mezi klíčové výhody použití nového protokolu patří:

* Generování tokenu je poměrně jednoduché (ve srovnání s certifikáty).
* Žádná další data vypršení platnosti – máte kontrolu nad svými ověřovacími tokeny a jejich odvolání.
* Datové vytížení teď můžou být až 4 KB.
* Synchronní zpětná vazba
* Pracujete na nejnovějším protokolu Apple – certifikáty pořád používají binární protokol, který je označený pro zastaralost.

Použití tohoto nového mechanismu se dá provést ve dvou krocích:

* Získejte potřebné informace z portálu Apple Developer Account Portal.
* Nakonfigurujte centrum oznámení o nové informace.

Notification Hubs je teď nastavená tak, aby používala nový ověřovací systém s APNS.

Počítejte s tím, že pokud jste migrovali z používání přihlašovacích údajů certifikátu pro službu APN, vlastnosti tokenu přepíšou certifikát v našem systému, ale aplikace bude nadále přijímat oznámení bez problémů.

## <a name="obtaining-authentication-information-from-apple"></a>Získání ověřovacích informací od společnosti Apple

Pokud chcete povolit ověřování na základě tokenu, budete potřebovat následující vlastnosti z vašeho účtu Apple Developer:

### <a name="key-identifier"></a>Identifikátor klíče

Identifikátor klíče se dá získat ze stránky **klíče** v části **certifikáty, identifikátory & profily** ve vašem účtu Apple Developer:

![Certifikáty](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![Identifikátory](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Identifikátor aplikace a název aplikace

Název a identifikátor aplikace jsou také k dispozici na stránce **certifikáty, identifikátory & profily** v účtu vývojáře:

![Certifikáty a ID](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Konfigurace prostřednictvím sady .NET SDK nebo Azure Portal

Centrum můžete nakonfigurovat tak, aby používalo ověřování založené na tokenech pomocí [nejnovější klientské sady SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)nebo v Azure Portal. Pokud chcete na portálu povolit ověřování na základě tokenu, přihlaste se k Azure Portal a na panelu Nastavení centra oznámení **> Apple (APNs)** . Vyberte **token** z vlastnosti **režim ověřování** a aktualizujte své centrum se všemi odpovídajícími vlastnostmi tokenu.

![Konfigurace tokenu](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Zadejte vlastnosti, které jste načetli z účtu Apple Developer.
* Vyberte režim aplikace (**produkční** nebo **izolovaný prostor**).
* Kliknutím na tlačítko **Uložit** aktualizujte přihlašovací údaje služby APN.

Přihlašovací údaje založené na tokenech se skládají z následujících polí:

* **ID klíče**: identifikátor privátního klíče vygenerovaného na portálu pro vývojáře Apple; například `2USFGKSKLT` .
* **ID týmu**: taky se označuje jako předpona nebo předpona aplikace. Toto je identifikátor organizace na portálu pro vývojáře Apple. například `S4V3D7CHJR` .
* **ID sady**: označuje se taky jako ID aplikace. Toto je identifikátor sady prostředků pro aplikaci. například `com.example.myapp` . Všimněte si, že pro jednu aplikaci můžete použít jenom jeden klíč. Tato hodnota se mapuje na `apns-topic` hlavičku HTTP při odesílání oznámení a používá se k zacílení na konkrétní aplikaci. Hodnotu explicitně nemůžete nastavit `apns-topic` .
* **Token**: taky se označuje jako klíč nebo privátní klíč. To se získá ze souboru. P8 vygenerovaného na portálu pro vývojáře Apple. Klíč musí mít povolený APN (který je při generování klíče vybraný na portálu pro vývojáře Apple). Hodnota musí mít záhlaví nebo zápatí PEM z něj odstraněna, až ji dodáte na portál NH nebo rozhraní API.
* **Koncový bod**: Toto je přepínač v okně Notification Hubsového portálu a pole řetězce v rozhraní API. Platné hodnoty jsou `https://api.development.push.apple.com:443/3/device` nebo `https://api.sandbox.push.apple.com:443/3/device` . Notification Hubs používá tuto hodnotu pro prostředí produkčního prostředí nebo izolovaného prostoru (sandbox) pro odesílání oznámení. To se musí shodovat s `aps-environment` oprávněním v aplikaci, jinak se vygenerované tokeny zařízení APNs neshodují s prostředím a oznámení se nepodaří odeslat.

Zde je ukázka kódu ilustrující správné použití:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE&quot;;
string keyId = &quot;YOUR KEY ID HERE&quot;;
string appName = &quot;YOUR APP NAME HERE&quot;;
string appId = &quot;YOUR APP ID HERE&quot;;
NotificationHubDescription desc = new NotificationHubDescription(&quot;PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Další kroky

* [Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md)
* [Konfigurace centra oznámení v Azure Portal](create-notification-hub-portal.md)
