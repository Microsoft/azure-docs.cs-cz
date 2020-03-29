---
title: Vlastní ověření e-mailu
titleSuffix: Azure AD B2C
description: Přečtěte si, jak přizpůsobit ověřovací e-mail odeslaný zákazníkům, když se zaregistrují k používání aplikací podporujících Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6cc0508a63f26b955ac5e0ebf3ef58a184a35997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671633"
---
# <a name="custom-email-verification-in-azure-active-directory-b2c"></a>Vlastní ověření e-mailu ve službě Azure Active Directory B2C

Pomocí vlastního e-mailu ve službě Azure Active Directory B2C (Azure AD B2C) můžete odesílat přizpůsobené e-maily uživatelům, kteří se zaregistrují k používání vašich aplikací. Pomocí [Ovládacích prvků DisplayControls](display-controls.md) (aktuálně ve verzi preview) a poskytovatele e-mailu třetí strany můžete použít vlastní šablonu e-mailu a *od:* adresu a předmět, stejně jako podporu lokalizace a vlastní nastavení jednorázového hesla (OTP).

Vlastní ověření e-mailu vyžaduje použití poskytovatele e-mailu třetí strany, jako je [SendGrid](https://sendgrid.com) nebo [SparkPost](https://sparkpost.com), vlastní rozhraní REST API nebo jakýkoli poskytovatel e-mailu založený na protokolu HTTP (včetně vašeho vlastního). Tento článek popisuje nastavení řešení, které používá SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Vytvoření účtu SendGrid

Pokud ještě nemáte, začněte nastavením účtu SendGrid (zákazníci Azure můžou každý měsíc odemknout 25 000 e-mailů zdarma). Pokyny k nastavení najdete v části [Vytvoření účtu SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) v části [Jak odesílat e-maily pomocí SendGrid s Azure](../sendgrid-dotnet-how-to-send-email.md).

Nezapomeňte dokončit oddíl, ve kterém [vytvoříte klíč rozhraní API SendGrid](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key). Zaznamenejte klíč rozhraní API pro použití v pozdějším kroku.

## <a name="create-azure-ad-b2c-policy-key"></a>Vytvoření klíče zásad Azure AD B2C

Dále uložte klíč rozhraní API SendGrid v klíči zásad Azure AD B2C, na který mají vaše zásady odkazovat.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + předplatné** a zvolte adresář Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
1. V části `Manual` **Možnosti**zvolte .
1. Zadejte **název** klíče zásady. Například, `SendGridSecret`. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
1. V **poli Tajné**zadejte tajný klíč klienta, který jste dříve zaznamenali.
1. V případě použití `Signature` **klíče**vyberte .
1. Vyberte **Vytvořit**.

## <a name="create-sendgrid-template"></a>Vytvořit šablonu SendGrid

S vytvořeným účtem SendGrid a klíčem rozhraní API SendGrid uloženým v klíči zásad Azure AD B2C vytvořte [dynamickou transakční šablonu](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid .

1. Na webu SendGrid otevřete stránku [transakčních šablon](https://sendgrid.com/dynamic_templates) a vyberte **Vytvořit šablonu**.
1. Zadejte jedinečný název `Verification email` šablony jako like a pak vyberte **Uložit**.
1. Chcete-li začít upravovat novou šablonu, vyberte **Přidat verzi**.
1. Vyberte **Editor kódu** a **pokračujte**.
1. V editoru HTML vložte následující šablonu HTML nebo použijte vlastní. `{{otp}}` Parametry `{{email}}` a budou dynamicky nahrazeny jednorázovou hodnotou hesla a e-mailovou adresou uživatele.

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
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
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

1. Rozbalte **nastavení** vlevo a v tématu **Předmět e-mailu**zadejte `{{subject}}`.
1. Vyberte **Uložit šablonu**.
1. Vraťte se na stránku Transakční šablony výběrem **šipky** zpět.
1. Zaznamenejte **ID** šablony, kterou jste vytvořili pro pozdější krok. Například, `d-989077fbba9746e89f3f6411f596fb96`. Toto ID zadáte při [přidání transformace deklarací identity](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Přidání typů deklarací deklarací Azure AD B2C

V zásadách přidejte následující typy `<ClaimsSchema>` deklarací do prvku v rámci `<BuildingBlocks>`aplikace .

Tyto typy deklarací jsou nezbytné ke generování a ověření e-mailové adresy pomocí jednorázového hesla (OTP) kódu.

```XML
<ClaimType Id="Otp">
  <DisplayName>Secondary One-time password</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="sendGridReqBody">
  <DisplayName>SendGrid request body</DisplayName>
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

Dále potřebujete transformace deklarací pro výstup deklarace řetězce JSON, která bude tělem požadavku odeslaného sendgridu.

Struktura objektu JSON je definována ID v dot notaci InputParameters a TransformationClaimTypes InputClaims. Čísla v dot notaci implikují pole. Hodnoty pocházejí z inputclaims 'hodnoty a InputParameters '"Hodnota" vlastnosti. Další informace o transformace nároky JSON naleznete v tématu [JSON nároky transformace](json-transformations.md).

Přidejte následující deklarace `<ClaimsTransformations>` transformace `<BuildingBlocks>`prvek v rámci . Proveďte následující aktualizace xml transformace deklarací identity:

* Aktualizujte `template_id` hodnotu InputParameter iD transakční šablony SendGrid, kterou jste vytvořili dříve v [šabloně Create SendGrid](#create-sendgrid-template).
* Aktualizujte `from.email` hodnotu adresy. Použijte platnou e-mailovou adresu, abyste zabránili označení ověřovacího e-mailu jako spamu.
* Aktualizujte hodnotu `personalizations.0.dynamic_template_data.subject` vstupního parametru řádku předmětu řádkem předmětu odpovídajícím vaší organizaci.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
  </InputClaims>
  <InputParameters>
    <!-- Update the template_id value with the ID of your SendGrid template. -->
    <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
    <!-- Update with a subject line appropriate for your organization. -->
    <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim"/>
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="add-datauri-content-definition"></a>Přidání definice obsahu DataUri

Pod transformace deklarací `<BuildingBlocks>`v rámci přidejte následující [ContentDefinition](contentdefinitions.md) odkazna verzi 2.0.0 data URI:

```XML
<ContentDefinitions>
 <ContentDefinition Id="api.localaccountsignup">
    <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.0.0</DataUri>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="create-a-displaycontrol"></a>Vytvoření ovládacího prvku DisplayControl

Ovládací prvek zobrazení ověření se používá k ověření e-mailové adresy pomocí ověřovacího kódu, který je odeslán uživateli.

Tento příklad ovládacího prvku zobrazení je nakonfigurován tak, aby:

1. Shromážděte `email` typ deklarace adresy od uživatele.
1. Počkejte, až uživatel `verificationCode` poskytne typ deklarace s kódem odeslaným uživateli.
1. Vraťte `email` zpět do samoobslužného technického profilu, který má odkaz na tento ovládací prvek zobrazení.
1. Pomocí `SendCode` akce vygenerujte kód OTP a odešlete uživateli e-mail s kódem OTP.

![Akce e-mailu odeslat ověřovací kód](media/custom-email/display-control-verification-email-action-01.png)

V části definice obsahu, stále v rámci `<BuildingBlocks>`, přidejte do zásad následující Ovládací prvek [DisplayControl](display-controls.md) typu [VerificationControl.](display-control-verification.md)

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
          <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendGrid" />
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

## <a name="add-otp-technical-profiles"></a>Přidání technických profilů OTP

Technický `GenerateOtp` profil vygeneruje kód pro e-mailovou adresu. Technický `VerifyOtp` profil ověří kód přidružený k e-mailové adrese. Můžete změnit konfiguraci formátu a vypršení platnosti jednorázového hesla. Další informace o technických profilech OTP naleznete [v tématu Definování jednorázového technického profilu hesla](one-time-password-technical-profile.md).

Přidejte do prvku `<ClaimsProviders>` následující technické profily.

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

## <a name="add-a-rest-api-technical-profile"></a>Přidání technického profilu rozhraní REST API

Tento technický profil rozhraní REST API generuje obsah e-mailu (pomocí formátu SendGrid). Další informace o technických profilech RESTful naleznete [v tématu Definování technického profilu RESTful](restful-technical-profile.md).

Stejně jako u technických profilů OTP `<ClaimsProviders>` přidejte k prvku následující technické profily.

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendGrid">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Odkaz na ovládací prvek DisplayControl

V posledním kroku přidejte odkaz na DisplayControl, který jste vytvořili. Pokud jste `LocalAccountSignUpWithLogonEmail` použili starší verzi zásad Azure AD B2C, nahraďte svůj stávající vlastní technický profil následujícím. Tento technický `DisplayClaims` profil používá s odkazem na DisplayControl.

Další informace naleznete [v tématech Vlastní-tvrdil technický profil](restful-technical-profile.md) a [DisplayControl](display-controls.md).

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <DisplayName>Email signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
        <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
        <Item Key="language.button_continue">Create</Item>
        
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceed the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceed the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
      </InputClaims>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" Required="true" />
        <OutputClaim ClaimTypeReferenceId="objectId" />
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" />
        <OutputClaim ClaimTypeReferenceId="newUser" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
      </ValidationTechnicalProfiles>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>[Nepovinné] Lokalizovat e-mail

Chcete-li lokalizovat e-mail, musíte odeslat lokalizované řetězce SendGrid nebo poskytovateli e-mailu. Například lokalizovat předmět e-mailu, text, kódovou zprávu nebo podpis e-mailu. Chcete-li tak učinit, můžete použít [GetLocalizedStringsTransformation nároky](string-transformations.md) transformace ke kopírování lokalizovaných řetězců do typů deklarací. V `GenerateSendGridRequestBody` transformaci deklarací identity, která generuje datovou část JSON, používá vstupní deklarace, které obsahují lokalizované řetězce.

1. Ve vaší politice definujte následující řetězcové deklarace: předmět, zprávu, codeIntro a podpis.
1. Definujte [GetLocalizedStringsTransformation nároky](string-transformations.md) transformace nahradit lokalizované řetězcové hodnoty do deklarací z kroku 1.
1. Změňte `GenerateSendGridRequestBody` transformaci deklarací tak, aby používala vstupní deklarace s následujícím fragmentem XML.
1. Aktualizujte šablonu SendGrind tak, aby místo všech řetězců, které budou lokalizovány azure AD B2C, používaly dynamické parametry.

```XML
<ClaimsTransformation Id="GenerateSendGridRequestBody" TransformationMethod="GenerateJson">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
    <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
    <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
    <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
    <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
    <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
    <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="sendGridReqBody" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

## <a name="next-steps"></a>Další kroky

Příklad vlastní zásady pro ověření e-mailu najdete na GitHubu:

[Vlastní ověření e-mailu - DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)

Informace o použití vlastního rozhraní REST API nebo libovolného poskytovatele e-mailu smtp založeného na protokolu HTTP najdete [v tématu Definování technického profilu RESTful v zásadách vlastních zásad Azure AD B2C](restful-technical-profile.md).
