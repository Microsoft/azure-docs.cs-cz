---
title: Získání tokenu v aplikaci pro Android
titleSuffix: Azure AD B2C
description: Jak vytvořit aplikaci pro Android, která používá AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31ad373b1544fc601a9c37e05e324a9c1dfb3f73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183771"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Přihlášení pomocí aplikace pro Android ve službě Azure Active Directory B2C

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Tyto standardy umožňují využít všechny knihovny, které chcete integrovat s Azure Active Directory B2C. Chcete-li použít jiné knihovny, můžete pomocí návodu, jako je tento, předvést, jak nakonfigurovat knihovny třetích stran pro připojení k platformě identit microsoftu. Většina knihoven, které [implementují specifikace RFC6749 OAuth2,](https://tools.ietf.org/html/rfc6749) se může připojit k platformě Microsoft Identity.

> [!WARNING]
> Společnost Microsoft neposkytuje opravy pro knihovny třetích stran a neprovedla kontrolu těchto knihoven. Tato ukázka používá knihovnu třetích stran s názvem AppAuth, která byla testována na kompatibilitu v základních scénářích s Azure AD B2C. Problémy a požadavky na funkce by měly být směrovány do open source projektu knihovny. Další informace naleznete v [tomto článku.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)
>
>

Pokud jste ještě nikdy nepracovali s OAuth2 nebo OpenID Connect, pak vám tahle vzorová konfigurace možná nebude moc dávat smysl. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](tutorial-create-tenant.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

Dále zaregistrujte aplikaci v tenantovi Azure AD B2C. To poskytuje Azure AD informace, které potřebuje ke komunikaci bezpečně s vaší aplikací.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

Také zaznamenejte vlastní identifikátor URI přesměrování pro pozdější krok. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Vytvoření uživatelských toků

Ve službě Azure AD B2C je každé uživatelské prostředí definováno [tok emitovaného uživatelem](user-flow-overview.md), což je sada zásad, které řídí chování Azure AD. Tato aplikace vyžaduje přihlášení a přihlášení uživatele tok. Při vytváření toku uživatele nezapomeňte:

* V toku uživatele zvolte **zobrazovaný název** jako atribut registrace.
* V každém toku uživatele zvolte deklarace identity **zobrazované jméno** a **ID objektu.** Můžete zvolit i další deklarace identity.
* Zkopírujte **název** každého toku uživatele po jeho vytvoření. Měl by mít předponu `b2c_1_`.  Název toku uživatele budete potřebovat později.

Po vytvoření toků uživatelů jste připraveni vytvořit aplikaci.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu

Poskytli jsme pracovní ukázku, která používá AppAuth s Azure AD B2C [na GitHubu](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Můžete si stáhnout kód a spustit jej. Můžete rychle začít s vlastní aplikací pomocí vlastní konfigurace Azure AD B2C podle pokynů v [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Vzorek je modifikace vzorku poskytnuté [AppAuth](https://openid.github.io/AppAuth-Android/). Další informace o AppAutha a jeho funkcích naleznete na jejich stránce.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikace tak, aby používala Azure AD B2C s AppAuth

> [!NOTE]
> AppAuth podporuje Android API 16 (Jellybean) a výše. Doporučujeme používat rozhraní API 23 a vyšší.
>

### <a name="configuration"></a>Konfigurace

Můžete nakonfigurovat komunikaci s Azure AD B2C buď zadáním identifikátoru URI zjišťování nebo zadáním koncový bod autorizace a token koncový bod URI. V obou případech budete potřebovat následující informace:

* ID nájemce (např. contoso.onmicrosoft.com)
* Uživatelské tokové jméno (např.\_b2C 1\_SignUpIn)

Pokud se rozhodnete automaticky zjišťovat identifikátory URI pro autorizaci a koncový bod tokenu, budete muset načíst informace z identifikátoru URI zjišťování. Identifikátor URI zjišťování lze vygenerovat\_nahrazením ID\_klienta a názvu zásady v následující adrese URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Potom můžete získat identifikátory URI koncový bod autorizace a tokenu a vytvořit objekt AuthorizationServiceConfiguration spuštěním následujícího:

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

Místo použití zjišťování k získání identifikátorů URI pro autorizaci a koncový bod\_tokenu je\_můžete také explicitně zadat nahrazením ID klienta a názvu zásady v následujících adresách URL:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Spusťte následující kód a vytvořte objekt AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autorizace

Po konfiguraci nebo načtení konfigurace autorizační služby lze sestavit žádost o autorizaci. Chcete-li vytvořit požadavek, budete potřebovat následující informace:

* ID klienta (ID aplikace), které jste zaznamenali dříve. Například, `00000000-0000-0000-0000-000000000000`.
* Vlastní identifikátor URI přesměrování, který jste zaznamenali dříve. Například, `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Obě položky měly být uloženy při [registraci aplikace](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Přečtěte si [prosím AppAuth průvodce](https://openid.github.io/AppAuth-Android/) o dokončení zbytku procesu. Pokud potřebujete rychle začít pracovat s pracovní aplikací, podívejte se na [náš ukázkový příklad](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Podle pokynů v [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) zadejte vlastní konfiguraci Azure AD B2C.
