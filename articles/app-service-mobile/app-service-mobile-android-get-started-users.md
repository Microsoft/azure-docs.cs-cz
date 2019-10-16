---
title: Přidání ověřování na Androidu pomocí Mobile Apps | Microsoft Docs
description: Naučte se používat funkci Mobile Apps Azure App Service k ověřování uživatelů vaší aplikace pro Android prostřednictvím celé řady poskytovatelů identity, včetně Google, Facebook, Twitteru a Microsoftu.
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
editor: ''
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b520f6129e7d7fa1a4800143d5987d3eba625e98
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388953"
---
# <a name="add-authentication-to-your-android-app"></a>Přidání ověřování do aplikace pro Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center podporuje vývoj kompletních integrovaných služeb, které jsou důležité pro vývoj mobilních aplikací. Vývojáři mohou využít služby pro **sestavování**, **testování** a **distribuci** a nastavit kanál pro průběžnou integraci a doručování. Jakmile je aplikace nasazená, mohou vývojáři monitorovat její stav a využití pomocí **analytických** a **diagnostických** služeb a spolupracovat s uživateli pomocí služby **Push**. Vývojáři mohou také využít **Auth** k ověřování svých uživatelů a službu and **Data** k uchování dat aplikace a jejich synchronizaci v cloudu.
>
> Pokud chcete v mobilní aplikaci integrovat cloudové služby, zaregistrujte se [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) dnes.

## <a name="summary"></a>Souhrn
V tomto kurzu přidáte do projektu rychlý Start ToDoList v Androidu ověřování pomocí podporovaného zprostředkovatele identity. Tento kurz je založený na kurzu [Začínáme s Mobile Apps] , který musíte nejdřív provést.

## <a name="register"></a>Zaregistrovat aplikaci pro ověřování a nakonfigurovat Azure App Service
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>Přidání aplikace do povolených externích adres URL pro přesměrování

Zabezpečené ověřování vyžaduje, abyste pro svou aplikaci nadefinovali nové schéma URL. To umožňuje, aby systém ověřování po dokončení procesu ověřování přesměroval zpátky do vaší aplikace. V tomto kurzu používáme _celé rozhraní_ příkazového schématu URL. Můžete ale použít jakékoli schéma URL, které si zvolíte. Měl by být jedinečný pro vaši mobilní aplikaci. Zapnutí přesměrování na straně serveru:

1. V [Azure Portal]vyberte App Service.

2. Klikněte na možnost nabídka **ověřování/autorizace** .

3. Do pole **povolené externí adresy URL pro přesměrování**zadejte `appname://easyauth.callback`.  _AppName_ v tomto řetězci je schéma adresy URL vaší mobilní aplikace.  Měla by následovat po běžné specifikaci adresy URL protokolu (použijte pouze písmena a čísla a začněte písmenem).  Měli byste si poznamenat řetězec, který si zvolíte, protože budete muset upravit kód mobilní aplikace pomocí schématu adresy URL na několika místech.

4. Klikněte na **OK**.

5. Klikněte na **Uložit**.

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
  Naučte se přidat do aplikace podporu offline pomocí Mobile Apps back-endu. Při offline synchronizaci můžou uživatelé interaktivně pracovat s mobilní aplikací @ no__t-0viewing, přidávat nebo upravovat data @ no__t-1even, když není k dispozici žádné síťové připojení.

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
