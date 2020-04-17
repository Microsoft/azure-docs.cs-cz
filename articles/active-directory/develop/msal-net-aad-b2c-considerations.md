---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních aspektech při používání Azure AD B2C s Knihovnou ověřování Microsoftu pro .NET (MSAL.NET).
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
ms.openlocfilehash: d31cf3a4e024dc59b865d096cbd0829d50f61a1a
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81533951"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Použití MSAL.NET k přihlášení uživatelů se sociální identitou

Pomocí MSAL.NET můžete přihlašovat uživatele pomocí sociálních identit pomocí [Služby Azure Active Directory B2C (Azure AD B2C).](https://aka.ms/aadb2c) Azure AD B2C je postavenna na pojetí zásad. V MSAL.NET se určení zásady promítá do poskytování oprávnění.

- Při vytváření instanitu veřejné klientské aplikace, je třeba zadat zásady v autoritě.
- Chcete-li použít zásadu, musíte volat přepsání `AcquireTokenInteractive` obsahující `authority` ho parametr.

Tato stránka je pro MSAL 3.x. Pokud máte zájem o MSAL 2.x, naleznete [v tématu Azure AD B2C specifika v MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autorita pro klienta Azure AD B2C a zásady

Používá se jako `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` příslušný orgán, kde:

- `azureADB2CHostname`je název klienta Azure AD B2C plus `{your-tenant-name}.b2clogin.com`hostitele (například ),
- `tenant`je úplný název klienta Azure AD B2C `{your-tenant-name}.onmicrosoft.com`(například) nebo identifikátoru GUID pro klienta,
- `policyName`název zásady nebo toku uživatele použít (například "b2c_1_susi" pro přihlášení nebo přihlášení).

Další informace o autoritách Azure AD B2C naleznete v této [dokumentaci](/azure/active-directory-b2c/b2clogin).

## <a name="instantiating-the-application"></a>Vytvoření instance aplikace

Při vytváření aplikace je třeba poskytnout oprávnění.

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

Získání tokenu pro rozhraní API chráněné ho Azure AD B2C ve veřejné klientské aplikaci vyžaduje použití přepsání s autoritou:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

textem:

- `policy`jedním z předchozích řetězců (například). `PolicySignUpSignIn`
- `ParentActivityOrWindow`je vyžadována pro Android (Aktivita) a volitelná pro jiné platformy, které podporují nadřazené ui, jako jsou windows v systému Windows a UIViewController v systému iOS. Další informace naleznete [zde v dialogovém okně ui](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`je metoda, která vyhledá účet pro danou zásadu. Příklad:

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

Použití zásad nebo toku uživatele (například umožnění koncovému uživateli upravit jeho `AcquireTokenInteractive`profil nebo resetovat heslo) se v současné době provádí voláním . V případě těchto dvou zásad nepoužíváte vrácený token / výsledek ověřování.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Zvláštní případ zásad EditProfile a ResetPassword

Pokud chcete poskytnout prostředí, kde se vaši koncoví uživatelé přihlašují pomocí sociální identity a pak upravují svůj profil, chcete použít zásady upravit profil Azure AD B2C. Způsob, jak to provést, je volání `AcquireTokenInteractive` s konkrétní autoritou pro `Prompt.NoPrompt` tuto zásadu a výzvu nastavit, aby se zabránilo zobrazení dialogového okna výběru účtu (jako uživatel je již přihlášen a má aktivní cookie relace).

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
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Přihlašovací údaje pro heslo vlastníka prostředků (ROPC) s Azure AD B2C
Další podrobnosti o toku ROPC naleznete v této [dokumentaci](v2-oauth-ropc.md).

Tento tok **se nedoporučuje,** protože vaše aplikace žádají uživatele o jeho heslo není zabezpečená. Další informace o tomto problému naleznete v [tomto článku](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Pomocí uživatelského jména / hesla se vzdáváte řady věcí:
- Základní principy moderní identity: heslo se vyloví, přehraje. Protože máme koncept sdíleného tajemství, které může být zachyceno. To je nekompatibilní s bez hesla.
- Uživatelé, kteří potřebují provádět vícefaktorové hodnocení, se nebudou moci přihlásit (protože neexistuje žádná interakce).
- Uživatelé nebudou moci provést jednotné přihlášení.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurace toku ROPC v Azure AD B2C
V tenantovi Azure AD B2C vytvořte nový tok uživatelů a **vyberte Přihlásit se pomocí ROPC**. To umožní zásady ROPC pro vašeho tenanta. Další [podrobnosti najdete v tématu Konfigurace toku přihlašovacích údajů vlastníka prostředku.](/azure/active-directory-b2c/configure-ropc)

`IPublicClientApplication`obsahuje metodu:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Tato metoda bere jako parametry:
- *Obory,* pro které chcete požádat o přístupový token.
- *Uživatelské jméno*.
- SecureString *heslo* pro uživatele.

Nezapomeňte použít autoritu, která obsahuje zásady ROPC.

### <a name="limitations-of-the-ropc-flow"></a>Omezení toku ROPC
 - Tok ROPC **funguje jenom pro místní účty** (kde se zaregistrujete s Azure AD B2C pomocí e-mailu nebo uživatelského jména). Tento tok nefunguje, pokud federating na některý z poskytovatelů identity podporované Azure AD B2C (Facebook, Google, atd.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth a embedded Webview

Pokud jste vývojář Azure AD B2C, který používá Google jako poskytovatele identity, doporučujeme vám používat systémový prohlížeč, protože Google nepovoluje [ověřování z vložených webových zobrazení](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). V současné `login.microsoftonline.com` době je důvěryhodný orgán s Google. Použití tohoto oprávnění bude fungovat s vloženým webovým zobrazením. Použití `b2clogin.com` však není důvěryhodnou autoritou společnosti Google, takže uživatelé nebudou moci ověřit.

Pokud se věci změní, poskytneme aktualizaci tohoto [problému.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688)

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Ukládání do mezipaměti s Azure AD B2C v MSAL.Net

### <a name="known-issue-with-azure-ad-b2c"></a>Známý problém s Azure AD B2C

MSAL.Net podporuje [mezipaměť tokenů](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet). Klíč mezipaměti tokenu je založen na deklaracích vrácených zprostředkovatelem identity. V současné době MSAL.Net potřebuje dva deklarace identity k vytvoření klíče mezipaměti tokenu:
- `tid`což je ID klienta Azure AD a
- `preferred_username`

Obě tyto deklarace identity chybí v mnoha scénářích Azure AD B2C.

Dopad na zákazníka je, že při pokusu o zobrazení pole uživatelského jména, jsou získáváte "Chybí z odpovědi tokenu" jako hodnotu? Pokud ano, je to proto, že Azure AD B2C nevrátí hodnotu v IdToken pro preferred_username z důvodu omezení s účty sociální ch a externí zprostředkovatelé identity (IdPs). Azure AD vrátí hodnotu pro preferred_username protože ví, kdo je uživatel, ale pro Azure AD B2C, protože uživatel můžete přihlásit pomocí místního účtu, Facebook, Google, GitHub preferred_username, atd. Chcete-li odblokovat MSAL z zavádění mezipaměti kompatibility s ADAL, rozhodli jsme se použít "Chybějící z odpovědi tokenu" na naší straně při práci s účty Azure AD B2C při IdToken vrátí nic pro preferred_username. MSAL musí vrátit hodnotu pro preferred_username zachovat kompatibilitu mezipaměti mezi knihovnami.

### <a name="workarounds"></a>Alternativní řešení

#### <a name="mitigation-for-the-missing-tenant-id"></a>Zmírnění pro chybějící ID klienta

Navrhované řešení je použití ukládání do [mezipaměti podle zásad](#acquire-a-token-to-apply-a-policy)

Případně můžete použít deklaraci identity, `tid` pokud používáte vlastní [zásady B2C](https://aka.ms/ief), protože poskytuje možnost vrátit další deklarace identity do aplikace. Další informace o [transformaci deklarací identity](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Zmírnění pro "Chybějící z odpovědi tokenu"
Jednou z možností je použít "jméno" tvrzení jako upřednostňované uživatelské jméno. Proces je uveden v tomto [B2C doc](../../active-directory-b2c/user-flow-overview.md) -> "Ve sloupci Nárok na vrácení zvolte deklarace identity, které chcete vrátit v tokenech autorizace odeslaných zpět do vaší aplikace po úspěšném prostředí pro úpravu profilu. Vyberte například Zobrazované jméno, PSČ."

## <a name="next-steps"></a>Další kroky

Další podrobnosti o získávání tokenů interaktivně s MSAL.NET pro aplikace Azure AD B2C jsou k dispozici v následující ukázce.

| Ukázka | Platforma | Popis|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-nativní](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UPW | Jednoduchá aplikace Xamarin Forms, která ukazuje, jak používat MSAL.NET k ověřování uživatelů prostřednictvím Azure AD B2C a přístupu k webovému rozhraní API s výslednými tokeny.|
