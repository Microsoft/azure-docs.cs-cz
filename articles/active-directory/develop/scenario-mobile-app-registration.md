---
title: Registrace mobilních aplikací, které volají webová api | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit mobilní aplikaci, která volá webová API (konfigurace kódu aplikace)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5b65bbdc790a27a06298a77aac2721e071adec8
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882705"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrace mobilních aplikací, které volají webová api

Tento článek obsahuje pokyny, které vám pomohou zaregistrovat mobilní aplikaci, kterou vytváříte.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů, které podporují vaše mobilní aplikace, závisí na prostředí, které chcete povolit, a na tocích, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro interaktivní pořízení tokenu

Většina mobilních aplikací používá interaktivní ověřování. Pokud vaše aplikace používá tento formulář ověřování, můžete se přihlásit k uživatelům z libovolného [typu účtu](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Cílová skupina pro integrované ověřování systému Windows, uživatelské heslo a B2C

Pokud máte aplikaci univerzální platformy Windows (UPW), můžete k přihlášení uživatelů použít integrované ověřování systému Windows. Chcete-li používat integrované ověřování systému Windows nebo ověřování pomocí uživatelského jména a hesla, musí vaše aplikace přihlásit uživatele do vlastního vývojářského tenanta obchodního podniku (LOB). Ve scénáři nezávislého dodavatele softwaru (ISV) může vaše aplikace přihlašovat uživatele v organizacích Azure Active Directory. Tyto toky ověřování nejsou podporovány pro osobní účty Microsoft.

Můžete také přihlásit uživatele pomocí sociálníidentity, které projdou autority B2C a zásady. Chcete-li použít tuto metodu, můžete použít pouze interaktivní ověřování a ověřování uživatelského jména a hesla. Ověřování pomocí uživatelského jména a hesla je aktuálně podporováno pouze v systémech Xamarin.iOS, Xamarin.Android a UPW.

Další informace naleznete v [tématu Scénáře a podporované toky ověřování](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) a [Scénáře a podporované platformy a jazyky](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Konfigurace platformy a přesměrování identifikátorů URI  

### <a name="interactive-authentication"></a>Interaktivní ověřování

Při vytváření mobilní aplikace, která používá interaktivní ověřování, je nejdůležitějším krokem registrace identifikátor URI přesměrování. Interaktivní ověřování můžete nastavit prostřednictvím [konfigurace platformy v okně **Ověřování** ](https://aka.ms/MobileAppReg).

Toto prostředí umožní vaší aplikaci získat jednotné přihlašování (SSO) prostřednictvím Microsoft Authenticator (a Portál společnosti Intune v systému Android). Bude také podporovat zásady správy zařízení.

Portál registrace aplikací poskytuje prostředí náhledu, které vám pomůže vypočítat zprostředkované uri odpovědi pro aplikace pro iOS a Android:

1. Na portálu pro registraci aplikací vyberte **Ověřování** > **Vyzkoušejte nové prostředí**.

   ![Okno Ověřování, ve kterém si vyberete nové prostředí](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Vyberte **Přidat platformu**.

   ![Přidání platformy](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Když je seznam platforem podporován, vyberte **iOS**.

   ![Výběr mobilní aplikace](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Zadejte ID sady a pak vyberte **Registrovat**.

   ![Zadejte ID svého balíčku](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po dokončení kroků se vypočítá identifikátor URI přesměrování za vás, stejně jako na následujícím obrázku.

![Výsledný identifikátor URI přesměrování](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Pokud dáváte přednost ruční konfiguraci identifikátoru URI přesměrování, můžete tak učinit prostřednictvím manifestu aplikace. Zde je doporučený formát manifestu:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Zadejte například`msauth.com.yourcompany.appName://auth`
- **Android**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Algoritmus hash podpisu Android umíte pomocí klíče verze nebo ladicího klíče pomocí příkazu KeyTool.

### <a name="username-password-authentication"></a>Ověřování pomocí uživatelského jména a hesla

Pokud vaše aplikace používá jenom ověřování pomocí uživatelského hesla, nemusíte registrovat identifikátor URI přesměrování pro vaši aplikaci. Tento tok provádí odezvu na koncový bod platformy identit microsoftu verze 2.0. Vaše aplikace nebude volána zpět na žádné konkrétní identifikátor URI. 

Je však nutné identifikovat aplikaci jako veřejnou klientskou aplikaci. Chcete-li tak učinit, začněte v části **Ověřování** aplikace. V podčásti **Upřesnit nastavení** v odstavci **Výchozí typ klienta** pro **otázku Považovat aplikaci za veřejného klienta**vyberte **Ano**.

## <a name="api-permissions"></a>Oprávnění rozhraní API

Mobilní aplikace volají rozhraní API jménem přihlášeného uživatele. Vaše aplikace musí požádat o delegovaná oprávnění. Tato oprávnění se také nazývají obory. V závislosti na požadované mantinelně můžete požádat o delegovaná oprávnění staticky prostřednictvím portálu Azure. Nebo je můžete požádat dynamicky za běhu. 

Statickou registrací oprávnění umožníte správcům snadno schválit vaši aplikaci. Doporučuje se statická registrace.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu](scenario-mobile-app-configuration.md)
