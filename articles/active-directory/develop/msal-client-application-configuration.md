---
title: Konfigurace aplikace klienta (knihovna Microsoft Authentication Library) | Azure
description: Další informace o možnostech konfigurace pro veřejné klienty a důvěrnému klientovi aplikací v Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5e175a8cdd1622add90bd80df63303fe914ab9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430803"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

Ve vašem kódu, je inicializovat nového klienta veřejné nebo důvěrnými informacemi (nebo uživatelský agent pro MSAL.js) aplikace k ověření a získávat tokeny. Při inicializaci klienta aplikace v Microsoft Authentication Library (MSAL), můžete nastavit několik možností konfigurace. Tyto možnosti se dělí do dvou skupin:

- Možnosti registrace, včetně:
    - [Autorita](#authority) (tvořený zprostředkovatele identity [instance](#cloud-instance) a přihlaste se [cílovou skupinu](#application-audience) pro aplikace a případně ID tenanta).
    - [ID klienta](#client-id).
    - [Identifikátor URI pro přesměrování](#redirect-uri).
    - [tajný kód klienta](#client-secret) (pro aplikace důvěrnému klientovi).
- [Možnosti protokolování](#logging), včetně úroveň protokolu, ovládacího osobní údaje a název komponenty pomocí knihovny.

## <a name="authority"></a>Autorita
Oprávnění je adresa URL, která určuje adresář, který MSAL můžete požádat o tokeny od. Běžné autority jsou:

- https://login.microsoftonline.com/&lt ; tenanta&gt; /, kde &lt; tenanta&gt; je ID tenanta domény přidružené k tohoto tenanta Azure AD nebo tenanta Azure Active Directory (Azure AD). Použít pouze k přihlášení uživatelů konkrétní organizace.
- https://login.microsoftonline.com/common/. Použít k přihlášení uživatelů s pracovní a školní účty nebo osobní účty Microsoft.
- https://login.microsoftonline.com/organizations/. Použít k přihlášení uživatelů s pracovním a školním účtům.
- https://login.microsoftonline.com/consumers/. Použít k přihlášení uživatelů s (dříve označované jako účty Windows Live ID) jenom osobní účty Microsoft.

Nastavení autority musí být v souladu s co je deklarován v portálu pro registraci aplikace.

Adresa URL autority se skládá z instance a cílová skupina.

Oprávnění může být:
- Autoritu cloudové služby Azure AD.
- autoritu Azure AD B2C. Zobrazit [B2C specifika](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Autoritu Active Directory Federation Services (ADFS). Zobrazit [podpory služby AD FS](https://aka.ms/msal-net-adfs-support).

Azure AD cloudu autority mít dvě části:
- Zprostředkovatel identity *instance*
- Přihlášení *cílovou skupinu* pro aplikaci

Instance a cílová skupina může zřetězit a zadat jako adresu URL autority. Ve verzích MSAL.NET starších než MSAL 3. *x*, měl oprávnění sestavit sami, založené na cloudu, vám chtěli cíl a cílovou skupinu přihlášení.  Tento diagram znázorňuje, jak se skládá adresu URL autority:

![Jak se skládá adresu URL autority](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instance cloudu
*Instance* slouží k určení, pokud je vaše aplikace přihlašování uživatelů z veřejného cloudu Azure nebo z národních cloudů. S využitím MSAL ve vašem kódu, můžete nastavit instanci cloudu Azure pomocí výčtu nebo předáním adresu URL [instanci národního cloudu](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` člena (pokud ho znáte).

MSAL.NET vyvolá výjimku explicitní, pokud mají oba `Instance` a `AzureCloudInstance` jsou uvedeny.

Pokud nechcete zadat instanci, vaše aplikace se zaměří na instanci veřejného cloudu Azure (instance URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Cílové skupiny aplikací

Cílová skupina přihlášení závisí na obchodních potřeb pro vaši aplikaci:
- Pokud budete psát obchodní (LOB) pro vývojáře, budete pravděpodobně vytvářet aplikace v jazyce jednoho tenanta, který se použije pouze ve vaší organizaci. V takovém případě musíte určit organizaci, a to buď podle jeho ID tenanta (ID instance služby Azure AD) nebo název domény přidružený k instanci služby Azure AD.
- Pokud jste nezávislý dodavatel softwaru, můžete chtít přihlásit uživatele pomocí jejich pracovním a školním účtům v jakékoli organizaci nebo v některých organizacích (víceklientskou aplikaci). Ale můžete také nechat uživatele přihlásit pomocí svých osobních účtů Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak určit cílovou skupinu v kódu/konfigurace
S využitím MSAL ve vašem kódu, zadejte cílovou skupinu pomocí jedné z následujících hodnot:
- Výčet cílovou skupinu autority Azure AD
- ID tenanta, který může být:
  - Identifikátor GUID (ID instance služby Azure AD), pro aplikace s jedním tenantem
  - Název domény přidružený k vaší instanci Azure AD (také pro aplikace s jedním tenantem)
- Jedna z těchto zástupných symbolů jako ID tenanta místo cílové skupiny výčtu autority Azure AD:
    - `organizations` pro víceklientské aplikace
    - `consumers` k přihlášení uživatelů jenom pomocí svých osobních účtů
    - `common` Chcete-li přihlásit uživatele pomocí jejich pracovních a školních účtů nebo svých osobních účtů Microsoft

Knihovna MSAL vyvolá smysluplné výjimku, pokud zadáte cílovou skupinu autority Azure AD a ID tenanta.

Pokud nechcete zadat cílovou skupinou, vaše aplikace se zaměří na Azure AD a osobní účty Microsoft, jako cílovou skupinou. (To znamená, bude se chovat stejně jako by `common` byly zadány.)

### <a name="effective-audience"></a>Efektivní cílové skupiny
Efektivní cílovou skupinu pro vaši aplikaci budou mít minimální (pokud existuje průnik) cílové skupiny, které jste nastavili ve vaší aplikaci a cílová skupina zadaná v registraci aplikace. Ve skutečnosti [registrace aplikací](https://aka.ms/appregistrations) experience vám umožňuje určit cílové skupiny (typy podporovaných účtů) pro aplikaci. Další informace najdete v tématu [rychlý start: Registrace aplikace s platformou identity Microsoft](quickstart-register-app.md).

V současné době je jediný způsob, jak získat aplikace pro přihlásit uživatele pomocí osobních účtů Microsoft ke konfiguraci těchto nastavení:
- Nastavte cílové skupiny registrací aplikací na `Work and school accounts and personal accounts`.
- Nastavte v kódu nebo konfiguraci tak, aby cílová skupina `AadAuthorityAudience.PersonalMicrosoftAccount` (nebo `TenantID` = "příjemci").

## <a name="client-id"></a>ID klienta
ID klienta je ID jedinečné aplikace (klient) přiřazené vaší aplikaci pomocí Azure AD, kdy byl zaregistrován aplikace.

## <a name="redirect-uri"></a>Identifikátor URI pro přesměrování
Přesměrování identifikátoru URI je identifikátor URI zprostředkovatele identity se posílají tokeny zabezpečení.

### <a name="redirect-uri-for-public-client-apps"></a>Identifikátor URI přesměrování pro veřejné klientské aplikace.
Pokud jste vývojář aplikace veřejným klientem, který je s využitím MSAL:
- Není nutné předat `RedirectUri` vzhledem k tomu, že je vypočtena automaticky podle MSAL. Toto přesměrování identifikátoru URI je nastaven na jednu z těchto hodnot, v závislosti na platformě:
   - `urn:ietf:wg:oauth:2.0:oob` pro všechny platformy Windows
   - `msal{ClientId}://auth` pro Xamarin Android a iOS

- Musíte nakonfigurovat přesměrování identifikátory URI v [registrace aplikací](https://aka.ms/appregistrations):

   ![Identifikátor URI pro přesměrování v registrace aplikací](media/msal-client-application-configuration/redirect-uri.png)

Identifikátor URI pro přesměrování lze přepsat pomocí `RedirectUri` vlastnosti (například, pokud používáte můžou být zprostředkovatelé). Tady je několik příkladů identifikátory URI pro přesměrování pro tento scénář:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Podrobnosti najdete v tématu [dokumentaci pro Android a iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).

### <a name="redirect-uri-for-confidential-client-apps"></a>Identifikátor URI přesměrování pro důvěrné klientské aplikace.
Identifikátor URI pro přesměrování (nebo identifikátor URI odpovědi) je pro webové aplikace, identifikátor URI, který Azure AD použije k odeslání tokenu zpět do aplikace. Pokud jeden z nich je důvěrné aplikace, může to být adresa URL na webovou aplikaci nebo webové rozhraní API. Přesměrování identifikátor URI musí být zaregistrované v registraci aplikace. Tuto registrace je obzvláště důležité, když nasazujete aplikaci, která původně místně otestujete. Potom musíte přidat příslušnou odpovědní adresu URL nasazené aplikace v portálu pro registraci aplikace.

Pro aplikace démonů není nutné určit identifikátor URI přesměrování.

## <a name="client-secret"></a>Tajný klíč klienta
Tato možnost určuje tajný kód klienta pro aplikaci důvěrnému klientovi. Tento tajný kód (heslo aplikace) je poskytován portál pro registraci aplikací nebo součástí do služby Azure AD během registrace aplikace Azure AD PowerShell, AzureRM Powershellu nebo rozhraní příkazového řádku Azure.

## <a name="logging"></a>Protokolování
Další možnosti konfigurace povolit protokolování a řešení potíží. Zobrazit [protokolování](msal-logging.md) , kde najdete podrobnosti o tom, jak je používat.

## <a name="next-steps"></a>Další postup
Další informace o [vytvoření klientské aplikace pomocí MSAL.NET](msal-net-initializing-client-applications.md).

Další informace o [vytvoření klientské aplikace s využitím MSAL.js](msal-js-initializing-client-applications.md).
