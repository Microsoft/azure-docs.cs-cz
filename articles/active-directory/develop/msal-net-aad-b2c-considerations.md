---
title: Azure AD B2C (Microsoft Authentication Library pro .NET) | Azure
description: Další informace o konkrétní aspekty při používání Azure AD B2C s knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c26a5007c2dcaa5d41be46f685f0f259866ca2c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544059"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Pomocí MSAL.NET k přihlášení uživatelů se sociálními identitami

Můžete použít k přihlášení uživatelů se sociálními identitami pomocí MSAL.NET [Azure Active Directory B2C (Azure AD B2C)](https://aka.ms/aadb2c). Azure AD B2C je postavené na pojem zásady. MSAL.NET určení zásad přeloží poskytovat autoritu.

- Při vytváření instance aplikace veřejným klientem, budete muset zadat zásady autority.
- Pokud chcete použít zásady, je třeba volat přepsání `AcquireTokenInteractive` obsahující `authority` parametru.

Tato stránka je pro MSAL 3.x. Pokud vás zajímají MSAL 2.x, najdete v [specifika Azure AD B2C v MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorita pro tenanta Azure AD B2C a zásady

Oprávnění k použití je `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` kde:

- `tenant` je název tenanta Azure AD B2C 
- `policyName` Název zásady tak, aby použít (například "b2c_1_susi" pro přihlašování – v registrace/přihlášení).

Aktuální pokyny z Azure AD B2C, je použít `b2clogin.com` jako autoritu. Například, `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Další informace najdete v tomto [dokumentaci](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Vytvoření instance aplikace

Při vytváření aplikace, musíte poskytnout oprávnění.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://fabrikamb2c.b2clogin.com/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Získání tokenu použijte zásady

Získání tokenu pro Azure AD B2C chráněné rozhraní API aplikace veřejným klientem vyžaduje použití přepsání s autoritou sítě:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

textem:

- `policy` právě jeden z předchozích řetězce (například `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)` je metoda, která najde účet pro danou zásadu. Příklad:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Použití zásad (například a umožní koncový uživatel upravovat svůj profil nebo resetování hesla) se momentálně provádí voláním `AcquireTokenInteractive`. V případě tyto dvě zásady, které nepoužíváte vrácený token / dojít k ověření.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Zvláštní případ úprava profilu a ResetPassword zásad

Pokud chcete poskytovat prostředí, které vaši koncoví uživatelé přihlásit pomocí sociálních identit a pak upravte svůj profil chcete použít zásady úprava profilu Azure AD B2C. Způsob, jak to provést, je voláním `AcquireTokenInteractive` s konkrétní oprávnění pro tuto zásadu a výzva nastavení `Prompt.NoPrompt` , aby se účet výběru zobrazení dialogového okna (jak je uživatel již přihlášeni)

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Oprávnění hesla vlastníka (ROPC) prostředku v Azure AD B2C
Podrobné informace o toku ROPC, přečtěte si tento [dokumentaci](v2-oauth-ropc.md).

Tento tok je **ale nedoporučený krok** vzhledem k tomu, že vaše aplikace s žádostí uživatele k zadání hesla není zabezpečený. Další informace o tomto problému najdete v tématu [v tomto článku](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Pomocí uživatelského jména a hesla, můžete se poskytuje up několik věcí:
- základní tenantů moderního identity: získá podléhá heslo, znovu přehrát. Když máme tento koncept tajného kódu sdílené složky, do které může. To není kompatibilní s passwordless.
- Uživatelé, kteří vyžadují vícefaktorové ověřování nebudou můžou přihlašovat (protože není žádná interakce).
- Uživatelé nebudou moct jednotného přihlašování.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Nakonfigurujte tok ROPC v Azure AD B2C
Ve vašem tenantovi Azure AD B2C, vytvořit nový tok uživatele a vybrat **Přihlaste se pomocí ROPC**. Tato možnost povolí zásady ROPC pro vašeho tenanta. Zobrazit [konfigurovat tok přihlašovacího hesla vlastníka prostředku](/azure/active-directory-b2c/configure-ropc) další podrobnosti.

`IPublicClientApplication` obsahuje metodu:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Tato metoda přijímá jako parametry:
- *Obory* požádat o token přístupu.
- A *uživatelské jméno*.
- SecureString *heslo* pro daného uživatele.

Nezapomeňte použít oprávnění, která obsahuje ROPC zásad.

### <a name="limitations-of-the-ropc-flow"></a>Omezení ROPC toku
 - Tok ROPC **funguje jenom pro místní účty** (při registraci v Azure AD B2C pomocí uživatelského jména nebo e-mailu). Tento tok nefunguje, pokud federaci do všech zprostředkovatelů identity, které jsou podporovány službou Azure AD B2C (Facebook, Google, atd.).
 - V současné době neexistuje **žádné id_token vrácená z Azure AD B2C** při implementaci ROPC tok MSAL. To znamená, že se že objekt účtu nelze vytvořit, tak v mezipaměti, bude existovat žádný účet a žádný uživatel. AcquireTokenSilent tok nebude fungovat v tomto scénáři. Však ROPC Nezobrazovat uživatelské rozhraní, takže nebudou jakéhokoli dopadu na činnost koncového uživatele.

## <a name="google-auth-and-embedded-webview"></a>Ověřování Google a vložené Webview

Pokud jste pro vývojáře Azure AD B2C pomocí Google jako zprostředkovatele identity jsme recommand použijete prohlížeč systému, protože Google nepovoluje [ověřování z vložená zobrazení Webview](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). V současné době `login.microsoftonline.com` je důvěryhodné autority službou Google. Pomocí této autority bude fungovat s vložený webview. Nicméně použití `b2clogin.com` není důvěryhodné autority službou Google, takže uživatelé nebudou moct ověřit.

Budeme poskytovat aktualizace na stránkách wiki a to [problém](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) změně věci.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Ukládání do mezipaměti s Azure AD B2C v MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Známý problém s Azure AD B2C

Podporuje MSAL.Net [token mezipaměti](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Ukládání do mezipaměti klíče tokenu je na základě deklarací vrácený zprostředkovatelem Identity. Aktuálně MSAL.Net vyžaduje dvě deklarace identity sestavit mezipaměť tokenu klíč:  
- `tid` což je ID Tenanta služby Azure AD a 
- `preferred_username` 

Obě tyto deklarace nebyly nalezeny v řadě scénářů Azure AD B2C. 

Dopad pro zákazníka je, že při pokusu o zobrazení pole uživatelské jméno, máte k dispozici "Chybí z odpovědi tokenu" jako hodnota? Pokud ano, je to proto, že Azure AD B2C nevrací hodnotu v IdToken pro preferred_username z důvodu omezení účtů v sociálních sítích a externích zprostředkovatelů identity (IDP). Azure AD vrací hodnotu pro preferred_username, protože ví, kdo je uživatel, ale pro Azure AD B2C, protože uživatel se můžete přihlásit pomocí místního účtu, Facebook, Google, Githubu, atd. existuje není konzistentní hodnotu pro Azure AD B2C pro preferred_username. Odblokování MSAL z zavádět mezipaměti kompatibilitu s ADAL, jsme se rozhodli použít "Chybí z odpovědi tokenu" na naší straně při práci s účty Azure AD B2C, když IdToken nic pro preferred_username nespouští. Knihovna MSAL musí vracet hodnotu preferred_username zachovala kompatibilita mezipaměti napříč knihovny.

### <a name="workarounds"></a>Alternativní řešení

#### <a name="mitigation-for-the-missing-tenant-id"></a>Zmírnění dopadů pro chybí ID tenanta

Navrhovaná alternativní řešení, je použít [ukládání do mezipaměti podle zásad](#acquire-a-token-to-apply-a-policy)

Alternativně můžete použít `tid` deklarace identity, pokud používáte [vlastní zásady B2C](https://aka.ms/ief), protože poskytuje schopnost vrátit další deklarace identity do aplikace. Další informace o [transformaci deklarací identity](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Zmírnění dopadů pro "Chybí z odpovědi tokenu"
Jednou z možností je použít deklarace identity "name" jako upřednostňované uživatelské jméno. Proces je uvedený v tomto [B2C doc](/azure/active-directory-b2c/active-directory-b2c-reference-policies#frequently-asked-questions) -> "ve sloupci návratový deklarace identity, zvolte deklarace identit, se mají vracet v autorizačních tokenech odesílaných zpět do aplikace po úspěšném upravování profilu. Vyberte například zobrazované jméno, poštovní směrovací číslo."

## <a name="next-steps"></a>Další postup 

Další podrobnosti o získávání tokenů interaktivně pomocí MSAL.NET pro aplikace Azure AD B2C jsou uvedeny v následující ukázce.

| Ukázka | Platforma | Popis|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin pro iOS, Xamarin pro Android, UPW | Jednoduchá aplikace Xamarin Forms která ukazuje použití MSAL.NET ověřování uživatelů prostřednictvím Azure AD B2C a přístup k webovému rozhraní API s použitím výsledných tokenů.|
