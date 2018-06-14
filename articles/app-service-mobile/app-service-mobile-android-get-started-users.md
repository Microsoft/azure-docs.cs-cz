---
title: Přidání ověřování v systému Android s Mobile Apps | Microsoft Docs
description: Další informace o použití funkce Mobile Apps služby Azure App Service ověřovat uživatele vaší aplikace pro Android prostřednictvím řady různých zprostředkovatelů identity, včetně Google, Facebook, Twitter a společnosti Microsoft.
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
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2018
ms.locfileid: "27595223"
---
# <a name="add-authentication-to-your-android-app"></a>Přidání ověřování do aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Souhrn
V tomto kurzu jste přidání ověřování do seznamu úkolů projektu pro rychlý start v systému Android pomocí zprostředkovatele identity podporované. V tomto kurzu vychází z [Začínáme s Mobile Apps] kurz, který je třeba nejprve provést.

## <a name="register"></a>Registrace aplikace pro ověřování a konfigurace Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do adresy URL pro povolené externí přesměrování

Zabezpečené ověřování vyžaduje, můžete definovat nové schéma adresy URL pro vaši aplikaci. To umožňuje ověřování systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme schématu adresy URL _appname_ v průběhu. Můžete však použít žádné schéma adresy URL, které zvolíte. Musí být jedinečné pro mobilní aplikace. Chcete povolit přesměrování na straně serveru:

1. V [portál Azure], vyberte svoji službu aplikace.

2. Klikněte **ověřování / autorizace** možnost nabídky.

3. V **povoleno externí adres URL pro přesměrování**, zadejte `appname://easyauth.callback`.  _Appname_ v tento řetězec je schéma adresy URL pro mobilní aplikace.  Měl by splňovat specifikaci normální adresu URL pro určitý protokol (používejte písmena a čísla pouze a začněte s písmenem).  Měli byste si poznamenat řetězce, který zvolíte, jako je třeba upravit kód mobilní aplikace s schéma adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

## <a name="permissions"></a>Omezit oprávnění k ověření uživatelé
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V nástroji Android Studio otevřete projekt dokončení kurzu [Začínáme s Mobile Apps]. Z **spustit** nabídky, klikněte na tlačítko **spuštění aplikace**a ověřte, že k neošetřené výjimce s stavový kód 401 (Neautorizováno) se vyvolá po spuštění aplikace.

     Tato výjimka se stane, protože se aplikace pokusí o přístup k back-end jako neověřený uživatel, ale *TodoItem* tabulka nyní vyžaduje ověření.

Potom aktualizujte aplikace k ověření uživatelů před vyžádáním prostředky z back-end mobilní aplikace.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokeny ověřování mezipaměti na straně klienta
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další postup
Teď, když jste dokončili kurz základní ověřování, vezměte v úvahu pokračovat na jednu z následujících kurzů:

* [Přidání nabízených oznámení do vaší aplikace pro Android](app-service-mobile-android-get-started-push.md).
  Naučte se konfigurovat Mobile Apps back-end vašeho používat Azure notification hubs k odesílání nabízených oznámení.
* [Zapnutí offline synchronizace pro svoji aplikaci pro Android](app-service-mobile-android-get-started-offline-data.md).
  Zjistěte, jak přidat podporu offline režimu do aplikace pomocí back-end mobilní aplikace. S offline synchronizací, mohou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidávat a upravovat data&mdash;i v případě, že není žádné síťové připojení.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Začínáme s Mobile Apps]: app-service-mobile-android-get-started.md
[portál Azure]: https://portal.azure.com/
