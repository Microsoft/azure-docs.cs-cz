---
title: ID lokalizačních řetězců – Azure Active Directory B2C | Microsoft Docs
description: Zadejte ID definice obsahu s ID API. signuporsignin ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: da1390de4e2eb0624032dc490416e7b6e5d61baa
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "67846835"
---
# <a name="localization-string-ids"></a>ID řetězců lokalizací

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Localization** umožňuje v zásadách pro cesty uživatelů podporovat více národních prostředí nebo jazyků. Tento článek poskytuje seznam identifikátorů lokalizací, které můžete použít ve svých zásadách. Seznamte se s lokalizací uživatelského rozhraní [](localization.md), najdete v tématu lokalizace.

## <a name="sign-up-or-sign-in-page-elements"></a>Prvky přihlašovacích nebo přihlašovacích stránek

Následující identifikátory se používají pro definici obsahu s ID `api.signuporsignin`.

| id | Výchozí hodnota |
| -- | ------------- |
| **local_intro_email** | Přihlaste se se svým existujícím účtem. |
| **logonIdentifier_email** | E-mailová adresa |
| **requiredField_email** | Zadejte prosím svůj e-mail. |
| **invalid_email** | Zadejte prosím platnou e-mailovou adresu. |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' *+/=? ^ _\`{\|} ~-] + @ [a-za-Z0-9-] + (?:\\. [ a-zA-Z0-9-] +)* $ |
| **local_intro_username** | Přihlásit uživatelským jménem |
| **logonIdentifier_username** | Uživatelské jméno |
| **requiredField_username** | Zadejte prosím své uživatelské jméno. |
| **Heslo** | Heslo |
| **requiredField_password** | Zadejte prosím své heslo. |
| **invalid_password** | Heslo, které jste zadali, nemá očekávaný formát. |
| **forgotpassword_link** | Zapomněli jste heslo? |
| **createaccount_intro** | Nemáte účet? |
| **createaccount_link** | Zaregistrujte se |
| **divider_title** | NEBO |
| **cancel_message** | Uživatel zapomněl svoje heslo. |
| **button_signin** | Přihlášení |
| **social_intro** | Přihlaste se svým sociálním účtem. |
  **remember_me** |Neodhlašovat|
| **unknown_error** | Máme potíže s přihlášením. Zkuste to prosím znovu později. |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na přihlašovací stránce nebo na přihlašovací stránce:

![Prvky uživatelského rozhraní pro registraci nebo přihlašovací stránku](./media/localization-string-ids/localization-susi.png)

ID zprostředkovatelů identity je nakonfigurováno v elementu **ClaimsExchange** na cestě uživatele. Chcete-li lokalizovat název poskytovatele identity, je typ **ElementType** nastaven na `ClaimsProvider`hodnotu, zatímco **StringId** je `ClaimsExchange`nastaven na ID.

