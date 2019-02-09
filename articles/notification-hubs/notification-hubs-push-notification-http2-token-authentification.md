---
title: Ověřování založené na tokenech (HTTP/2) pro služby APN ve službě Azure Notification Hubs | Dokumentace Microsoftu
description: Toto téma vysvětluje, jak využít nový token ověřování pro služby APN
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: d0d181afd9bb8f1d387ce8e33f50a78dae3a11ec
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961427"
---
# <a name="token-based-http2-authentication-for-apns"></a>Ověřování založené na tokenech (HTTP/2) pro služby APN

## <a name="overview"></a>Přehled

Tento článek podrobně popisuje, jak používat nový protokol APNS HTTP/2 pomocí ověřování na základě tokenu.

Klíčové výhody používání nového protokolu zahrnout:

* Generování tokenů je poměrně starosti, (ve srovnání s certifikáty)
* Žádná další data vypršení platnosti – máte pod kontrolou ověřovacích tokenů a jejich odvolání
* Datové části se teď dá až 4 KB
* Synchronní zpětnou vazbu
* Už brzo budete od společnosti Apple nejnovější protokol – certifikáty dál používat binární protokol, který je označen pro odstranění

Pomocí tento nový mechanismus lze provést ve dvou krocích za pár minut:

1. Získejte potřebné informace z portálu Apple Developer účtu
2. Konfigurace centra oznámení s novými informacemi

Modul Notification Hubs je teď nastavené a můžete používat nový systém ověřování s APNS.

Poznámka: Pokud jste migrovali ze pomocí přihlašovacích údajů certifikát služby APN:

* token vlastnosti přepsat vašeho certifikátu v našem systému
* ale aplikace i nadále bez problémů přijímat oznámení.

## <a name="obtaining-authentication-information-from-apple"></a>Získávání informací o ověřování od společnosti Apple

Pokud chcete povolit ověřování pomocí tokenu, musíte z vašeho účtu vývojáře Apple následující vlastnosti:

### <a name="key-identifier"></a>Identifikátor klíče

Identifikátor klíče můžete získat ze stránky "Klíče" v účtu Apple Developer

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identifikátor aplikace & název aplikace

Název aplikace je k dispozici prostřednictvím App ID stránky v účtu pro vývojáře.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Identifikátor aplikace je k dispozici prostřednictvím stránky podrobnosti o členství v účtu pro vývojáře.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Ověřovací token

Ověřovací token si můžete stáhnout po vygenerování tokenu pro vaši aplikaci. Podrobnosti o tom, jak generovat tento token [dokumentace Applu pro vývojáře](http://help.apple.com/xcode/mac/current/#/dev11b059073?sub=dev1eb5dfe65).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurace vašeho centra oznámení, která používá ověřování založené na tokenech

### <a name="configure-via-the-azure-portal"></a>Konfigurace prostřednictvím webu Azure portal

Pokud chcete povolit ověřování pomocí tokenu založené na portálu, přihlaste se k webu Azure portal a přejděte do vašeho centra oznámení > Notification Services > panely APNS.

Existuje nová vlastnost – *režim ověřování*. Vyberte Token umožňuje aktualizovat vaše Centrum relevantní token vlastnosti.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Zadejte vlastnosti, které jste získali z vašeho účtu vývojáře Apple
* Zvolte režim vaší aplikace (produkční nebo izolovaného prostoru)
* Klikněte na tlačítko **Uložit** tlačítko Aktualizovat přihlašovací údaje APNS

### <a name="configure-via-management-api-rest"></a>Konfigurace prostřednictvím rozhraní API (REST) pro správu

Můžete použít naši [rozhraní API pro správu](https://msdn.microsoft.com/library/azure/dn495827.aspx) aktualizovat vaše Centrum oznámení, která používá ověřování založené na tokenech.
V závislosti na tom, zda aplikace, kterou konfigurujete je izolovaný prostor nebo produkčního prostředí (zadané v účtu Apple Developer) použijte jeden z odpovídající koncových bodů:

* Koncový bod izolovaného prostoru: [https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Produkční koncový bod: [https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Ověřování založené na tokenech, musíte jako verzi rozhraní API: **2017-04 nebo novější**.

Tady je příklad požadavku PUT aktualizovat Centrum ověřování pomocí tokenu:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Konfigurace prostřednictvím sady .NET SDK

Můžete nakonfigurovat centrem určený pomocí tokenu ověřování na základě našich [nejnovější Klientská sada SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Tady je ukázka kódu ilustrující správné použití:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Návrat k použití ověřování pomocí certifikátů

Kdykoli můžete vrátit zpět k používání ověřování pomocí certifikátů pomocí libovolné metody předchozí a předáním certifikátu místo token vlastností. Tato akce přepíše dříve uložené přihlašovací údaje.
