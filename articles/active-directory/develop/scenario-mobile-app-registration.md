---
title: Registrovat mobilní aplikace, které volají webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (konfigurace kódu aplikace).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 33510015f4f05661ad2ea041b1fd3da0e8bfb1ed
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76702075"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobilní aplikace, která volá webová rozhraní API – registrace aplikace

Tento článek obsahuje pokyny k registraci aplikace pro vytvoření mobilní aplikace.

## <a name="supported-accounts-types"></a>Podporované typy účtů

Typy účtů podporované v mobilních aplikacích závisí na zkušenostech, které chcete povolit, a na tokůch, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro získání interaktivního tokenu

Většina mobilních aplikací používá interaktivní ověřování. V takovém případě se můžete přihlásit uživatele z libovolného [typu účtu](quickstart-register-app.md#register-a-new-application-using-the-azure-portal) .

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Cílová skupina pro integrované ověřování, uživatelské jméno a heslo a B2C

- Pokud máte v úmyslu používat integrované ověřování systému Windows (možné v aplikacích UWP) nebo uživatelské jméno/heslo, musí vaše aplikace přihlašovat uživatele ve vašem vlastním tenantovi (obchodní vývojář) nebo v organizacích Azure Active Directory (ISV). Tyto toky ověřování nejsou podporované pro osobní účty Microsoft.
- Pokud se přihlašujete uživatelům pomocí sociálních identit, které procházejí B2C autoritou a zásadami, můžete použít jenom interaktivní ověřování a ověřování pomocí hesla uživatele. Uživatelské jméno a heslo se v tuto chvíli podporuje jenom v Xamarin. iOS, Xamarin. Android a UWP.

Informace o velkém obrázku najdete v tématu [scénáře a podporované](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) postupy a [scénáře ověřování a podporované platformy a jazyky](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages) .

## <a name="platform-configuration-and-redirect-uris"></a>Konfigurace platformy a identifikátory URI pro přesměrování  

### <a name="interactive-authentication"></a>Interaktivní ověřování

Při vytváření mobilní aplikace pomocí interaktivního ověřování je nejnejdůležitějším krokem registrace identifikátor URI přesměrování. To lze nastavit pomocí [konfigurace platformy v okně ověřování](https://aka.ms/MobileAppReg).

Toto prostředí umožní vaší aplikaci získat jednotné přihlašování (SSO) prostřednictvím Microsoft Authenticator (a Portál společnosti Intune na Androidu) a taky podporovat zásady správy zařízení.

Všimněte si, že na portálu pro registraci aplikací je prostředí Preview, které vám pomůžou vypočítat identifikátor URI zprostředkované odpovědi pro aplikace pro iOS a Android:

1. V části registrace aplikace vyberte **ověřování** a výběr **zkuste vyzkoušet nové prostředí**
   ![image](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Vyberte **přidat** ![bitovou kopii
   platformy](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Když se podporuje seznam platforem, vyberte ![image
   pro **iOS**](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Zadejte ID sady prostředků podle požadavků a potom stiskněte **registrovat**
   ![bitová kopie](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Identifikátor URI přesměrování je vypočítán za vás.
   ![image](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Pokud upřednostňujete ruční konfiguraci identifikátoru URI přesměrování, můžete to provést prostřednictvím manifestu aplikace. Doporučený formát je následující:

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (pro instanci "msauth. com. yourcompany. AppName://auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Hodnota hash podpisu Androidu se dá vygenerovat pomocí vydaných nebo ladicích kláves pomocí příkazu nástroje.

### <a name="username-password"></a>Heslo pro uživatelské jméno

Pokud vaše aplikace používá jenom uživatelské jméno a heslo, nemusíte pro svoji aplikaci registrovat identifikátor URI přesměrování. To znamená, že tento tok provede zpáteční cestu ke koncovému bodu Microsoft Identity Platform v 2.0 a vaše aplikace se nebude volat zpátky na žádný konkrétní identifikátor URI. Musíte ale vyjádřit, že vaše aplikace je veřejná klientská aplikace. Tuto konfiguraci dosáhnete tak, že v podčásti pro **ověřování** nasadíte aplikaci a v podčásti **Upřesnit nastavení** zvolíte **Ano**, na dotaz **považovat aplikaci za veřejného klienta** (ve výchozím odstavci **typ klienta** ).

## <a name="api-permissions"></a>Oprávnění rozhraní API

Mobilní aplikace volají rozhraní API jménem přihlášeného uživatele. Vaše aplikace potřebuje požádat o delegovaná oprávnění, označovaná také jako obory. V závislosti na požadovaném prostředí lze to provést staticky prostřednictvím Azure Portal nebo dynamicky v době běhu. Statická registrace oprávnění umožňuje správcům snadno schválit vaši aplikaci a doporučuje se.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu](scenario-mobile-app-configuration.md)
