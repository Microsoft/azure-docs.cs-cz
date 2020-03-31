---
title: Android MSAL konfigurační soubor | Azure
titleSuffix: Microsoft identity platform
description: Přehled konfiguračního souboru Knihovny Ověřování Microsoft (MSAL) androida, který představuje konfiguraci aplikace ve službě Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 9e35ba5a3f3705a52e80262da9bbfbfda489bf83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050376"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Konfigurační soubor knihovny ověřování androida Microsoft

Android Microsoft Authentication Library (MSAL) je dodáván s [výchozí konfigurací souboru JSON,](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) který upravíte definovat chování vaší aplikace veřejného klienta pro věci, jako je například výchozí autorita, které orgány budete používat a tak dále.

Tento článek vám pomůže pochopit různá nastavení v konfiguračním souboru a jak určit konfigurační soubor, který se má použít v aplikaci založené na MSAL.

## <a name="configuration-settings"></a>Nastavení konfigurace

### <a name="general-settings"></a>Obecná nastavení

| Vlastnost | Typ dat | Požaduje se | Poznámky |
|-----------|------------|-------------|-------|
| `client_id` | Řetězec | Ano | ID klienta vaší aplikace na [stránce registrace aplikace](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Řetězec | Ano | Identifikátor URI přesměrování aplikace z [registrační stránky aplikace](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `authorities` | >\<úřadu seznamu | Ne | Seznam úřadů, které vaše aplikace potřebuje |
| `authorization_user_agent` | Autorizační agent (výčet) | Ne | Možné `DEFAULT`hodnoty: `BROWSER`, ,`WEBVIEW` |
| `http` | Konfigurace http | Ne | Konfigurace `HttpUrlConnection` `connect_timeout` a konfigurace`read_timeout` |
| `logging` | Konfigurace protokolování | Ne | Určuje úroveň podrobností protokolování. `pii_enabled`Volitelné konfigurace zahrnují: , který má logickou hodnotu, a `VERBOSE` `log_level`, který trvá `ERROR`, `WARNING`, `INFO`, nebo . |

### <a name="client_id"></a>client_id

ID klienta nebo ID aplikace, které bylo vytvořeno při registraci aplikace.

### <a name="redirect_uri"></a>redirect_uri

Identifikátor URI přesměrování, který jste zaregistrovali při registraci aplikace. Pokud je identifikátor URI přesměrování na zprostředkovatelskou aplikaci, podívejte se na [identifikátor URI přesměrování pro veřejné klientské aplikace](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) a ujistěte se, že používáte správný formát URI přesměrování pro vaši aplikaci pro brokery.

### <a name="authorities"></a>Orgány

Seznam úřadů, které znáte a důvěřujete vám. Kromě zde uvedených úřadů se společnost MSAL také dotazuje společnosti Microsoft, aby získala seznam cloudů a úřadů známých společnosti Microsoft. V tomto seznamu úřadů zadejte typ autority a všechny další `"audience"`volitelné parametry, jako je například , které by měly být v souladu s okruhem uživatelů aplikace na základě registrace vaší aplikace. Následuje příklad seznamu úřadů:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken or acquireTokenSilent call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenantId": "contoso.com" // Provide your specific tenant ID here
    }
},
// Example AzureAD Multiple Organizations
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMultipleOrgs"
    }
},
//Example PersonalMicrosoftAccount
{
    "type": "AAD",
    "audience": {
        "type": "PersonalMicrosoftAccount"
    }
}
```

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mapování oprávnění AAD & cílových skupin na koncové body platformy identit Microsoftu

| Typ | Cílová skupina | ID tenanta | Authority_Url | Výsledný koncový bod | Poznámky |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common`je alias klienta, kde je účet. Například konkrétní klient služby Azure Active Directory nebo systém účtů Microsoft. |
| AAD | AzureADMyorg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Token mohou získat pouze účty přítomné v contoso.com. Všechny ověřené domény nebo identifikátor GUID klienta lze použít jako ID klienta. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | S tímto koncovým bodem lze použít pouze účty služby Azure Active Directory. Účty Microsoft mohou být členy organizací. Chcete-li získat token pomocí účtu Microsoft pro prostředek v organizaci, zadejte klienta organizace, od kterého chcete token. |
| AAD | Osobní účet Microsoft | | | `https://login.microsoftonline.com/consumers` | Tento koncový bod mohou používat pouze účty Microsoft. |
| B2C | | | Viz Výsledný koncový bod | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Token můžou získat pouze účty přítomné v contoso.onmicrosoft.com tenanta. V tomto příkladu je zásada B2C součástí cesty adresy URL úřadu. |

> [!NOTE]
> Ověření autority nelze v msal povolit a zakázat.
> Úřady jsou buď známy jako vývojář, jak je uvedeno prostřednictvím konfigurace, nebo známé společnosti Microsoft prostřednictvím metadat.
> Pokud MSAL obdrží požadavek na token neznáméautority, `MsalClientException` `UnknownAuthority` výsledky typu.

#### <a name="authority-properties"></a>Vlastnosti úřadu

