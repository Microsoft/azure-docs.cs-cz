---
title: Přidání ověřování v systému Android s funkcí Mobile Apps | Dokumentace Microsoftu
description: Informace o používání funkce Mobile Apps služby Azure App Service k ověřování uživatelů vaší aplikace pro Android prostřednictvím různých poskytovatelů identit, včetně Google, Facebook, Twitter a Microsoft.
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 11/16/2017
ms.author: crdun
ms.openlocfilehash: 4ee71e00807fcfe698a7e965979434f338f5b870
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38595524"
---
# <a name="add-authentication-to-your-android-app"></a>Přidání ověřování do aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte ověřování do seznamu úkolů projektu v systému Android pomocí zprostředkovatele podporovanou identitu. Tento kurz je založený na [Začínáme s Mobile Apps] kurzu, který je třeba nejprve provést.

## <a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externího přesměrování

Zabezpečené ověřování, musíte definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schéma adresy URL _appname_ v průběhu. Můžete ale použít jakékoli schéma adresy URL, kterou zvolíte. Musí být jedinečné pro vaši mobilní aplikaci. Pokud chcete povolit přesměrování na straně serveru:

1. V [Azure Portal], vyberte službu App Service.

2. Klikněte na tlačítko **ověřování / autorizace** nabídky.

3. V **povolené externí adresy URL pro přesměrování**, zadejte `appname://easyauth.callback`.  _Appname_ v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měla by odpovídat specifikaci normální adresu URL pro určitý protokol (použití písmena a čísla jenom a začíná písmenem).  By měl poznamenejte řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V nástroji Android Studio, otevřete projekt, dokončení kurzu [Začínáme s Mobile Apps]. Z **spustit** nabídky, klikněte na tlačítko **spuštění aplikace**a ověřte, že po spuštění aplikace je vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno).

     Tato výjimka se stane, protože se aplikace pokusí o přístup k back-endu jako neověřený uživatel, ale *TodoItem* tabulka nyní vyžaduje ověřování.

Dále aktualizujete aplikaci k ověření uživatelů před požadováním prostředky z back-endu Mobile Apps.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokeny ověřování mezipaměti na straně klienta
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili kurz základní ověřování, vezměte v úvahu pokračováním jednu z následujících kurzů:

* [Přidání nabízených oznámení do aplikace pro Android](app-service-mobile-android-get-started-push.md).
  Zjistěte, jak nakonfigurovat vaší mobilní aplikace back-endu pomocí Azure notification hubs k odesílání nabízených oznámení.
* [Povolení offline synchronizace u aplikace pro Android](app-service-mobile-android-get-started-offline-data.md).
  Zjistěte, jak přidat do aplikace podporu offline režimu pomocí back-end Mobile Apps. Offline synchronizace, mohou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není žádné síťové připojení.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Začínáme s Mobile Apps]: app-service-mobile-android-get-started.md
[Azure Portal]: https://portal.azure.com/