```XML
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

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Chybové zprávy týkající se registrace nebo přihlašování

| id | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Vaše heslo není správné. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nedaří se nám najít váš účet. |
| **UserMessageIfOldPasswordUsed** | Vypadá to, že jste použili staré heslo. |
| **DefaultMessage** | Neplatné uživatelské jméno nebo heslo. |
| **UserMessageIfUserAccountDisabled** | Váš účet byl uzamčen. Řekněte pracovníkovi podpory, aby ho odemkl, a pak to zkuste znovu. |
| **UserMessageIfUserAccountLocked** | Váš účet je dočasně zamčený, aby nedocházelo k neoprávněnému použití. Opakujte akci později. |
| **AADRequestsThrottled** | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Prvky uživatelského rozhraní pro registraci a samoobslužné stránky

Níže jsou uvedená ID pro `api.localaccountsignup` definici obsahu s ID nebo libovolnou definicí obsahu, která `api.selfasserted`začíná, například `api.selfasserted.profileupdate` a `api.localaccountpasswordreset`.

| id | Výchozí hodnota |
| -- | ------------- |
| **ver_sent** | Ověřovací kód se odeslal na: |
| **ver_but_default** | Výchozí |
| **cancel_message** | Uživatel zrušil zadávání informací o vlastním vyhodnocení. |
| **preloader_alt** | Počkejte prosím. |
| **ver_but_send** | Poslat ověřovací kód |
| **alert_yes** | Ano |
| **error_fieldIncorrect** | Některá pole jsou vyplněna nesprávně. Zkontrolujte prosím své položky a zkuste to znovu. |
| **jednolet** | Rok |
| **verifying_blurb** | Počkejte prosím, než zpracujeme vaše údaje. |
| **button_cancel** | Zrušit |
| **ver_fail_no_retry** | Provedli jste příliš mnoho nesprávných pokusů. Zkuste to prosím znovu později. |
| **měsíčně** | Měsíc |
| **ver_success_msg** | E-mailová adresa byla ověřena. Nyní můžete pokračovat. |
| **months** | leden, únor, březen, duben, květen, červen, červenec, srpen, září, říjen, listopad, prosinec |
| **ver_fail_server** | Při ověřování vaší e-mailové adresy máme problémy. Zadejte prosím platnou e-mailovou adresu a zkuste to znovu. |
| **error_requiredFieldMissing** | Chybí povinné pole. Vyplňte prosím všechna povinná pole a zkuste to znovu. |
| **initial_intro** | Zadejte prosím následující údaje. |
| **ver_but_resend** | Poslat nový kód |
| **button_continue** | Create |
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
| **alert_title** | Zrušit zadávání údajů |
| **required_field** | Tyto informace jsou požadovány. |
| **alert_message** | Opravdu chcete zrušit zadávání údajů? |
| **ver_intro_msg** | Ověření je nezbytné. Klikněte prosím na tlačítko Odeslat. |
| **ver_input** | Ověřovací kód |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Chybové zprávy pro registraci a samoobslužné stránky

| id | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Uživatel se zadaným ID už existuje. Zvolte prosím jiný. |
| **UserMessageIfClaimNotVerified** | Deklarace identity není ověřená:{0} |
| **UserMessageIfIncorrectPattern** | Nesprávný vzor pro:{0} |
| **UserMessageIfMissingRequiredElement** | Chybí požadovaný element:{0} |
| **UserMessageIfValidationError** | Chyba při ověřování uživatelem:{0} |
| **UserMessageIfInvalidInput** | {0}má neplatný vstup. |
| **ServiceThrottled** | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce pro registraci:

![Přihlašovací stránka s názvy prvků uživatelského rozhraní označená](./media/localization-string-ids/localization-sign-up.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce registrace, poté, co uživatel klikne na tlačítko Odeslat ověřovací kód:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Prvky uživatelského rozhraní stránky pro ověřování na telefonním faktoru

Níže jsou uvedené identifikátory pro definici obsahu s ID `api.phonefactor`.

| id | Výchozí hodnota |
| -- | ------------- |
| **button_verify** | Zavolejte mi |
| **country_code_label** | Kód země |
| **cancel_message** | Uživatel zrušil službu Multi-Factor Authentication. |
| **text_button_send_second_code** | poslat nový kód |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Pro vás budeme nahrávat následující číslo. K ověření vám můžeme poslat kód přes SMS nebo telefon. |
| **intro_mixed_p** | Pro vás budeme nahrávat následující čísla. Vyberte číslo, které můžeme použít k telefonování, nebo pošle kód přes SMS, abychom vás ověřili. |
| **button_verify_code** | Ověřit kód |
| **requiredField_code** | Zadejte prosím ověřovací kód, který jste dostali. |
| **invalid_code** | Zadejte prosím 6místný kód, který jste dostali. |
| **button_cancel** | Zrušit |
| **local_number_input_placeholder_text** | Telefonní číslo |
| **button_retry** | Retry |
| **alternative_text** | Nemám telefon. |
| **intro_phone_p** | Pro vás budeme nahrávat následující čísla. Vyberte číslo, na které můžeme telefon ověřit. |
| **intro_phone** | Pro vás budeme nahrávat následující číslo. Budeme vám telefonicky ověřit. |
| **enter_code_text_intro** | Zadejte níže ověřovací kód, nebo  |
| **intro_entry_phone** | Níže zadejte číslo, na které můžeme zavolat, abychom vás ověřili. |
| **intro_entry_sms** | Níže zadejte číslo, na které můžeme poslat kód přes SMS, abychom vás ověřili. |
| **button_send_code** | Poslat kód |
| **invalid_number** | Zadejte platné telefonní číslo. |
| **intro_sms** | Pro vás budeme nahrávat následující číslo. Prostřednictvím SMS vám pošleme kód, abychom vás ověřili. |
| **intro_entry_mixed** | Níže zadejte číslo, na které můžeme poslat kód přes SMS nebo zavolat, abychom vás ověřili. |
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Pro vás budeme nahrávat následující čísla. Vyberte číslo, na které můžeme poslat kód prostřednictvím SMS, abychom vás mohli ověřit. |
| **requiredField_countryCode** | Vyberte prosím kód země. |
| **requiredField_number** | Zadejte prosím svoje telefonní číslo. |
| **country_code_input_placeholder_text** |Země nebo oblast |
| **number_label** | Telefonní číslo |
| **error_tryagain** | Telefonní číslo, které jste zadali, je zaneprázdněno nebo není k dispozici. Zkontrolujte prosím číslo a zkuste to znovu. |
| **error_incorrect_code** | Ověřovací kód, který jste zadali, neodpovídá našim záznamům. Zkuste to prosím znovu nebo si vyžádejte nový kód. |
| **countryList** | {\"Default\":země/\"oblast,AF\":\"Afghánistán,\"AX:Aland\"\"\"\"\" Ostrovy\",\"Al:\"Albánie\",DZ\":Alžírsko,jako\":\"\"\"\"\"\" Americká Samoa\",\"ad\":Andorry\",\"Ao:\"Angola,AI\":\"\"\"\" \"Anguilla,\"AQ\":Antarktida\",AG: Antigua aBarbuda\",\"\"\"\"\"\"Ar\":\"Argentina,am\":Arménská, AW:\"Aruba\"\"\"\"\"\" \",Au\":\"Austrálie,\"na:Rakousko,\"\"\"\"\"\" AZ\":\"Ázerbájdžán,\"BS\":Bahamy\",BH:\"\"\"\"\" Bahrajn\",\"BD:\"Bangladéš\",BB\":Barbados,\"\"\"\"\" O\":\"Bělorusko,\":Belgie\",BZ:\"\"\"\"\"\" Belize\",\"BJ:\"Benin\", BM:Bermudy\",\"\"\"\"\"BT\":Bhútán\",\"Bo:\"Bolívie,BQ–:\"\"\"\"\"\" Bonaire\",\"ba:Bosna\"aHercegovina\",černobílá\":Botswana\"\"\"<span class="notransla class=""></span class="notransla> Odlehlé ostrovy\",\"VI\": USA\" Panenské ostrovy\",\"g\":Uganda\",\"UA:\"Ukrajina,AE:\"\"\"\"\"\" Spojené arabské emiráty\",\"GB\":\"spojenékrálovství\",USA\":USA\"uy\"\"\" \":Uruguay\",\"uz:\"Uzbekistán, VU:Vanuatu\",\"\"\"\"\"\" \"VA:\"Vatikán\",ve\":Venezuela,vn\": Vietnam\"\"\"\"\"\" \",WF\":Wallisa\"Futuna,je\":\"Jemen,\"zm\"\"\"\"\":Zambie,\"ZW:Zimbabwe\"}\"\"\"\" |
| **error_448** | Telefonní číslo, které jste zadali, není dostupné. |
| **error_449** | Uživatel překročil počet opakování pokusů. |
| **verification_code_input_placeholder_text** | Ověřovací kód |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce pro zápis MFA:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-mfa1.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce ověřování MFA:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-mfa2.png)







