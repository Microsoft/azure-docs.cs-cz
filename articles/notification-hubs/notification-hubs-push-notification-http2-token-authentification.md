---
title: Ověřování založené na tokenech (HTTP/2) pro službu APN v Azure Notification Hubs | Microsoft Docs
description: Toto téma vysvětluje, jak využít nové ověřování tokenu pro APNS.
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
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213116"
---
# <a name="token-based-http2-authentication-for-apns"></a>Ověřování založené na tokenech (HTTP/2) na APNS

## <a name="overview"></a>Přehled

Tento článek podrobně popisuje použití nového protokolu HTTP/2 služby APN s ověřováním na základě tokenů.

Mezi klíčové výhody použití nového protokolu patří:

* Generování tokenu je poměrně bezplatné (ve srovnání s certifikáty).
* Žádná další data vypršení platnosti – máte kontrolu nad svými ověřovacími tokeny a jejich odvolání.
* Datové vytížení teď můžou být až 4 KB.
* Synchronní zpětná vazba
* Pracujete na nejnovějším protokolu Apple – certifikáty pořád používají binární protokol, který je označený pro zastaralost.

Použití tohoto nového mechanismu se dá udělat během několika minut ve dvou krocích:

1. Získání nezbytných informací z portálu Apple Developer Account
2. Konfigurace centra oznámení s novými informacemi

Notification Hubs je teď nastavená tak, aby používala nový ověřovací systém s APNS.

Všimněte si, že pokud jste migrovali z použití přihlašovacích údajů certifikátu pro APNS:

* vlastnosti tokenu přepíšou váš certifikát v našem systému.
* ale vaše aplikace bude nadále přijímat oznámení bez problémů.

## <a name="obtaining-authentication-information-from-apple"></a>Získání ověřovacích informací od společnosti Apple

Pokud chcete povolit ověřování na základě tokenu, budete potřebovat následující vlastnosti z vašeho účtu Apple Developer:

### <a name="key-identifier"></a>Identifikátor klíče

Identifikátor klíče se dá získat ze stránky klíče ve vašem účtu Apple Developer.

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Identifikátor aplikace & název aplikace

Název aplikace je k dispozici prostřednictvím stránky ID aplikací v účtu vývojáře.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

Identifikátor aplikace je k dispozici prostřednictvím stránky s podrobnostmi členství v účtu vývojáře.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Ověřovací token

Ověřovací token se dá stáhnout po vygenerování tokenu pro vaši aplikaci. Podrobnosti o tom, jak vygenerovat tento token, najdete v [dokumentaci pro vývojáře společnosti Apple](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1).

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Konfigurace centra oznámení pro použití ověřování založeného na tokenech

### <a name="configure-via-the-azure-portal"></a>Konfigurace prostřednictvím Azure Portal

Pokud chcete na portálu povolit ověřování na základě tokenu, přihlaste se k Azure Portal a v centru oznámení > Notification Services > na panelu APNS.

Je k dispozici nová vlastnost – *režim ověřování*. Výběr tokenu vám umožní aktualizovat centrum pomocí všech odpovídajících vlastností tokenu.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Zadejte vlastnosti, které jste načetli z účtu Apple Developer.
* Výběr režimu aplikace (produkční nebo izolovaný režim)
* Kliknutím na tlačítko **Uložit** aktualizujte přihlašovací údaje služby APN.

### <a name="configure-via-management-api-rest"></a>Konfigurace přes rozhraní API pro správu (REST)

Pomocí našich [rozhraní API pro správu](https://msdn.microsoft.com/library/azure/dn495827.aspx) můžete aktualizovat centrum oznámení tak, aby používalo ověřování založené na tokenech.
V závislosti na tom, jestli je aplikace, kterou konfigurujete, v izolovaném prostoru nebo v produkční aplikaci (zadané v účtu Apple Developer), použijte jeden z odpovídajících koncových bodů:

* Koncový bod izolovaného prostoru:[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Produkční koncový bod:[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Ověřování založené na tokenech vyžaduje rozhraní API verze: **2017-04 nebo novější**.

Tady je příklad žádosti PUT o aktualizaci centra pomocí ověřování založeného na tokenech:

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

Centrum můžete nakonfigurovat tak, aby používalo ověřování založené na tokenech, a to s využitím [nejnovější klientské sady SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Zde je ukázka kódu ilustrující správné použití:

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

## <a name="reverting-to-using-certificate-based-authentication"></a>Vrácení zpět se změnami pomocí ověřování na základě certifikátu

Kdykoli se můžete vrátit k použití ověřování založeného na certifikátech, a to pomocí předchozí metody a předáním certifikátu namísto vlastností tokenu. Tato akce přepíše dříve uložené přihlašovací údaje.
