---
title: Ověřování a autorizace
description: Koncepční referenční informace a přehled funkcí ověřování a autorizace pro Azure App Service, konkrétně pro mobilní aplikace.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77459459"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Ověřování a autorizace v Azure App Service pro mobilní aplikace

Tento článek popisuje, jak ověřování a autorizace funguje při vývoji nativních mobilních aplikací pomocí back-endu App Service. App Service poskytuje integrované ověřování a autorizaci, takže vaše mobilní aplikace mohou podepisovat uživatele bez nutnosti měnit kód v App Service. Poskytuje snadný způsob ochrany vaší aplikace a práci s daty jednotlivých uživatelů. 

Tento článek se zaměřuje na vývoj mobilních aplikací. Pokud chcete rychle začít s App Service ověřováním a autorizací pro vaši mobilní aplikaci, přečtěte si jedno z následujících kurzů, [které přidávají ověřování do vaší aplikace pro iOS][iOS] (nebo [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]nebo [Cordova]). 

Informace o tom, jak ověřování a autorizace fungují v App Service, najdete v tématu [ověřování a autorizace v Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Ověřování pomocí sady SDK pro poskytovatele

Až se všechno v App Service nakonfigurují, můžete mobilním klientům upravit, aby se přihlásili pomocí App Service. Existují dva způsoby:

* Použijte sadu SDK, kterou daný zprostředkovatel identit zveřejňuje k navázání identity, a pak Získejte přístup k App Service.
* Použijte jediný řádek kódu, aby Mobile Apps klientská sada SDK se může přihlašovat uživatelům.

> [!TIP]
> Většina aplikací by měla použít sadu SDK poskytovatele, aby získala jednotnější prostředí, když se uživatelé přihlásí, využijte podporu aktualizace tokenů a získali další výhody, které poskytovatel určí.
> 
> 

Když použijete sadu SDK poskytovatele, uživatelé se mohou přihlašovat k prostředí, které se těsně integruje s operačním systémem, na kterém je aplikace spuštěná. Tato metoda také poskytuje token poskytovatele a informace o uživatelích na straně klienta, což výrazně usnadňuje využívání rozhraní Graph API a přizpůsobení uživatelského prostředí. V případě blogů a fór se někdy označuje jako "tok klienta" nebo "tok směrovaného klienta", protože kód klienta přihlašuje uživatele a klientský kód má přístup k tokenu poskytovatele.

Po získání tokenu poskytovatele je potřeba odeslat App Service k ověření. Po App Service ověří token, App Service vytvoří nový App Service token, který se vrátí klientovi. Sada SDK Mobile Apps klienta obsahuje pomocné metody pro správu tohoto serveru Exchange a k automatickému připojení tokenu všem žádostem back-endu aplikace. Vývojáři můžou také uchovávat odkazy na token zprostředkovatele.

Další informace o toku ověřování najdete v tématu [App Service tok ověřování](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Ověřování bez sady SDK pro poskytovatele

Pokud nechcete nastavit sadu SDK poskytovatele, můžete povolit funkci Mobile Apps Azure App Service, abyste se přihlásili za vás. Sada SDK Mobile Apps klienta otevře webové zobrazení pro zvoleného poskytovatele a přihlášení uživatele. V případě blogů a fór se někdy označuje jako "tok serveru" nebo "řízený tok serveru", protože Server spravuje proces, který podepisuje uživatele, a klientská sada SDK nikdy neobdrží token poskytovatele.

Kód pro spuštění tohoto toku je obsažen v kurzu ověřování pro každou platformu. Na konci toku má klientská sada SDK token App Service a token je automaticky připojen ke všem žádostem o back-end aplikace.

Další informace o toku ověřování najdete v tématu [App Service tok ověřování](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Další zdroje informací

V následujících kurzech se dozvíte, jak do mobilních klientů přidat ověřování pomocí [toku směrovaného serverem](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Přidání ověřování do aplikace pro iOS][iOS]
* [Přidání ověřování do aplikace pro Android][Android]
* [Přidání ověřování do aplikace pro Windows][Windows]
* [Přidání ověřování do aplikace Xamarin. iOS][Xamarin.iOS]
* [Přidání ověřování do aplikace Xamarin. Android][Xamarin.Android]
* [Přidání ověřování do aplikace Xamarin. Forms][Xamarin.Forms]
* [Přidání ověřování do aplikace Cordova][Cordova]

Pokud chcete pro Azure Active Directory použít [tok směrovaný na klienta](../app-service/overview-authentication-authorization.md#authentication-flow) , použijte následující prostředky:

* [Použití Active Directory Authentication Library pro iOS][ADAL-iOS]
* [Použití Active Directory Authentication Library pro Android][ADAL-Android]
* [Použití Active Directory Authentication Library pro Windows a Xamarin][ADAL-dotnet]

V případě, že chcete pro Facebook použít [tok směrovaný z klienta](../app-service/overview-authentication-authorization.md#authentication-flow) , použijte následující prostředky:

* [Použití sady Facebook SDK pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Následující prostředky použijte v případě, že chcete pro Twitter použít [tok směrovaný na klienta](../app-service/overview-authentication-authorization.md#authentication-flow) :

* [Použití prostředků infrastruktury Twitteru pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Následující prostředky použijte, pokud chcete použít [tok směrovaný od klienta](../app-service/overview-authentication-authorization.md#authentication-flow) pro Google:

* [Použití sady SDK přihlášení k Google pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: ../app-service/configure-authentication-provider-aad.md
[Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: ../app-service/configure-authentication-provider-microsoft.md
[Twitter]: ../app-service/configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
