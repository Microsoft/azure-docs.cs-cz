---
title: Azure AD B2C (Microsoft Authentication Library pro .NET) | Azure
description: Přečtěte si o konkrétních otázkách při použití Azure AD B2C s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7444ecfd7a59224d0f08390385c508e4ecc40ddd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532712"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Použití MSAL.NET k přihlašování uživatelů pomocí sociálních identit

Pomocí MSAL.NET [(Azure AD B2C)](https://aka.ms/aadb2c)se můžete přihlásit k uživatelům pomocí sociálních Azure Active Directory B2C identit. Azure AD B2C je vybudována kolem pojmu zásad. V MSAL.NET určení zásady se překládá na poskytování autority.

- Při vytváření instance veřejné klientské aplikace musíte zadat zásadu v autoritě.
- Pokud chcete použít zásadu, je nutné zavolat přepsání `AcquireTokenInteractive` `authority` obsahujícího parametr.

Tato stránka je určena pro MSAL 3. x. Pokud vás zajímá MSAL 2. x, přečtěte si prosím [Azure AD B2C specifické v MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorita pro klienta Azure AD B2C a zásady

Autorita, která se `https://login.microsoftonline.com/tfp/{tenant}/{policyName}` má použít, je zde:

- `tenant`je název tenanta Azure AD B2C, 
- `policyName`Název zásady, která se má použít (například "b2c_1_susi" pro přihlášení nebo přihlášení).

Aktuální pokyny od Azure AD B2C `b2clogin.com` slouží jako autorita. Například, `$"https://{your-tenant-name}.b2clogin.com/tfp/{your-tenant-ID}/{policyname}"`. Další informace najdete v této [dokumentaci](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Vytvoření instance aplikace

Při sestavování aplikace je potřeba zadat autoritu.

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

## <a name="acquire-a-token-to-apply-a-policy"></a>Získání tokenu pro použití zásady

Získání tokenu pro Azure AD B2C Protected API ve veřejné klientské aplikaci vyžaduje, abyste použili přepsání se autoritou:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireToken(scopes, parentWindow)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .ExecuteAsync();
```

textem:

- `policy`jeden z předchozích řetězců (pro instanci `PolicySignUpSignIn`).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`je metoda, která najde účet pro danou zásadu. Příklad:

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

Použití zásad (například umožnění, aby koncový uživatel upravil svůj profil nebo resetování hesla), je aktuálně prováděno `AcquireTokenInteractive`voláním. V případě těchto dvou zásad nepoužívejte výsledek vráceného tokenu nebo ověřování.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Zvláštní případ zásad EditProfile a ResetPassword

Pokud chcete zajistit prostředí, kde se koncoví uživatelé přihlásí pomocí sociální identity, a pak upravte svůj profil, který chcete použít Azure AD B2C zásady EditProfile. To lze provést tak, že zavoláte `AcquireTokenInteractive` se specifickou autoritou pro tuto zásadu a zobrazí se výzva `Prompt.NoPrompt` , aby se zobrazovalo dialogové okno pro výběr účtu (když je uživatel už přihlášený).

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Přihlašovací údaje pro heslo vlastníka prostředku (ROPC) s Azure AD B2C
Další podrobnosti o toku ROPC najdete v této [dokumentaci](v2-oauth-ropc.md).

Tento tok se **nedoporučuje** , protože aplikace, která žádá uživatele o heslo, není zabezpečená. Další informace o tomto problému najdete v [tomto článku](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/). 

Pomocí uživatelského jména a hesla máte k dispozici několik věcí:
- základní klienti moderní identity: heslo se zachová a přehraje. Protože máme tento koncept sdíleného tajného kódu, který se dá zachytit. Nejedná se o nekompatibilní bez hesla.
- Uživatelé, kteří potřebují provést MFA, se nebudou moct přihlásit (protože žádná interakce není).
- Uživatelé nebudou moct provádět jednotné přihlašování.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurace toku ROPC v Azure AD B2C
Ve vašem tenantovi Azure AD B2C vytvořte nový tok uživatelů a vyberte možnost **Přihlásit se pomocí ROPC**. Tím se povolí zásady ROPC pro vašeho tenanta. Další podrobnosti najdete v tématu [Konfigurace toku přihlašovacích údajů pro heslo vlastníka prostředku](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication`obsahuje metodu:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Tato metoda přijímá jako parametry:
- *Obory* pro vyžádání přístupového tokenu pro.
- *Uživatelské jméno*.
- SecureString *heslo* pro uživatele.

Nezapomeňte použít autoritu, která obsahuje zásady ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Omezení toku ROPC
 - Tok ROPC **funguje jenom pro místní účty** (kde se zaregistrujete ve službě Azure AD B2C pomocí e-mailu nebo uživatelského jména). Tento tok nefunguje, pokud federování na kteréhokoli zprostředkovatele identity, který podporuje Azure AD B2C (Facebook, Google atd.).
 - V současné době není Azure AD B2C při implementaci toku ROPC z MSALu **vrácen žádný id_token** . To znamená, že objekt účtu se nedá vytvořit, takže v mezipaměti nebude žádný účet ani uživatel. Tok AcquireTokenSilent nebude v tomto scénáři fungovat. ROPC ale nezobrazuje uživatelské rozhraní, takže nebude mít vliv na činnost koncového uživatele.

## <a name="google-auth-and-embedded-webview"></a>Google auth a vložené WebView

Pokud jste vývojář Azure AD B2C, který používá Google jako poskytovatele identity, provedete to tak, že použijete prohlížeč systému, protože Google neumožňuje [ověřování z vložených](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html)webviews. V současné době je důvěryhodná autorita s Google. `login.microsoftonline.com` Použití této autority bude fungovat s vloženým webviewem. Použití `b2clogin.com` není důvěryhodnou autoritou s Google, takže se uživatelé nebudou moct ověřit.

Pokud se něco změní, budeme na wikiwebu poskytovat aktualizaci wiki a tento [problém](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) .

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Ukládání do mezipaměti s Azure AD B2C v MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Známý problém s Azure AD B2C

MSAL.Net podporuje [mezipaměť tokenů](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Klíč mezipaměti tokenu je založen na deklaracích vrácených zprostředkovatelem identity. V současné době MSAL.Net potřebuje dvě deklarace identity pro sestavení klíče mezipaměti tokenu:  
- `tid`což je ID tenanta Azure AD a 
- `preferred_username` 

V mnoha scénářích Azure AD B2C chybí obě tyto deklarace identity. 

Dopadem na zákazníky je to, že při pokusu o zobrazení pole s uživatelským jménem se jako hodnota zobrazí zpráva "chybí odpověď na token"? Pokud ano, je to proto, že Azure AD B2C nevrací hodnotu IdToken pro preferred_username z důvodu omezení s účty sociálních sítí a externími zprostředkovateli identity (zprostředkovatelů identity). Azure AD vrátí hodnotu pro preferred_username, protože ví, kdo je, ale pro Azure AD B2C, protože se uživatel může přihlásit pomocí místního účtu, Facebooku, Google, GitHubu atd. není konzistentní hodnota pro Azure AD B2C, která se má použít pro preferred_username. Pokud chcete odblokovat MSAL z zavedení kompatibility mezipaměti pomocí ADAL, rozhodli jsme se na našem konci použít "chybějící v odpovědi na token", a to při práci s účty Azure AD B2C, když IdToken vrátí hodnotu Nothing pro preferred_username. MSAL musí vracet hodnotu pro preferred_username, aby se zachovala kompatibilita mezipaměti napříč knihovnami.

### <a name="workarounds"></a>Alternativní řešení

#### <a name="mitigation-for-the-missing-tenant-id"></a>Zmírnění omezení pro chybějící ID tenanta

Doporučeným řešením je použití [zásad ukládání do mezipaměti podle zásad](#acquire-a-token-to-apply-a-policy)

Alternativně můžete použít `tid` deklaraci identity, pokud používáte [vlastní zásady B2C](https://aka.ms/ief), protože poskytují možnost vracet do aplikace další deklarace identity. Další informace o [transformaci deklarací identity](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Zmírnění omezení u možnosti chybějící v odpovědi na token
Jednou z možností je použít jako preferované uživatelské jméno deklaraci identity "Name". Tento proces se zmiňuje v tomto [dokumentu B2C doc](../../active-directory-b2c/active-directory-b2c-reference-policies.md) -> ve sloupci návratová deklarace vyberte deklarace identity, které se mají vrátit v autorizačních tokenech odesílaných zpět do aplikace po úspěšném prostředí pro úpravu profilu. Vyberte například zobrazované jméno, PSČ.

## <a name="next-steps"></a>Další kroky 

Další podrobnosti o interaktivním získání tokenů pomocí MSAL.NET pro Azure AD B2C aplikace jsou k dispozici v následující ukázce.

| Ukázka | Platforma | Popis|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Jednoduchá aplikace Xamarin Forms předvádí, jak používat MSAL.NET k ověřování uživatelů prostřednictvím Azure AD B2C a přístup k webovému rozhraní API s výslednými tokeny.|
