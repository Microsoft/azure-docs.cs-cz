---
title: Získávání tokenu pomocí aplikace pro Android v Azure Active Directory B2C | Microsoft Docs
description: Tento článek vám ukáže, jak vytvořit aplikaci pro Android používající AppAuth s Azure Active Directory B2C ke správě identit uživatelů a ověřování uživatelů.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/06/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7c0a72e03eaa8d12c26b1bbbf6a05b4d94e72358
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709902"
---
# <a name="azure-ad-b2c-sign-in-using-an-android-application"></a>Azure AD B2C: Přihlaste se pomocí aplikace pro Android

Platforma Microsoft identity používá otevřené standardy, jako je například OAuth2 nebo OpenID Connect. Těchto standardů umožňují využívat žádné knihovny, který chcete integrovat s Azure Active Directory B2C. Můžete použít jiné knihovny, můžete v návod podobné následujícímu ukazují, jak konfigurovat 3. stran knihovny pro připojení k identitu platformy Microsoft. Většina knihovny, které implementují [specifikace RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) můžete připojit k platformě Microsoft Identity.

> [!WARNING]
> Společnost Microsoft neposkytuje opravy pro 3. stran knihovny a nebyl provádí kontrolu těchto knihoven. Tato ukázka používá knihovnu 3. stran volat AppAuth, který byl testován pro kompatibilitu v základní scénáře s Azure AD B2C. Problémy a žádosti o funkce se mají směrovat knihovny open-source projekt. Najdete v tématu [v tomto článku](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) Další informace.  
>
>

Pokud jste ještě nikdy nepracovali s OAuth2 nebo OpenID Connect, pak vám tahle vzorová konfigurace možná nebude moc dávat smysl. Doporučujeme prohlédnout si stručný [přehled protokolu, který uvádíme tady](active-directory-b2c-reference-protocols.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Získání adresáře služby Azure AD B2C

Před použitím Azure AD B2C musíte vytvořit adresář, nebo klienta. Adresář je kontejner pro všechny vaše uživatele, aplikace, skupiny a další. Pokud ho ještě nemáte, [vytvořte adresář B2C](active-directory-b2c-get-started.md) předtím, než budete pokračovat.

## <a name="create-an-application"></a>Vytvoření aplikace

Dále musíte vytvořit aplikaci v adresáři B2C. Azure AD díky tomu získá informace potřebné k bezpečné komunikaci s vaší aplikací. Při vytváření mobilních aplikací, postupujte podle [tyto pokyny](active-directory-b2c-app-registration.md). Ujistěte se, že:

* Zahrnout **nativního klienta** v aplikaci.
* Poznamenejte si **ID aplikace** přiřazené vaší aplikaci. Budete potřebovat později.
* Nastavit nativního klienta **identifikátor URI pro přesměrování** (např. com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect). To také budete potřebovat později.

## <a name="create-your-policies"></a>Vytvořte svoje zásady

V Azure AD B2C je každé uživatelské rozhraní definováno [zásadou](active-directory-b2c-reference-policies.md). Tato aplikace obsahuje jeden prostředí identit: například společné přihlášení a odhlášení. Je potřeba vytvořit tuto zásadu, jak je popsáno v [článku o zásadách](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Při vytváření zásady nezapomeňte na následující:

* Vyberte **zobrazovaný název** jako atribut registrace ve svojí zásadě.
* Zvolit deklarace identity aplikace **Zobrazovaný název** a **ID objektu** v každé zásadě. Můžete zvolit i další deklarace identity.
* Po vytvoření každé zásady si poznamenejte její **Název**. Měl by mít předponu `b2c_1_`.  Název zásady budete potřebovat později.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Po vytvoření zásad jste připraveni k sestavení aplikace.

## <a name="download-the-sample-code"></a>Stažení ukázkového kódu

Uvádíme pracovní vzorku, který používá AppAuth s Azure AD B2C [na Githubu](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Můžete stáhnout kód a spusťte ho. Můžete rychle začít pracovat s vlastní aplikace pomocí pokynů v konfiguraci Azure AD B2C [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Ukázka je změna vzorku poskytované [AppAuth](https://openid.github.io/AppAuth-Android/). Navštivte jejich stránky, další informace o AppAuth a jejich funkce.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Úprava aplikaci pomocí Azure AD B2C AppAuth

> [!NOTE]
> AppAuth podporuje Android API 16 (Jellybean) a vyšší. Doporučujeme používat rozhraní API 23 a vyšší.
>

### <a name="configuration"></a>Konfigurace

Komunikaci s Azure AD B2C můžete nakonfigurovat tak, že zadáte zjišťování URI nebo zadáním koncový bod autorizace a koncový bod tokenu identifikátory URI. V obou případech budete potřebovat následující informace:

* ID klienta (např. contoso.onmicrosoft.com)
* Název zásady (například B2C\_1\_SignUpIn)

Pokud zvolíte možnost automaticky zjistit autorizace a koncový bod tokenu identifikátory URI, musíte se k načtení informací ze zjišťování identifikátor URI. Zjišťování může být generována URI nahrazení klienta\_ID a zásady\_názvem v adrese URL následující:

```java
String mDiscoveryURI = "https://login.microsoftonline.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Potom můžete získat autorizace a koncový bod tokenu identifikátory URI a vytvořit objekt AuthorizationServiceConfiguration spuštěním následující:

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

Namísto použití zjišťování získat autorizace a koncový bod tokenu identifikátory URI, můžete také zadat je explicitně nahrazením klienta\_ID a zásady\_názvem v adrese URL níže:

```java
String mAuthEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://login.microsoftonline.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Spusťte následující kód k vytvoření objektu AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Probíhá autorizace.

Po konfiguraci nebo načítání konfigurace povolení služby, lze sestavit požadavek autorizace. Pokud chcete vytvořit žádost, budete potřebovat následující informace:

* ID klienta (například 00000000-0000-0000-0000-000000000000)
* Identifikátor URI pro přesměrování s vlastní schéma (např. com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

Obě položky by byly uloženy když jste byli [aplikaci zaregistrujete](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Naleznete [AppAuth průvodce](https://openid.github.io/AppAuth-Android/) o tom, jak dokončete proces. Pokud budete potřebovat rychle začít s pracovní aplikace, podívejte se na [naše ukázka](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Postupujte podle kroků v [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) k zadání vlastní konfigurace Azure AD B2C.

Snažíme se vždy otevřený a názory a návrhy! Pokud máte jakékoli problémy s tímto článkem nebo doporučení pro zlepšení tohoto obsahu, by nám chcete sdělit svůj názor v dolní části stránky. Pro žádosti o funkce, přidejte je do [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

