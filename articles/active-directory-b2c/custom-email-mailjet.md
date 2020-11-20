---
title: Vlastní ověření e-mailu pomocí Mailjet
titleSuffix: Azure AD B2C
description: Naučte se integrovat s Mailjet, abyste mohli přizpůsobit ověřovací e-mail odesílaný zákazníkům při registraci k používání aplikací s podporou Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b74de2bdf1f6239f1006c820579a336946939421
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949577"
---
# <a name="custom-email-verification-with-mailjet"></a>Vlastní ověření e-mailu pomocí Mailjet

Pomocí vlastního e-mailu v Azure Active Directory B2C (Azure AD B2C) můžete posílat přizpůsobené e-maily uživatelům, kteří se zaregistrují k používání vašich aplikací. Pomocí [DisplayControls](display-controls.md) (aktuálně ve verzi Preview) a poskytovatele e-mailu třetích stran Mailjet můžete použít vlastní e-mailovou šablonu a z těchto umístění *:* adresa a předmět, stejně jako lokalizace podpory a vlastní nastavení jednorázového hesla (jednorázového hesla).

Vlastní ověřování e-mailu vyžaduje použití poskytovatele e-mailu jiného výrobce, jako je [Mailjet](https://Mailjet.com), [SendGrid](./custom-email-sendgrid.md)nebo [SparkPost](https://sparkpost.com), vlastní REST API nebo libovolného poskytovatele e-mailu založeného na protokolu HTTP (včetně vaší vlastní). Tento článek popisuje nastavení řešení, které používá Mailjet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Vytvoření účtu Mailjet

Pokud ho ještě nemáte, začněte tím, že nastavíte účet Mailjet (zákazníci Azure můžou odemknout 6 000 e-mailů s omezením 200 e-mailů za den). 

1. Postupujte podle pokynů k instalaci v tématu [Vytvoření účtu Mailjet](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. Aby bylo možné odesílat e-maily, [Registrovat a ověřovat](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) vaši e-mailovou adresu nebo doménu odesílatele.
2. Přejděte na [stránku správy klíčů rozhraní API](https://app.mailjet.com/account/api_keys). Zaznamenejte **klíč rozhraní API** a **tajný klíč** pro použití v pozdějším kroku. Oba klíče jsou generovány automaticky při vytvoření vašeho účtu.  

## <a name="create-azure-ad-b2c-policy-key"></a>Vytvořit klíč zásad Azure AD B2C

V dalším kroku uložte klíč rozhraní API Mailjet do klíče zásad Azure AD B2C, aby se zásady odkazovaly na.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce **Přehled** vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. V případě **možností** vyberte možnost **ručně**.
1. Zadejte **název** klíče zásad. Například, `MailjetApiKey`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Do **tajného** klíče zadejte svůj **klíč rozhraní API** Mailjet, který jste předtím nahráli.
1. V případě **použití klíče** vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. V případě **možností** vyberte možnost **ručně**.
1. Zadejte **název** klíče zásad. Například, `MailjetSecretKey`. Předpona `B2C_1A_` se automaticky přidá do názvu vašeho klíče.
1. Do **tajného** klíče zadejte svůj **tajný klíč** Mailjet, který jste předtím nahráli.
1. V případě **použití klíče** vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.

## <a name="create-a-mailjet-template"></a>Vytvoření šablony Mailjet

Když je vytvořený účet Mailjet a klíč rozhraní Mailjet API uložený v klíči zásad Azure AD B2C, vytvořte [šablonu dynamické transakce](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)Mailjet.

1. Na webu Mailjet otevřete stránku [transakční šablony](https://app.mailjet.com/templates/transactional) a vyberte **vytvořit novou šablonu**.
1. Vyberte **ho pomocí kódování ve formátu HTML** a pak vyberte **kód od začátku**.
1. Zadejte jedinečný název šablony, jako `Verification email` je, a pak vyberte **vytvořit**.
1. V editoru HTML vložte následující šablonu HTML nebo použijte vlastní. `{{var:otp:""}}`Parametry a `{{var:email:""}}` budou dynamicky nahrazeny hodnotou jednorázového hesla a e-mailovou adresou uživatele.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Rozbalte položku **upravit předmět** v levém horním rohu.
    1. Pro **Předmět** zadejte výchozí hodnotu předmětu. Mailjet tuto hodnotu používá, když rozhraní API neobsahuje parametr Subject.
    1. Jako **název** zadejte název vaší společnosti.
    1. V poli **adresa** vyberte svou e-mailovou adresu.
    1. Vyberte **Uložit**.
1. V pravém horním rohu vyberte **uložit & publikovat** a pak klikněte na **Ano, publikovat změny** .
1. Poznamenejte si **ID** šablony, kterou jste vytvořili pro použití v pozdějším kroku. Toto ID můžete zadat při [přidávání transformace deklarací identity](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Přidat Azure AD B2C typy deklarací identity

V zásadách přidejte následující typy deklarací identity do `<ClaimsSchema>` prvku v rámci `<BuildingBlocks>` .

Tyto typy deklarací identity jsou nezbytné k vygenerování a ověření e-mailové adresy pomocí kódu jednorázového hesla (jednorázového hesla).

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="emailRequestBody">
  <DisplayName>Mailjet request body</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="VerificationCode">
  <DisplayName>Secondary Verification Code</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Enter your email verification code</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

## <a name="add-the-claims-transformation"></a>Přidání transformace deklarací identity

V dalším kroku budete potřebovat transformaci deklarací identity pro výstup deklarace řetězce JSON, který bude podléhat textu žádosti odeslané do Mailjet.

Struktura objektu JSON je definována ID v desítkovém zápisu vstupními parametry a TransformationClaimTypesem InputClaims. Čísla v zápisu teček implikují pole. Hodnoty pocházejí z hodnot InputClaims a vstupní hodnoty vlastností value. Další informace o transformacích deklarací JSON najdete v tématu [transformace deklarací JSON](json-transformations.md).

Přidejte následující transformaci deklarací identity do `<ClaimsTransformations>` elementu v rámci `<BuildingBlocks>` . Proveďte následující aktualizace XML transformace deklarací identity:

* Aktualizujte `Messages.0.TemplateID` hodnotu InputParameter pomocí ID transakční šablony Mailjet, kterou jste vytvořili dříve v části [Vytvoření šablony Mailjet](#create-a-mailjet-template).
* Aktualizujte `Messages.0.From.Email` hodnotu adresy. Pomocí platné e-mailové adresy můžete zabránit tomu, aby byl ověřovací e-mail označený jako spam.
* Aktualizujte hodnotu `Messages.0.Subject` vstupního parametru řádku předmětu s řádkem předmětu, který je vhodný pro vaši organizaci.

```XML
<ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your Mailjet template. -->
    <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
    <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

    <!-- Update with an email appropriate for your organization. -->
    <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Přidat definici obsahu DataUri

Pod transformací deklarací identity v rámci `<BuildingBlocks>` přidejte následující [ContentDefinition](contentdefinitions.md) , které odkazují na identifikátor URI dat 2.1.0 verze:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
  </ContentDefinition>
  <ContentDefinition Id="api.localaccountpasswordreset">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Vytvoření zobrazitelné ovládací prvek

K ověření e-mailové adresy s ověřovacím kódem, který se pošle uživateli, se použije ovládací prvek zobrazení ověřování.

Tento příklad ovládacího prvku zobrazení je nakonfigurován na:

1. Shromažďovat `email` typ deklarace adres od uživatele
1. Počkejte, až uživatel poskytne `verificationCode` typ deklarace s kódem odesílaným uživateli.
1. Vraťte se `email` do technického profilu s vlastním uplatněním, který má odkaz na tento ovládací prvek zobrazení.
1. Pomocí `SendCode` Akce vygenerujte kód pro jednorázové heslo a odešlete e-mail s kódem jednorázového hesla uživateli.

   ![Akce poslat e-mail s ověřovacím kódem](media/custom-email-mailjet/display-control-verification-email-action-01.png)

V části definice obsahu dál `<BuildingBlocks>` přidejte do zásady následující [ovládací prvek](display-controls.md) typu [VerificationControl](display-control-verification.md) .

```XML
<DisplayControls>
  <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
    <DisplayClaims>
      <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
      <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
    </DisplayClaims>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="email" />
    </OutputClaims>
    <Actions>
      <Action Id="SendCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
        </ValidationClaimsExchange>
      </Action>
      <Action Id="VerifyCode">
        <ValidationClaimsExchange>
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
        </ValidationClaimsExchange>
      </Action>
    </Actions>
  </DisplayControl>
</DisplayControls>
```

## <a name="add-otp-technical-profiles"></a>Přidat technické profily jednorázového hesla

`GenerateOtp`Technický profil generuje kód pro e-mailovou adresu. `VerifyOtp`Technický profil ověří kód přidružený k e-mailové adrese. Můžete změnit konfiguraci formátu a vypršení platnosti jednorázového hesla. Další informace o technických profilech pro jednorázové heslo najdete v tématu [definice technického profilu s](one-time-password-technical-profile.md)jednorázovým heslem.

Do prvku přidejte následující technické profily `<ClaimsProviders>` .

```XML
<ClaimsProvider>
  <DisplayName>One time password technical profiles</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="GenerateOtp">
      <DisplayName>Generate one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">GenerateCode</Item>
        <Item Key="CodeExpirationInSeconds">1200</Item>
        <Item Key="CodeLength">6</Item>
        <Item Key="CharacterSet">0-9</Item>
        <Item Key="ReuseSameCode">true</Item>
        <Item Key="MaxNumAttempts">5</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
      </OutputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="VerifyOtp">
      <DisplayName>Verify one time password</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="Operation">VerifyCode</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
      </InputClaims>
    </TechnicalProfile>
   </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="add-a-rest-api-technical-profile"></a>Přidat profil REST API Technical

Tento REST API technický profil generuje obsah e-mailu (pomocí formátu Mailjet). Další informace o technických profilech RESTful najdete v tématu [definice technického profilu RESTful](restful-technical-profile.md).

Stejně jako u technických profilů jednorázového hesla přidejte do elementu následující technické profily `<ClaimsProviders>` .

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Vytvořit odkaz na ovládací prvek zobrazit

V posledním kroku přidejte odkaz na ovládací prvek, který jste vytvořili. Stávající `LocalAccountSignUpWithLogonEmail` a `LocalAccountDiscoveryUsingEmailAddress` vlastní kontrolní profily nahraďte následujícími. Pokud jste použili starší verzi Azure AD B2C zásad. Tyto technické profily využívají `DisplayClaims` odkaz na ovládací prvek zobrazit.

Další informace najdete v tématu [technický profil s vlastním kontrolním](restful-technical-profile.md) výrazem a [Zobrazit](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Volitelné Lokalizace e-mailu

Chcete-li lokalizovat e-mail, je nutné odeslat lokalizované řetězce do Mailjet nebo poskytovatele e-mailu. Můžete například lokalizovat předmět, tělo, zprávu kódu nebo podpis e-mailu. K tomu můžete použít transformaci deklarací [GetLocalizedStringsTransformation](string-transformations.md) ke kopírování lokalizovaných řetězců do typů deklarací. `GenerateEmailRequestBody`Transformace deklarací identity, která generuje datovou část JSON, používá vstupní deklarace identity, které obsahují lokalizované řetězce.

1. V zásadách definujte následující deklarace řetězců: předmět, zpráva, codeIntro a podpis.
1. Definujte transformaci deklarací [GetLocalizedStringsTransformation](string-transformations.md) k nahrazení lokalizovaných hodnot řetězce v deklaracích z kroku 1.
1. Změňte `GenerateEmailRequestBody` transformaci deklarací identity tak, aby používala vstupní deklarace identity s následujícím fragmentem kódu XML.
1. Aktualizujte šablonu Mailjet tak, aby používala dynamické parametry místo všech řetězců, které budou lokalizovány Azure AD B2C.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Přidejte následující element [lokalizace](localization.md) .

    ```xml
    <Localization Enabled="true">
      <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
        <SupportedLanguage>en</SupportedLanguage>
        <SupportedLanguage>es</SupportedLanguage>
      </SupportedLanguages>
      <LocalizedResources Id="api.custom-email.en">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
        </LocalizedStrings>
        </LocalizedStrings>
      </LocalizedResources>
      <LocalizedResources Id="api.custom-email.es">
        <LocalizedStrings>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
          <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
        </LocalizedStrings>
      </LocalizedResources>
    </Localization>
    ```

1. Přidejte odkazy na prvky LocalizedResources aktualizací elementu [ContentDefinitions](contentdefinitions.md) .

    ```xml
    <ContentDefinitions>
      <ContentDefinition Id="api.localaccountsignup">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
      <ContentDefinition Id="api.localaccountpasswordreset">
        <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri>
        <LocalizedResourcesReferences MergeBehavior="Prepend">
          <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
          <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
        </LocalizedResourcesReferences>
      </ContentDefinition>
    </ContentDefinitions>
    ```

1. Nakonec přidejte následující transformaci vstupních deklarací identity do `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` technických profilů a.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>Volitelné Lokalizace uživatelského rozhraní

Element Localization umožňuje v zásadách pro cesty uživatelů podporovat více národních prostředí nebo jazyků. Podpora lokalizace v zásadách umožňuje poskytnout řetězce pro konkrétní jazyk pro [ovládací prvky uživatelského rozhraní pro zobrazení ověřování](localization-string-ids.md#verification-display-control-user-interface-elements)a [jednorázové chybové zprávy hesla](localization-string-ids.md#one-time-password-error-messages). Přidejte následující LocalizedString do svého LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
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
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Po přidání lokalizovaných řetězců odeberte metadata zpráv o chybách ověřování jednorázovým heslem z technických profilů LocalAccountSignUpWithLogonEmail a LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Další kroky

Příklad vlastní zásady ověřování e-mailu najdete na GitHubu:

- [Ověření vlastního e-mailu – DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Informace o použití vlastního REST API nebo kteréhokoli poskytovatele e-mailové adresy SMTP založeného na protokolu HTTP najdete v tématu [definice RESTful Technical Profile ve vlastních zásadách Azure AD B2C](restful-technical-profile.md).
