---
title: Veřejné klientské aplikace s jedním a několika účty | Azure
description: Přehled veřejných klientských aplikací jednoho a více účtů.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 5bea1753c87c11094e78f95a1bbadb02fb0b95e2
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752976"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Veřejné klientské aplikace s jedním a několika účty

Tento článek vám pomůže pochopit typy používané v veřejných klientských aplikacích s jedním a více účty a soustředit se na veřejné klientské aplikace s jedním účtem. 

Model knihovny Azure Active Directory Authentication Library (ADAL) serveru.  Knihovna Microsoft Authentication Library (MSAL) místo toho vytváří model klientské aplikace.  Většina aplikací pro Android se považuje za veřejné klienty. Veřejný klient je aplikace, která nemůže bezpečně uchovávat tajný klíč.  

MSAL specializuje plochu rozhraní API, `PublicClientApplication` aby zjednodušila a objasnila vývojové prostředí pro aplikace, které umožňují používat pouze jeden účet v jednom okamžiku. `PublicClientApplication` je podtříd `SingleAccountPublicClientApplication` a `MultipleAccountPublicClientApplication` .  Následující diagram znázorňuje vztah mezi těmito třídami.

![Diagram tříd UML SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Veřejná klientská aplikace s jedním účtem

`SingleAccountPublicClientApplication`Třída umožňuje vytvořit aplikaci založenou na MSAL, která umožňuje, aby byl pouze jeden účet přihlášen v jednom okamžiku. `SingleAccountPublicClientApplication` se liší od `PublicClientApplication` následujících způsobů:

- MSAL sleduje aktuálně přihlášený účet.
  - Pokud vaše aplikace používá zprostředkovatele (výchozí během Azure Portal registrace aplikace) a je nainstalovaný na zařízení, ve kterém je zprostředkovatel přítomen, MSAL ověří, jestli je účet pořád k dispozici na zařízení.
- `signIn` slouží k tomu, abyste se k účtu přihlásili explicitně a nezávisle na žádosti o obory.
- `acquireTokenSilent` nevyžaduje parametr účtu.  Pokud účet zadáte a účet, který zadáte, se neshoduje s aktuálním účtem sledovaným nástrojem MSAL, `MsalClientException` je vyvolána výjimka.
- `acquireToken` neumožňuje uživateli přepnout účty. Pokud se uživatel pokusí přepnout na jiný účet, je vyvolána výjimka.
- `getCurrentAccount` Vrátí objekt výsledku, který poskytuje následující:
  - Logická hodnota označující, zda byl účet změněn. Účet může být změněn v důsledku odebrání ze zařízení, například.
  - Předchozí účet. To je užitečné v případě, že při odebrání účtu ze zařízení nebo při přihlášení k novému účtu potřebujete provést jakékoli místní vyčištění dat.
  - CurrentAccount.
- `signOut` Odebere ze zařízení všechny tokeny přidružené ke klientovi.  

Když je na zařízení nainstalovaný zprostředkovatel ověřování Androidu, jako je Microsoft Authenticator nebo Portál společnosti Intune, a vaše aplikace je nakonfigurovaná tak, aby používala zprostředkovatele, `signOut` účet neodebere ze zařízení.

## <a name="single-account-scenario"></a>Scénář pro jeden účet

Následující pseudo kód ilustruje použití `SingleAccountPublicClientApplication` .

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

`MultipleAccountPublicClientApplication`Třída se používá k vytváření aplikací založených na MSAL, které umožňují přihlášení více účtů současně. Umožňuje získat, přidat a odebrat účty následujícím způsobem:

### <a name="add-an-account"></a>Přidání účtu

Použijte jeden nebo více účtů v aplikaci voláním `acquireToken` jednoho nebo více než jednou.  

### <a name="get-accounts"></a>Získání účtů

- Zavolejte `getAccount` na získat konkrétní účet.
- Volá `getAccounts` se, aby se získal seznam účtů, které aplikace aktuálně zná.

Vaše aplikace nebude moct zobrazit výčet všech účtů Microsoft Identity Platform na zařízení, které je v aplikaci zprostředkovatele známé. Může vytvořit jenom výčet účtů, které vaše aplikace použila.  Pomocí těchto funkcí se nevrátí účty, které jste odebrali ze zařízení.

### <a name="remove-an-account"></a>Odebrat účet

Odeberte účet voláním `removeAccount` pomocí identifikátoru účtu.

Pokud je vaše aplikace nakonfigurovaná tak, aby používala zprostředkovatele, a v zařízení je nainstalovaný zprostředkovatel, při volání se účet neodebere ze zprostředkovatele `removeAccount` .  Odeberou se jenom tokeny přidružené k vašemu klientovi.

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
