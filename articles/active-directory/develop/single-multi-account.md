---
title: Aplikace pro veřejné klienty s jedním a více klienty s více a více Azure
description: Přehled aplikací pro veřejné klienty s jedním a více účtů.
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
ms.openlocfilehash: f2ce993b8fbf2a1b04ea4ad9d992ba278dbc964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701412"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Jedno a více klientských aplikací s více obchodními vztahy

Tento článek vám pomůže pochopit typy používané v aplikacích veřejného klienta s jedním a více účtů se zaměřením na aplikace veřejného klienta s jedním účtem. 

Azure Active Directory Authentication Library (ADAL) modely serveru.  Knihovna ověřování společnosti Microsoft (MSAL) místo toho modeluje klientskou aplikaci.  Většina aplikací pro Android jsou považovány za veřejné klienty. Veřejný klient je aplikace, která nemůže bezpečně udržet tajemství.  

MSAL se specializuje na `PublicClientApplication` povrch rozhraní API pro zjednodušení a objasnění prostředí pro vývoj aplikací, které umožňují používat pouze jeden účet najednou. `PublicClientApplication`je podtřídou `SingleAccountPublicClientApplication` `MultipleAccountPublicClientApplication`podle a .  Následující diagram znázorňuje vztah mezi těmito třídami.

![Diagram třídy UML aplikace SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Jednoúčtu veřejné klientské aplikace

Třída `SingleAccountPublicClientApplication` umožňuje vytvořit aplikaci založenou na MSAL, která umožňuje pouze jeden účet, který má být přihlášen najednou. `SingleAccountPublicClientApplication`se liší `PublicClientApplication` od následujících způsobů:

- MSAL sleduje aktuálně přihlášený účet.
  - Pokud vaše aplikace používá zprostředkovatele (výchozí během registrace aplikace Portálazure) a je nainstalovaný na zařízení, kde je k dispozici zprostředkovatele, MSAL ověří, že účet je stále k dispozici na zařízení.
- `signIn`umožňuje přihlásit účet explicitně a odděleně od požadujících oborů.
- `acquireTokenSilent`nevyžaduje parametr účtu.  Pokud zadáte účet a účet, který zadáte, neodpovídá aktuálnímu účtu sledovanému `MsalClientException` msal, je vyvolána.
- `acquireToken`neumožňuje uživateli přepínat účty. Pokud se uživatel pokusí přepnout na jiný účet, je vyvolána výjimka.
- `getCurrentAccount`vrátí výsledek objektu, který poskytuje následující:
  - Logická hodnota označující, zda se účet změnil. Účet může být změněn v důsledku odebrání ze zařízení, například.
  - Předchozí účet. To je užitečné, pokud potřebujete provést vyčištění místních dat při odebrání účtu ze zařízení nebo při přihlášení nového účtu.
  - CurrentAccount.
- `signOut`odebere ze zařízení všechny tokeny přidružené k vašemu klientovi.  

Když je v zařízení nainstalovaný zprostředkovatel ověřování androida, jako je Microsoft Authenticator nebo `signOut` Portál společnosti Intune, a vaše aplikace je nakonfigurovaná tak, aby používala zprostředkovatele, účet ze zařízení neodebere.

## <a name="single-account-scenario"></a>Scénář jednoho účtu

Následující pseudo kód ilustruje `SingleAccountPublicClientApplication`použití .

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

## <a name="multiple-account-public-client-application"></a>Více klientská aplikace účtu

Třída `MultipleAccountPublicClientApplication` se používá k vytvoření aplikací založených na MSAL, které umožňují přihlášení více účtů současně. To vám umožní získat, přidat a odebrat účty takto:

### <a name="add-an-account"></a>Přidání účtu

Použijte jeden nebo více účtů `acquireToken` v aplikaci voláním jednou nebo vícekrát.  

### <a name="get-accounts"></a>Získání účtů

- Zavolejte `getAccount` a získejte konkrétní účet.
- Volání `getAccounts`získat seznam účtů, které jsou v současné době známé aplikaci.

Vaše aplikace nebude moct vytvořit výčet všech účtů platformy identit Microsoftu na zařízení známém aplikaci broker. Může pouze vytvořit výčet účtů, které byly používány vaší aplikací.  Účty, které byly odebrány ze zařízení, nebudou těmito funkcemi vráceny.

### <a name="remove-an-account"></a>Odebrání účtu

Odeberte účet `removeAccount` voláním s identifikátorem účtu.

Pokud je vaše aplikace nakonfigurovaná tak, aby používala makléře, a v zařízení `removeAccount`je nainstalovaný broker, účet se z brokera při volání neodebere .  Jsou odebrány pouze tokeny přidružené k vašemu klientovi.

## <a name="multiple-account-scenario"></a>Scénář více účtů

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
