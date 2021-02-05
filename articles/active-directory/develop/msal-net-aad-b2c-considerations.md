---
title: Azure AD B2C a MSAL.NET
titleSuffix: Microsoft identity platform
description: Co je třeba zvážit při použití Azure AD B2C s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2020
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: b28454e9b60654541d4f62ec1d8455b30cfc2906
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580823"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Použití MSAL.NET k přihlašování uživatelů pomocí sociálních identit

Pomocí MSAL.NET [(Azure AD B2C)](../../active-directory-b2c/overview.md)se můžete přihlásit k uživatelům pomocí sociálních Azure Active Directory B2C identit. Azure AD B2C je vybudována kolem pojmu zásad. V MSAL.NET určení zásady se překládá na poskytování autority.

- Při vytváření instance veřejné klientské aplikace určete zásadu v rámci autority.
- Pokud chcete použít zásadu, zavolejte přepsání `AcquireTokenInteractive` , které přijímá `authority` parametr.

Tento článek se týká MSAL.NET 3. x. Pro MSAL.NET 2. x si přečtěte část [Azure AD B2C specifické v MSAL 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x) na wikiwebu MSAL.NET na GitHubu.

## <a name="authority-for-an-azure-ad-b2c-tenant-and-policy"></a>Autorita pro klienta Azure AD B2C a zásady

Formát autority pro Azure AD B2C: `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}`

- `azureADB2CHostname` – Název klienta Azure AD B2C a hostitele. Například *contosob2c.b2clogin.com*.
- `tenant` – Název domény nebo adresář (tenant) ID klienta Azure AD B2C. Například *contosob2c.onmicrosoft.com* nebo GUID, v uvedeném pořadí.
- `policyName` – Název toku uživatele nebo vlastní zásady, které se mají použít. Například zásady registrace a přihlašování jako *b2c_1_susi*.

Další informace o Azure AD B2C autoritách najdete v tématu [nastavení adres URL pro přesměrování na b2clogin.com](../../active-directory-b2c/b2clogin.md).

## <a name="instantiating-the-application"></a>Vytvoření instance aplikace

Poskytněte autoritu voláním `WithB2CAuthority()` při vytváření objektu aplikace:

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

Získání tokenu pro rozhraní API chráněného Azure AD B2C ve veřejné klientské aplikaci vyžaduje použití přepsání se autoritou:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application.AcquireTokenInteractive(scopes)
                                           .WithAccount(GetAccountByPolicy(accounts, policy))
                                           .WithParentActivityOrWindow(ParentActivityOrWindow)
                                           .ExecuteAsync();
```

V předchozím fragmentu kódu:

- `policy` je řetězec obsahující název Azure AD B2C toku uživatele nebo vlastní zásady (například `PolicySignUpSignIn` ).
- `ParentActivityOrWindow` vyžaduje se pro Android (aktivita) a je volitelný pro jiné platformy, které podporují nadřazené uživatelské rozhraní, jako je Windows v Microsoft Windows a UIViewController v iOS. Další informace o dialogovém okně uživatelského rozhraní najdete v tématu [WithParentActivityOrWindow](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow) na wikiwebu MSAL.
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

Použití toku uživatele nebo vlastní zásady (například umožnění úprav svého profilu nebo resetování hesla uživatelem) se právě provádí voláním `AcquireTokenInteractive` . U těchto dvou zásad nepoužíváte vrácený výsledek tokenu/ověřování.

## <a name="profile-edit-policies"></a>Zásady úprav profilu

Pokud chcete uživatelům umožnit, aby se přihlásili pomocí sociální identity a pak upravili svůj profil, použijte Azure AD B2C upravit zásadu profilu.

Uděláte to tak, že zavoláte `AcquireTokenInteractive` se autoritou pro tuto zásadu. Vzhledem k tomu, že je uživatel již přihlášen a má aktivní relaci souborů cookie, použijte `Prompt.NoPrompt` k zabránění zobrazení dialogu pro výběr účtu.

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
    }
}
```

## <a name="resource-owner-password-credentials-ropc"></a>Přihlašovací údaje pro heslo vlastníka prostředku (ROPC)

Další informace o toku ROPC najdete v tématu [přihlášení pomocí přihlašovacích údajů pro heslo vlastníka prostředku](v2-oauth-ropc.md).

Tok ROPC se **nedoporučuje** , protože dotazování uživatele na heslo v aplikaci není zabezpečené. Další informace o tomto problému najdete v tématu [co je řešení rostoucího problému s hesly?](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/).

Pomocí uživatelského jména a hesla v toku ROPC jste si zarovnali několik věcí:

