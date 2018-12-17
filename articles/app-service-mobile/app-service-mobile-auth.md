---
title: Ověřování a autorizace ve službě Azure App Service mobile apps | Dokumentace Microsoftu
description: Reference konceptu postupu a přehled o ověřování / autorizace funkcí pro službu Azure App Service, konkrétně pro mobilní aplikace
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
ms.openlocfilehash: de501b79107aafa61c489db607c37d086a5f4ed4
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408013"
---
# <a name="authentication-and-authorization-in-azure-app-service-for-mobile-apps"></a>Ověřování a autorizace ve službě Azure App Service pro mobilní aplikace

Tento článek popisuje, jak ověřování a autorizace funguje při vývoji nativních mobilních aplikací pomocí App Service back-endu. App Service poskytuje integrované ověřování a autorizaci, abyste své mobilní aplikace mohli přihlásit uživatele beze změny kódu ve službě App Service. Poskytuje snadný způsob, jak chránit vaše aplikace a pracovat s daty jednotlivých uživatelů. 

Tento článek se zaměřuje na vývoj mobilních aplikací. Abyste mohli rychle začít s App Service ověřování a autorizace pro mobilní aplikace, najdete v jednom z následujících kurzů [přidání ověřování do aplikace pro iOS] [ iOS] (nebo [Android], [Windows], [Xamarin.iOS], [Xamarin.Android], [Xamarin.Forms], nebo [Cordova]). 

Informace o ověřování a autorizace fungování ve službě App Service najdete v tématu [ověřování a autorizace ve službě Azure App Service](../app-service/app-service-authentication-overview.md).

## <a name="authentication-with-provider-sdk"></a>Ověřování pomocí poskytovatele sady SDK

Poté, co všechno, co je nakonfigurovaná ve službě App Service, můžete upravit mobilních klientů se přihlásit pomocí služby App Service. Existují dva přístupy tady:

* Pomocí sady SDK, který publikuje zprostředkovatele danou identitu k vytvoření identity a přístupu do služby App Service.
* Použijte jeden řádek kódu tak, aby Klientská sada SDK pro Mobile Apps můžete přihlásit uživatele.

> [!TIP]
> Většina aplikací používejte poskytovatele sady SDK získat konzistentní prostředí při přihlášení uživatele, používat podporu obnovení tokenu a získání dalších výhod, které určuje poskytovatele.
> 
> 

Pokud používáte poskytovatele sady SDK, uživatel může přihlásit k prostředí, které bude lépe se integruje se s operačním systémem, na kterém aplikace běží na. Tato metoda také umožňuje zprostředkovatele tokenu a některé informace o uživateli na klientovi, což umožňuje mnohem snadněji využívat rozhraní API graphu a přizpůsobení činnosti koncového uživatele. Čas od času na blogy a fóra, to se nazývá "tok klienta" nebo "tok přesměruje klienta" kód na klienta přihlásí uživatele a klientský kód má přístup k zprostředkovatele tokenu.

Po získání zprostředkovatele tokenu se musí odeslat do služby App Service pro ověření. Jakmile App Service ověří token, App Service vytvoří nový token služby App Service, který je vrácen do klienta. Mobile Apps Klientská sada SDK obsahuje pomocné metody pro správu tohoto serveru exchange a automaticky token připojení pro všechny požadavky na back-endu aplikace. Vývojáři také zachovat odkaz na token zprostředkovatele.

Další informace o toku ověřování najdete v tématu [tok ověřování služby App Service](../app-service/app-service-authentication-overview.md#authentication-flow). 

## <a name="authentication-without-provider-sdk"></a>Ověřování bez poskytovatele sady SDK

Pokud nechcete nastavit poskytovatele sady SDK, můžete povolit funkci Mobile Apps služby Azure App Service pro vás přihlásit. Otevře webové zobrazení k poskytovateli podle vašeho výběru se klientská sada SDK pro Mobile Apps a uživatele. Čas od času na blogy a fóra, je volána "serveru tok" nebo "tok směrované na serveru" protože server spravuje proces, který se přihlásí uživatelé a klientská sada SDK přijímá nikdy zprostředkovatele tokenu.

Kód pro spuštění tohoto toku je součástí kurzu ověřování pro každou platformu. Na konci tok Klientská sada SDK obsahuje token služby App Service a token, který je automaticky připojen k všechny požadavky na back-endové aplikace.

Další informace o toku ověřování najdete v tématu [tok ověřování služby App Service](../app-service/app-service-authentication-overview.md#authentication-flow). 
## <a name="more-resources"></a>Další zdroje informací

Následující kurzy vám ukážou, jak přidat ověřování do vašich mobilních klientů pomocí [směrované na server tok](../app-service/app-service-authentication-overview.md#authentication-flow):

* [Přidání ověřování do aplikace pro iOS][iOS]
* [Přidání ověřování do aplikace pro Android][android]
* [Přidání ověřování do aplikace Windows][Windows]
* [Přidání ověřování do aplikace Xamarin.iOS][Xamarin.iOS]
* [Přidání ověřování do aplikace Xamarin.Android][Xamarin.Android]
* [Přidání ověřování do aplikace Xamarin.Forms][Xamarin.Forms]
* [Přidání ověřování do aplikace Cordova][Cordova]

Použijte následující prostředky, pokud chcete použít [přesměruje klienta tok](../app-service/app-service-authentication-overview.md#authentication-flow) pro Azure Active Directory:

* [Jak používat Active Directory Authentication Library pro iOS][ADAL-iOS]
* [Použití služby Active Directory Authentication Library pro Android][ADAL-Android]
* [Použití knihovny ověřování služby Active Directory pro Windows a Xamarin][ADAL-dotnet]

Použijte následující prostředky, pokud chcete použít [přesměruje klienta tok](../app-service/app-service-authentication-overview.md#authentication-flow) pro Facebooku:

* [Použití sady SDK Facebooku pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#facebook-sdk)

Použijte následující prostředky, pokud chcete použít [přesměruje klienta tok](../app-service/app-service-authentication-overview.md#authentication-flow) pro Twitter:

* [Použití Twitteru prostředků infrastruktury pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#twitter-fabric)

Použijte následující prostředky, pokud chcete použít [přesměruje klienta tok](../app-service/app-service-authentication-overview.md#authentication-flow) pro Google:

* [Jak používat Google přihlášení sady SDK pro iOS](../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#google-sdk)

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
