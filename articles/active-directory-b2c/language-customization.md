---
title: Přizpůsobení jazyka v Azure Active Directory B2C
description: Přečtěte si, jak přizpůsobit prostředí jazyka v uživatelském toku v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e29ef68bb6f02e329d0406cd80dceb25beab8932
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645904"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Přizpůsobení jazyka v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Přizpůsobení jazyka v Azure Active Directory B2C (Azure AD B2C) umožňuje, aby tok uživatelů vyhovoval různým jazykům, aby vyhovoval vašim potřebám zákazníků. Společnost Microsoft poskytuje překlady pro [jazyky 36](#supported-languages), ale můžete také zadat vlastní překlady pro libovolný jazyk. I v případě, že je vaše prostředí k dispozici pouze pro jeden jazyk, můžete přizpůsobit libovolný text na stránkách.

## <a name="how-language-customization-works"></a>Jak funguje přizpůsobení jazyka

Pomocí vlastního nastavení jazyka můžete vybrat jazyky, ve kterých je tok uživatele k dispozici. Po povolení funkce můžete zadat parametr řetězce dotazu, `ui_locales` z vaší aplikace. Když zavoláte Azure AD B2C, vaše stránka je přeložena do národního prostředí, které jste uvedli. Tento typ konfigurace poskytuje plnou kontrolu nad jazyky v uživatelském toku a ignoruje nastavení jazyka v prohlížeči zákazníka.

Možná nebudete potřebovat tuto úroveň kontroly nad jazyky, které zákazník uvidí. Pokud neposkytnete `ui_locales` parametr, zkušenost zákazníka je vydiktována nastavením prohlížeče. Můžete si i nadále řídit, na které jazyky se má tok uživatele přeložit, a to tak, že ho přidáte jako podporovaný jazyk. Pokud je prohlížeč zákazníka nastavený tak, aby zobrazoval jazyk, který nechcete podporovat, zobrazí se místo toho jazyk, který jste vybrali jako výchozí v podporovaných jazykových verzích.

* **jazykově definované národní prostředí**: po povolení přizpůsobení jazyka se tok uživatele převede do jazyka, který je zde určen.
* **Požadovaný jazyk v prohlížeči**: Pokud není `ui_locales` zadaný žádný parametr, tok uživatele se převede na jazyk požadovaný prohlížečem, *Pokud je podporovaný jazyk*.
* **Výchozí jazyk zásad**: Pokud prohlížeč neurčí jazyk, nebo neurčí, který není podporovaný, tok uživatele se převede do výchozího jazyka toku uživatele.

