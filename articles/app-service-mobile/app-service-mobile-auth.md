---
title: Ověřování a autorizace
description: Koncepční odkaz a přehled funkce Ověřování / Autorizace pro azure app service, konkrétně pro mobilní aplikace.
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 4a9ef62178b9a58fa8703413a09114a617d1d239
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459459"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Ověřování a autorizace ve službě Azure App Service pro mobilní aplikace

Tento článek popisuje, jak ověřování a autorizace funguje při vývoji nativní chod mobilních aplikací s back-endem služby App Service. Služba App Service poskytuje integrované ověřování a autorizaci, takže vaše mobilní aplikace můžou přihlašovat uživatele beze změny kódu ve službě App Service. Poskytuje snadný způsob ochrany aplikace a práci s daty pro jednotlivé uživatele. 

Tento článek se zaměřuje na vývoj mobilních aplikací. Chcete-li rychle začít s ověřováním a autorizací služby App Service pro mobilní aplikaci, podívejte se na jeden z následujících kurzů [Přidání ověřování do aplikace pro iOS][iOS] (nebo [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms]nebo [Cordova]). 

Informace o tom, jak ověřování a autorizace fungují ve službě App Service, najdete [v tématu Ověřování a autorizace ve službě Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="authentication-with-provider-sdk"></a>Ověřování pomocí sady SDK zprostředkovatele

Po nakonfigurování všeho ve službě App Service můžete upravit mobilní klienty tak, aby se přihlašovali pomocí služby App Service. Existují dva přístupy zde:

* Pomocí sady SDK, kterou daný poskytovatel identity publikuje, vytvořte identitu a pak získejte přístup ke službě App Service.
* Použijte jeden řádek kódu, aby se sada SDK klienta mobilních aplikací mohla přihlašovat uživatele.

> [!TIP]
> Většina aplikací by měla používat zprostředkovatele SDK získat konzistentnější prostředí při přihlášení uživatelů, použít podporu aktualizace tokenu a získat další výhody, které určuje zprostředkovatel.
> 
> 

Při použití zprostředkovatele SDK, uživatelé se mohou přihlásit k prostředí, které se integruje těsněji s operačním systémem, který je aplikace spuštěna na. Tato metoda také poskytuje token zprostředkovatele a některé informace o uživateli na straně klienta, což usnadňuje využití rozhraní API grafu a přizpůsobení uživatelského prostředí. Občas na blogy a fóra, se označuje jako "tok klienta" nebo "klient-řízený tok", protože kód na klienta přihlášení v uživatele a klientský kód má přístup k tokenu zprostředkovatele.

Po získání tokenu zprostředkovatele je potřeba odeslat do služby App Service pro ověření. Po App Service ověří token, App Service vytvoří nový token služby App Service, který se vrátí klientovi. Sada SDK klienta mobilních aplikací má pomocné metody pro správu této výměny a automaticky připojit token ke všem požadavkům back-endu aplikace. Vývojáři mohou také zachovat odkaz na token zprostředkovatele.

Další informace o toku ověřování najdete v tématu [Tok ověřování služby App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Ověřování bez zprostředkovatele sady SDK

Pokud nechcete nastavit sadu SDK poskytovatele, můžete povolit, aby se za vás přihlásila funkce Mobilní aplikace služby Azure App Service. Sada SDK klienta mobilních aplikací otevře webové zobrazení poskytovateli podle vašeho výběru a přihlásí se k uživateli. Občas na blogy a fóra, se nazývá "tok serveru" nebo "server-řízený tok", protože server spravuje proces, který se přihlásí k uživatelům a klient SDK nikdy obdrží token zprostředkovatele.

Kód pro spuštění tohoto toku je součástí kurzu ověřování pro každou platformu. Na konci toku klienta SDK má token služby App Service a token je automaticky připojen ke všem požadavkům back-endu aplikace.

Další informace o toku ověřování najdete v tématu [Tok ověřování služby App Service](../app-service/overview-authentication-authorization.md#authentication-flow). 
## <a name="more-resources"></a>Další zdroje informací

Následující kurzy ukazují, jak přidat ověřování do mobilních klientů pomocí [toku řízeného serverem](../app-service/overview-authentication-authorization.md#authentication-flow):

* [Přidání ověřování do aplikace pro iOS][iOS]
* [Přidání ověřování do aplikace pro Android][Android]
* [Přidání ověřování do aplikace pro Windows][Windows]
* [Přidání ověřování do aplikace Xamarin.iOS][Xamarin.iOS]
* [Přidání ověřování do aplikace Xamarin.Android][Xamarin.Android]
* [Přidání ověřování do aplikace Xamarin.Forms][Xamarin.Forms]
* [Přidání ověřování do aplikace Cordova][Cordova]

Pokud chcete použít [tok řízený klientem](../app-service/overview-authentication-authorization.md#authentication-flow) pro službu Azure Active Directory, použijte následující prostředky:

* [Použití knihovny ověřování služby Active Directory pro iOS][ADAL-iOS]
* [Použití knihovny ověřování služby Active Directory pro Android][ADAL-Android]
* [Použití knihovny ověřování služby Active Directory pro systém Windows a Xamarin][ADAL-dotnet]

Pokud chcete pro Facebook použít [tok řízený klientem,](../app-service/overview-authentication-authorization.md#authentication-flow) použijte následující prostředky:

* [Použití sady Facebook SDK pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Pokud chcete pro Twitter použít [tok řízený klientem,](../app-service/overview-authentication-authorization.md#authentication-flow) použijte následující prostředky:

* [Použití twitterové tkaniny pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Pokud chcete použít [tok řízený klientem](../app-service/overview-authentication-authorization.md#authentication-flow) pro Google, použijte následující zdroje:

* [Použití sady Google Sign-In SDK pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
