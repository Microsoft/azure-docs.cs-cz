---
title: Veřejné klientské aplikace s jedním a několika účty | Azure
description: Přehled veřejných klientských aplikací jednoho a více účtů.
services: active-directory
documentationcenter: ''
author: shoatman
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae798c6108ec78b92b1ee6ac167b01c2f72c26d9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679708"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Veřejné klientské aplikace s jedním a několika účty

Tento článek vám pomůže pochopit typy používané v veřejných klientských aplikacích s jedním a více účty a soustředit se na veřejné klientské aplikace s jedním účtem. 

Model knihovny Azure Active Directory Authentication Library (ADAL) serveru.  Knihovna Microsoft Authentication Library (MSAL) místo toho vytváří model klientské aplikace.  Většina aplikací pro Android se považuje za veřejné klienty. Veřejný klient je aplikace, která nemůže bezpečně uchovávat tajný klíč.  

MSAL specializuje plochu rozhraní API `PublicClientApplication`, aby zjednodušila a objasnila vývojové prostředí pro aplikace, které umožňují používat pouze jeden účet v jednom okamžiku. `PublicClientApplication` je podtříd `SingleAccountPublicClientApplication` a `MultipleAccountPublicClientApplication`.  Následující diagram znázorňuje vztah mezi těmito třídami.

![Diagram tříd UML SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Veřejná klientská aplikace s jedním účtem

Třída `SingleAccountPublicClientApplication` umožňuje vytvořit aplikaci založenou na MSAL, která umožňuje, aby byl pouze jeden účet přihlášen v jednom okamžiku. `SingleAccountPublicClientApplication` se od `PublicClientApplication` liší následujícími způsoby:

- MSAL sleduje aktuálně přihlášený účet.
  - Pokud vaše aplikace používá zprostředkovatele (výchozí během Azure Portal registrace aplikace) a je nainstalovaný na zařízení, ve kterém je zprostředkovatel přítomen, MSAL ověří, jestli je účet pořád k dispozici na zařízení.
- `signIn` vám umožňuje explicitně podepsat účet a nezávisle na tom, jaké obory požadují.
- `acquireTokenSilent` nevyžaduje parametr účtu.  Pokud účet zadáte a účet, který zadáte, se neshoduje s aktuálním účtem sledovaným nástrojem MSAL, je vyvolána `MsalClientException`.
- `acquireToken` neumožňuje uživateli přepnout účty. Pokud se uživatel pokusí přepnout na jiný účet, je vyvolána výjimka.
- `getCurrentAccount` vrátí objekt výsledku, který poskytuje následující:
  - Logická hodnota označující, zda byl účet změněn. Účet může být změněn v důsledku odebrání ze zařízení, například.
  - Předchozí účet. To je užitečné v případě, že při odebrání účtu ze zařízení nebo při přihlášení k novému účtu potřebujete provést jakékoli místní vyčištění dat.
  - CurrentAccount.
- `signOut` odebere ze zařízení všechny tokeny přidružené ke klientovi.  

Když je na zařízení nainstalovaný zprostředkovatel ověřování Androidu, jako je Microsoft Authenticator nebo Portál společnosti Intune, a vaše aplikace je nakonfigurovaná tak, aby používala zprostředkovatele, `signOut` neodebere účet ze zařízení.

## <a name="single-account-scenario"></a>Scénář pro jeden účet

Následující pseudo kód ilustruje použití `SingleAccountPublicClientApplication`.

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Veřejná klientská aplikace s více účty

Třída `MultipleAccountPublicClientApplication` slouží k vytváření aplikací založených na MSAL, které umožňují přihlášení více účtů současně. Umožňuje získat, přidat a odebrat účty následujícím způsobem:

### <a name="add-an-account"></a>Přidání účtu

Použijte jeden nebo více účtů v aplikaci voláním `acquireToken` jednou nebo vícekrát.  

### <a name="get-accounts"></a>Získání účtů

- Pro získání konkrétního účtu volejte `getAccount`.
- Voláním `getAccounts`získáte seznam účtů, které aplikace aktuálně zná.

Vaše aplikace nebude moct zobrazit výčet všech účtů Microsoft Identity Platform na zařízení, které je v aplikaci zprostředkovatele známé. Může vytvořit jenom výčet účtů, které vaše aplikace použila.  Pomocí těchto funkcí se nevrátí účty, které jste odebrali ze zařízení.

### <a name="remove-an-account"></a>Odebrat účet

Odeberte účet voláním `removeAccount` s identifikátorem účtu.

Pokud je vaše aplikace nakonfigurovaná tak, aby používala zprostředkovatele, a v zařízení je nainstalovaný zprostředkovatel, při volání `removeAccount`se účet neodebere z zprostředkovatele.  Odeberou se jenom tokeny přidružené k vašemu klientovi.

## <a name="multiple-account-scenario"></a>Scénář s více účty

Následující pseudo kód ukazuje, jak vytvořit aplikaci s více účty, vypsat účty v zařízení a získat tokeny.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
