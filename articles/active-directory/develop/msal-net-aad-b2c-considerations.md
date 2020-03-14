---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních otázkách při použití Azure AD B2C s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262812"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Použití MSAL.NET k přihlašování uživatelů pomocí sociálních identit

Pomocí MSAL.NET [(Azure AD B2C)](https://aka.ms/aadb2c)se můžete přihlásit k uživatelům pomocí sociálních Azure Active Directory B2C identit. Azure AD B2C je vybudována kolem pojmu zásad. V MSAL.NET určení zásady se překládá na poskytování autority.

- Při vytváření instance veřejné klientské aplikace musíte zadat zásadu v autoritě.
- Pokud chcete použít zásadu, je nutné volat přepsání `AcquireTokenInteractive` obsahujícího parametr `authority`.

Tato stránka je určena pro MSAL 3. x. Pokud vás zajímá MSAL 2. x, přečtěte si prosím [Azure AD B2C specifické v MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorita pro klienta Azure AD B2C a zásady

Autorita, která se má použít, je `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`, kde:

- `azureADB2CHostname` je název tenanta Azure AD B2C a hostitele (například `{your-tenant-name}.b2clogin.com`),
- `tenant` je úplný název tenanta Azure AD B2C (například `{your-tenant-name}.onmicrosoft.com`) nebo identifikátor GUID klienta. 
- `policyName` název zásady nebo toku uživatele, který se má použít (například "b2c_1_susi" pro registraci a přihlášení).

Další informace o Azure AD B2C autoritách najdete v této [dokumentaci](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Vytvoření instance aplikace

Při sestavování aplikace je potřeba zadat autoritu.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
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
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

textem:

- `policy` je jeden z předchozích řetězců (pro `PolicySignUpSignIn`instance).
- pro Android (aktivita) se vyžaduje `ParentActivityOrWindow` a volitelné pro jiné platformy, které podporují nadřazené uživatelské rozhraní, jako je Windows v systému Windows a UIViewController v iOS. Další informace najdete [v dialogovém okně uživatelského rozhraní](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
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

Použití zásad nebo toku uživatele (například umožnění, aby koncový uživatel upravil svůj profil nebo resetování hesla), je v současné době proveden voláním `AcquireTokenInteractive`. V případě těchto dvou zásad nepoužívejte výsledek vráceného tokenu nebo ověřování.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Zvláštní případ zásad EditProfile a ResetPassword

Pokud chcete zajistit, aby se koncoví uživatelé přihlásili pomocí sociální identity a pak upravili svůj profil, chcete použít Azure AD B2C upravit zásadu profilu. To můžete udělat tak, že zavoláte `AcquireTokenInteractive` se specifickou autoritou pro danou zásadu a zobrazí se výzva nastavená na `Prompt.NoPrompt`, aby se zabránilo zobrazení dialogu pro výběr účtu (když je uživatel už přihlášený a má aktivní relaci souborů cookie).

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

Pomocí uživatelského jména a hesla získáte několik věcí:
- Základní principy moderní identity: heslo se zachová a přehraje. Protože máme tento koncept sdíleného tajného kódu, který se dá zachytit. Nejedná se o nekompatibilní bez hesla.
- Uživatelé, kteří potřebují provést MFA, se nebudou moct přihlásit (protože žádná interakce není).
- Uživatelé nebudou moct provádět jednotné přihlašování.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurace toku ROPC v Azure AD B2C
Ve vašem tenantovi Azure AD B2C vytvořte nový tok uživatelů a vyberte možnost **Přihlásit se pomocí ROPC**. Tím se povolí zásady ROPC pro vašeho tenanta. Další podrobnosti najdete v tématu [Konfigurace toku přihlašovacích údajů pro heslo vlastníka prostředku](/azure/active-directory-b2c/configure-ropc) .

`IPublicClientApplication` obsahuje metodu:
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

## <a name="google-auth-and-embedded-webview"></a>Google auth a vložené WebView

Pokud jste vývojář Azure AD B2C, který používá Google jako poskytovatele identity, provedete to tak, že použijete prohlížeč systému, protože Google neumožňuje [ověřování z vložených webviews](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). V současné době je `login.microsoftonline.com` důvěryhodnou autoritou s Google. Použití této autority bude fungovat s vloženým webviewem. Použití `b2clogin.com` ale není důvěryhodnou autoritou s Google, takže se uživatelé nebudou moct ověřit.

Pokud dojde ke změně, poskytneme vám aktualizaci tohoto [problému](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) .

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Ukládání do mezipaměti s Azure AD B2C v MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Známý problém s Azure AD B2C

MSAL.Net podporuje [mezipaměť tokenů](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Klíč mezipaměti tokenu je založen na deklaracích vrácených zprostředkovatelem identity. V současné době MSAL.Net potřebuje dvě deklarace identity pro sestavení klíče mezipaměti tokenu:  
- `tid`, což je ID tenanta služby Azure AD a 
- `preferred_username` 

V mnoha scénářích Azure AD B2C chybí obě tyto deklarace identity. 

Dopadem na zákazníky je to, že při pokusu o zobrazení pole s uživatelským jménem se jako hodnota zobrazí zpráva "chybí odpověď na token"? Pokud ano, je to proto, že Azure AD B2C nevrací hodnotu v IdToken pro preferred_username z důvodu omezení s účty sociálních sítí a externími zprostředkovateli identity (zprostředkovatelů identity). Azure AD vrátí hodnotu pro preferred_username, protože ví, kdo je, ale pro Azure AD B2C, protože se uživatel může přihlásit pomocí místního účtu, Facebooku, Google, GitHubu atd. není konzistentní hodnota pro Azure AD B2C, která se má použít pro preferred_username. Pokud chcete odblokovat MSAL z zavedení kompatibility mezipaměti pomocí ADAL, rozhodli jsme se na našem konci použít "chybějící v odpovědi na token", a to při práci s účty Azure AD B2C, když IdToken vrátí hodnotu Nothing pro preferred_username. MSAL musí vracet hodnotu, aby preferred_username zachovalo kompatibilitu mezipaměti napříč knihovnami.

### <a name="workarounds"></a>Alternativní řešení

#### <a name="mitigation-for-the-missing-tenant-id"></a>Zmírnění omezení pro chybějící ID tenanta

Doporučeným řešením je použití [zásad ukládání do mezipaměti podle zásad](#acquire-a-token-to-apply-a-policy)

Alternativně můžete použít `tid` deklarace identity, pokud používáte [vlastní zásady B2C](https://aka.ms/ief), protože poskytují možnost vracet do aplikace další deklarace identity. Další informace o [transformaci deklarací identity](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Zmírnění omezení u možnosti chybějící v odpovědi na token
Jednou z možností je použít jako preferované uživatelské jméno deklaraci identity "Name". Tento proces se zmiňuje v tomto [dokumentu B2C doc](../../active-directory-b2c/user-flow-overview.md) -> ve sloupci návratová deklarace vyberte deklarace identity, které se mají vrátit v autorizačních tokenech odesílaných zpět do aplikace po úspěšném prostředí pro úpravu profilu. Vyberte například zobrazované jméno, PSČ.

## <a name="next-steps"></a>Další kroky 

Další podrobnosti o interaktivním získání tokenů pomocí MSAL.NET pro Azure AD B2C aplikace jsou k dispozici v následující ukázce.

| Ukázka | Platforma | Popis|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Jednoduchá aplikace Xamarin Forms předvádí, jak používat MSAL.NET k ověřování uživatelů prostřednictvím Azure AD B2C a přístup k webovému rozhraní API s výslednými tokeny.|
