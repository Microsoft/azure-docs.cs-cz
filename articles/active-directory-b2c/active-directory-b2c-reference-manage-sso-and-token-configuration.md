---
title: Správa jednotného přihlašování a token přizpůsobení pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o správě jednotného přihlašování a token přizpůsobení pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c0f5be7fd77ae195b66f8a8fb052ab8573d48171
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856355"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Správa jednotného přihlašování a token přizpůsobení pomocí vlastních zásad v Azure Active Directory B2C

Tento článek obsahuje informace o tom, jak můžete spravovat token, relace a konfigurace jednotného přihlašování (SSO) pomocí [vlastní zásady](active-directory-b2c-overview-custom.md) v Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Konfigurace tokenu životnosti a deklarace identity

Chcete-li změnit nastavení na vaše životností tokenů, je přidat [ClaimsProviders](claimsproviders.md) element v souboru předávající strana zásady měly týkat.  **ClaimsProviders** element je podřízeným prvkem [TrustFrameworkPolicy](trustframeworkpolicy.md) elementu. Uvnitř budete muset poskytnout informace, které má vliv na vaše životností tokenů. XML vypadá jako v tomto příkladu:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Následující hodnoty jsou nastavené v předchozím příkladu:

- **Přístup k životností tokenů** – životnost tokenu přístupu, hodnota se nastaví pomocí **token_lifetime_secs** položku metadat. Výchozí hodnota je 3 600 sekund (60 minut).
- **Životnost tokenu ID** – ID dobu životnosti tokenu hodnota se nastaví pomocí **id_token_lifetime_secs** položku metadat. Výchozí hodnota je 3 600 sekund (60 minut).
- **Doba života obnovovacího tokenu** – aktualizace dobu životnosti tokenu hodnota se nastaví pomocí **refresh_token_lifetime_secs** položku metadat. Výchozí hodnota je 1209600 sekund (14 dnů).
- **Token doba života posuvného okna obnovovacího** – Pokud chcete nastavit doba života posuvného okna k aktualizaci tokenu, nastavte hodnotu **rolling_refresh_token_lifetime_secs** položku metadat. Výchozí hodnota je 7776000 (90 dnů). Pokud nechcete, aby k vynucení doba života posuvného okna, nahraďte položku s `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Deklarace identity vystavitele (iss)** – deklarace identity vystavitele (iss) nastavená **IssuanceClaimPattern** položku metadat. Příslušné hodnoty jsou `AuthorityAndTenantGuid` a `AuthorityWithTfp`.
- **Nastavení deklarace identity představující ID zásady** – možnosti pro nastavení této hodnoty jsou `TFP` (framework zásady důvěryhodnosti) a `ACR` (informace o kontextu ověřování). `TFP` Doporučená hodnota. Nastavte **AuthenticationContextReferenceClaimPattern** s hodnotou `None`. 

    V **ClaimsSchema** prvku, přidejte tento element: 
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    Ve vaší **OutputClaims** prvku, přidejte tento element:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pro službu ACR, odeberte **AuthenticationContextReferenceClaimPattern** položky.

- **Deklarace identity subjektu (sub)** – výchozí hodnota této možnosti ObjectID, pokud byste chtěli přepnutí tohoto nastavení na `Not Supported`, nahraďte tento řádek: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    Tento řádek:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Chování relace a jednotné přihlašování

Chcete-li změnit chování relace a konfigurace jednotného přihlašování, můžete přidat **UserJourneyBehaviors** ovládacího prvku [RelyingParty](relyingparty.md) element.  **UserJourneyBehaviors** prvek musí následovat bezprostředně **DefaultUserJourney**. Uvnitř vaší **UserJourneyBehavors** prvek by měl vypadat jako v tomto příkladu:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Následující hodnoty jsou nakonfigurované v předchozím příkladu:

- **Jednotné přihlašování (SSO)** -jednotného přihlašování se nakonfigurují **SingleSignOn**. Příslušné hodnoty jsou `Tenant`, `Application`, `Policy`, a `Suppressed`. 
- **Webové aplikace životnost relace (minuty)** – relace webové aplikace životnost nastavená **SessionExpiryInSeconds** elementu. Výchozí hodnota je 86 400 sekund (1 440 minut).
- **Časový limit relace webové aplikace** – časový limit se nastavuje pomocí webové aplikace relace **sessionexpirytype pro** elementu. Příslušné hodnoty jsou `Absolute` a `Rolling`.
