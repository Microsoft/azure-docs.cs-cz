---
title: ID lokalizačních řetězců – Azure Active Directory B2C | Microsoft Docs
description: Zadejte ID definice obsahu s ID API. signuporsignin ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 11/09/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4fa0f4d87567bd77faacfd2373dba0d2b8996446
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2020
ms.locfileid: "94873365"
---
# <a name="localization-string-ids"></a>ID řetězců lokalizací

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Localization** umožňuje v zásadách pro cesty uživatelů podporovat více národních prostředí nebo jazyků. Tento článek poskytuje seznam identifikátorů lokalizací, které můžete použít ve svých zásadách. Seznamte se s lokalizací uživatelského rozhraní, najdete v tématu [lokalizace](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Prvky přihlašovacích nebo přihlašovacích stránek

Následující identifikátory se používají pro definici obsahu s ID `api.signuporsignin` a [technickým profilem s vlastním uplatněním](self-asserted-technical-profile.md).

| ID | Výchozí hodnota | Verze rozložení stránky |
| -- | ------------- | ------ |
| **forgotpassword_link** | Forgot your password? | `All` |
| **createaccount_intro** | Nemáte účet? | `All` |
| **button_signin** | Přihlásit se | `All` |
| **social_intro** | Přihlaste se pomocí účtu sociální sítě. | `All` |
| **remember_me** |Zůstat přihlášeni. | `All` |
| **unknown_error** | Máme potíže s přihlášením. Zkuste to později. | `All` |
| **divider_title** | NEBO | `All` |
| **local_intro_email** | Přihlaste se pomocí svého existujícího účtu | `< 2.0.0` |
| **logonIdentifier_email** | E-mailová adresa | `< 2.0.0` |
| **requiredField_email** | Zadejte prosím svůj e-mail. | `< 2.0.0` |
| **invalid_email** | Zadejte prosím platnou e-mailovou adresu. | `< 2.0.0` |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' \* +/=? ^ \_ \` { \| } ~-] + @ [a-za-Z0-9-] + (?: \\ . [ a-zA-Z0-9-] +) \* $ |`< 2.0.0` |
| **local_intro_username** | Přihlaste se pomocí svého uživatelského jména. | `< 2.0.0` |
| **logonIdentifier_username** | Uživatelské jméno | `< 2.0.0` |
| **requiredField_username** | Zadejte prosím svoje uživatelské jméno. | `< 2.0.0` |
| **heslo** | Heslo | `< 2.0.0` |
| **requiredField_password** | Zadejte prosím svoje heslo. | `< 2.0.0` |
| **createaccount_link** | Zaregistrujte se | `< 2.0.0` |
| **cancel_message** | Uživatel zapomněl svoje heslo. | `< 2.0.0` |
| **invalid_password** | Heslo, které jste zadali, není v očekávaném formátu. | `< 2.0.0` |
| **createaccount_one_link** | Zaregistrujte se | `>= 2.0.0` |
| **createaccount_two_links** | Zaregistrujte se pomocí {0} nebo {1} | `>= 2.0.0` |
| **createaccount_three_links** | Zaregistrujte se pomocí {0} , {1} nebo {2} | `>= 2.0.0` |
| **local_intro_generic** | Přihlaste se pomocí svého {0} | `>= 2.1.0` |
| **requiredField_generic** | Zadejte prosím svoje {0} | `>= 2.1.0` |
| **invalid_generic** | Zadejte prosím platný {0} | `>= 2.1.1` |
| **Adresář** | Přihlásit se | `>= 2.1.1` |


> [!NOTE]
> * Zástupné symboly jako {0} budou automaticky doplněny `DisplayName` hodnotou `ClaimType` . 
> * Další informace o lokalizaci najdete `ClaimType` v tématu [registrace a přihlášení – příklad](#signupsigninexample).

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na přihlašovací stránce nebo na přihlašovací stránce:

:::image type="content" source="./media/localization-string-ids/localization-susi-2.png" alt-text="Snímek obrazovky zobrazující registrační nebo přihlašovací stránku U X prvků":::

### <a name="sign-up-or-sign-in-identity-providers"></a>Poskytovatelé přihlašovacích nebo přihlašování identity

ID zprostředkovatelů identity je nakonfigurováno v elementu  **ClaimsExchange** na cestě uživatele. Chcete-li lokalizovat název poskytovatele identity, je typ **ElementType** nastaven na hodnotu `ClaimsProvider` , zatímco **STRINGID** je nastaven na ID `ClaimsExchange` .

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="MicrosoftExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccount" />
  </ClaimsExchanges>
</OrchestrationStep>
```

V následujícím příkladu je lokalizován poskytovatel identity Facebooku do arabštiny:

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

### <a name="sign-up-or-sign-in-error-messages"></a>Chybové zprávy týkající se registrace nebo přihlašování

| ID | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Heslo není správné. |
| **UserMessageIfPasswordExpired**| Platnost vašeho hesla vypršela.|
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nemůžeme se zdát, že váš účet najdete. |
| **UserMessageIfOldPasswordUsed** | Vypadá to, že jste použili staré heslo. |
| **DefaultMessage** | Neplatné uživatelské jméno nebo heslo. |
| **UserMessageIfUserAccountDisabled** | Váš účet byl uzamčen. Řekněte pracovníkovi podpory, aby ho odemkl, a pak to zkuste znovu. |
| **UserMessageIfUserAccountLocked** | Váš účet je dočasně zamčený, aby nedocházelo k neoprávněnému použití. Zkuste to později. |
| **AADRequestsThrottled** | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |

<a name="signupsigninexample"></a>
### <a name="sign-up-or-sign-in-example"></a>Příklad registrace nebo přihlášení

```xml
<LocalizedResources Id="api.signuporsignin.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="heading">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="social_intro">Sign in with your social account</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_intro_generic">Sign in with your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_password">Please enter your password</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_generic">Please enter your {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_generic">Please enter a valid {0}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_one_link">Sign up now</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_two_links">Sign up with {0} or {1}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="createaccount_three_links">Sign up with {0}, {1}, or {2}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">Forgot your password?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_signin">Sign in</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="divider_title">OR</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="unknown_error">We are having trouble signing you in. Please try again later.</LocalizedString>
    <!-- Uncomment the remember_me only if the keep me signed in is activated. 
    <LocalizedString ElementType="UxElement" StringId="remember_me">Keep me signed in</LocalizedString> -->
    <LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">Your password is incorrect.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfPasswordExpired">Your password has expired.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">Looks like you used an old password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">Invalid username or password.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Prvky uživatelského rozhraní pro registraci a samoobslužné vyhodnocení stránek

Níže jsou uvedená ID pro definici obsahu s ID `api.localaccountsignup` nebo libovolnou definicí obsahu, která začíná `api.selfasserted` , například `api.selfasserted.profileupdate` a a `api.localaccountpasswordreset` [technického profilu s vlastním uplatněním](self-asserted-technical-profile.md).

| ID | Výchozí hodnota |
| -- | ------------- |
| **ver_sent** | Ověřovací kód byl odeslán do: |
| **ver_but_default** | Výchozí |
| **cancel_message** | Uživatel zrušil zadávání informací o vlastním vyhodnocení. |
| **preloader_alt** | Počkejte prosím |
| **ver_but_send** | Odeslat ověřovací kód |
| **alert_yes** | Yes |
| **error_fieldIncorrect** | Některá pole jsou vyplněna nesprávně. Zkontrolujte prosím své položky a zkuste to znovu. |
| **jednolet** | Year (Rok) |
| **verifying_blurb** | Počkejte prosím, než zpracujeme vaše informace. |
| **button_cancel** | Zrušit |
| **ver_fail_no_retry** | Provedli jste příliš mnoho nesprávných pokusů. Zkuste to později. |
| **měsíčně** | Month (Měsíc) |
| **ver_success_msg** | E-mailová adresa byla ověřena. Nyní můžete pokračovat. |
| **months** | Leden, únor, březen, duben, květen, červen, červenec, srpen, září, říjen, listopad, prosinec |
| **ver_fail_server** | Při ověřování vaší e-mailové adresy máme problémy. Zadejte prosím platnou e-mailovou adresu a zkuste to znovu. |
| **error_requiredFieldMissing** | Chybí povinné pole. Vyplňte prosím všechna povinná pole a zkuste to znovu. |
| **initial_intro** | Zadejte prosím následující údaje. |
| **ver_but_resend** | Poslat nový kód |
| **button_continue** | Vytvořit |
| **error_passwordEntryMismatch** | Pole pro zadání hesla se neshodují. Zadejte prosím do obou polí stejné heslo a zkuste to znovu. |
| **ver_incorrect_format** | Nesprávný formát |
| **ver_but_edit** | Změnit e-mail |
| **ver_but_verify** | Ověřit kód |
| **alert_no** | Ne |
| **ver_info_msg** | Ověřovací kód byl odeslán do vaší doručené pošty. Zkopírujte ho prosím do následujícího pole pro zadání. |
| **dnu** | Den |
| **ver_fail_throttled** | Existuje příliš mnoho žádostí o ověření této e-mailové adresy. Chvíli prosím počkejte a pak to zkuste znovu. |
| **helplink_text** | Co je to? |
| **ver_fail_retry** | Kód je nesprávný. Zkuste to prosím znovu. |
| **alert_title** | Zrušit zadávání podrobností |
| **required_field** | Tato informace je povinná. |
| **alert_message** | Opravdu chcete zrušit zadávání podrobností? |
| **ver_intro_msg** | Ověření je nezbytné. Klikněte prosím na tlačítko Odeslat. |
| **ver_input** | Ověřovací kód |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Chybové zprávy týkající se registrace a stránek s vlastním uplatněním

| ID | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Uživatel se zadaným ID už existuje. Zvolte prosím jiný. |
| **UserMessageIfClaimNotVerified** | Deklarace identity není ověřená: {0} |
| **UserMessageIfIncorrectPattern** | Nesprávný vzor pro: {0} |
| **UserMessageIfMissingRequiredElement** | Chybí požadovaný element: {0} |
| **UserMessageIfValidationError** | Chyba při ověřování uživatelem: {0} |
| **UserMessageIfInvalidInput** | {0} má neplatný vstup. |
| **ServiceThrottled** | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce pro registraci:

![Přihlašovací stránka s názvy prvků uživatelského rozhraní označená](./media/localization-string-ids/localization-sign-up.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce registrace, poté, co uživatel klikne na tlačítko Odeslat ověřovací kód:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-email-verification.png)

## <a name="sign-up-and-self-asserted-pages-example"></a>Příklad stránek se zápisem a vlastním kontrolním příznakem

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email Address</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Email address that can be used to contact you.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">Enter new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">Confirm New Password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">Confirm new password</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">Display Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">Your display name.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">Surname</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">Your surname (also known as family name or last name).</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">Given Name</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">Your given name (also known as first name).</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_continue">Create</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">A required field is missing. Please fill out all required fields and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="helplink_text">What is this?</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="initial_intro">Please provide the following details.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="preloader_alt">Please wait</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="required_field">This information is required.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_edit">Change e-mail</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_resend">Send new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_send">Send verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_but_verify">Verify code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">That code is incorrect. Please try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_server">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_info_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_input">Verification code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="ver_success_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">Claim not verified: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">A user with the specified ID already exists. Please choose a different one.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">Incorrect pattern for: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">{0} has invalid input.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">Missing required element: {0}</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">Error in validation by: {0}</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="phone-factor-authentication-page-user-interface-elements"></a>Prvky uživatelského rozhraní stránky pro ověřování na telefonním faktoru

Níže jsou uvedené identifikátory definice obsahu s ID `api.phonefactor` a [technickým profilem pro telefonní faktor](phone-factor-technical-profile.md).

| ID | Výchozí hodnota |
| -- | ------------- |
| **button_verify** | Zavolej mi |
| **country_code_label** | Kód země |
| **cancel_message** | Uživatel zrušil službu Multi-Factor Authentication. |
| **text_button_send_second_code** | poslat nový kód |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Pro vás budeme nahrávat následující číslo. K ověření vám můžeme poslat kód přes SMS nebo telefon. |
| **intro_mixed_p** | Pro vás budeme nahrávat následující čísla. Vyberte číslo, které můžeme použít k telefonování, nebo pošle kód přes SMS, abychom vás ověřili. |
| **button_verify_code** | Ověřit kód |
| **requiredField_code** | Zadejte prosím ověřovací kód, který jste dostali. |
| **invalid_code** | Zadejte prosím kód o 6 číslicích, který jste dostali. |
| **button_cancel** | Zrušit |
| **local_number_input_placeholder_text** | Telefonní číslo |
| **button_retry** | Zkusit znovu |
| **alternative_text** | Nemám svůj telefon |
| **intro_phone_p** | Pro vás budeme nahrávat následující čísla. Vyberte číslo, na které můžeme telefon ověřit. |
| **intro_phone** | Pro vás budeme nahrávat následující číslo. Budeme vám telefonicky ověřit. |
| **enter_code_text_intro** | Níže zadejte svůj ověřovací kód nebo  |
| **intro_entry_phone** | Níže zadejte číslo, na které můžeme telefon ověřit. |
| **intro_entry_sms** | Níže zadejte číslo, na které můžeme poslat kód prostřednictvím SMS, abychom vás mohli ověřit. |
| **button_send_code** | Odeslat kód |
| **invalid_number** | Zadejte prosím platné telefonní číslo. |
| **intro_sms** | Pro vás budeme nahrávat následující číslo. Prostřednictvím SMS vám pošleme kód, abychom vás ověřili. |
| **intro_entry_mixed** | Níže zadejte číslo, které vám můžeme poslat kód přes SMS nebo telefon, abychom vás ověřili. |
| **number_pattern** | ^\\+ (?: [0-9] [ \\ x20-]?) {6,14} [0-9] $ |
| **intro_sms_p** |Pro vás budeme nahrávat následující čísla. Vyberte číslo, na které můžeme poslat kód prostřednictvím SMS, abychom vás mohli ověřit. |
| **requiredField_countryCode** | Vyberte prosím kód země. |
| **requiredField_number** | Zadejte prosím svoje telefonní číslo. |
| **country_code_input_placeholder_text** |Země nebo oblast |
| **number_label** | Telefonní číslo |
| **error_tryagain** | Telefonní číslo, které jste zadali, je zaneprázdněno nebo není k dispozici. Zkontrolujte prosím číslo a zkuste to znovu. |
| **error_incorrect_code** | Ověřovací kód, který jste zadali, neodpovídá našim záznamům. Zkuste to prosím znovu nebo si vyžádejte nový kód. |
| **countryList** | Podívejte [se na seznam zemí](#phone-factor-authentication-page-example). |
| **error_448** | Telefonní číslo, které jste zadali, je nedosažitelné. |
| **error_449** | Uživatel překročil počet opakovaných pokusů. |
| **verification_code_input_placeholder_text** | Ověřovací kód |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce pro zápis MFA:

![Prvky uživatelského prostředí pro zápis ověřování pro telefonní faktor](./media/localization-string-ids/localization-mfa1.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce ověřování MFA:

![Prvky uživatelského prostředí ověřování pro telefonní faktor](./media/localization-string-ids/localization-mfa2.png)

## <a name="phone-factor-authentication-page-example"></a>Příklad stránky ověřování od telefonního faktoru

```xml
<LocalizedResources Id="api.phonefactor.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="button_verify">Call Me</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_label">Country Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="cancel_message">The user has canceled multi-factor authentication</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="text_button_send_second_code">Send a new code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="code_pattern">\d{6}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed">We have the following number on record for you. We can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_mixed_p">We have the following numbers on record for you. Choose a number that we can phone or send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_code">Please enter the verification code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_code">Please enter the 6-digit code you received</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="local_number_input_placeholder_text">Phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_retry">Retry</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="alternative_text">I don't have my phone</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone_p">We have the following numbers on record for you. Choose a number that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_phone">We have the following number on record for you. We will phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="enter_code_text_intro">Enter your verification code below, or</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_phone">Enter a number below that we can phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_sms">Enter a number below that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="button_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="invalid_number">Please enter a valid phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms">We have the following number on record for you. We will send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_entry_mixed">Enter a number below that we can send a code via SMS or phone to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_pattern">^\+(?:[0-9][\x20-]?){6,14}[0-9]$</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="intro_sms_p">We have the following numbers on record for you. Choose a number that we can send a code via SMS to authenticate you.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_countryCode">Please select your country code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="requiredField_number">Please enter your phone number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="country_code_input_placeholder_text">Country or region</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="number_label">Phone Number</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_tryagain">The phone number you provided is busy or unavailable. Please check the number and try again.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_incorrect_code">The verification code you have entered does not match our records. Please try again, or request a new code.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="countryList">{"DEFAULT":"Country/Region","AF":"Afghanistan","AX":"Åland Islands","AL":"Albania","DZ":"Algeria","AS":"American Samoa","AD":"Andorra","AO":"Angola","AI":"Anguilla","AQ":"Antarctica","AG":"Antigua and Barbuda","AR":"Argentina","AM":"Armenia","AW":"Aruba","AU":"Australia","AT":"Austria","AZ":"Azerbaijan","BS":"Bahamas","BH":"Bahrain","BD":"Bangladesh","BB":"Barbados","BY":"Belarus","BE":"Belgium","BZ":"Belize","BJ":"Benin","BM":"Bermuda","BT":"Bhutan","BO":"Bolivia","BQ":"Bonaire","BA":"Bosnia and Herzegovina","BW":"Botswana","BV":"Bouvet Island","BR":"Brazil","IO":"British Indian Ocean Territory","VG":"British Virgin Islands","BN":"Brunei","BG":"Bulgaria","BF":"Burkina Faso","BI":"Burundi","CV":"Cabo Verde","KH":"Cambodia","CM":"Cameroon","CA":"Canada","KY":"Cayman Islands","CF":"Central African Republic","TD":"Chad","CL":"Chile","CN":"China","CX":"Christmas Island","CC":"Cocos (Keeling) Islands","CO":"Colombia","KM":"Comoros","CG":"Congo","CD":"Congo (DRC)","CK":"Cook Islands","CR":"Costa Rica","CI":"Côte d'Ivoire","HR":"Croatia","CU":"Cuba","CW":"Curaçao","CY":"Cyprus","CZ":"Czech Republic","DK":"Denmark","DJ":"Djibouti","DM":"Dominica","DO":"Dominican Republic","EC":"Ecuador","EG":"Egypt","SV":"El Salvador","GQ":"Equatorial Guinea","ER":"Eritrea","EE":"Estonia","ET":"Ethiopia","FK":"Falkland Islands","FO":"Faroe Islands","FJ":"Fiji","FI":"Finland","FR":"France","GF":"French Guiana","PF":"French Polynesia","TF":"French Southern Territories","GA":"Gabon","GM":"Gambia","GE":"Georgia","DE":"Germany","GH":"Ghana","GI":"Gibraltar","GR":"Greece","GL":"Greenland","GD":"Grenada","GP":"Guadeloupe","GU":"Guam","GT":"Guatemala","GG":"Guernsey","GN":"Guinea","GW":"Guinea-Bissau","GY":"Guyana","HT":"Haiti","HM":"Heard Island and McDonald Islands","HN":"Honduras","HK":"Hong Kong SAR","HU":"Hungary","IS":"Iceland","IN":"India","ID":"Indonesia","IR":"Iran","IQ":"Iraq","IE":"Ireland","IM":"Isle of Man","IL":"Israel","IT":"Italy","JM":"Jamaica","JP":"Japan","JE":"Jersey","JO":"Jordan","KZ":"Kazakhstan","KE":"Kenya","KI":"Kiribati","KR":"Korea","KW":"Kuwait","KG":"Kyrgyzstan","LA":"Laos","LV":"Latvia","LB":"Lebanon","LS":"Lesotho","LR":"Liberia","LY":"Libya","LI":"Liechtenstein","LT":"Lithuania","LU":"Luxembourg","MO":"Macao SAR","MK":"North Macedonia","MG":"Madagascar","MW":"Malawi","MY":"Malaysia","MV":"Maldives","ML":"Mali","MT":"Malta","MH":"Marshall Islands","MQ":"Martinique","MR":"Mauritania","MU":"Mauritius","YT":"Mayotte","MX":"Mexico","FM":"Micronesia","MD":"Moldova","MC":"Monaco","MN":"Mongolia","ME":"Montenegro","MS":"Montserrat","MA":"Morocco","MZ":"Mozambique","MM":"Myanmar","NA":"Namibia","NR":"Nauru","NP":"Nepal","NL":"Netherlands","NC":"New Caledonia","NZ":"New Zealand","NI":"Nicaragua","NE":"Niger","NG":"Nigeria","NU":"Niue","NF":"Norfolk Island","KP":"North Korea","MP":"Northern Mariana Islands","NO":"Norway","OM":"Oman","PK":"Pakistan","PW":"Palau","PS":"Palestinian Authority","PA":"Panama","PG":"Papua New Guinea","PY":"Paraguay","PE":"Peru","PH":"Philippines","PN":"Pitcairn Islands","PL":"Poland","PT":"Portugal","PR":"Puerto Rico","QA":"Qatar","RE":"Réunion","RO":"Romania","RU":"Russia","RW":"Rwanda","BL":"Saint Barthélemy","KN":"Saint Kitts and Nevis","LC":"Saint Lucia","MF":"Saint Martin","PM":"Saint Pierre and Miquelon","VC":"Saint Vincent and the Grenadines","WS":"Samoa","SM":"San Marino","ST":"São Tomé and Príncipe","SA":"Saudi Arabia","SN":"Senegal","RS":"Serbia","SC":"Seychelles","SL":"Sierra Leone","SG":"Singapore","SX":"Sint Maarten","SK":"Slovakia","SI":"Slovenia","SB":"Solomon Islands","SO":"Somalia","ZA":"South Africa","GS":"South Georgia and South Sandwich Islands","SS":"South Sudan","ES":"Spain","LK":"Sri Lanka","SH":"St Helena, Ascension, Tristan da Cunha","SD":"Sudan","SR":"Suriname","SJ":"Svalbard","SZ":"Swaziland","SE":"Sweden","CH":"Switzerland","SY":"Syria","TW":"Taiwan","TJ":"Tajikistan","TZ":"Tanzania","TH":"Thailand","TL":"Timor-Leste","TG":"Togo","TK":"Tokelau","TO":"Tonga","TT":"Trinidad and Tobago","TN":"Tunisia","TR":"Turkey","TM":"Turkmenistan","TC":"Turks and Caicos Islands","TV":"Tuvalu","UM":"U.S. Outlying Islands","VI":"U.S. Virgin Islands","UG":"Uganda","UA":"Ukraine","AE":"United Arab Emirates","GB":"United Kingdom","US":"United States","UY":"Uruguay","UZ":"Uzbekistan","VU":"Vanuatu","VA":"Vatican City","VE":"Venezuela","VN":"Vietnam","WF":"Wallis and Futuna","YE":"Yemen","ZM":"Zambia","ZW":"Zimbabwe"}</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_448">The phone number you provided is unreachable.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="error_449">User has exceeded the number of retry attempts.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_code_input_placeholder_text">Verification code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="strongAuthenticationPhoneNumber" StringId="DisplayName">Phone Number</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>

```

## <a name="verification-display-control-user-interface-elements"></a>Ovládací prvky uživatelského rozhraní pro zobrazení ověření

Níže jsou uvedené identifikátory pro [ovládací prvek zobrazení ověřování](display-control-verification.md) s [verzí rozložení stránky](page-layout.md) 2.1.0 nebo vyšší.

| ID | Výchozí hodnota |
| -- | ------------- |
|intro_msg| Ověření je nezbytné. Klikněte prosím na tlačítko Odeslat.|
|success_send_code_msg | Ověřovací kód byl odeslán do vaší doručené pošty. Zkopírujte ho prosím do následujícího pole pro zadání.|
|failure_send_code_msg | Při ověřování vaší e-mailové adresy máme problémy. Zadejte prosím platnou e-mailovou adresu a zkuste to znovu.|
|success_verify_code_msg | E-mailová adresa byla ověřena. Nyní můžete pokračovat.|
|failure_verify_code_msg | Při ověřování vaší e-mailové adresy máme problémy. Zkuste to prosím znovu.|
|but_send_code | Odeslat ověřovací kód|
|but_verify_code | Ověřit kód|
|but_send_new_code | Poslat nový kód|
|but_change_claims | Změnit e-mail|

### <a name="verification-display-control-example"></a>Příklad ovládacího prvku zobrazení ověření

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
   <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="verification-display-control-user-interface-elements-deprecated"></a>Ovládací prvky uživatelského rozhraní pro zobrazení ověření (zastaralé)

Níže jsou uvedené identifikátory pro [ovládací prvek zobrazení ověřování](display-control-verification.md) s [verzí rozložení stránky](page-layout.md) 2.0.0.

| ID | Výchozí hodnota |
| -- | ------------- |
|verification_control_but_change_claims |Změnit |
|verification_control_fail_send_code |Nepovedlo se odeslat kód, zkuste to prosím znovu později. |
|verification_control_fail_verify_code |Nepovedlo se ověřit kód, zkuste to prosím znovu později. |
|verification_control_but_send_code |Odeslat kód |
|verification_control_but_send_new_code |Poslat nový kód |
|verification_control_but_verify_code |Ověřit kód |
|verification_control_code_sent| Ověřovací kód byl odeslán. Zkopírujte ho prosím do následujícího pole pro zadání. |

### <a name="verification-display-control-example-deprecated"></a>Příklad ovládacího prvku zobrazení ověření (zastaralé)

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_change_claims">Change</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_send_code">Failed to send the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_fail_verify_code">Failed to verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_code">Send Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_send_new_code">Send New Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_but_verify_code">Verify Code</LocalizedString>
    <LocalizedString ElementType="UxElement" StringId="verification_control_code_sent">Verification code has been sent. Please copy it to the input box below.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="restful-service-error-messages"></a>Chybové zprávy služby RESTful

Níže jsou uvedené identifikátory pro chybové zprávy [technického profilu služby RESTful](restful-technical-profile.md) :

| ID | Výchozí hodnota |
| -- | ------------- |
|DefaultUserMessageIfRequestFailed | Nepovedlo se navázat připojení ke koncovému bodu služby RESTful. Adresa URL služby RESTful: {0} |
|UserMessageIfCircuitOpen | {0} Adresa URL služby RESTful: {1} |
|UserMessageIfDnsResolutionFailed | Nepovedlo se přeložit název hostitele koncového bodu služby RESTful. Adresa URL služby RESTful: {0} |
|UserMessageIfRequestTimeout | Nepovedlo se navázat připojení ke koncovému bodu služby RESTful během časového limitu {0} sekund. Adresa URL služby RESTful: {1} |


### <a name="restful-service-example"></a>Příklad služby RESTful

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="DefaultUserMessageIfRequestFailed">Failed to establish connection to restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCircuitOpen">Unable to connect to the restful service end point.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfDnsResolutionFailed">Failed to resolve the hostname of the restful service endpoint.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfRequestTimeout">Failed to establish connection to restful service end point within timeout limit.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-mfa-error-messages"></a>Chybové zprávy Azure AD MFA

Níže jsou uvedené identifikátory pro chybové zprávy [technického profilu Azure AD MFA](multi-factor-auth-technical-profile.md) :

| ID | Výchozí hodnota |
| -- | ------------- |
|UserMessageIfCouldntSendSms | SMS nejde odeslat na telefon, zkuste prosím použít jiné telefonní číslo. |
|UserMessageIfInvalidFormat | Vaše telefonní číslo nemá platný formát, opravte ho prosím a zkuste to znovu.|
|UserMessageIfMaxAllowedCodeRetryReached | Nesprávný kód byl zadán příliš mnohokrát, zkuste to prosím znovu později.|
|UserMessageIfServerError | Nejde použít službu MFA, zkuste to prosím znovu později.|
|UserMessageIfThrottled | Vaše žádost byla omezená, zkuste to prosím znovu později.|
|UserMessageIfWrongCodeEntered|Byl zadán nesprávný kód, zkuste to prosím znovu.|

### <a name="azure-ad-mfa-example"></a>Příklad Azure AD MFA

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfCouldntSendSms">Cannot Send SMS to the phone, please try another phone number.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidFormat">Your phone number is not in a valid format, please correct it and try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxAllowedCodeRetryReached">Wrong code entered too many times, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfServerError">Cannot use MFA service, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">Your request has been throttled, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfWrongCodeEntered">Wrong code entered, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="azure-ad-sspr"></a>Samoobslužné resetování hesla Azure AD

Níže jsou uvedené identifikátory pro chybové zprávy [Technical Profile Azure AD SSPR](aad-sspr-technical-profile.md) :

| ID | Výchozí hodnota |
| -- | ------------- |
|UserMessageIfChallengeExpired | Platnost kódu vypršela.|
|UserMessageIfInternalError | U e-mailové služby došlo k vnitřní chybě, zkuste to prosím znovu později.|
|UserMessageIfThrottled | Odeslali jste moc požadavků, zkuste to prosím znovu později.|
|UserMessageIfVerificationFailedNoRetry | Překročili jste maximální počet pokusů o ověření.|
|UserMessageIfVerificationFailedRetryAllowed | Ověření se nepovedlo, zkuste to prosím znovu.|


### <a name="azure-ad-sspr-example"></a>Příklad Azure AD SSPR

```XML
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInternalError">We are having trouble verifying your email address. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfThrottled">There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfChallengeExpired">That code is expired. Please request a new code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedNoRetry">You've made too many incorrect attempts. Please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="one-time-password-error-messages"></a>Jednorázové chybové zprávy hesla

Níže jsou uvedené identifikátory pro chybové zprávy s [technickým profilem pro jednorázové heslo](one-time-password-technical-profile.md) .

| ID | Výchozí hodnota |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Jednorázové heslo poskytnuté při ověřování překročilo maximální počet pokusů. |
|UserMessageIfSessionDoesNotExist |Jedna časová relace pro ověření hesla vypršela. |
|UserMessageIfSessionConflict |Jedna časová relace ověření hesla je v konfliktu. |
|UserMessageIfInvalidCode |Jedno časové heslo zadané pro ověření není správné. |
|UserMessageIfVerificationFailedRetryAllowed |Kód je nesprávný. Zkuste to prosím znovu. | 

### <a name="one-time-password-example"></a>Příklad hesla s jedním časem

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
   <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">That code is incorrect. Please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="claims-transformations-error-messages"></a>Chybové zprávy transformace deklarací identity

Pro chybové zprávy transformace deklarací identity se používají následující identifikátory:

| ID | Transformace deklarací identity | Výchozí hodnota |
| -- | ------------- |------------- |
|UserMessageIfClaimsTransformationBooleanValueIsNotEqual |[AssertBooleanClaimIsEqualToValue](boolean-transformations.md#assertbooleanclaimisequaltovalue) | Pro typ deklarace "inputClaim" se nezdařilo porovnání hodnoty logické deklarace.| 
|DateTimeGreaterThan |[AssertDateTimeIsGreaterThan](date-transformations.md#assertdatetimeisgreaterthan) | Porovnání hodnoty deklarace identity se nezdařilo: zadaný levý operand je větší než pravý operand.|
|UserMessageIfClaimsTransformationStringsAreNotEqual |[AssertStringClaimsAreEqual](string-transformations.md#assertstringclaimsareequal) | Porovnání hodnoty deklarace identity se nezdařilo pomocí StringComparison "OrdinalIgnoreCase".|

### <a name="claims-transformations-example"></a>Příklad transformací deklarací identity

```xml
<LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedStrings>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationBooleanValueIsNotEqual">Your email address hasn't been verified.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="DateTimeGreaterThan">Expiration date must be greater that the current date.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsTransformationStringsAreNotEqual">The email entry fields do not match. Please enter the same email address in both fields and try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

## <a name="next-steps"></a>Další kroky

Příklady lokalizace najdete v následujících článcích:

- [Přizpůsobení jazyka s vlastními zásadami v Azure Active Directory B2C](custom-policy-localization.md)
- [Přizpůsobení jazyka pomocí toků uživatelů v Azure Active Directory B2C](user-flow-language-customization.md)
