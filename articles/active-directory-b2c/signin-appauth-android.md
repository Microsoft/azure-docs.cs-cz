---
title: Získání tokenu v aplikaci pro Android
titleSuffix: Azure AD B2C
description: Jak vytvořit aplikaci pro Android, která používá AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: acbd2918bd311cec1c27018763ad10771d779d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953317"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Přihlaste se pomocí aplikace pro Android v Azure Active Directory B2C

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Tyto standardy umožňují integraci s Azure Active Directory B2C s využitím libovolné knihovny. Abyste mohli využít jiné knihovny, můžete použít Názorný postup, který vám ukáže, jak nakonfigurovat knihovny třetích stran pro připojení k platformě Microsoft identity. Většina knihoven, které implementují [specifikaci RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) , se může připojit k platformě Microsoft identity.

> [!WARNING]
> Microsoft neposkytuje opravy pro knihovny třetích stran a neprovádí revize těchto knihoven. Tato ukázka používá knihovnu třetích stran s názvem AppAuth, která byla testována z hlediska kompatibility v základních scénářích s Azure AD B2C. Žádosti o problémy a funkce by měly být směrovány na open source projekt knihovny. Další informace najdete v [tomto článku](../active-directory/develop/reference-v2-libraries.md) .
>
>

Pokud jste ještě nikdy nepracovali s OAuth2 nebo OpenID Connect, pak vám tahle vzorová konfigurace možná nebude moc dávat smysl. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](tutorial-create-tenant.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

V dalším kroku zaregistrujete aplikaci do svého tenanta Azure AD B2C. To poskytuje službě Azure AD informace, které potřebuje pro bezpečnou komunikaci s vaší aplikací.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

Také zaznamenejte vlastní identifikátor URI přesměrování pro použití v pozdějším kroku. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Vytvoření uživatelských toků

V Azure AD B2C je každé uživatelské prostředí definované [uživatelským tokem](user-flow-overview.md), což je sada zásad, které řídí chování služby Azure AD. Tato aplikace vyžaduje přihlášení a uživatelský tok pro přihlášení. Při vytváření toku uživatele nezapomeňte:

* V toku uživatele vyberte **Zobrazovaný název** jako atribut pro registraci.
* V toku každého uživatele vyberte **zobrazované deklarace názvu** a **ID objektu** . Můžete zvolit i další deklarace identity.
* Po vytvoření si zkopírujte **název** každého toku uživatele. Měl by mít předponu `b2c_1_`.  Název toku uživatele budete potřebovat později.

Po vytvoření toků uživatelů budete připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu

Poskytujeme pracovní ukázku, která používá AppAuth s Azure AD B2C [na GitHubu](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Můžete stáhnout kód a spustit ho. Můžete rychle začít s vlastní aplikací pomocí vlastní konfigurace Azure AD B2C podle pokynů v tématu [Readme.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Ukázka je úprava ukázky, kterou poskytuje [AppAuth](https://openid.github.io/AppAuth-Android/). Další informace o AppAuth a jejích funkcích najdete na stránce.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikace pro použití Azure AD B2C s AppAuth

> [!NOTE]
> AppAuth podporuje Android API 16 (Jellybean) a vyšší. Doporučujeme používat rozhraní API 23 a novější.
>

### <a name="configuration"></a>Konfigurace

Komunikaci s Azure AD B2C můžete nakonfigurovat buď zadáním identifikátoru URI zjišťování, nebo zadáním identifikátoru URI koncového bodu autorizace i koncových bodů tokenu. V obou případech budete potřebovat následující informace:

* ID tenanta (např. contoso.onmicrosoft.com)
* Název toku uživatele (např. B2C \_ 1 \_ SignUpIn)

Pokud se rozhodnete automaticky zjistit identifikátory URI koncového bodu autorizace a tokenu, bude nutné načíst informace z identifikátoru URI zjišťování. Identifikátor URI zjišťování lze vygenerovat nahrazením `<tenant-id>` a `<policy-name>` v následující adrese URL:

```java
String mDiscoveryURI = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/v2.0/.well-known/openid-configuration";
```

Pak můžete získat identifikátory URI koncového bodu autorizace a tokenu a vytvořit objekt AuthorizationServiceConfiguration spuštěním následujícího:

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

Místo použití funkce zjišťování k získání identifikátorů URI koncového bodu autorizace a tokenu je také možné je explicitně zadat nahrazením `<tenant-id>` a `<policy-name>` v níže uvedených adresách URL:

```java
String mAuthEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/authorize";

String mTokenEndpoint = "https://<tenant-name>.b2clogin.com/<tenant-id>/<policy-name>/oauth2/v2.0/token";
```

Spusťte následující kód pro vytvoření objektu AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizace

Jakmile nakonfigurujete nebo načtete konfiguraci autorizační služby, může být vytvořen požadavek na autorizaci. Chcete-li vytvořit požadavek, budete potřebovat následující informace:

* ID klienta (ID aplikace), které jste si poznamenali dříve. Například, `00000000-0000-0000-0000-000000000000`.
* Vlastní identifikátor URI pro přesměrování, který jste si poznamenali dříve. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Obě položky by měly být uloženy při [registraci aplikace](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Další informace o tom, jak dokončit zbytek procesu, najdete v [Průvodci AppAuth](https://openid.github.io/AppAuth-Android/) . Pokud potřebujete rychle začít pracovat s pracovní aplikací, podívejte se na [naši ukázku](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Postupujte podle kroků v [Readme.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) a zadejte vlastní konfiguraci Azure AD B2C.