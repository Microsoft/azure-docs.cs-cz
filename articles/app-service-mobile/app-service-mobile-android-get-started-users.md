---
title: Přidání ověřování v Androidu
description: Naučte se používat Azure App Service k ověřování uživatelů vaší aplikace pro Android pomocí zprostředkovatelů identity, jako jsou Google, Facebook, Twitter a Microsoft.
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 705ebb5809840155e6bbf3f8eef091eb95f63e63
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77461636"
---
# <a name="add-authentication-to-your-android-app"></a>Přidání ověřování do aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte do projektu rychlý Start ToDoList v Androidu ověřování pomocí podporovaného zprostředkovatele identity. Tento kurz je založený na kurzu [Začínáme s Mobile Apps] , který musíte nejdřív provést.

## <a name="register"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V [Azure Portal]vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `appname://easyauth.callback`.  _AppName_ v tomto řetězci je schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na tlačítko **OK**.

5. Klikněte na možnost **Uložit**.

## <a name="permissions"></a>Omezení oprávnění pro ověřené uživatele
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* V Android Studio otevřete projekt, který jste dokončili v kurzu [Začínáme s Mobile Apps]. V nabídce **Spustit** klikněte na **Spustit aplikaci**a ověřte, že po spuštění aplikace se vyvolá Neošetřená výjimka se stavovým kódem 401 (Neautorizováno).

     K této výjimce dochází, protože se aplikace pokusí získat přístup k back-endu jako neověřeného uživatele, ale tabulka *TodoItem* nyní vyžaduje ověření.

V dalším kroku aplikaci aktualizujete, aby ověřovala uživatele před vyžádáním prostředků z Mobile Apps back-endu.

## <a name="add-authentication-to-the-app"></a>Přidání ověřování do aplikace
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]



## <a name="cache-tokens"></a>Tokeny ověřování mezipaměti na klientovi
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Další kroky
Teď, když jste dokončili tento kurz základního ověřování, zvažte pokračování v jednom z následujících kurzů:

* [Přidejte do svojí aplikace pro Android nabízená oznámení](app-service-mobile-android-get-started-push.md).
  Naučte se, jak nakonfigurovat back-end Mobile Apps, abyste mohli používat centra oznámení Azure k odesílání nabízených oznámení.
* [Povolte offline synchronizaci vaší aplikace pro Android](app-service-mobile-android-get-started-offline-data.md).
  Naučte se přidat do aplikace podporu offline pomocí Mobile Apps back-endu. Při offline synchronizaci můžou uživatelé komunikovat s mobilní aplikací&mdash;zobrazení, přidání nebo úprava dat&mdash;i v případě, že není k dispozici žádné síťové připojení.

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