- Základní principy moderní identity: heslo se dá lovit nebo přehrávat, protože se dá zachytit sdílený tajný klíč. Podle definice ROPC je nekompatibilní s toky bez hesla.
- Uživatelé, kteří potřebují provést MFA, se nebudou moct přihlásit (protože žádná interakce není).
- Uživatelé nebudou moct používat jednotné přihlašování (SSO).

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>Konfigurace toku ROPC v Azure AD B2C

V tenantovi Azure AD B2C vytvořte nový tok uživatelů a vyberte možnost přihlásit se **pomocí ROPC** a povolte ROPC toku uživatele. Další informace najdete v tématu [Konfigurace toku přihlašovacích údajů pro heslo vlastníka prostředku](../../active-directory-b2c/add-ropc-policy.md).

`IPublicClientApplication` obsahuje `AcquireTokenByUsernamePassword` metodu:

```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Tato `AcquireTokenByUsernamePassword` Metoda používá následující parametry:

- *Obory* , pro které se má získat přístupový token
- *Uživatelské jméno*.
- SecureString *heslo* pro uživatele.

### <a name="limitations-of-the-ropc-flow"></a>Omezení toku ROPC

Tok ROPC **funguje jenom pro místní účty**, kde se uživatelé zaregistrovali s Azure AD B2C pomocí e-mailové adresy nebo uživatelského jména. Tento tok nefunguje, když federování na externího zprostředkovatele identity, který podporuje Azure AD B2C (Facebook, Google atd.).

## <a name="google-auth-and-embedded-webview"></a>Google auth a vložené WebView

Pokud používáte Google jako poskytovatele identity, doporučujeme použít prohlížeč systému, protože Google neumožňuje [ověřování z vložených WebView](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). V současné době `login.microsoftonline.com` je důvěryhodná autorita s Google a bude fungovat s vloženým webviewem. Nejedná `b2clogin.com` se ale o důvěryhodnou autoritu s Google, takže se uživatelé nebudou moct ověřit.

Pokud se něco změní, poskytneme vám aktualizaci tohoto [problému](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) .

## <a name="token-caching-in-msalnet"></a>Ukládání tokenů do mezipaměti v MSAL.NET

### <a name="known-issue-with-azure-ad-b2c"></a>Známý problém s Azure AD B2C

MSAL.NET podporuje [mezipaměť tokenů](/dotnet/api/microsoft.identity.client.tokencache). Klíč mezipaměti tokenu je založen na deklaracích vrácených zprostředkovatelem identity (IdP).

V současné době MSAL.NET potřebuje ke sestavení klíče mezipaměti tokenu dvě deklarace identity:

- `tid` (ID tenanta Azure AD)
- `preferred_username`

Obě tyto deklarace můžou chybět v Azure AD B2C scénářích, protože ne všichni poskytovatelé sociálních identit (Facebook, Google a další) je vrátí v tokenech, které vrátí do Azure AD B2C.

Příznakem takového scénáře je, že MSAL.NET vrací `Missing from the token response` přístup k `preferred_username` hodnotě deklarace identity v tokenech vydaných Azure AD B2C. MSAL používá `Missing from the token response` hodnotu pro `preferred_username` zajištění mezikompatibility mezipaměti mezi knihovnami.

### <a name="workarounds"></a>Alternativní řešení

#### <a name="mitigation-for-missing-tenant-id"></a>Zmírnění omezení chybějícího ID tenanta

Alternativním řešením je použít [ukládání do mezipaměti podle zásad](#acquire-a-token-to-apply-a-policy) popsaných výše.

Případně můžete použít `tid` deklaraci identity, pokud používáte [vlastní zásady](../../active-directory-b2c/custom-policy-get-started.md) v Azure AD B2C. Vlastní zásady mohou vracet další deklarace identity do aplikace pomocí [transformace deklarací identity](../../active-directory-b2c/claims-transformation-technical-profile.md).

#### <a name="mitigation-for-missing-from-the-token-response"></a>Zmírnění omezení u možnosti chybějící v odpovědi na token

Jednou z možností je použít `name` deklaraci identity místo `preferred_username` . Pokud chcete zahrnout `name` deklaraci identity v tokenech ID vydaných Azure AD B2C, vyberte při konfiguraci toku uživatele možnost **Zobrazit název** .

Další informace o určení, které deklarace identity vrátí vaše uživatelské toky, najdete v tématu [kurz: vytvoření toků uživatelů v Azure AD B2C](../../active-directory-b2c/tutorial-create-user-flows.md).

## <a name="next-steps"></a>Další kroky

Další podrobnosti o interaktivním získání tokenů pomocí MSAL.NET pro Azure AD B2C aplikace jsou k dispozici v následující ukázce.

| Ukázka | Platforma | Description|
|------ | -------- | -----------|
|[Active-Directory-B2C-Xamarin-Native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | Aplikace Xamarin Forms, která používá MSAL.NET k ověřování uživatelů prostřednictvím Azure AD B2C a přístup k webovému rozhraní API s vrácenými tokeny.|
