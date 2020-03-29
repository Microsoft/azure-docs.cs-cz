---
title: Přidání ověřování v systému Android
description: Přečtěte si, jak pomocí služby Azure App Service ověřovat uživatele aplikace pro Android pomocí poskytovatelů identit, jako jsou Google, Facebook, Twitter a Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461636"
---
# <a name="add-authentication-to-your-android-app"></a>Přidání ověřování do aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte ověřování do projektu rychlého startu todolista v systému Android pomocí podporovaného zprostředkovatele identity. Tento kurz je založen na kurzu [Začínáme s mobilními aplikacemi,] který musíte dokončit jako první.

## <a name="register-your-app-for-authentication-and-configure-azure-app-service"></a><a name="register"></a>Registrace aplikace pro ověřování a konfigurace služby Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a><a name="redirecturl"></a>Přidání aplikace do adres URL allowed externího přesměrování

Zabezpečené ověřování vyžaduje, abyste pro aplikaci definovali nové schéma adres URL. To umožňuje ověřovacímu systému přesměrovat zpět do aplikace po dokončení procesu ověřování. V tomto kurzu používáme _název aplikace_ schématu ADRES URL v celém textu. Můžete však použít libovolné schéma adres URL, které zvolíte. To by mělo být jedinečné pro vaši mobilní aplikaci. Povolení přesměrování na straně serveru:

1. Na [webu Azure Portal]vyberte službu App Service.

2. Klepněte na možnost nabídky **Ověřování / Autorizace.**

3. V **adresách URL allowed external redirect zadejte** `appname://easyauth.callback`.  _Název aplikace_ v tomto řetězci je schéma adresy URL pro vaši mobilní aplikaci.  Měl by se řídit běžnou specifikací adresy URL protokolu (používejte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adres URL na několika místech.

4. Klikněte na tlačítko **OK**.

5. Klikněte na **Uložit**.

## <a name="restrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Ve Studiu pro Android otevřete projekt, který jste dokončili s kurzem [Začínáme s mobilními aplikacemi]. V nabídce **Spustit** klikněte na **spustit aplikaci**a ověřte, zda je po spuštění aplikace vyvolána neošetřená výjimka se stavovým kódem 401 (Neautorizováno).

     K této výjimce dochází, protože se aplikace pokusí o přístup k back-endu jako neověřený uživatel, ale tabulka *TodoItem* nyní vyžaduje ověření.

Dále aktualizovat aplikaci k ověření uživatelů před vyžádáním prostředků z mobilní aplikace back-end.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Ověřovací tokeny mezipaměti na straně klienta
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili tento základní kurz ověřování, zvažte pokračování na jeden z následujících kurzů:

* [Přidejte nabízená oznámení do aplikace pro Android](app-service-mobile-android-get-started-push.md).
  Přečtěte si, jak nakonfigurovat back-end mobilních aplikací tak, aby k odesílání nabízených oznámení používala centra oznámení Azure.
* [Povolte offline synchronizaci pro aplikaci pro Android](app-service-mobile-android-get-started-offline-data.md).
  Přečtěte si, jak do aplikace přidat podporu offline pomocí back-endu mobilních aplikací. Při offline synchronizaci mohou uživatelé&mdash;pracovat se zobrazením,&mdash;přidáváním nebo úpravou dat v mobilní aplikaci, i když neexistuje žádné síťové připojení.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Začínáme s mobilními aplikacemi]: app-service-mobile-android-get-started.md
[Portál Azure]: https://portal.azure.com/
