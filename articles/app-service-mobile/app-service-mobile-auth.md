---
title: Ověřování a autorizace ve službě Azure App Service pro mobilní aplikace | Microsoft Docs
description: Reference konceptu a Přehled ověřování / autorizace funkcí pro Azure App Service, konkrétně pro mobilní aplikace
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 237310c607eb8488e53631b6e69d01703d1ebf99
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Ověřování a autorizace ve službě Azure App Service pro mobilní aplikace

Tento článek popisuje, jak ověřování a autorizace funguje při vývoji nativní mobilních aplikací pomocí App Service back-end. Služba App Service poskytuje integrované ověřování a autorizaci, takže vaše mobilní aplikace se můžete přihlásit uživatele beze změny žádný kód ve službě App Service. Poskytuje snadný způsob, jak chránit vaše aplikace a pracovat s daty na uživatele. 

Tento článek se zaměřuje na vývoj mobilních aplikací. Abyste mohli rychle začít s aplikační služby ověřování a autorizace pro mobilní aplikace, najdete v jednom z následujících kurzů [přidání ověřování do vaší aplikace pro iOS] [ iOS] (nebo [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], nebo [Cordova ]). 

Informace o ověřování a autorizace fungování ve službě App Service najdete v tématu [ověřování a autorizace ve službě Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Ověřování u poskytovatele sady SDK

Jakmile je všechno nastavené ve službě App Service, můžete upravit mobilních klientů se přihlásit pomocí služby App Service. Existují dva přístupy tady:

* Pomocí sady SDK, který publikuje danou identitu zprostředkovatele k vytvoření identity a přístup do služby App Service.
* Použijte jeden řádek kódu tak, aby klient SDK pro Mobile Apps můžete přihlásit uživatele.

> [!TIP]
> Většina aplikace by měly používat poskytovatele sady SDK potřebujete jednotnější prostředí po přihlášení uživatelé, používat podporu tokenu obnovení a získat další výhody, které určuje zprostředkovatele.
> 
> 

Pokud používáte poskytovatele sady SDK, mohou uživatelé přihlašují k prostředí, které více úzce se integruje se službou operačního systému, který aplikace běží na. Tato metoda také umožňuje token zprostředkovatele a některé informace o uživateli na straně klienta, takže je mnohem jednodušší graf rozhraní API přehledná a přizpůsobit činnost koncového uživatele. Čas od času na blogy a fóra, ho se označuje jako "tok klienta" nebo "přesměruje klienta pohybu" kód na klienta přihlásí uživatele a kód klienta má přístup k tokenu zprostředkovatele.

Po získání tokenu zprostředkovatele musí být odeslány do služby App Service pro ověření. Po služby App Service ověří token, služby App Service vytvoří nové služby App Service token, který je vrácen do klienta. Mobilní aplikace klienta SDK obsahuje pomocné metody pro správu této exchange a automaticky přiřadit všechny požadavky na back-end aplikace token. Vývojáři mohou také ponechat odkaz na token zprostředkovatele.

Další informace o toku ověřování najdete v tématu [tok ověřování služby App Service](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Ověřování bez poskytovatele sady SDK

Pokud nechcete nastavit poskytovatele sady SDK, můžete povolit funkce Mobile Apps služby Azure App Service se můžete přihlásit. Mobilní aplikace klienta SDK se jim otevře webové zobrazení k poskytovateli dle vašeho výběru a přihlásit uživatele. Čas od času na blogy a fóra, nazývá "serveru pohybu" nebo "směrované serveru pohybu" protože server spravuje proces, který se přihlásí uživatele a klient SDK nikdy obdrží token zprostředkovatele.

Kód do začátku tento tok je obsažena v tomto kurzu ověřování pro každou platformu. Na konci tok tokenu služby App Service má klient SDK a token se automaticky přiloží k všechny požadavky na back-end aplikace.

Další informace o toku ověřování najdete v tématu [tok ověřování služby App Service](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>Další zdroje informací

Následující kurzy ukazují, jak přidat ověřování do mobilních klientů pomocí [směrované serveru toku](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Přidání ověřování do vaší aplikace pro iOS][iOS]
* [Přidání ověřování do aplikace pro Android][Android]
* [Přidání ověřování do aplikace pro Windows][Windows]
* [Přidání ověřování do aplikace pro Xamarin.iOS][Xamarin.iOS]
* [Přidání ověřování do aplikace Xamarin.Android][Xamarin.Android]
* [Přidání ověřování do aplikace Xamarin.Forms][Xamarin.Forms]
* [Přidání ověřování do aplikace Cordova][Cordova ]

Použijte v následujících zdrojích informací, pokud chcete použít [přesměruje klienta toku](../app-service/app-service-authentication-overview.md#authentication-flow) pro Azure Active Directory:

* [Použít Active Directory Authentication Library pro iOS][ADAL-iOS]
* [Použití knihovny pro ověřování služby Active Directory pro Android][ADAL-Android]
* [Použití knihovny pro ověřování služby Active Directory pro Windows a Xamarin][ADAL-dotnet]

Použijte v následujících zdrojích informací, pokud chcete použít [přesměruje klienta toku](../app-service/app-service-authentication-overview.md#authentication-flow) pro Facebook:

* [Použití sady SDK Facebook pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Použijte v následujících zdrojích informací, pokud chcete použít [přesměruje klienta toku](../app-service/app-service-authentication-overview.md#authentication-flow) pro Twitter:

* [Pomocí služby Twitter prostředků infrastruktury pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Použijte v následujících zdrojích informací, pokud chcete použít [přesměruje klienta toku](../app-service/app-service-authentication-overview.md#authentication-flow) pro Google:

* [Použití Google přihlášení sady SDK pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

[iOS]: ../app-service-mobile/app-service-mobile-ios-get-started-users.md
[Android]: ../app-service-mobile/app-service-mobile-android-get-started-users.md
[Xamarin.iOS]: ../app-service-mobile/app-service-mobile-xamarin-ios-get-started-users.md
[Xamarin.Android]: ../app-service-mobile/app-service-mobile-xamarin-android-get-started-users.md
[Xamarin.Forms]: ../app-service-mobile/app-service-mobile-xamarin-forms-get-started-users.md
[Windows]: ../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md
[Cordova ]: ../app-service-mobile/app-service-mobile-cordova-get-started-users.md

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
