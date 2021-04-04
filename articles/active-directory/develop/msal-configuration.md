---
title: Konfigurační soubor Android MSAL | Azure
titleSuffix: Microsoft identity platform
description: Přehled konfiguračního souboru Microsoft Authentication Library (MSAL) pro Android, který představuje konfiguraci aplikace v Azure Active Directory.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 09/12/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: aa0ce6a5f909e67f0551c8667bb7e5c5e6d7eb04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92275602"
---
# <a name="android-microsoft-authentication-library-configuration-file"></a>Konfigurační soubor knihovny Microsoft Authentication Library pro Android

Knihovna Microsoft Authentication Library (MSAL) pro Android je dodávána s [výchozím konfiguračním souborem JSON](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json) , který si přizpůsobíte, abyste mohli definovat chování vaší veřejné klientské aplikace pro věci, jako je například výchozí autorita, kterou budete používat, a tak dále.

Tento článek vám pomůže porozumět různým nastavením konfiguračního souboru a určit konfigurační soubor, který se má použít v aplikaci založené na MSAL.

## <a name="configuration-settings"></a>Nastavení konfigurace

### <a name="general-settings"></a>Obecná nastavení

| Vlastnost | Typ dat | Vyžadováno | Poznámky |
|-----------|------------|-------------|-------|
| `client_id` | Řetězec | Yes | ID klienta vaší aplikace ze stránky pro [registraci aplikace](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `redirect_uri`   | Řetězec | Yes | Identifikátor URI pro přesměrování vaší aplikace ze [stránky pro registraci aplikace](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) |
| `broker_redirect_uri_registered` | Logická hodnota | No | Možné hodnoty: `true` , `false` |
| `authorities` | Seznam\<Authority> | No | Seznam úřadů, které vaše aplikace potřebuje |
| `authorization_user_agent` | AuthorizationAgent (Enum) | No | Možné hodnoty: `DEFAULT` , `BROWSER` , `WEBVIEW` |
| `http` | HttpConfiguration | No | Konfigurace `HttpUrlConnection` `connect_timeout` a `read_timeout` |
| `logging` | LoggingConfiguration | No | Určuje úroveň podrobností protokolování. Mezi volitelné konfigurace patří: `pii_enabled` , který přijímá logickou hodnotu, a `log_level` , která přijímá `ERROR` , `WARNING` , `INFO` nebo `VERBOSE` . |

### <a name="client_id"></a>client_id

ID klienta nebo ID aplikace, které byly vytvořeny při registraci aplikace.

### <a name="redirect_uri"></a>redirect_uri

Identifikátor URI přesměrování, který jste zaregistrovali při registraci aplikace. Pokud je identifikátor URI pro přesměrování aplikace zprostředkovatele, přečtěte si odkaz [URI pro veřejné klientské aplikace](msal-client-application-configuration.md#redirect-uri-for-public-client-apps) , abyste měli jistotu, že používáte správný formát identifikátoru URI přesměrování pro aplikaci zprostředkovatele.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Pokud chcete použít zprostředkované ověřování, `broker_redirect_uri_registered` musí být vlastnost nastavena na `true` . V případě zprostředkovaných ověřování, pokud aplikace není ve správném formátu pro komunikaci se zprostředkovatelem, jak je popsáno v tématu [identifikátor URI přesměrování pro veřejné klientské aplikace](msal-client-application-configuration.md#redirect-uri-for-public-client-apps), aplikace ověří váš identifikátor URI přesměrování a vyvolá výjimku při spuštění.

### <a name="authorities"></a>autority

Seznam autorit, které jsou známé a důvěryhodné pro vás. Kromě zde uvedených autorit se MSAL také dotazování Microsoftu na získání seznamu cloudů a autorit známých Microsoftu. V tomto seznamu autorit určete typ autority a jakékoli další volitelné parametry `"audience"` , například, které by měly být zarovnány s cílovou skupinou vaší aplikace na základě registrace vaší aplikace. Následuje příklad seznamu autorit:

```javascript
// Example AzureAD and Personal Microsoft Account
{
    "type": "AAD",
    "audience": {
        "type": "AzureADandPersonalMicrosoftAccount"
    },
    "default": true // Indicates that this is the default to use if not provided as part of the acquireToken call
},
// Example AzureAD My Organization
{
    "type": "AAD",
    "audience": {
        "type": "AzureADMyOrg",
        "tenant_id": "contoso.com" // Provide your specific tenant ID here
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

#### <a name="map-aad-authority--audience-to-microsoft-identity-platform-endpoints"></a>Mapování autority AAD & cílovou skupinu na koncové body platformy Microsoft identity

| Typ | Cílová skupina | ID tenanta | Authority_Url | Výsledný koncový bod | Poznámky |
|------|------------|------------|----------------|----------------------|---------|
| AAD | AzureADandPersonalMicrosoftAccount | | | `https://login.microsoftonline.com/common` | `common` je alias tenanta, pro který je účet. Například konkrétního tenanta Azure Active Directory nebo účet Microsoft systému. |
| AAD | AzureADMyOrg | contoso.com | | `https://login.microsoftonline.com/contoso.com` | Tokenu mohou získat pouze účty, které jsou přítomny ve contoso.com. Jako ID tenanta se dá použít jakákoli ověřená doména nebo identifikátor GUID tenanta. |
| AAD | AzureADMultipleOrgs | | | `https://login.microsoftonline.com/organizations` | S tímto koncovým bodem se dají používat jenom účty Azure Active Directory. Účty Microsoft můžou být členy organizací. Pokud chcete získat token pomocí účet Microsoft pro prostředek v organizaci, zadejte tenanta organizace, ze kterého chcete token použít. |
| AAD | PersonalMicrosoftAccount | | | `https://login.microsoftonline.com/consumers` | Tento koncový bod můžou používat jenom účty Microsoft. |
| B2C | | | Zobrazit výsledný koncový bod | `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/` | Tokenu mohou získat pouze účty, kteří jsou přítomni v tenantovi contoso.onmicrosoft.com. V tomto příkladu jsou zásady B2C součástí cesty URL autority. |

> [!NOTE]
> Ověřování autority nejde v MSAL povolit a zakázat.
> Autority se označují buď jako vývojář, jak je uvedeno v části konfigurace, nebo označované jako Microsoft prostřednictvím metadat.
> Pokud MSAL obdrží požadavek na token na neznámou autoritu, `MsalClientException` výsledkem je typ `UnknownAuthority` .
> Zprostředkované ověřování nefunguje pro Azure AD B2C.

#### <a name="authority-properties"></a>Vlastnosti autority

| Vlastnost | Datový typ  | Vyžadováno | Poznámky |
|-----------|-------------|-----------|--------|
| `type` | Řetězec | Yes | Zrcadlí cílovou skupinu nebo typ účtu, na které vaše aplikace cílí. Možné hodnoty: `AAD` , `B2C` |
| `audience` | Objekt | No | Platí pouze v případě typu Type = `AAD` . Určuje identitu, na kterou aplikace cílí. Použití hodnoty z registrace vaší aplikace |
| `authority_url` | Řetězec | Yes | Vyžadováno pouze v případě typu Type = `B2C` . Určuje adresu URL nebo zásadu pro autoritu, kterou by měla vaše aplikace používat.  |
| `default` | boolean | Yes | Je- `"default":true` li zadán jeden nebo více autorit, je vyžadován jeden. |

#### <a name="audience-properties"></a>Vlastnosti cílové skupiny

| Vlastnost | Typ dat  | Vyžadováno | Poznámky |
|-----------|-------------|------------|-------|
| `type` | Řetězec | Yes | Určuje cílovou skupinu, kterou chce vaše aplikace cílit. Možné hodnoty: `AzureADandPersonalMicrosoftAccount` , `PersonalMicrosoftAccount` , `AzureADMultipleOrgs` , `AzureADMyOrg` |
| `tenant_id` | Řetězec | Yes | Vyžadováno pouze v případě `"type":"AzureADMyOrg"` . Volitelné pro jiné `type` hodnoty. Může to být doména tenanta `contoso.com` , například, nebo ID tenanta, například). `72f988bf-86f1-41af-91ab-2d7cd011db46` |

### <a name="authorization_user_agent"></a>authorization_user_agent

Označuje, jestli se má při přihlašování k účtu nebo autorizaci přístupu k prostředku použít vložené WebView nebo výchozí prohlížeč na zařízení.

Možné hodnoty:
- `DEFAULT`: Upřednostňuje prohlížeč systému. Používá vložené webové zobrazení, pokud není v zařízení k dispozici prohlížeč.
- `WEBVIEW`: Použijte vložené webové zobrazení.
- `BROWSER`: Používá výchozí prohlížeč v zařízení.

### <a name="multiple_clouds_supported"></a>multiple_clouds_supported

U klientů, kteří podporují více národních cloudů, zadejte `true` . Platforma Microsoft identity se pak automaticky přesměruje na správný národní Cloud během autorizace a uplatnění tokenu. Národní Cloud přihlášeného účtu můžete určit prověřením autority přidruženého k `AuthenticationResult` . Všimněte si, že `AuthenticationResult` neposkytuje národní adresu koncového bodu specifického pro Cloud, pro kterou požadujete token.

### <a name="broker_redirect_uri_registered"></a>broker_redirect_uri_registered

Logická hodnota, která označuje, zda používáte identifikátor URI přesměrování kompatibilního se zprostředkovatelem Microsoft Identity Broker. Nastavte na, `false` Pokud nechcete zprostředkovatele používat v rámci vaší aplikace.

Pokud používáte autoritu AAD s cílovou skupinou nastavenou na `"MicrosoftPersonalAccount"` , zprostředkovatel se nepoužije.

### <a name="http"></a>HTTP

Nakonfigurujte globální nastavení pro vypršení časových limitů protokolu HTTP, například:

| Vlastnost | Datový typ | Vyžadováno | Poznámky |
| ---------|-----------|------------|--------|
| `connect_timeout` | int | No | Čas v milisekundách |
| `read_timeout` | int | No | Čas v milisekundách |

### <a name="logging"></a>protokolování

Následující globální nastavení slouží k protokolování:

| Vlastnost | Typ dat  | Vyžadováno | Poznámky |
| ----------|-------------|-----------|---------|
| `pii_enabled`  | boolean | No | Bez ohledu na to, jestli se mají posílat osobní údaje |
| `log_level`   | řetězec | No | Které zprávy protokolu mají být ve výstupu. Mezi podporované úrovně protokolu patří `ERROR` , `WARNING` , `INFO` a `VERBOSE` . |
| `logcat_enabled` | boolean | No | Bez ohledu na to, zda se má kromě rozhraní protokolování nakládat do log Cat |

### <a name="account_mode"></a>account_mode

Určuje, kolik účtů se dá v aplikaci používat v daném okamžiku. Možné hodnoty jsou:

- `MULTIPLE` Výchozí
- `SINGLE`

Sestavení `PublicClientApplication` pomocí režimu účtu, který se neshoduje s tímto nastavením, bude mít za následek výjimku.

Další informace o rozdílech mezi jedním a více účty najdete v tématu [aplikace s jedním a více](single-multi-account.md)účty.

### <a name="browser_safelist"></a>browser_safelist

Seznam povolených prohlížečů, které jsou kompatibilní s MSAL. Tyto prohlížeče správně zpracovávají přesměrování na vlastní záměry. Do tohoto seznamu můžete přidat. Výchozí hodnota je k dispozici ve výchozí konfiguraci zobrazené níže.
``
## <a name="the-default-msal-configuration-file"></a>Výchozí konfigurační soubor MSAL

Výchozí konfigurace MSAL, která je dodávána s MSAL, je uvedena níže. Nejnovější verzi si můžete prohlédnout na [GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-android/blob/dev/msal/src/main/res/raw/msal_default_config.json).

Tato konfigurace je doplněna o hodnoty, které zadáte. Hodnoty, které poskytnete, mají přednost před výchozími hodnotami.

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

Následující příklad znázorňuje základní konfiguraci, která určuje ID klienta, identifikátor URI přesměrování, zda je zaregistrováno přesměrování zprostředkovatele a seznam autorit.

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

1. Vytvořte konfigurační soubor. V nástroji doporučujeme vytvořit vlastní konfigurační soubor `res/raw/auth_config.json` . Můžete ho ale umístit kamkoli tam, kde chcete.
2. Sdělte MSAL, kde můžete hledat vaši konfiguraci při vytváření `PublicClientApplication` . Příklad:

   ```java
   //On Worker Thread
   IMultipleAccountPublicClientApplication sampleApp = null; 
   sampleApp = new PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.auth_config);
   ```
