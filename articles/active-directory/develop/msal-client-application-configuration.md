---
title: Konfigurace aplikace klienta (knihovna Microsoft Authentication Library) | Azure
description: Další informace o možnostech konfigurace pro veřejné klienty a důvěrnému klientovi aplikací v Microsoft Authentication Library (MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a48eea9fedd2d82f44693d58b31ee0d5c8c288d
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138539"
---
# <a name="application-configuration-options"></a>Možnosti konfigurace aplikace

Ve vašem kódu, je inicializovat nového klienta veřejné nebo důvěrnými informacemi (nebo uživatelský agent pro MSAL.js) aplikace k ověření a získávat tokeny.  Existuje několik různých možností konfigurace, které můžete nastavit při inicializaci klienta aplikace v MSAL. Tyto možnosti lze rozdělit do dvou skupin:

- Možnosti registrace, včetně:
    - [Autorita](#authority) (tvořený zprostředkovatele identity [instance](#cloud-instance) a přihlaste se [cílovou skupinu](#application-audience) pro aplikace a případně ID tenanta).
    - [ID klienta](#client-id)
    - [Identifikátor URI pro přesměrování](#redirect-uri)
    - [tajný kód klienta](#client-secret) (pro aplikace důvěrnému klientovi).
- [Možnosti protokolování](#logging), včetně úroveň protokolu, ovládacího osobní údaje a název komponenty pomocí knihovny.

## <a name="authority"></a>Autorita
Oprávnění je určující adresář, který MSAL můžete požádat o tokeny od adresy URL. Jsou obvykle autority:

- https://login.microsoftonline.com/&lt; tenanta&gt;/, kde &lt;tenanta&gt; je ID tenanta domény přidružené k tohoto tenanta Azure AD nebo tenanta Azure AD.  Použít pouze k přihlášení uživatelů konkrétní organizace.
- https://login.microsoftonline.com/common/. Použít k přihlášení uživatelů s pracovní a školní účty nebo osobní účet Microsoft.
- https://login.microsoftonline.com/organizations/. Použít k přihlášení uživatelů s pracovním a školním účtům.
- https://login.microsoftonline.com/consumers/. Používá k přihlašování uživatelů pomocí jenom osobní účet Microsoft (live).

Nastavení autority musí být konzistentní s co je deklarován v portálu pro registraci aplikace.

Adresa URL autority se skládá z instance a cílová skupina.

Oprávnění může být:
- autoritu cloudové služby Azure Active directory
- autoritu Azure AD B2C. Zobrazit [B2C specifika](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- autoritu služby AD FS. Zobrazit [podpory služby AD FS](https://aka.ms/msal-net-adfs-support).

Azure AD cloudu autority mít dvě části:
- Zprostředkovatel identity **instance**
- přihlášení **cílovou skupinu** pro aplikaci

Instance a cílová skupina může zřetězit a zadat jako adresu URL autority. Ve verzích MSAL.NET před MSAL 3.x, měl oprávnění sestavit sami v závislosti na cloud, kdybyste chtěli cíl a cílovou skupinu přihlášení.  Následující diagram znázorňuje, jak se adresa URL autority skládá.

![Autorita](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Instance cloudu
**Instance** slouží k určení, pokud je vaše aplikace přihlašování uživatelů z veřejného cloudu Microsoft Azure nebo z národních cloudů. S využitím MSAL ve vašem kódu, instance Azure cloud je možné nastavit pomocí výčtu nebo předáním adresu URL [instanci národního cloudu](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` člena (pokud ho znáte).

MSAL.NET vyvolá výjimku explicitní, pokud mají oba `Instance` a `AzureCloudInstance` jsou uvedeny. 

Pokud nechcete zadat instanci, vaše aplikace se zaměří na instanci veřejného cloudu Azure (instance URL `https://login.onmicrosoftonline.com`).

## <a name="application-audience"></a>Cílové skupiny aplikací

Cílová skupina přihlášení závisí na obchodních potřeb pro vaši aplikaci:
- Pokud řádek pro vývojáře obchodní (LOB), budete pravděpodobně vytvářet aplikace jednoho tenanta, který se použije pouze ve vaší organizaci. V takovém případě musíte určit, co tato organizace je, tak, že jeho ID tenanta (ID služby Azure Active Directory), nebo název domény přidružený k Azure Active Directory.
- Pokud jste nezávislý dodavatel softwaru, můžete chtít přihlásit uživatele pomocí jejich pracovních a školních účtů v jakékoli organizaci, nebo některé organizace (aplikace s více tenanty), ale můžete také chtít mít uživatele, přihlaste se svým osobním účtem Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak určit cílovou skupinu v kódu/konfigurace
S využitím MSAL ve vašem kódu, zadejte cílovou skupinu s použitím:
- buď Azure AD autority cílovou skupinu výčet. 
- nebo ID tenanta, který může být:
  - GUID (ID služby Azure Active Directory) pro aplikace pro jednoho tenanta
  - název domény přidružený k Azure Active Directory (také pro aplikace s jedním tenantem)
- nebo jeden z těchto zástupných symbolů jako ID tenanta místo cílové skupiny výčtu autority Azure AD:
    - `organizations` pro aplikaci s více tenanty
    - `consumers` k přihlášení uživatelů jenom pomocí svých osobních účtů
    - `common` Chcete-li přihlásit uživatele pomocí jejich pracovní a školní účet nebo osobní účet Microsoft

Knihovna MSAL vyvolá smysluplné výjimku, pokud zadáte cílovou skupinu autority Azure AD a ID tenanta. 

Pokud nechcete zadat cílová skupina Azure AD se zaměří na vaši aplikaci a osobní účty Microsoft, jako cílovou skupinou (to znamená `common`).

### <a name="effective-audience"></a>Efektivní cílové skupiny
Efektivní cílovou skupinu pro vaši aplikaci budou mít minimální (pokud existuje průnik) cílové skupiny, které nastavíte v aplikaci a cílová skupina zadaná v registraci aplikace. Ve skutečnosti prostředí registrace aplikací ([registrace aplikace](https://aka.ms/appregistrations)) umožňuje určit cílové skupiny (typy podporovaných účtů) pro aplikaci. Zobrazit [rychlý start: Registrace aplikace s platformou identity Microsoft](quickstart-register-app.md) Další informace.

V současné době je jediný způsob, jak získat aplikace k přihlášení uživatelů s jenom osobní účty Microsoft pro nastavení:
- Cílová skupina registrace aplikace nastavte na "Pracovní a školní účty a osobní účty" a
- a nastavte cílovou skupinu ve vašem kódu / konfigurace `AadAuthorityAudience.PersonalMicrosoftAccount` (nebo `TenantID `= "příjemci")

## <a name="client-id"></a>ID klienta
Jedinečné (klient) ID přiřazené vaší aplikaci pomocí Azure AD, kdy byl zaregistrován aplikace.

## <a name="redirect-uri"></a>Přesměrovat identifikátor URI
Identifikátor URI pro přesměrování je identifikátor URI, kde se zprostředkovatel identity odeslat zpět tokeny zabezpečení. 

### <a name="redirect-uri-for-public-client-applications"></a>Identifikátor URI přesměrování pro veřejné klientské aplikace
Pro vývojáře aplikace veřejným klientem s využitím MSAL:
- Není nutné předat ``RedirectUri`` jak se počítá automaticky podle MSAL. Toto přesměrování identifikátoru URI je nastaven na následující hodnoty v závislosti na platformě:

- ``urn:ietf:wg:oauth:2.0:oob`` pro všechny platformy Windows
- ``msal{ClientId}://auth`` pro Xamarin Android a iOS

Ale přesměrování identifikátoru URI je potřeba nakonfigurovat v [registrace aplikace](https://aka.ms/appregistrations).

![Identifikátor URI pro přesměrování na portálu](media/msal-client-application-configuration/redirect-uri.png)

Je možné přepsat pomocí identifikátoru URI přesměrování `RedirectUri` vlastnost, například pokud používáte zprostředkovatelů. Tady je několik příkladů identifikátory URI pro přesměrování v takovém případě:

- `RedirectUriOnAndroid` = "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos` = $"msauth.{Bundle.ID}://auth";

Podrobnosti najdete v tématu s Androidem specifika a [specifika iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)

### <a name="redirect-uri-for-confidential-client-applications"></a>Identifikátor URI přesměrování pro důvěrné klientské aplikace
Identifikátor URI pro přesměrování (nebo v identifikátor URI odpovědi) je pro webové aplikace, identifikátor URI, na které Azure AD bude zpět kontaktovat aplikace s tímto tokenem. To může být adresa URL webové aplikace / webové rozhraní API, pokud důvěrné je jedním z nich.  Tento identifikátor URI pro přesměrování musí být zaregistrované v registraci aplikace. To je obzvláště důležité, když nasazujete aplikaci, která původně lokálně otestovat. Potom musíte přidat příslušnou odpovědní adresu URL nasazené aplikace v portálu pro registraci aplikace.

Pro aplikace démonů není nutné určit identifikátor URI přesměrování.

## <a name="client-secret"></a>Tajný klíč klienta
Tajný kód klienta pro aplikaci důvěrnému klientovi. Tento tajný kód (heslo aplikace) je poskytuje portál pro registraci aplikací nebo součástí do služby Azure AD během registrace aplikace Azure AD PowerShell, AzureRM Powershellu nebo rozhraní příkazového řádku Azure.

## <a name="logging"></a>Protokolování
Další možnosti Povolit protokolování a řešení potíží. Další informace najdete v tématu [protokolování](msal-logging.md) stránku podrobné informace o tom, jak je používat.

## <a name="next-steps"></a>Další postup
Další informace o [vytvoření klientské aplikace pomocí MSAL.NET](msal-net-initializing-client-applications.md).

Další informace o [vytvoření klientské aplikace s využitím MSAL.js](msal-js-initializing-client-applications.md).