> [!NOTE]
> Pokud používáte vlastní atributy uživatele, je třeba zadat vlastní překlady. Další informace najdete v tématu [přizpůsobení řetězců](#customize-your-strings).

::: zone pivot="b2c-custom-policy"

Lokalizace vyžaduje tři kroky: 

1. Nastavení explicitního seznamu podporovaných jazyků
1. Poskytování řetězců a kolekcí specifických pro jazyk
1. Upravte [definici obsahu](contentdefinitions.md) stránky. 

::: zone-end 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>Podpora požadovaných jazyků pro ui_locales

Zásady, které byly vytvořené před všeobecnou dostupností přizpůsobení jazyka, musí nejdřív tuto funkci povolit. Zásady a uživatelské toky, které se vytvořily po nastavení jazyka, jsou ve výchozím nastavení povolené.

Když v toku uživatele povolíte přizpůsobení jazyka, můžete řídit jazyk toku uživatele přidáním `ui_locales` parametru.

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, u kterého chcete povolit překlady.
1. Vyberte **jazyky**.
1. Vyberte možnost **Povolit přizpůsobení jazyka**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Vyberte jazyky, ve kterých je tok uživatele povolený.

Povolte sadu jazyků pro váš uživatelský tok, který se bude překládat na v případě, že ho prohlížeč požaduje bez `ui_locales` parametru.

1. Ujistěte se, že tok uživatele má povolené přizpůsobení jazyka z předchozích pokynů.
1. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete podporovat.
1. V podokně Vlastnosti změňte možnost **povoleno** na **Ano**.
1. V horní části podokna Vlastnosti vyberte **Uložit** .

>[!NOTE]
>Pokud `ui_locales` parametr není zadán, bude stránka přeložena do jazyka prohlížeče zákazníka pouze v případě, že je povolena.
>

## <a name="customize-your-strings"></a>Přizpůsobení řetězců

Přizpůsobení jazyka umožňuje přizpůsobení libovolného řetězce v toku uživatele.

1. Ujistěte se, že uživatelský tok má v předchozích pokynech povolené přizpůsobení jazyka.
1. Na stránce **jazyky** toku uživatele vyberte jazyk, který chcete přizpůsobit.
1. V části **soubory prostředků na úrovni stránky** vyberte stránku, kterou chcete upravit.
1. Vyberte možnost **Stáhnout výchozí** (nebo **Stáhnout přepsání** , pokud jste tento jazyk už dříve upravovali).

Tyto kroky poskytují soubor JSON, který můžete použít k zahájení úprav řetězců.

### <a name="change-any-string-on-the-page"></a>Změna libovolného řetězce na stránce

1. Otevřete soubor JSON stažený z předchozích pokynů v editoru JSON.
1. Vyhledejte prvek, který chcete změnit. Můžete vyhledat `StringId` řetězec, který hledáte, nebo vyhledat `Value` atribut, který chcete změnit.
1. Aktualizujte `Value` atribut tak, co chcete zobrazit.
1. Pro každý řetězec, který chcete změnit, změňte `Override` na `true` .
1. Uložte soubor a nahrajte změny. (Ovládací prvek nahrávání můžete najít na stejném místě, kam jste stáhli soubor JSON.)

> [!IMPORTANT]
> Pokud potřebujete přepsat řetězec, ujistěte se, že jste nastavili `Override` hodnotu na `true` . Pokud se hodnota nezmění, bude položka ignorována.

### <a name="change-extension-attributes"></a>Změnit atributy rozšíření

Pokud chcete změnit řetězec pro vlastní atribut uživatele nebo ho chcete přidat k formátu JSON, je v následujícím formátu:

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

Nahraďte `<ExtensionAttribute>` názvem vlastního atributu uživatele.

Nahraďte `<ExtensionAttributeValue>` novým řetězcem, který se má zobrazit.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Zadání seznamu hodnot pomocí LocalizedCollections

Pokud chcete poskytnout seznam sad hodnot pro odpovědi, je nutné vytvořit `LocalizedCollections` atribut. `LocalizedCollections` je pole `Name` a `Value` páry. Pořadí položek bude zobrazeno v pořadí. Chcete-li přidat `LocalizedCollections` , použijte následující formát:

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId` je atributem uživatele, `LocalizedCollections` na který je tento atribut odpovědí.
* `Name` je hodnota, která se zobrazí uživateli.
* `Value` je to, co se v deklaraci identity vrátí, když je tato možnost vybraná.

### <a name="upload-your-changes"></a>Nahrání změn

1. Po dokončení změn souboru JSON se vraťte k B2C tenantovi.
1. Vyberte **toky uživatelů** a klikněte na tok uživatele, u kterého chcete povolit překlady.
1. Vyberte **jazyky**.
1. Vyberte jazyk, na který chcete překládat.
1. Vyberte stránku, kde chcete zadat překlady.
1. Vyberte ikonu složky a vyberte soubor JSON, který se má nahrát.

Změny se uloží do toku uživatele automaticky.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Přizpůsobení uživatelského rozhraní stránky pomocí přizpůsobení jazyka

[Obsah HTML](customize-ui-with-html.md)můžete lokalizovat dvěma způsoby. Jedním ze způsobů je zapnout [přizpůsobení jazyka](language-customization.md). Povolením této funkce umožníte Azure AD B2C přeslat parametr OpenID Connect `ui-locales` do svého koncového bodu. Server obsahu může tento parametr použít k poskytování přizpůsobených stránek HTML, které jsou specifické pro konkrétní jazyk.

Alternativně můžete vyžádat obsah z různých míst na základě používaného národního prostředí. V rámci koncového bodu s povoleným CORS můžete nastavit strukturu složky pro hostování obsahu pro konkrétní jazyky. Pokud použijete zástupnou hodnotu, zavoláte tu správnou `{Culture:RFC5646}` . Předpokládejme například, že se jedná o vlastní identifikátor URI stránky:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Můžete načíst stránku v `fr` . Když stránka vyžádá obsah HTML a CSS, stáhne se z:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Přidat vlastní jazyky

Můžete také přidat jazyky, které Microsoft v současné době neposkytuje překlady pro. Budete muset zadat překlady pro všechny řetězce v toku uživatele. Kódy jazyků a kódů národního prostředí jsou omezené na ty, které jsou ve standardu ISO 639-1. Formát kódu národního prostředí by měl být například "ISO_639-1_code"-"CountryCode" `en-GB` . Další informace o formátech ID národního prostředí najdete v tématu. https://docs.microsoft.com/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
2. Klikněte na tok uživatele, do kterého chcete přidat vlastní jazyky, a pak klikněte na **jazyky**.
3. V horní části stránky vyberte **Přidat vlastní jazyk** .
4. V kontextovém podokně, které se otevře, identifikujte jazyk, pro který chcete překlady, zadáním platného kódu národního prostředí.
5. Pro každou stránku můžete stáhnout sadu přepsání pro angličtinu a pracovat s překlady.
6. Až se soubory JSON dokončí, můžete je nahrát na každou stránku.
7. Vyberte **Povolit** a tok uživatele teď může zobrazit tento jazyk pro vaše uživatele.
8. Uložte jazyk.

>[!IMPORTANT]
>Před uložením je třeba povolit vlastní jazyky nebo pro ně nahrajte přepsání.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>Nastavení seznamu podporovaných jazyků

Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Přidejte `Localization` element s podporovanými jazyky: angličtina (výchozí) a španělština.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>Zadání popisků specifických pro jazyk

[LocalizedResources](localization.md#localizedresources) `Localization` elementu obsahuje seznam lokalizovaných řetězců. Lokalizovaný prvek Resources má identifikátor, který slouží k jednoznačné identifikaci lokalizovaných prostředků. Tento identifikátorem se používá později v prvku [definice obsahu](contentdefinitions.md) .

Nakonfigurujete lokalizované prvky prostředků pro definici obsahu a jakýkoli jazyk, který chcete podporovat. Chcete-li přizpůsobit jednotné registrační nebo přihlašovací stránky pro angličtinu a španělštinu, přidejte následující `LocalizedResources` prvky po ukončení `</SupportedLanguages>` elementu.

> [!NOTE]
> V následující ukázce jsme přidali symbol křížku `#` na BEGGING každého řádku, takže můžete easly najít lokalizované popisky na obrazovce.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>Upravte definici obsahu pomocí lokalizace.

Vložte celý obsah elementu ContentDefinitions, který jste zkopírovali jako podřízený prvek BuildingBlocks elementu.

V následujícím příkladu jsou vlastní řetězce anglické (EN) a španělština (ES) přidány na přihlašovací stránku nebo přihlašovací stránku a na stránku pro registraci místního účtu. **LocalizedResourcesReferenceId** pro každý **LocalizedResourcesReference** je stejný jako národní prostředí, ale můžete použít libovolný řetězec jako identifikátor. Pro každou kombinaci jazyka a stránky odkazujete na odpovídající **LocalizedResources** , které jste vytvořili dříve.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

##  <a name="upload-and-test-your-updated-custom-policy"></a>Nahrávání a testování aktualizovaných vlastních zásad

### <a name="upload-the-custom-policy"></a>Nahrajte vlastní zásadu.

1. Uložte soubor rozšíření.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu**.
1. Nahrajte soubor rozšíření, který jste předtím změnili.

### <a name="test-the-custom-policy-by-using-run-now"></a>Otestujte vlastní zásady pomocí rutiny **spustit hned**

1. Vyberte zásady, které jste nahráli, a pak vyberte **Spustit nyní**.
1. Měli byste vidět lokalizovanou stránku pro registraci nebo přihlášení.
1. Klikněte na odkaz pro registraci a měli byste vidět lokalizovanou stránku pro registraci.
1. Přepněte výchozí jazyk prohlížeče na španělštinu. Můžete také přidat parametr řetězce dotazu `ui_locales` k žádosti o autorizaci. Například: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>Další informace

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>Popisky přizpůsobení uživatelského rozhraní stránky jako přepsání

Když povolíte přizpůsobení jazyka, zůstanou předchozí úpravy popisků pomocí přizpůsobení uživatelského rozhraní stránky v souboru JSON pro angličtinu (EN). Můžete pokračovat ve změnách popisků a dalších řetězců tím, že nahrajete jazykové prostředky v přizpůsobení jazyka.

::: zone-end

### <a name="up-to-date-translations"></a>Aktuální překlady

Společnost Microsoft se zavazuje, že poskytuje nejaktuálnější překlady pro vaše používání. Microsoft průběžně vylepšuje překlady a udržuje je v souladu s nimi. Microsoft bude identifikovat chyby a změny v globální terminologii a dělat aktualizace, které budou v toku uživatelů fungovat bez problémů.

### <a name="support-for-right-to-left-languages"></a>Podpora jazyků se zápisem zprava doleva

Microsoft v současnosti neposkytuje podporu pro jazyky se zápisem zprava doleva. To lze provést pomocí vlastních národních prostředí a pomocí šablon stylů CSS změnit způsob zobrazení řetězců. Pokud tuto funkci potřebujete, Hlasujte pro ni prosím na základě [názoru na Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Překlady zprostředkovatele sociálních identit

Microsoft poskytuje `ui_locales` parametr OIDC pro přihlášení přes sociální sítě. Někteří poskytovatelé sociálních identit, včetně Facebooku a Google, je nedodržují.

### <a name="browser-behavior"></a>Chování prohlížeče

Chrome a Firefox obě požadavky na svůj jazyk sady. Pokud se jedná o podporovaný jazyk, zobrazí se před výchozím nastavením. Microsoft Edge aktuálně nepožaduje jazyk a přímo do výchozího jazyka.

## <a name="supported-languages"></a>Podporované jazyky

Azure AD B2C zahrnuje podporu pro následující jazyky. Jazyky toku uživatelů poskytuje Azure AD B2C. Jazyky oznámení služby Multi-Factor Authentication (MFA) poskytuje [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Jazyk              | Kód jazyka | Toky uživatele         | Oznámení MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arabština                | snížen            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Bulharština             | BG            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Bengálština                | bn            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Katalánština               | určen            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Čeština                 | cs            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Dánština                | &            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Němčina                | &            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Řečtina                 | El            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| angličtina               | en            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| španělština               | es            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Estonština              | et            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Baskičtina                | Balijšitna            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Finština               | WiFi            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Francouzština                | FR            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Galicijština              | GL            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Gudžarátština              | Gu            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Hebrejština                | uvede            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Hindština                 | Ahoj            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Chorvatština              | hod            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Maďarština             | hu            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Indonéština            | id            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Italština               | její            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Japonština              | dža            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Kazaština                | kk            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Kannadština               | KN            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Korejština                | Ko            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Litevština            | lt            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Lotyština               | Lotyšsko            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Malajalámština             | ml            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Maráthština               | Vážený            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Malajština                 | Arial            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Norština – Bokmal      | NB            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Nizozemština                 | belgick            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Norština             | ne            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Paňdžábština               | PA            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Polština                | pl            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Portugalština – Brazílie   | pt-br         | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Portugalština – Portugalsko | pt-pt         | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Rumunština              | loď            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Ruština               | ru            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Slovenština                | SM            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Slovinština             | SSL            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Srbština – cyrilice    | SR-cryl-cs    | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Srbština – latinka       | SR-Latn-cs    | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Švédština               | činí            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Tamilština                 | Ta            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Telugština                | te            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![X značí ne.](./media/user-flow-language-customization/no.png) |
| Thajština                  | Kolik            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Turečtina               | recenzent            | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Ukrajinština             | Velká Británie            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Vietnamština            | InterDev            | ![X značí ne.](./media/user-flow-language-customization/no.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Čínština – zjednodušená  | zh – Hans       | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |
| Čínština – tradiční | zh – Hant       | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) | ![Zelená značka zaškrtnutí.](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>Další kroky

::: zone pivot="b2c-user-flow"

Další informace o tom, jak můžete přizpůsobit uživatelské rozhraní svých aplikací, najdete v tématu [přizpůsobení uživatelského rozhraní aplikace v Azure Active Directory B2C](customize-ui-with-html.md).

::: zone-end 

::: zone pivot="b2c-custom-policy"

- Další informace o elementu [lokalizace](localization.md) najdete v referenčních informacích k IEF.
- Podívejte se na seznam [ID lokalizačních řetězců](localization-string-ids.md) dostupných v Azure AD B2C.

::: zone-end 
