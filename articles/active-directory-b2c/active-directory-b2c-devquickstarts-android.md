---
title: Získání tokenu pomocí aplikace pro Android v Azure Active Directory B2C | Dokumentace Microsoftu
description: Tento článek vám ukáže postup vytvoření aplikace pro Android, která používá AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a5bf15289e91cc568524e8110702b5608118bc2d
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52833920"
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: Aplikace pro Android pomocí přihlášení

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Tyto normy vám umožňují využívat všechny knihovny, které chcete integrovat s Azure Active Directory B2C. Můžete použít další knihovny, můžete v návodu podobný následujícímu ukazují, jak konfigurovat 3. stran knihovny pro připojení k platformě Microsoft identity. Většinu knihoven, které implementují [specifikace RFC6749 oauth2](https://tools.ietf.org/html/rfc6749) můžete připojit k platformě Microsoft Identity.

> [!WARNING]
> Společnost Microsoft neposkytuje opravy pro 3. stran knihovny a přezkoumání těchto knihoven, jako jednička. Tato ukázka používá 3. stran knihovnu s názvem AppAuth, který byl testován pro kompatibilitu s Azure AD B2C v základní scénáře. Problémy a žádosti o funkce směrovat do projektu open source knihovny. Podrobnosti najdete na [v tomto článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) Další informace.  
>
>

Pokud jste ještě nikdy nepracovali s OAuth2 nebo OpenID Connect, pak vám tahle vzorová konfigurace možná nebude moc dávat smysl. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](active-directory-b2c-get-started.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

Dále musíte vytvořit aplikaci v adresáři B2C. Azure AD díky tomu získá informace potřebné k bezpečné komunikaci s vaší aplikací. Při vytváření mobilních aplikací, postupujte podle [tyto pokyny](active-directory-b2c-app-registration.md). Ujistěte se, že:

* Zahrnout **nativního klienta** v aplikaci.
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Ho budete potřebovat později.
* Nastavení nativního klienta **identifikátor URI pro přesměrování** (třeba com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). To také budete potřebovat později.

## <a name="create-your-user-flows"></a>Vytvářet toky uživatelů

V Azure AD B2C je každé uživatelské rozhraní určené [tok uživatele](active-directory-b2c-reference-policies.md), což je sada zásad, které řídí chování Azure AD. Tato aplikace obsahuje možnosti pro jednu identitu: kombinované přihlášení a registraci uživatelů flowu. Je potřeba vytvořit tento tok uživatele, jak je popsáno v [článku toku uživatele](active-directory-b2c-reference-policies.md#create-a-sign-up-user-flow). Když vytvoříte tok uživatele, nezapomeňte na následující:

* Zvolte **zobrazovaný název** jako atribut registrace ve svém toku uživatele.
* Zvolte **zobrazovaný název** a **ID objektu** deklaracemi identity aplikace v každé tok uživatele. Můžete zvolit i další deklarace identity.
* Kopírovat **název** každý toku uživatele. po jeho vytvoření. Měl by mít předponu `b2c_1_`.  Název toku uživatele budete potřebovat později.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po vytvoření toků uživatelů, budete připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stáhněte si ukázkový kód

Poskytujeme ukázku práci, která používá AppAuth s Azure AD B2C [na Githubu](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Můžete stáhnout kód a spustíme ji. Můžete rychle začít s vlastní aplikací pomocí pokynů v konfiguraci Azure AD B2C [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Ukázka je úprava ukázka poskytuje [AppAuth](https://openid.github.io/AppAuth-Android/). Navštivte jejich stránku získat další informace o AppAuth a jeho funkcí.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikace pro použití Azure AD B2C s AppAuth

> [!NOTE]
> AppAuth podporuje Android API 16 (Jellybean) a vyšší. Doporučujeme použít rozhraní API 23 a vyšší.
>

### <a name="configuration"></a>Konfigurace

Komunikace s Azure AD B2C můžete nakonfigurovat tak, že zadáte identifikátor URI zjišťování nebo zadáním koncový bod autorizace a koncový bod tokenu identifikátorů URI. V obou případech budete potřebovat následující informace:

* ID tenanta (například contoso.onmicrosoft.com).
* Název toku uživatele (například B2C\_1\_SignUpIn)

Pokud se rozhodnete k automatickému zjišťování autorizace a koncový bod tokenu identifikátory URI, je potřeba načíst informace ze zjišťování identifikátoru URI. Zjišťování identifikátoru URI je vygenerovat tak, že nahradíte Tenanta\_ID a zásad\_názvu v následující adrese URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Pak můžete získat autorizaci a koncový bod tokenu identifikátory URI a vytvořit objekt AuthorizationServiceConfiguration spuštěním následujícího:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Místo použití zjišťování za účelem získání autorizace a koncový bod tokenu identifikátory URI, můžete také zadat explicitně nahrazením Tenanta\_ID a zásad\_názvu v adrese URL vaší níže:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Spusťte následující kód k vytvoření objektu AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Probíhá autorizace.

Po konfiguraci nebo načítání konfigurace povolení služby, lze sestavit žádost o autorizaci. Pokud chcete vytvořit žádost, budete potřebovat následující informace:

* ID klienta (například 00000000-0000-0000-0000-000000000000)
* Identifikátor URI přesměrování s vlastním schématem (třeba com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Obě položky by se uložily. Pokud byste byli [zaregistrujete aplikaci](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Najdete [AppAuth průvodce](https://openid.github.io/AppAuth-Android/) o tom, k dokončení procesu. Pokud chcete rychle začít s funkční aplikaci, přečtěte si [naše ukázka](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Postupujte podle pokynů [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) zadat konfiguraci Azure AD B2C.

Máme vždy otevřít na názory a návrhy! Pokud nemají nějaké problémy s tímto článkem, nebo máte doporučení k vylepšení tohoto obsahu, uvítáme vaše zpětná vazba v dolní části stránky. Pro žádosti o funkce, přidejte je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