| Vlastnost | Datový typ  | Požaduje se | Poznámky |
|-----------|-------------|-----------|--------|
| `type` | Řetězec | Ano | Zrcadlí okruh uživatelů nebo typ účtu, na který se vaše aplikace zaměřuje. Možné hodnoty: `AAD`,`B2C` |
| `audience` | Objekt | Ne | Platí pouze v`AAD`případě, že type= . Určuje identitu, na kterou se vaše aplikace zaměřuje. Použití hodnoty z registrace aplikace |
| `authority_url` | Řetězec | Ano | Povinné pouze v`B2C`případě, že type= . Určuje adresu URL autority nebo zásady, které by aplikace měla používat.  |
| `default` | Boolean | Ano | Jeden `"default":true` je vyžadován, pokud je zadán jeden nebo více úřadů. |

#### <a name="audience-properties"></a>Vlastnosti cílové skupiny

| Vlastnost | Typ dat  | Požaduje se | Poznámky |
|-----------|-------------|------------|-------|
| `type` | Řetězec | Ano | Určuje okruh uživatelů, na které chce vaše aplikace cílit. Možné `AzureADandPersonalMicrosoftAccount`hodnoty: `PersonalMicrosoftAccount` `AzureADMultipleOrgs`, , ,`AzureADMyOrg` |
| `tenant_id` | Řetězec | Ano | Povinné pouze `"type":"AzureADMyOrg"`v případě, že . Volitelné pro `type` ostatní hodnoty. Může se jedná o `contoso.com`doménu klienta, například , nebo ID klienta, například `72f988bf-86f1-41af-91ab-2d7cd011db46`) |

### <a name="authorization_user_agent"></a>authorization_user_agent

Označuje, zda se má při přihlašování k účtu nebo autorizaci přístupu k prostředku používat vložené webové zobrazení nebo výchozí prohlížeč v zařízení.

Možné hodnoty:
- `DEFAULT`: Preferuje systémový prohlížeč. Používá vložené webové zobrazení, pokud prohlížeč není v zařízení k dispozici.
- `WEBVIEW`: Použijte vložené webové zobrazení.
- `BROWSER`: Používá v zařízení výchozí prohlížeč.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

Pro klienty, kteří podporují `true`více národních cloudů, zadejte . Platforma identit Microsoftu se pak během autorizace a uplatnění tokenu automaticky přesměruje na správný národní cloud. Národní cloud přihlášený účet můžete určit tak, že prozkoumáte `AuthenticationResult`autoritu přidruženou k souboru . Všimněte `AuthenticationResult` si, že neposkytuje národní cloudovou adresu koncového bodu prostředku, pro který požadujete token.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Logická hodnota, která označuje, zda používáte identifikátor URI přesměrování zprostředkovatele identity společnosti Microsoft kompatibilní s identifikátorem URI. Nastavte `false` na, pokud nechcete používat makléře v rámci vaší aplikace.

Pokud používáte autoritu AAD s nastavenou na okruh uživatelů , `"MicrosoftPersonalAccount"`makléř se nepoužije.

### <a name="http"></a>HTTP

Nakonfigurujte globální nastavení pro časové výčasové rozsahy protokolu HTTP, například:

| Vlastnost | Datový typ | Požaduje se | Poznámky |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | Ne | Čas v milisekundách |
| `read_timeout` | int | Ne | Čas v milisekundách |

### <a name="logging"></a>protokolování

Pro protokolování jsou následující globální nastavení:

| Vlastnost | Typ dat  | Požaduje se | Poznámky |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | Boolean | Ne | Zda vypouštět osobní údaje |
| `log_level`   | Boolean | Ne | Které protokolové zprávy do výstupu |
| `logcat_enabled` | Boolean | Ne | Zda výstup pro protokolování kočky kromě rozhraní protokolování |

### <a name="account_mode"></a>account_mode

Určuje, kolik účtů lze v aplikaci najednou používat. Možné hodnoty jsou:

- `MULTIPLE`(Výchozí)
- `SINGLE`

Vytvoření `PublicClientApplication` pomocí režimu účtu, který neodpovídá toto nastavení bude mít za následek výjimku.

Další informace o rozdílech mezi jedním a více účty naleznete v [tématu Aplikace pro jeden a více účtů](single-multi-account.md).

### <a name="browser_safelist"></a>browser_safelist

Povolený seznam prohlížečů, které jsou kompatibilní s MSAL. Tyto prohlížeče správně zpracovávají přesměrování na vlastní záměry. Můžete přidat do tohoto seznamu. Výchozí hodnota je uvedena ve výchozí konfiguraci uvedené níže.
``
## <a name="the-default-msal-configuration-file"></a>Výchozí konfigurační soubor MSAL

