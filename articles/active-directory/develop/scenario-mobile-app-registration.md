---
title: Registrovat mobilní aplikace, které volají webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Informace o tom, jak vytvořit mobilní aplikaci, která volá webová rozhraní API (registrace aplikace)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 902348063a24b50d1de0a821d9d6e67931cc53cd
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582532"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrovat mobilní aplikace, které volají webová rozhraní API

Tento článek obsahuje pokyny, které vám pomůžou při registraci mobilní aplikace, kterou vytváříte.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů, které vaše mobilní aplikace podporují, závisí na prostředí, které chcete povolit, a na tokůch, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro získání interaktivního tokenu

Většina mobilních aplikací používá interaktivní ověřování. Pokud vaše aplikace používá tuto formu ověřování, můžete se přihlásit z libovolného [typu účtu](quickstart-register-app.md).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Cílová skupina pro integrované ověřování systému Windows, uživatelské jméno a heslo a B2C

Pokud máte aplikaci Univerzální platforma Windows (UWP), můžete se přihlásit k uživatelům pomocí integrovaného ověřování systému Windows. Chcete-li použít integrované ověřování systému Windows nebo ověřování pomocí hesla uživatele, musí vaše aplikace přihlašovat uživatele ve vlastním obchodním tenantovi (LOB). Ve scénáři nezávislého výrobce softwaru (ISV) se vaše aplikace může přihlašovat uživatelům v Azure Active Directorych organizacích. Tyto toky ověřování nejsou podporované pro osobní účty Microsoft.

Uživatele můžete také přihlašovat pomocí sociálních identit, které procházejí B2C autoritou a zásadami. Chcete-li použít tuto metodu, můžete použít pouze interaktivní ověřování a ověřování pomocí hesla uživatele. Ověřování uživatelského jména a hesla se v současné době podporuje jenom v Xamarin. iOS, Xamarin. Androidu a UWP.

Další informace najdete v tématu [scénáře a podporované toky](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) a scénáře ověřování a [podporované platformy a jazyky](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Konfigurace platformy a identifikátory URI pro přesměrování

### <a name="interactive-authentication"></a>Interaktivní ověřování

Když vytváříte mobilní aplikaci, která používá interaktivní ověřování, je nejnejdůležitějším krokem registrace identifikátor URI přesměrování. Interaktivní ověřování můžete nastavit prostřednictvím [konfigurace platformy v okně **ověřování**](https://aka.ms/MobileAppReg).

Tato činnost umožní vaší aplikaci získat jednotné přihlašování (SSO) prostřednictvím Microsoft Authenticator (a Portál společnosti Intune na Androidu). Bude taky podporovat zásady správy zařízení.

Portál pro registraci aplikací nabízí prostředí Preview, které vám umožní vypočítat identifikátor URI zprostředkované odpovědi pro aplikace pro iOS a Android:

1. Na portálu pro registraci aplikací vyberte **ověřování**  >  **vyzkoušet nové prostředí**.

   ![Okno ověřování, kde si zvolíte nové prostředí](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Vyberte **Přidat platformu**.

   ![Přidat platformu](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Až se seznam platforem podporuje, vyberte **iOS**.

   ![Zvolit mobilní aplikaci](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Zadejte ID sady prostředků a pak vyberte **Registrovat**.

   ![Zadejte ID sady prostředků.](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po dokončení tohoto postupu se identifikátor URI pro přesměrování vypočítává za vás, jak je znázorněno na následujícím obrázku.

![Výsledný identifikátor URI pro přesměrování](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Pokud upřednostňujete ruční konfiguraci identifikátoru URI přesměrování, můžete to provést prostřednictvím manifestu aplikace. Tady je doporučený formát pro manifest:

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - Zadejte například `msauth.com.yourcompany.appName://auth`
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Hodnotu hash podpisu pro Android můžete vygenerovat pomocí klíče verze nebo klíče pro ladění pomocí příkazu nástroje.

### <a name="username-password-authentication"></a>Uživatelské jméno – ověřování hesla

Pokud vaše aplikace používá pouze ověřování pomocí uživatelského jména a hesla, nemusíte registrovat identifikátor URI přesměrování pro aplikaci. Tento tok provede zpáteční cestu k platformě Microsoft identity. Vaše aplikace se nebude volat zpátky na žádný konkrétní identifikátor URI.

Identifikujte však aplikaci jako veřejnou klientskou aplikaci. Postupujte následovně:

1. Pořád v <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>vyberte svou aplikaci v **Registrace aplikací** a pak vyberte **ověřování**.
1. V části **Upřesnit nastavení**  >  **Povolit toky veřejného klienta**  >  **Povolte následující toky mobilních a desktopových toků:** vyberte **Ano**.

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Povolit nastavení veřejného klienta v podokně ověřování v Azure Portal":::

## <a name="api-permissions"></a>Oprávnění rozhraní API

Mobilní aplikace volají rozhraní API jménem přihlášeného uživatele. Vaše aplikace potřebuje požádat o delegovaná oprávnění. Tato oprávnění se také nazývají obory. V závislosti na prostředí, které chcete, můžete požadovat delegovaná oprávnění staticky prostřednictvím Azure Portal. Nebo je můžete vyžádat dynamicky za běhu.

Staticky registrací oprávnění umožňuje správcům snadno schválit vaši aplikaci. Doporučuje se statická registrace.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Konfigurace kódu aplikace](scenario-mobile-app-configuration.md).
