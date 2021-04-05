---
title: Konfigurace klientské aplikace (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s možnostmi konfigurace pro veřejné klientské a důvěrné klientské aplikace pomocí knihovny Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/20/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 00768f363d08bc476350e57a8eac69eafd9c3589
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99580934"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

K ověřování a získání tokenů inicializujete novou veřejnou nebo důvěrnou klientskou aplikaci ve vašem kódu. Při inicializaci klientské aplikace v knihovně Microsoft Authentication Library (MSAL) můžete nastavit několik možností konfigurace. Tyto možnosti spadají do dvou skupin:

- Možnosti registrace, včetně:
  - [Autorita](#authority) (složená z [instance](#cloud-instance) zprostředkovatele identity a [cílové skupiny](#application-audience) přihlášení pro aplikaci a případně ID tenanta)
  - [ID klienta](#client-id)
  - [Identifikátor URI přesměrování](#redirect-uri)
  - [Tajný kód klienta](#client-secret) (pro důvěrné klientské aplikace)
- [Možnosti protokolování](#logging), včetně úrovně protokolování, řízení osobních údajů a názvu komponenty pomocí knihovny

## <a name="authority"></a>Autorita

Autorita je adresa URL, která označuje adresář, ze kterého může MSAL žádat o tokeny.

Mezi běžné autority patří:

| Adresy URL běžných autorit | Kdy je použít |
|--|--|
| `https://login.microsoftonline.com/<tenant>/` | Přihlaste se pouze uživatelům konkrétní organizace. `<tenant>`V adrese URL je ID tenanta klienta Azure Active Directory (Azure AD) (GUID) nebo jeho domény tenanta. |
| `https://login.microsoftonline.com/common/` | Přihlaste se uživatelům pomocí pracovních a školních účtů nebo osobních účtů Microsoft. |
| `https://login.microsoftonline.com/organizations/` | Přihlaste se uživatelům pomocí pracovních a školních účtů. |
| `https://login.microsoftonline.com/consumers/` | Přihlaste se k uživatelům pouze pomocí osobních účtů Microsoft (MSA). |

Autorita, kterou zadáte v kódu, musí být konzistentní s **podporovanými typy účtů** , které jste zadali pro aplikaci v **Registrace aplikací** Azure Portal.

Autorita může být:

- Cloudová autorita Azure AD.
- Azure AD B2C autorita. Viz [konkrétní B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Autorita Active Directory Federation Services (AD FS) (AD FS). Viz [podpora AD FS](https://aka.ms/msal-net-adfs-support).

Cloudové autority Azure AD mají dvě části:

- *Instance* zprostředkovatele identity
- *Cílová skupina* pro přihlášení k aplikaci

Instance a cílová skupina se dají zřetězit a zadat jako adresu URL autority. Tento diagram znázorňuje, jak se adresa URL autority skládá:

![Jak se skládá adresa URL autority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Cloudová instance

*Instance* se používá k určení, jestli vaše aplikace podepisuje uživatele z veřejného cloudu Azure nebo z národních cloudů. Pomocí MSAL ve svém kódu můžete nastavit cloudovou instanci Azure pomocí výčtu nebo předáním adresy URL [národním cloudovým instancí](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` člena (pokud ho znáte).

MSAL.NET vyvolá explicitní výjimku `Instance` , pokud `AzureCloudInstance` jsou zadány obě i.

Pokud instanci neurčíte, vaše aplikace bude cílit na instanci veřejného cloudu Azure (instance adresy URL `https://login.onmicrosoftonline.com` ).

## <a name="application-audience"></a>Cílová skupina aplikace

Cílová skupina pro přihlášení závisí na obchodních potřebách vaší aplikace:

- Pokud pracujete jako obchodní vývojář (LOB), budete pravděpodobně vydávat jedinou aplikaci, která bude použita pouze ve vaší organizaci. V takovém případě určete organizaci podle jejího ID (ID vaší instance služby Azure AD) nebo podle názvu domény přidruženého k instanci Azure AD.
- Pokud jste nezávislý výrobce softwaru, možná budete chtít uživatele přihlašovat pomocí pracovních a školních účtů v jakékoli organizaci nebo v některých organizacích (víceklientské aplikace). Můžete ale také chtít, aby se uživatelé přihlásili pomocí osobních účtů Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Určení cílové skupiny v kódu/konfiguraci

Pomocí MSAL ve svém kódu určíte cílovou skupinu pomocí jedné z následujících hodnot:

- Výčet cílových skupin autorit Azure AD
- ID tenanta, které může být:
  - Identifikátor GUID (ID vaší instance služby Azure AD) pro aplikace s jedním klientem
  - Název domény, který je přidružený k vaší instanci služby Azure AD (také pro aplikace pro jednoho tenanta)
- Jedna z těchto zástupných symbolů jako ID tenanta místo výčtu cílové skupiny autorit Azure AD:
  - `organizations` pro víceklientské aplikace
  - `consumers` přihlášení uživatelů jenom pomocí svých osobních účtů
  - `common` přihlášení uživatelů pomocí pracovních a školních účtů nebo jejich osobních účtů Microsoft

MSAL vyvolá smysluplnou výjimku, pokud zadáte jak cílovou skupinu autority Azure AD, tak i ID tenanta.

Pokud nezadáte cílovou skupinu, vaše aplikace bude cílit na Azure AD a osobní účty Microsoft jako cílovou skupinu. (To znamená, že se bude chovat, jako by `common` byly zadány.)

### <a name="effective-audience"></a>Efektivní cílová skupina

Efektivní cílovou skupinou vaší aplikace bude minimální (pokud existuje průnik) cílové skupiny, kterou jste v aplikaci nastavili, a cílové skupině, která je zadaná v registraci aplikace. Ve skutečnosti vám [Registrace aplikací](https://aka.ms/appregistrations) prostředí umožňuje určit cílovou skupinu (podporované typy účtů) pro aplikaci. Další informace najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md).

V současné době jediným způsobem, jak získat aplikaci pro přihlašování uživatelů jenom k osobním účtům Microsoft, je nakonfigurovat obě tato nastavení:

- Nastavte cílovou skupinu registrace aplikace na `Work and school accounts and personal accounts` .
- Nastavte cílovou skupinu v kódu/konfiguraci na `AadAuthorityAudience.PersonalMicrosoftAccount` (nebo `TenantID` = "spotřebitelé").

## <a name="client-id"></a>ID klienta

ID klienta je jedinečné ID aplikace (klienta) přiřazené vaší aplikaci službou Azure AD při registraci aplikace.

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Identifikátor URI přesměrování je identifikátor URI, kterému poskytovatel identity pošle tokeny zabezpečení zpátky.

### <a name="redirect-uri-for-public-client-apps"></a>Identifikátor URI pro přesměrování pro veřejné klientské aplikace

Pokud jste vývojář aplikace veřejného klienta, který používá MSAL:

- Chcete použít `.WithDefaultRedirectUri()` v aplikacích klasické pracovní plochy nebo UWP (MSAL.NET 4.1 +). Tato metoda nastaví vlastnost přesměrování URI veřejné klientské aplikace na výchozí doporučený identifikátor URI pro přesměrování pro veřejné klientské aplikace.

  | Platforma | Identifikátor URI pro přesměrování |
  |--|--|
  | Aplikace klasické pracovní plochy (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient` |
  | UWP | hodnota `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . To umožňuje jednotné přihlašování s prohlížečem nastavením hodnoty na výsledek WebAuthenticationBroker. GetCurrentApplicationCallbackUri (), který se musí zaregistrovat. |
  | .NET Core | `https://localhost`. Díky tomu může uživatel používat prohlížeč systému pro interaktivní ověřování, protože .NET Core nemá v současnosti uživatelské rozhraní pro vložené webové zobrazení. |

- Pokud vytváříte aplikaci pro Xamarin Android a iOS, která nepodporuje identifikátor URI přesměrování zprostředkovatele, nemusíte přidávat identifikátor URI přesměrování. Automaticky se nastaví na `msal{ClientId}://auth` pro Xamarin Android a iOS.

- Nakonfigurujte identifikátor URI přesměrování v [Registrace aplikací](https://aka.ms/appregistrations):

   ![Identifikátor URI přesměrování v Registrace aplikací](media/msal-client-application-configuration/redirect-uri.png)

Identifikátor URI přesměrování můžete přepsat pomocí `RedirectUri` vlastnosti (například při použití zprostředkovatelů). Tady jsou některé příklady identifikátorů URI přesměrování pro tento scénář:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.Microsoft.identity.Client.Sample";
- `RedirectUriOnIos` = $ "msauth. {Svazek. ID}://auth ";

Další podrobnosti o iOS najdete v tématu [migrace aplikací pro iOS, které používají Microsoft Authenticator od ADAL.NET k MSAL.NET](msal-net-migration-ios-broker.md) a [využití zprostředkovatele v iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Další podrobnosti o Androidu najdete v tématu zprostředkované [ověřování v Androidu](msal-android-single-sign-on.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Identifikátor URI přesměrování pro důvěrné klientské aplikace

U webových aplikací je identifikátor URI přesměrování (nebo adresa URL odpovědi) identifikátorem URI, který bude služba Azure AD používat k odeslání tokenu zpět do aplikace. Tento identifikátor URI může být adresa URL webové aplikace nebo webového rozhraní API, pokud je důvěrná aplikace jednou z nich. Identifikátor URI pro přesměrování musí být zaregistrován v registraci aplikace. Tato registrace je zvláště důležitá, když nasadíte aplikaci, kterou jste původně otestovali místně. Pak je potřeba přidat adresu URL odpovědi nasazené aplikace na portál pro registraci aplikací.

Pro aplikace démona není nutné zadávat identifikátor URI přesměrování.

## <a name="client-secret"></a>Tajný klíč klienta

Tato možnost určuje tajný klíč klienta pro důvěrnou klientskou aplikaci. Tento tajný klíč (heslo aplikace) poskytuje portál pro registraci aplikací nebo poskytovaný službě Azure AD během registrace aplikace pomocí PowerShellu AzureAD, PowerShell AzureRM nebo Azure CLI.

## <a name="logging"></a>protokolování
Pro pomoc při řešení potíží při ladění a selhání ověřování poskytuje knihovna Microsoft Authentication Library integrovanou podporu protokolování. Protokolování je každá knihovna zahrnuta v následujících článcích:

:::row:::
    :::column:::
        - [Protokolování v MSAL.NET](msal-logging-dotnet.md)
        - [Protokolování v MSAL pro Android](msal-logging-android.md)
        - [Protokolování v MSAL.js](msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Protokolování v MSAL pro iOS/macOS](msal-logging-ios.md)
        - [Protokolování v MSAL pro Javu](msal-logging-java.md)
        - [Protokolování v MSAL pro Python](msal-logging-python.md)
    :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Další kroky

Přečtěte si o [vytváření instancí klientských aplikací pomocí MSAL.NET](msal-net-initializing-client-applications.md) a [vytváření instancí klientských aplikací pomocí MSAL.js](msal-js-initializing-client-applications.md).