Výchozí konfigurace MSAL, která je dodávána s MSAL, je uvedena níže. Nejnovější verzi můžete vidět na [GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Tato konfigurace je doplněna o hodnoty, které zadáte. Zadanými hodnotami přepíší výchozí hodnoty.

```javascript
{
  "authorities": [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      },
      "default": true
    }
  ],
  "authorization_user_agent": "DEFAULT",
  "multiple_clouds_supported": false,
  "broker_redirect_uri_registered": false,
  "http": {
    "connect_timeout": 10000,
    "read_timeout": 30000
  },
  "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": false
  },
  "shared_device_mode_supported": false,
  "account_mode": "MULTIPLE",
  "browser_safelist": [
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "45"
    },
    {
      "browser_package_name": "com.android.chrome",
      "browser_signature_hashes": [
        "7fmduHKTdHHrlMvldlEqAIlSfii1tl35bxj1OXN5Ve8c4lU6URVu4xtSHc3BVZxS6WWJnxMDhIfQN0N0K2NDJg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "org.mozilla.firefox",
      "browser_signature_hashes": [
        "2gCe6pR_AO_Q2Vu8Iep-4AsiKNnUHQxu0FaDHO_qa178GByKybdT_BuE8_dYk99G5Uvx_gdONXAOO2EaXidpVQ=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "57"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : true,
      "browser_version_lower_bound": "4.0"
    },
    {
      "browser_package_name": "com.sec.android.app.sbrowser",
      "browser_signature_hashes": [
        "ABi2fbt8vkzj7SJ8aD5jc4xJFTDFntdkMrYXL3itsvqY1QIw-dZozdop5rgKNxjbrQAd5nntAGpgh9w84O1Xgg=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.cloudmosa.puffinFree",
      "browser_signature_hashes": [
        "1WqG8SoK2WvE4NTYgr2550TRhjhxT-7DWxu6C_o6GrOLK6xzG67Hq7GCGDjkAFRCOChlo2XUUglLRAYu3Mn8Ag=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.duckduckgo.mobile.android",
      "browser_signature_hashes": [
        "S5Av4cfEycCvIvKPpKGjyCuAE5gZ8y60-knFfGkAEIZWPr9lU5kA7iOAlSZxaJei08s0ruDvuEzFYlmH-jAi4Q=="
      ],
      "browser_use_customTab" : false
    },
    {
      "browser_package_name": "com.explore.web.browser",
      "browser_signature_hashes": [
        "BzDzBVSAwah8f_A0MYJCPOkt0eb7WcIEw6Udn7VLcizjoU3wxAzVisCm6bW7uTs4WpMfBEJYf0nDgzTYvYHCag=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.ksmobile.cb",
      "browser_signature_hashes": [
        "lFDYx1Rwc7_XUn4KlfQk2klXLufRyuGHLa3a7rNjqQMkMaxZueQfxukVTvA7yKKp3Md3XUeeDSWGIZcRy7nouw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.microsoft.emmx",
      "browser_signature_hashes": [
        "Ivy-Rk6ztai_IudfbyUrSHugzRqAtHWslFvHT0PTvLMsEKLUIgv7ZZbVxygWy_M5mOPpfjZrd3vOx3t-cA6fVQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.browser",
      "browser_signature_hashes": [
        "FIJ3IIeqB7V0qHpRNEpYNkhEGA_eJaf7ntca-Oa_6Feev3UkgnpguTNV31JdAmpEFPGNPo0RHqdlU0k-3jWJWw=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "com.opera.mini.native",
      "browser_signature_hashes": [
        "TOTyHs086iGIEdxrX_24aAewTZxV7Wbi6niS2ZrpPhLkjuZPAh1c3NQ_U4Lx1KdgyhQE4BiS36MIfP6LbmmUYQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "mobi.mgeek.TunnyBrowser",
      "browser_signature_hashes": [
        "RMVoXuK1sfJZuGZ8onG1yhMc-sKiAV2NiB_GZfdNlN8XJ78XEE2wPM6LnQiyltF25GkHiPN2iKQiGwaO2bkyyQ=="
      ],
      "browser_use_customTab" : false
    },

    {
      "browser_package_name": "org.mozilla.focus",
      "browser_signature_hashes": [
        "L72dT-stFqomSY7sYySrgBJ3VYKbipMZapmUXfTZNqOzN_dekT5wdBACJkpz0C6P0yx5EmZ5IciI93Q0hq0oYA=="
      ],
      "browser_use_customTab" : false
    }
  ]
}
```
## <a name="example-basic-configuration"></a>Příklad základní konfigurace

Následující příklad ilustruje základní konfiguraci, která určuje ID klienta, přesměrování identifikátoru URI, zda je registrováno přesměrování zprostředkovatele, a seznam úřadů.

```javascript
{
  "client_id" : "4b0db8c2-9f26-4417-8bde-3f0e3656f8e0",
  "redirect_uri" : "msauth://com.microsoft.identity.client.sample.local/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
  "broker_redirect_uri_registered": true,
  "authorities" : [
    {
      "type": "AAD",
      "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
      }
      "default": true
    }
  ]
}
```

## <a name="how-to-use-a-configuration-file"></a>Použití konfiguračního souboru

1. Vytvořte konfigurační soubor. Doporučujeme vytvořit vlastní konfigurační soubor v aplikaci `res/raw/auth_config.json`. Ale můžeš si ho dát, kam budeš chtít.
2. Řekněte MSAL, kde hledat konfiguraci `PublicClientApplication`při vytváření . Například:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
