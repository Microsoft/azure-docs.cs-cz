---
title: Konfigurace klientských aplikací (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Informace o možnostech konfigurace pro veřejné klientské a důvěrné klientské aplikace pomocí knihovny Microsoft Authentication Library (MSAL).
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
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534461"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

V kódu inicializujete novou veřejnou nebo důvěrnou klientskou aplikaci (nebo uživatelského agenta pro MSAL.js) k ověření a získání tokenů. Při inicializaci klientské aplikace v knihovně Microsoft Authentication Library (MSAL) můžete nastavit řadu možností konfigurace. Tyto možnosti spadají do dvou skupin:

- Možnosti registrace, včetně:
    - [Autorita](#authority) (skládá se z [instance](#cloud-instance) poskytovatele identity a přihlašovací [publikum](#application-audience) pro aplikaci a případně ID klienta).
    - [ID klienta](#client-id).
    - [Přesměrovat identifikátor URI](#redirect-uri).
    - [Tajný klíč klienta](#client-secret) (pro důvěrné klientské aplikace).
- [Možnosti protokolování](#logging), včetně úrovně protokolu, řízení osobních údajů a názvu komponenty pomocí knihovny.

## <a name="authority"></a>Autorita

Autorita je adresa URL, která označuje adresář, ze kterého může služba MSAL požadovat tokeny. Společnými orgány jsou:

- https\:\<//login.microsoftonline.com/\>tenanta &lt;&gt; /, kde tenant je ID klienta klienta klienta Azure Active Directory (Azure AD) tenanta nebo domény přidružené k tomuto tenantovi Azure AD. Používá se pouze k přihlášení uživatelů určité organizace.
- https\://login.microsoftonline.com/common/. Používá se k přihlašování uživatelů pomocí pracovních a školních účtů nebo osobních účtů Microsoft.
- https\://login.microsoftonline.com/organizations/. Slouží k přihlašování uživatelů pomocí pracovních a školních účtů.
- https\://login.microsoftonline.com/consumers/. Používá se k přihlašování uživatelů pouze k osobním účtům Microsoft (dříve označovaným jako účty Windows Live ID).

Nastavení autority musí být v souladu s tím, co je deklarováno na portálu pro registraci aplikací.

Adresa URL autority se skládá z instance a cílové skupiny.

Orgánem může být:
- Cloudová autorita Azure AD.
- Autorita Azure AD B2C. Viz [specifika B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Autorita služby AD FS (Active Directory Federation Services). Viz [podpora služby AD FS](https://aka.ms/msal-net-adfs-support).

Azure AD cloud ové úřady mají dvě části:
- *Instance* zprostředkovatele identity
- Přihlašovací *publikum* pro aplikaci

Instance a publikum mohou být zřetězené a zapředpokladu jako adresa URL autority. Ve verzích MSAL.NET starší než MSAL 3. *x*, museli jste sestavit autoritu sami, na základě cloudu, na který jste chtěli cílit, a na přihlašovací publikum.  Tento diagram znázorňuje, jak se skládá adresa URL autority:

![Jak se skládá adresa URL autority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instance cloudu

*Instance* se používá k určení, jestli vaše aplikace podepisuje uživatele z veřejného cloudu Azure nebo z národních cloudů. Pomocí MSAL ve vašem kódu můžete nastavit cloudovou instanci Azure pomocí výčtu nebo `Instance` předáním adresy URL na národní cloud [instance](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako člen (pokud ji znáte).

MSAL.NET vyvolá explicitní výjimku, pokud jsou zadány obě `Instance` a `AzureCloudInstance` jsou zadány.

Pokud nezadáte instanci, vaše aplikace se zaměří na instanci `https://login.onmicrosoftonline.com`veřejného cloudu Azure (instanci adresy URL).

## <a name="application-audience"></a>Cílová skupina aplikací

Přihlašovací okruh uživatelů závisí na obchodních potřebách vaší aplikace:
- Pokud jste vývojář oboru podnikání (LOB), pravděpodobně vytvoříte aplikaci s jedním tenantem, která bude použita pouze ve vaší organizaci. V takovém případě je potřeba určit organizaci, buď podle id klienta (ID instance Azure AD) nebo podle názvu domény přidruženého k instanci Azure AD.
- Pokud jste isv, můžete chtít přihlásit uživatele s jejich pracovní a školní účty v libovolné organizaci nebo v některých organizacích (víceklientské aplikace). Můžete ale také chtít, aby se uživatelé přihlašovali pomocí svých osobních účtů Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak zadat cílovou skupinu ve vašem kódu/konfiguraci

Pomocí msal v kódu, můžete zadat cílové skupiny pomocí jedné z následujících hodnot:
- Výčet publika autority Azure AD
- ID klienta, které může být:
  - Identifikátor GUID (ID instance Azure AD) pro aplikace s jedním klientem
  - Název domény přidružený k instanci Azure AD (také pro aplikace s jedním klientem)
- Jeden z těchto zástupných symbolů jako ID klienta namísto výčtu okruhu uživatelů úřadu Azure AD:
    - `organizations`pro víceklientské aplikace
    - `consumers`přihlášení uživatelů pouze pomocí jejich osobních účtů
    - `common`přihlášení uživatelů pomocí jejich pracovních a školních účtů nebo jejich osobních účtů Microsoft

MSAL vyvolá smysluplnou výjimku, pokud zadáte cílovou skupinu autority Azure AD i ID klienta.

Pokud okruh uživatelů nezadáte, vaše aplikace zacílí na Azure AD a osobní účty Microsoft jako okruh uživatelů. (To znamená, že se `common` bude chovat, jako by byly zadány.)

### <a name="effective-audience"></a>Efektivní publikum

Efektivní okruh uživatelů pro vaši aplikaci bude minimální (pokud existuje křižovatka) okruhu uživatelů, který jste nastavili v aplikaci, a okruhu uživatelů, který je určen v registraci aplikace. Ve skutečnosti prostředí [registrace aplikací](https://aka.ms/appregistrations) umožňuje určit okruh uživatelů (podporované typy účtů) pro aplikaci. Další informace naleznete [v tématu Úvodní příručka: Registrace aplikace na platformě identit společnosti Microsoft](quickstart-register-app.md).

V současné době je jediným způsobem, jak získat aplikaci pro přihlášení uživatelů pouze s osobními účty Microsoft, konfigurace obou těchto nastavení:
- Nastavte cílovou skupinu pro registraci aplikace na `Work and school accounts and personal accounts`.
- Nastavte okruh uživatelů v kódu/konfiguraci na `AadAuthorityAudience.PersonalMicrosoftAccount` (nebo `TenantID` ="spotřebitelé").

## <a name="client-id"></a>ID klienta

ID klienta je jedinečné ID aplikace (klienta) přiřazené vaší aplikaci službou Azure AD při registraci aplikace.

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování

Identifikátor URI přesměrování je identifikátor URI, do který poskytovatel identity odešle tokeny zabezpečení zpět.

### <a name="redirect-uri-for-public-client-apps"></a>Přesměrovat identifikátor URI pro aplikace veřejných klientů

Pokud jste vývojář aplikací pro veřejné klienty, který používá MSAL:
- Chcete je používat `.WithDefaultRedirectUri()` v aplikacích pro stolní počítače nebo UPW (MSAL.NET 4.1+). Tato metoda nastaví vlastnost uri přesměrování veřejné klientské aplikace na výchozí doporučenou uri přesměrování pro veřejné klientské aplikace.

  Platforma  | Identifikátor URI pro přesměrování
  ---------  | --------------
  Aplikace pro stolní počítače (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | hodnotu `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. To umožňuje s přihlašování s prohlížečem nastavením hodnoty na výsledek WebAuthenticationBroker.GetCurrentApplicationCallbackUri(), které je třeba zaregistrovat
  .NET Core | `https://localhost`. To umožňuje uživateli používat systémový prohlížeč pro interaktivní ověřování, protože rozhraní .NET Core nemá uživatelské rozhraní pro vložené webové zobrazení v tuto chvíli.

- Uri přesměrování nemusíte přidávat, pokud vytváříte aplikaci Xamarin pro Android a iOS, která nepodporuje zprostředkovatele (identifikátor URI přesměrování je automaticky nastaven na `msal{ClientId}://auth` Xamarin Android a iOS

- Uri přesměrování je třeba nakonfigurovat v [registracích aplikací](https://aka.ms/appregistrations):

   ![Přesměrování identifikátoru URI v registracích aplikací](media/msal-client-application-configuration/redirect-uri.png)

Uri přesměrování můžete přepsat pomocí vlastnosti `RedirectUri` (například pokud používáte zprostředkovatele). Tady je několik příkladů přesměrování identifikátorů URI pro tento scénář:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $"msauth. {Bundle.ID}://auth";

Další podrobnosti o iOS najdete [v tématu Migrace aplikací pro iOS, které používají Microsoft Authenticator z ADAL.NET do MSAL.NET](msal-net-migration-ios-broker.md) a využití [brokera v systému iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Další podrobnosti o Androidu najdete [v tématu Brokered auth in Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Přesměrování identifikátoru URI pro důvěrné klientské aplikace

Pro webové aplikace je identifikátor URI přesměrování (nebo identifikátor URI) identifikátor URI, který služba Azure AD použije k odeslání tokenu zpět do aplikace. Tento identifikátor URI může být adresou URL webové aplikace nebo webového rozhraní API, pokud je důvěrná aplikace jednou z nich. Identifikátor URI přesměrování musí být zaregistrován v registraci aplikace. Tato registrace je obzvláště důležité při nasazení aplikace, které jste původně testovali místně. Potom musíte přidat adresu URL odpovědi nasazené aplikace na portálu pro registraci aplikací.

U aplikací pro daemon není nutné zadávat identifikátor URI přesměrování.

## <a name="client-secret"></a>Tajný klíč klienta

Tato možnost určuje tajný klíč klienta pro důvěrnou klientskou aplikaci. Tento tajný klíč (heslo aplikace) poskytuje portál pro registraci aplikací nebo je poskytován službě Azure AD během registrace aplikace pomocí PowerShell AzureAD, PowerShell AzureRM nebo Azure CLI.

## <a name="logging"></a>Protokolování

Ostatní možnosti konfigurace umožňují protokolování a odstraňování potíží. Podrobnosti o jejich použití najdete v článku [Protokolování.](msal-logging.md)

## <a name="next-steps"></a>Další kroky

Informace o [vytváření instancí klientských aplikací pomocí MSAL.NET](msal-net-initializing-client-applications.md).
Informace o [vytváření instancí klientských aplikací pomocí souboru MSAL.js](msal-js-initializing-client-applications.md).
