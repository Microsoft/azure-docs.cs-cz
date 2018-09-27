---
title: Lokalizace řetězec ID – Azure Active Directory B2C | Dokumentace Microsoftu
description: Určete ID pro definici obsahu s Id api.signuporsignin vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7619ebf038e164c9e586118c70e34955ca7c4767
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222660"
---
# <a name="localization-string-ids"></a>ID lokalizace řetězce

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Lokalizace** element umožňuje podporovat více jazyky nebo národní prostředí v zásadách pro cesty uživatele. Tento článek obsahuje seznam lokalizace ID, které můžete použít ve svojí zásadě. Seznamte se s lokalizace uživatelského rozhraní, naleznete v tématu [lokalizace](localization.md).

## <a name="sign-up-or-sign-in-page-elements"></a>Prvky stránky registrace nebo přihlašování

Následující ID se používají pro definici obsahu s ID `api.signuporsignin`.

| ID | Výchozí hodnota |
| -- | ------------- |
| **local_intro_email** | Přihlaste se svým stávajícím účtem. |
| **logonIdentifier_email** | E-mailová adresa |
| **requiredField_email** | Zadejte prosím svůj e-mail. |
| **invalid_email** | Zadejte prosím platnou e-mailovou adresu. |
| **email_pattern** | ^ [a-zA-Z0-9.! #$% &'' * +/ =? ^ _' {}|} ~-]+@[a-zA-Z0-9-]+ (?:\\. [ -zA-Z0 - 9-] +) * $ |
| **local_intro_username** | Přihlaste se svým uživatelským jménem. |
| **logonIdentifier_username** | Uživatelské jméno |
| **requiredField_username** | Zadejte prosím svoje uživatelské jméno. |
| **Heslo** | Heslo |
| **requiredField_password** | Zadejte prosím svoje heslo. |
| **invalid_password** | Heslo, které jste zadali, nemá očekávaný formát. |
| **forgotpassword_link** | Zapomněli jste heslo? |
| **createaccount_intro** | Nemáte účet? |
| **createaccount_link** | Zaregistrujte se |
| **divider_title** | NEBO |
| **cancel_message** | Uživatel zapomněl heslo. |
| **button_signin** | Přihlášení |
| **social_intro** | Přihlaste se svým sociálním účtem. |
  **remember_me** |Neodhlašovat|
| **unknown_error** | Máme potíže vás přihlásit. Zkuste to prosím znova později. |

Následující příklad ukazuje použití některých uživatele prvky rozhraní na stránce registrace nebo přihlášení:

![Prvky uživatelského rozhraní stránky registrace nebo přihlašování](./media/localization-string-ids/localization-susi.png)

ID poskytovatele identit je nakonfigurovaný v cestě uživatele **ClaimsExchange** elementu. Chcete-li lokalizovat název zprostředkovatele identity **ElementType** je nastavena na `ClaimsProvider`, při **StringId** je nastavena na ID `ClaimsExchange`.

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

Následující příklad lokalizováno zprostředkovatele identity Facebook Arabské:

```XML
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">فيس بوك</LocalizedString>
```

## <a name="sign-up-or-sign-in-error-messages"></a>Registrace nebo přihlášení chybové zprávy

| ID | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfInvalidPassword** | Vaše heslo není správné. |
| **UserMessageIfClaimsPrincipalDoesNotExist** | Nedaří se nám najít váš účet. |
| **UserMessageIfOldPasswordUsed** | Vypadá to, že jste použili staré heslo. |  
| **DefaultMessage** | Neplatné uživatelské jméno nebo heslo. |  
| **UserMessageIfUserAccountDisabled** | Váš účet je zamknutý. Obraťte se na pracovníka podpory k jeho odemčení a akci opakujte. |  
| **UserMessageIfUserAccountLocked** | Váš účet je dočasně uzamčený, abyste zabránili neoprávněnému používání. Zkuste to znovu později. |  
| **AADRequestsThrottled** | V tomto okamžiku existuje příliš mnoho požadavků. Počkejte chvíli a zkuste to znovu. |  

## <a name="sign-up-and-self-asserted-pages-user-interface-elements"></a>Zaregistrujte se a Samoobslužná uplatněna stránky prvky uživatelského rozhraní

Toto jsou ID pro definici obsahu s ID `api.localaccountsignup` nebo jakékoli definice obsahu, který začíná `api.selfasserted`, jako například `api.selfasserted.profileupdate` a `api.localaccountpasswordreset`.

| ID | Výchozí hodnota |
| -- | ------------- |
| **ver_sent** | Ověřovací kód se poslal sem: |
| **ver_but_default** | Výchozí |
| **cancel_message** | Uživatel zrušil zadávání s vlastním potvrzením informací |
| **preloader_alt** | Počkejte prosím. |
| **ver_but_send** | Poslat ověřovací kód |
| **alert_yes** | Ano |
| **error_fieldIncorrect** | Jeden nebo více polí se vyplní nesprávně. Zkontrolujte zadání a akci opakujte. |
| **Rok** | Rok |
| **verifying_blurb** | Počkejte prosím, než zpracujeme vaše informace. |
| **button_cancel** | Zrušit |
| **ver_fail_no_retry** | Provedli jste příliš mnoho pokusů o nesprávné. Zkuste to prosím znova později. |
| **Měsíc** | Měsíc |
| **ver_success_msg** | Ověřit e-mailovou adresu. Teď můžete pokračovat. |
| **Měsíců** | leden, únor, březen, duben, květen, červen, červenec, srpen, září, říjen, listopad, prosinec |
| **ver_fail_server** | Máme potíže s ověřením vaší e-mailovou adresu. Zadejte platnou e-mailovou adresu a zkuste to znovu. |
| **error_requiredFieldMissing** | Chybí povinné pole. Vyplňte všechna povinná pole a akci opakujte. |
| **initial_intro** | Zadejte prosím následující údaje. |
| **ver_but_resend** | Poslat nový kód |
| **button_continue** | Vytvořit |
| **error_passwordEntryMismatch** | Pole pro zadání hesla se neshodují. Do obou polí zadejte stejné heslo a zkuste to znovu. |
| **ver_incorrect_format** | Nesprávný formát. |
| **ver_but_edit** | Změnit e-mail |
| **ver_but_verify** | Ověřit kód |
| **alert_no** | Ne |
| **ver_info_msg** | Poslali jsme ověřovací kód do vaší doručené pošty. Zkopírujte jej do vstupního pole dole. |
| **den** | Den |
| **ver_fail_throttled** | Byly příliš mnoho žádostí o ověření tuto e-mailovou adresu. Prosím počkejte a zkuste to znovu. |
| **helplink_text** | Co je to? |
| **ver_fail_retry** | Tento kód je nesprávný. Zkuste to prosím znovu. |
| **alert_title** | Zrušit zadávání podrobností |
| **required_field** | Tyto informace jsou požadovány. |
| **alert_message** | Určitě chcete zrušit zadávání podrobností? |
| **ver_intro_msg** | Ověření je nezbytné. Klepnutím na tlačítko Odeslat. |
| **ver_input** | Ověřovací kód |

## <a name="sign-up-and-self-asserted-pages-error-messages"></a>Zaregistrujte se a Samoobslužná uplatněna stránky chybových zpráv

| ID | Výchozí hodnota |
| -- | ------------- |
| **UserMessageIfClaimsPrincipalAlreadyExists** | Uživatel se zadaným ID už existuje. Zvolte prosím jiný. |
| **UserMessageIfClaimNotVerified** | Deklarace identity Neověřeno: {0} |
| **UserMessageIfIncorrectPattern** | Nesprávný model pro: {0} |
| **UserMessageIfMissingRequiredElement** | Chybí požadovaný element: {0} |
| **UserMessageIfValidationError** | Chyba při ověřování pomocí: {0} |
| **UserMessageIfInvalidInput** | {0} má neplatný vstup. |
| **ServiceThrottled** | V tomto okamžiku existuje příliš mnoho požadavků. Počkejte chvíli a zkuste to znovu. |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce registrace:

![Prvky uživatelského rozhraní stránku pro přihlášení](./media/localization-string-ids/localization-sign-up.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce registrace, po kliknutí na poslat ověřovací kód tlačítko:

![Prvky uživatelského rozhraní ověření e-mailu registrační stránku](./media/localization-string-ids/localization-email-verification.png)


## <a name="phone-factor-authentication-page-user-interface-elements"></a>Phone stránku uživatele u služby Multi-Factor authentication prvky rozhraní

Toto jsou ID pro definici obsahu s ID `api.phonefactor`. 

| ID | Výchozí hodnota |
| -- | ------------- |
| **button_verify** | Zavolejte mi |
| **country_code_label** | Kód země |
| **cancel_message** | Uživatel zrušil ověřování službou Multi-Factor Authentication |
| **text_button_send_second_code** | poslat nový kód |
| **code_pattern** | \\d{6} |
| **intro_mixed** | Máme následující číslo záznamu za vás. Nám můžete poslat kód prostřednictvím SMS nebo telefonní k vašemu ověření. |
| **intro_mixed_p** | Pro vás máme následující čísla na záznam. Zvolte číslo, které můžou telefon nebo poslat kód prostřednictvím SMS k vašemu ověření. |
| **button_verify_code** | Ověřit kód |
| **requiredField_code** | Zadejte prosím ověřovací kód, který jste dostali. |
| **invalid_code** | Zadejte prosím 6místný kód, který jste dostali. |
| **button_cancel** | Zrušit |
| **local_number_input_placeholder_text** | Telefonní číslo |
| **button_retry** | Retry |
| **alternative_text** | Nemám telefon. |
| **intro_phone_p** | Pro vás máme následující čísla na záznam. Zvolte číslo, které nám můžete phone k vašemu ověření. |
| **intro_phone** | Máme následující číslo záznamu za vás. Můžeme se telefonní k vašemu ověření. |
| **enter_code_text_intro** | Zadejte níže ověřovací kód, nebo  |
| **intro_entry_phone** | Níže zadejte číslo, na které můžeme zavolat, abychom vás ověřili. |
| **intro_entry_sms** | Níže zadejte číslo, na které můžeme poslat kód přes SMS, abychom vás ověřili. |
| **button_send_code** | Poslat kód |
| **invalid_number** | Zadejte platné telefonní číslo. |
| **intro_sms** | Máme následující číslo záznamu za vás. Pošleme kód zprávou SMS k vašemu ověření. |
| **intro_entry_mixed** | Níže zadejte číslo, na které můžeme poslat kód přes SMS nebo zavolat, abychom vás ověřili. |
| **number_pattern** | ^\\+ (?: [0-9] [\\x20-]?) {6,14}[0-9] $ |
| **intro_sms_p** |Pro vás máme následující čísla na záznam. Zvolte číslo, že vám můžeme poslat kód prostřednictvím SMS k vašemu ověření. |
| **requiredField_countryCode** | Vyberte prosím kód země. |
| **requiredField_number** | Zadejte prosím své telefonní číslo. |
| **country_code_input_placeholder_text** |Země nebo oblast |
| **number_label** | Telefonní číslo |
| **error_tryagain** | Telefonní číslo, které jste zadali, je zaneprázdněná nebo není k dispozici. Zkontrolujte číslo a zkuste to znovu. |
| **error_incorrect_code** | Ověřovací kód, který jste zadali, neodpovídá našim záznamům. Zkuste to znovu, nebo požádat o nový kód. |
| **countryList** | {\"Výchozí\":\"země/oblast\",\"AF\":\"Afghánistán\",\"AX\":\"Land Ostrovy\",\"AL\":\"Albánie\",\"DZ\":\"Alžírsko\",\"AS\":\" Americká Samoa\",\"AD\":\"Andorra\",\"AO\":\"Angola\",\"AI\": \"Anguilla\",\"AQ\":\"Antarktida\",\"AG\":\"Antigua a Barbuda\",\"AR\":\"Argentina\",\"AM\":\"Arménie\",\"AW\":\"Aruba \",\"AU\":\"Austrálie\",\"na\":\"Rakousko\",\" AZ\":\"Ázerbájdžán\",\"BS\":\"Bahamy\",\"BH\":\" Bahrajn\",\"BD\":\"Bangladéš\",\"BB\":\"Barbados\",\" PODLE\":\"Bělorusko\",\"BE\":\"Belgie\",\"BZ\":\" Belize\",\"BJ\":\"Benin\",\"BM\":\"Bermudy\",\"BT\":\"Bhútán\",\"BO\":\"Bolívie\",\"BQ\":\" Bonaire\",\"BA\":\"Bosna a Hercegovina\",\"BW\":\"Botswana<span class="notransla class=""></span class="notransla> Menší odlehlé ostrovy\",\"VI\":\"USA Panenské ostrovy\",\"UG\":\"Uganda\",\"UA\":\"Ukrajina\",\"AE\":\" Spojené arabské emiráty\",\"GB\":\"Spojené království\",\"USA\":\"USA\",\"UY \":\"Uruguay\",\"UZ\":\"Uzbekistán\",\"VU\":\"Vanuatu\", \"Posouzení ohrožení zabezpečení\":\"Vatikán\",\"VE\":\"Venezuela\",\"VN\":\"Vietnam \",\"WF\":\"Wallis a Futuna\",\"YE\":\"Jemen\",\"ZM\":\"Zambie\",\"ZW\":\"Zimbabwe\"} |
| **error_448** | Telefonní číslo, které jste zadali, není dostupné. |
| **error_449** | Uživatel překročil počet opakování pokusů. |
| **verification_code_input_placeholder_text** | Ověřovací kód |

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce registrace vícefaktorového ověřování:

![Prvky uživatelského rozhraní ověření e-mailu registrační stránku](./media/localization-string-ids/localization-mfa1.png)

Následující příklad ukazuje použití některých prvků uživatelského rozhraní na stránce ověřování MFA:

![Prvky uživatelského rozhraní ověření e-mailu registrační stránku](./media/localization-string-ids/localization-mfa2.png)







