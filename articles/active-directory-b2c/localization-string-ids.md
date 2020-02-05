---
title: ID lokalizačních řetězců – Azure Active Directory B2C | Microsoft Docs
description: Zadejte ID definice obsahu s ID API. signuporsignin ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 02/03/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: cd5e543a6d5dc2b59847a78bd925be68f8cdde25
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982853"
---
# <a name="localization-string-ids"></a>ID řetězců lokalizací

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **Localization** umožňuje v zásadách pro cesty uživatelů podporovat více národních prostředí nebo jazyků. Tento článek poskytuje seznam identifikátorů lokalizací, které můžete použít ve svých zásadách. Seznamte se s lokalizací uživatelského rozhraní, najdete v tématu [lokalizace](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Prvky přihlašovacích nebo přihlašovacích stránek

Pro definici obsahu s ID `api.signuporsignin`se použijí následující identifikátory.

| ID | Výchozí hodnota |
| -- | ------------- |
| **local_intro_email** | Přihlaste se pomocí svého existujícího účtu |
| **logonIdentifier_email** | E-mailová adresa |
| **requiredField_email** | Zadejte prosím svůj e-mail. |
| **invalid_email** | Zadejte prosím platnou e-mailovou adresu. |
| **email_pattern** | ^ [a-zA-Z0-9.! # $% & ' ' *+/=? ^ _\`{\|} ~-] + @ [a-za-Z0-9-] + (?:\\. [ a-zA-Z0-9-] +)* $ |
| **local_intro_username** | Přihlaste se pomocí svého uživatelského jména. |
| **logonIdentifier_username** | Uživatelské jméno |
| **requiredField_username** | Zadejte prosím svoje uživatelské jméno. |
| **Heslo** | Heslo |
| **requiredField_password** | Zadejte prosím svoje heslo. |
| **invalid_password** | Heslo, které jste zadali, není v očekávaném formátu. |
| **forgotpassword_link** | Zapomněli jste heslo? |
| **createaccount_intro** | Nemáte účet? |
| **createaccount_link** | Zaregistrujte se |
| **divider_title** | NEBO |
| **cancel_message** | Uživatel zapomněl svoje heslo. |
| **button_signin** | Přihlásit se |
| **social_intro** | Přihlaste se pomocí účtu sociální sítě. |
  **remember_me** |Zůstat přihlášeni|
| **unknown_error** | Máme potíže s přihlášením. Zkuste to prosím znova později. |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na přihlašovací stránce nebo na přihlašovací stránce:

![Prvky uživatelského rozhraní pro registraci nebo přihlašovací stránku](./media/localization-string-ids/localization-susi.png)

ID zprostředkovatelů identity je nakonfigurováno v elementu **ClaimsExchange** na cestě uživatele. Chcete-li lokalizovat název poskytovatele identity, je typ **ElementType** nastaven na hodnotu `ClaimsProvider`, zatímco **StringId** je nastaven na ID `ClaimsExchange`.

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

### <a name="sign-up-or-sign-in-error-messages"></a>Chybové zprávy týkající se registrace nebo přihlašování

| ID | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Heslo není správné. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nemůžeme se zdát, že váš účet najdete. |
| **UserMessageIfOldPasswordUsed** | Vypadá to, že jste použili staré heslo. |
| **DefaultMessage** | Neplatné uživatelské jméno nebo heslo. |
| **UserMessageIfUserAccountDisabled** | Váš účet byl uzamčen. Řekněte pracovníkovi podpory, aby ho odemkl, a pak to zkuste znovu. |
| **UserMessageIfUserAccountLocked** | Váš účet je dočasně zamčený, aby nedocházelo k neoprávněnému použití. Opakujte akci později. |
| **AADRequestsThrottled** | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Prvky uživatelského rozhraní pro registraci a samoobslužné stránky

Níže jsou uvedená ID pro definici obsahu s ID `api.localaccountsignup` nebo jakákoli definice obsahu, která začíná `api.selfasserted`, například `api.selfasserted.profileupdate` a `api.localaccountpasswordreset`.

| ID | Výchozí hodnota |
| -- | ------------- |
| **ver_sent** | Ověřovací kód byl odeslán do: |
| **ver_but_default** | Výchozí |
| **cancel_message** | Uživatel zrušil zadávání informací o vlastním vyhodnocení. |
| **preloader_alt** | Počkejte prosím |
| **ver_but_send** | Odeslat ověřovací kód |
| **alert_yes** | Ano |
| **error_fieldIncorrect** | Některá pole jsou vyplněna nesprávně. Zkontrolujte prosím své položky a zkuste to znovu. |
| **jednolet** | Year |
| **verifying_blurb** | Počkejte prosím, než zpracujeme vaše informace. |
| **button_cancel** | Zrušit |
| **ver_fail_no_retry** | Provedli jste příliš mnoho nesprávných pokusů. Zkuste to prosím znova později. |
| **měsíčně** | Month |
| **ver_success_msg** | E-mailová adresa byla ověřena. Nyní můžete pokračovat. |
| **months** | Leden, únor, březen, duben, květen, červen, červenec, srpen, září, říjen, listopad, prosinec |
| **ver_fail_server** | Při ověřování vaší e-mailové adresy máme problémy. Zadejte prosím platnou e-mailovou adresu a zkuste to znovu. |
| **error_requiredFieldMissing** | Chybí povinné pole. Vyplňte prosím všechna povinná pole a zkuste to znovu. |
| **initial_intro** | Zadejte prosím následující údaje. |
| **ver_but_resend** | Poslat nový kód |
| **button_continue** | Vytváření |
| **error_passwordEntryMismatch** | Pole pro zadání hesla se neshodují. Zadejte prosím do obou polí stejné heslo a zkuste to znovu. |
| **ver_incorrect_format** | Nesprávný formát |
| **ver_but_edit** | Změnit e-mail |
| **ver_but_verify** | Ověřit kód |
| **alert_no** | Ne |
| **ver_info_msg** | Ověřovací kód byl odeslán do vaší doručené pošty. Zkopírujte ho prosím do následujícího pole pro zadání. |
| **dnu** | Den |
| **ver_fail_throttled** | Existuje příliš mnoho žádostí o ověření této e-mailové adresy. Chvíli prosím počkejte a pak to zkuste znovu. |
| **helplink_text** | Co to je? |
| **ver_fail_retry** | Kód je nesprávný. Zkuste to prosím znovu. |
| **alert_title** | Zrušit zadávání podrobností |
| **required_field** | Tato informace je povinná. |
| **alert_message** | Opravdu chcete zrušit zadávání podrobností? |
| **ver_intro_msg** | Ověření je nezbytné. Klikněte prosím na tlačítko Odeslat. |
| **ver_input** | Ověřovací kód |

### <a name="sign-up-and-self-asserted-pages-error-messages"></a>Chybové zprávy pro registraci a samoobslužné stránky

| ID | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Uživatel se zadaným ID už existuje. Zvolte prosím jiný. |
| **UserMessageIfClaimNotVerified** | Deklarace identity není ověřená: {0} |
| **UserMessageIfIncorrectPattern** | Nesprávný vzor pro: {0} |
| **UserMessageIfMissingRequiredElement** | Chybí požadovaný element: {0} |
| **UserMessageIfValidationError** | Chyba při ověřování pomocí: {0} |
| **UserMessageIfInvalidInput** | {0} má neplatný vstup. |
| **ServiceThrottled** | V tuto chvíli je moc velký počet požadavků. Chvíli prosím počkejte a zkuste to znovu. |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce pro registraci:

![Přihlašovací stránka s názvy prvků uživatelského rozhraní označená](./media/localization-string-ids/localization-sign-up.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce registrace, poté, co uživatel klikne na tlačítko Odeslat ověřovací kód:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Prvky uživatelského rozhraní stránky pro ověřování na telefonním faktoru

Níže jsou uvedené identifikátory pro definici obsahu s ID `api.phonefactor`.

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
| **invalid_code** | Zadejte prosím 6. kód číslice, který jste dostali. |
| **button_cancel** | Zrušit |
| **local_number_input_placeholder_text** | Telefonní číslo |
| **button_retry** | Retry |
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
| **number_pattern** | ^\\+(?:[0-9][\\x20-]?){6,14}[0-9]$ |
| **intro_sms_p** |Pro vás budeme nahrávat následující čísla. Vyberte číslo, na které můžeme poslat kód prostřednictvím SMS, abychom vás mohli ověřit. |
| **requiredField_countryCode** | Vyberte prosím kód země. |
| **requiredField_number** | Zadejte prosím svoje telefonní číslo. |
| **country_code_input_placeholder_text** |Země nebo oblast |
| **number_label** | Telefonní číslo |
| **error_tryagain** | Telefonní číslo, které jste zadali, je zaneprázdněno nebo není k dispozici. Zkontrolujte prosím číslo a zkuste to znovu. |
| **error_incorrect_code** | Ověřovací kód, který jste zadali, neodpovídá našim záznamům. Zkuste to prosím znovu nebo si vyžádejte nový kód. |
| **countryList** | {\"DEFAULT\":\"Country/Region\",\"AF\":\"Afghanistan\",\"AX\":\"Åland Islands\",\"AL\":\"Albania\",\"DZ\":\"Algeria\",\"AS\":\"American Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\":\"Anguilla\",\"AQ\":\"Antarctica\",\"AG\":\"Antigua and Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Armenia\",\"AW\":\"Aruba\",\"AU\":\"Australia\",\"AT\":\"Austria\",\"AZ\":\"Azerbaijan\",\"BS\":\"Bahamas\",\"BH\":\"Bahrain\",\"BD\":\"Bangladesh\",\"BB\":\"Barbados\",\"BY\":\"Belarus\",\"BE\":\"Belgium\",\"BZ\":\"Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermuda\",\"BT\":\"Bhutan\",\"BO\":\"Bolivia\",\"BQ\":\"Bonaire\",\"BA\":\"Bosnia and Herzegovina\",\"BW\":\"Botswana\",\"BV\":\"Bouvet Island\",\"BR\":\"Brazil\",\"IO\":\"British Indian Ocean Territory\",\"VG\":\"British Virgin Islands\",\"BN\":\"Brunei\",\"BG\":\"Bulgaria\",\"BF\":\"Burkina Faso\",\"BI\":\"Burundi\",\"CV\":\"Cabo Verde\",\"KH\":\"Cambodia\",\"CM\":\"Cameroon\",\"CA\":\"Canada\",\"KY\":\"Cayman Islands\",\"CF\":\"Central African Republic\",\"TD\":\"Chad\",\"CL\":\"Chile\",\"CN\":\"China\",\"CX\":\"Christmas Island\",\"CC\":\"Cocos (Keeling) Islands\",\"CO\":\"Colombia\",\"KM\":\"Comoros\",\"CG\":\"Congo\",\"CD\":\"Congo (DRC)\",\"CK\":\"Cook Islands\",\"CR\":\"Costa Rica\",\"CI\":\"Côte d’Ivoire\",\"HR\":\"Croatia\",\"CU\":\"Cuba\",\"CW\":\"Curaçao\",\"CY\":\"Cyprus\",\"CZ\":\"Czech Republic\",\"DK\":\"Denmark\",\"DJ\":\"Djibouti\",\"DM\":\"Dominica\",\"DO\":\"Dominican Republic\",\"EC\":\"Ecuador\",\"EG\":\"Egypt\",\"SV\":\"El Salvador\",\"GQ\":\"Equatorial Guinea\",\"ER\":\"Eritrea\",\"EE\":\"Estonia\",\"ET\":\"Ethiopia\",\"FK\":\"Falkland Islands\",\"FO\":\"Faroe Islands\",\"FJ\":\"Fiji\",\"FI\":\"Finland\",\"FR\":\"France\",\"GF\":\"French Guiana\",\"PF\":\"French Polynesia\",\"TF\":\"French Southern Territories\",\"GA\":\"Gabon\",\"GM\":\"Gambia\",\"GE\":\"Georgia\",\"DE\":\"Germany\",\"GH\":\"Ghana\",\"GI\":\"Gibraltar\",\"GR\":\"Greece\",\"GL\":\"Greenland\",\"GD\":\"Grenada\",\"GP\":\"Guadeloupe\",\"GU\":\"Guam\",\"GT\":\"Guatemala\",\"GG\":\"Guernsey\",\"GN\":\"Guinea\",\"GW\":\"Guinea-Bissau\",\"GY\":\"Guyana\",\"HT\":\"Haiti\",\"HM\":\"Heard Island and McDonald Islands\",\"HN\":\"Honduras\",\"HK\":\"Hong Kong SAR\",\"HU\":\"Hungary\",\"IS\":\"Iceland\",\"IN\":\"India\",\"ID\":\"Indonesia\",\"IR\":\"Iran\",\"IQ\":\"Iraq\",\"IE\":\"Ireland\",\"IM\":\"Isle of Man\",\"IL\":\"Israel\",\"IT\":\"Italy\",\"JM\":\"Jamaica\",\"JP\":\"Japan\",\"JE\":\"Jersey\",\"JO\":\"Jordan\",\"KZ\":\"Kazakhstan\",\"KE\":\"Kenya\",\"KI\":\"Kiribati\",\"KR\":\"Korea\",\"KW\":\"Kuwait\",\"KG\":\"Kyrgyzstan\",\"LA\":\"Laos\",\"LV\":\"Latvia\",\"LB\":\"Lebanon\",\"LS\":\"Lesotho\",\"LR\":\"Liberia\",\"LY\":\"Libya\",\"LI\":\"Liechtenstein\",\"LT\":\"Lithuania\",\"LU\":\"Luxembourg\",\"MO\":\"Macao SAR\",\"MK\":\"North Macedonia\",\"MG\":\"Madagascar\",\"MW\":\"Malawi\",\"MY\":\"Malaysia\",\"MV\":\"Maldives\",\"ML\":\"Mali\",\"MT\":\"Malta\",\"MH\":\"Marshall Islands\",\"MQ\":\"Martinique\",\"MR\":\"Mauritania\",\"MU\":\"Mauritius\",\"YT\":\"Mayotte\",\"MX\":\"Mexico\",\"FM\":\"Micronesia\",\"MD\":\"Moldova\",\"MC\":\"Monaco\",\"MN\":\"Mongolia\",\"ME\":\"Montenegro\",\"MS\":\"Montserrat\",\"MA\":\"Morocco\",\"MZ\":\"Mozambique\",\"MM\":\"Myanmar\",\"NA\":\"Namibia\",\"NR\":\"Nauru\",\"NP\":\"Nepal\",\"NL\":\"Netherlands\",\"NC\":\"New Caledonia\",\"NZ\":\"New Zealand\",\"NI\":\"Nicaragua\",\"NE\":\"Niger\",\"NG\":\"Nigeria\",\"NU\":\"Niue\",\"NF\":\"Norfolk Island\",\"KP\":\"North Korea\",\"MP\":\"Northern Mariana Islands\",\"NO\":\"Norway\",\"OM\":\"Oman\",\"PK\":\"Pakistan\",\"PW\":\"Palau\",\"PS\":\"Palestinian Authority\",\"PA\":\"Panama\",\"PG\":\"Papua New Guinea\",\"PY\":\"Paraguay\",\"PE\":\"Peru\",\"PH\":\"Philippines\",\"PN\":\"Pitcairn Islands\",\"PL\":\"Poland\",\"PT\":\"Portugal\",\"PR\":\"Puerto Rico\",\"QA\":\"Qatar\",\"RE\":\"Réunion\",\"RO\":\"Romania\",\"RU\":\"Russia\",\"RW\":\"Rwanda\",\"BL\":\"Saint Barthélemy\",\"KN\":\"Saint Kitts and Nevis\",\"LC\":\"Saint Lucia\",\"MF\":\"Saint Martin\",\"PM\":\"Saint Pierre and Miquelon\",\"VC\":\"Saint Vincent and the Grenadines\",\"WS\":\"Samoa\",\"SM\":\"San Marino\",\"ST\":\"São Tomé and Príncipe\",\"SA\":\"Saudi Arabia\",\"SN\":\"Senegal\",\"RS\":\"Serbia\",\"SC\":\"Seychelles\",\"SL\":\"Sierra Leone\",\"SG\":\"Singapore\",\"SX\":\"Sint Maarten\",\"SK\":\"Slovakia\",\"SI\":\"Slovenia\",\"SB\":\"Solomon Islands\",\"SO\":\"Somalia\",\"ZA\":\"South Africa\",\"GS\":\"South Georgia and South Sandwich Islands\",\"SS\":\"South Sudan\",\"ES\":\"Spain\",\"LK\":\"Sri Lanka\",\"SH\":\"St Helena, Ascension, Tristan da Cunha\",\"SD\":\"Sudan\",\"SR\":\"Suriname\",\"SJ\":\"Svalbard\",\"SZ\":\"Swaziland\",\"SE\":\"Sweden\",\"CH\":\"Switzerland\",\"SY\":\"Syria\",\"TW\":\"Taiwan\",\"TJ\":\"Tajikistan\",\"TZ\":\"Tanzania\",\"TH\":\"Thailand\",\"TL\":\"Timor-Leste\",\"TG\":\"Togo\",\"TK\":\"Tokelau\",\"TO\":\"Tonga\",\"TT\":\"Trinidad and Tobago\",\"TN\":\"Tunisia\",\"TR\":\"Turkey\",\"TM\":\"Turkmenistan\",\"TC\":\"Turks and Caicos Islands\",\"TV\":\"Tuvalu\",\"UM\":\"U.S. Outlying Islands\",\"VI\":\"U.S. Virgin Islands\",\"UG\":\"Uganda\",\"UA\":\"Ukraine\",\"AE\":\"United Arab Emirates\",\"GB\":\"United Kingdom\",\"US\":\"United States\",\"UY\":\"Uruguay\",\"UZ\":\"Uzbekistan\",\"VU\":\"Vanuatu\",\"VA\":\"Vatican City\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam\",\"WF\":\"Wallis and Futuna\",\"YE\":\"Yemen\",\"ZM\":\"Zambia\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Telefonní číslo, které jste zadali, je nedosažitelné. |
| **error_449** | Uživatel překročil počet opakovaných pokusů. |
| **verification_code_input_placeholder_text** | Ověřovací kód |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce pro zápis MFA:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-mfa1.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce ověřování MFA:

![Prvky uživatelského prostředí ověřování e-mailů pro registraci stránky](./media/localization-string-ids/localization-mfa2.png)

## <a name="verification-display-control-user-interface-elements"></a>Ovládací prvky uživatelského rozhraní pro zobrazení ověření

Níže jsou uvedené identifikátory pro [ovládací prvek zobrazení ověřování](display-control-verification.md) .

| ID | Výchozí hodnota |
| -- | ------------- |
|verification_control_but_change_claims |Změnit |
|verification_control_fail_send_code |Nepovedlo se odeslat kód, zkuste to prosím znovu později. |
|verification_control_fail_verify_code |Nepovedlo se ověřit kód, zkuste to prosím znovu později. |
|verification_control_but_send_code |Odeslat kód |
|verification_control_but_send_new_code |Poslat nový kód |
|verification_control_but_verify_code |Ověřit kód |

## <a name="one-time-password-error-messages"></a>Jednorázové chybové zprávy hesla
Níže jsou uvedené identifikátory, které slouží jako jednorázové chybové zprávy pro [technický profil s heslem](one-time-password-technical-profile.md) .

| ID | Výchozí hodnota |
| -- | ------------- |
|UserMessageIfMaxRetryAttempted |Jednorázové heslo poskytnuté při ověřování překročilo maximální počet pokusů. |
|UserMessageIfSessionDoesNotExist |Jedna časová relace pro ověření hesla vypršela. |
|UserMessageIfSessionConflict |Jedna časová relace ověření hesla je v konfliktu. |
|UserMessageIfInvalidCode |Jedno časové heslo zadané pro ověření není správné. |








