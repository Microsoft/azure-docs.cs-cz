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
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 910007109e4751cf2fd509d1d568c66ae2a22cd2
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92200827"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

V kódu inicializujete novou veřejnou nebo důvěrnou klientskou aplikaci (nebo uživatelského agenta pro MSAL.js) pro ověřování a získání tokenů. Můžete nastavit řadu možností konfigurace při inicializaci klientské aplikace v knihovně Microsoft Authentication Library (MSAL). Tyto možnosti spadají do dvou skupin:

- Možnosti registrace, včetně:
    - [Autorita](#authority) (složená z [instance](#cloud-instance) zprostředkovatele identity a [cílové skupiny](#application-audience) přihlášení pro aplikaci a případně ID tenanta).
    - [ID klienta](#client-id).
    - [Identifikátor URI pro přesměrování](#redirect-uri)
    - [Tajný kód klienta](#client-secret) (u důvěrných klientských aplikací).
- [Možnosti protokolování](#logging), včetně úrovně protokolování, řízení osobních údajů a názvu komponenty pomocí knihovny.

## <a name="authority"></a>Autorita

Autorita je adresa URL, která označuje adresář, ze kterého může MSAL žádat o tokeny. Mezi běžné autority patří:

- https \: //Login.microsoftonline.com/ \<tenant\> /, kde &lt; tenant &gt; je ID tenanta klienta Azure Active Directory (Azure AD) nebo doménu přidruženou k tomuto tenantovi služby Azure AD. Používá se jenom pro přihlášení uživatelů určité organizace.
- https \: //Login.microsoftonline.com/Common/. Slouží k přihlašování uživatelů pomocí pracovních a školních účtů nebo osobních účtů Microsoft.
- https \: //Login.microsoftonline.com/Organizations/. Slouží k přihlašování uživatelů pomocí pracovních a školních účtů.
- https \: //Login.microsoftonline.com/consumers/. Slouží k přihlašování uživatelů pouze k osobním účtům Microsoft (dříve označovaným jako účty Windows Live ID).

Nastavení autority musí být konzistentní s tím, co je deklarované na portálu pro registraci aplikací.

Adresa URL autority se skládá z instance a cílové skupiny.

Autorita může být:
- Cloudová autorita Azure AD.
- Azure AD B2C autorita. Viz [konkrétní B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Autorita Active Directory Federation Services (AD FS) (AD FS). Viz [podpora AD FS](https://aka.ms/msal-net-adfs-support).

Cloudové autority Azure AD mají dvě části:
- *Instance* zprostředkovatele identity
- *Cílová skupina* pro přihlášení k aplikaci

Instance a cílová skupina se dají zřetězit a zadat jako adresu URL autority. Ve verzích MSAL.NET starších než MSAL 3. *x*jste museli vytvořit autoritu na základě cloudu, na který jste chtěli cílit, a cílové skupiny přihlášení.  Tento diagram znázorňuje, jak se adresa URL autority skládá:

![Jak se skládá adresa URL autority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Cloudová instance

*Instance* se používá k určení, jestli vaše aplikace podepisuje uživatele z veřejného cloudu Azure nebo z národních cloudů. Pomocí MSAL ve svém kódu můžete nastavit cloudovou instanci Azure pomocí výčtu nebo předáním adresy URL [národním cloudovým instancí](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` člena (pokud ho znáte).

MSAL.NET vyvolá explicitní výjimku `Instance` , pokud `AzureCloudInstance` jsou zadány obě i.

Pokud instanci neurčíte, vaše aplikace bude cílit na instanci veřejného cloudu Azure (instance adresy URL `https://login.onmicrosoftonline.com` ).

## <a name="application-audience"></a>Cílová skupina aplikace

Cílová skupina pro přihlášení závisí na obchodních potřebách vaší aplikace:
- Pokud pracujete jako obchodní vývojář (LOB), budete pravděpodobně vydávat jedinou aplikaci, která bude použita pouze ve vaší organizaci. V takovém případě je potřeba zadat organizaci buď podle jejího ID tenanta (ID vaší instance služby Azure AD), nebo podle názvu domény přidruženého k instanci Azure AD.
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

  Platforma  | Identifikátor URI pro přesměrování
  ---------  | --------------
  Aplikace klasické pracovní plochy (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | hodnota `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()` . To umožňuje jednotné přihlašování s prohlížečem nastavením hodnoty na výsledek WebAuthenticationBroker. GetCurrentApplicationCallbackUri (), který se musí zaregistrovat.
  .NET Core | `https://localhost`. Díky tomu může uživatel používat prohlížeč systému pro interaktivní ověřování, protože .NET Core nemá v současnosti uživatelské rozhraní pro vložené webové zobrazení.

- Pokud vytváříte aplikaci pro Xamarin Android a iOS, která nepodporuje zprostředkovatele (identifikátor URI pro přesměrování je automaticky nastavený na `msal{ClientId}://auth` pro Xamarin Android a iOS), nemusíte přidávat identifikátor URI pro přesměrování.

- V [Registrace aplikací](https://aka.ms/appregistrations)musíte nakonfigurovat identifikátor URI přesměrování:

   ![Identifikátor URI přesměrování v Registrace aplikací](media/msal-client-application-configuration/redirect-uri.png)

Identifikátor URI přesměrování můžete přepsat pomocí `RedirectUri` vlastnosti (například při použití zprostředkovatelů). Tady jsou některé příklady identifikátorů URI přesměrování pro tento scénář:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.Microsoft.identity.Client.Sample";
- `RedirectUriOnIos` = $ "msauth. {Svazek. ID}://auth ";

Další podrobnosti o iOS najdete v tématu [migrace aplikací pro iOS, které používají Microsoft Authenticator od ADAL.NET k MSAL.NET](msal-net-migration-ios-broker.md) a [využití zprostředkovatele v iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Další podrobnosti o Androidu najdete v tématu zprostředkované [ověřování v Androidu](msal-android-single-sign-on.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Identifikátor URI přesměrování pro důvěrné klientské aplikace

U webových aplikací je identifikátor URI přesměrování (nebo identifikátor URI odpovědi) identifikátor URI, který bude služba Azure AD používat k odeslání tokenu zpět do aplikace. Tento identifikátor URI může být adresa URL webové aplikace nebo webového rozhraní API, pokud je důvěrná aplikace jednou z nich. Identifikátor URI pro přesměrování musí být zaregistrován v registraci aplikace. Tato registrace je zvláště důležitá, když nasadíte aplikaci, kterou jste původně otestovali místně. Pak je potřeba přidat adresu URL odpovědi nasazené aplikace na portál pro registraci aplikací.

Pro aplikace démona není nutné zadávat identifikátor URI přesměrování.

## <a name="client-secret"></a>Tajný klíč klienta

Tato možnost určuje tajný klíč klienta pro důvěrnou klientskou aplikaci. Tento tajný klíč (heslo aplikace) poskytuje portál pro registraci aplikací nebo poskytovaný službě Azure AD během registrace aplikace pomocí PowerShellu AzureAD, PowerShell AzureRM nebo Azure CLI.

## <a name="logging"></a>protokolování

Další možnosti konfigurace umožňují protokolování a odstraňování potíží. Podrobnosti o tom, jak je používat, najdete v článku [protokolování](msal-logging.md) .

## <a name="next-steps"></a>Další kroky

Přečtěte si o [vytváření instancí klientských aplikací pomocí MSAL.NET](msal-net-initializing-client-applications.md).
Přečtěte si o [vytváření instancí klientských aplikací pomocí MSAL.js](msal-js-initializing-client-applications.md).
