---
title: Správa vlastního přivlastního přiřazu a přizpůsobení tokenů pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Přečtěte si o správě vlastního přistupujícího objektu zabezpečení a přizpůsobení tokenů pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189289"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Správa vlastního přistupuje a přizpůsobení tokenů pomocí vlastních zásad ve službě Azure Active Directory B2C

Tento článek obsahuje informace o tom, jak můžete spravovat konfigurace tokenu, relace a jednotného přihlašování pomocí [vlastních zásad](custom-policy-overview.md) ve službě Azure Active Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Konfigurace životnosti tokenů a deklarací identity

Chcete-li změnit nastavení na životnosttokenu, přidejte [ClaimsProviders](claimsproviders.md) prvek v souboru předávající strany zásady, které chcete ovlivnit.  **ClaimsProviders** element je podřízený [trustframeworkpolicy](trustframeworkpolicy.md) elementu.

Vložte element ClaimsProviders mezi element BasePolicy a element Předávající strany.

Uvnitř budete muset umístit informace, které ovlivňují životnost tokenu. Xml vypadá jako tento příklad:

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

V předchozím příkladu jsou nastaveny následující hodnoty:

- **Životnost přístupového tokenu** – hodnota životnosti přístupového tokenu je nastavena s **token_lifetime_secs** položkou metadat. Výchozí hodnota je 3600 sekund (60 minut).
- **Životnost tokenu ID** – hodnota životnosti tokenu ID je nastavena s **id_token_lifetime_secs** položkou metadat. Výchozí hodnota je 3600 sekund (60 minut).
- **Životnost obnovovacího tokenu** – hodnota životnosti obnovovacího tokenu je nastavena s **položkou metadat refresh_token_lifetime_secs.** Výchozí hodnota je 1209600 sekund (14 dní).
- **Obnovovat token posuvné okno životnost** - Pokud chcete nastavit životnost posuvné okno na obnovovací token, nastavte hodnotu **rolling_refresh_token_lifetime_secs** položky metadat. Výchozí hodnota je 7776000 (90 dní). Pokud nechcete vynutit dobu trvání posuvných `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`oken, nahraďte položku položkou .
- **Deklarace vystavitna (iss)** - Deklarace vystavitvystavit (iss) je nastavena pomocí položky metadat **IssuanceClaimPattern.** Příslušné hodnoty `AuthorityAndTenantGuid` jsou `AuthorityWithTfp`a .
- **Nastavení deklarace představující ID zásad** – možnosti nastavení této hodnoty jsou `TFP` (zásady architektury důvěryhodnosti) a `ACR` (odkaz na kontext ověřování). `TFP`je doporučená hodnota. Nastavte **authenticationContextReferenceClaimPattern** s `None`hodnotou .

    V **ClaimsSchema** prvek přidejte tento prvek:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Do elementu **OutputClaims** přidejte tento prvek:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pro ACR odeberte položku **AuthenticationContextReferenceClaimPattern.**

- **Předmět (dílčí) deklarace -** Tato možnost je výchozí na ObjectID, pokud chcete toto nastavení přepnout na `Not Supported`, nahraďte tento řádek:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    s tímto řádkem:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Chování relace a přiřazování a přihánění a přiř

Chcete-li změnit chování relace a konfigurace přiřazování k sizamu, přidejte prvek **UserJourneyBehaviors** uvnitř elementu [RelyingParty.](relyingparty.md)  Prvek **UserJourneyBehaviors** musí okamžitě následovat **DefaultUserJourney**. Vnitřek vašeho **UserJourneyBehavors** prvek by měl vypadat jako tento příklad:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

V předchozím příkladu jsou konfigurovány následující hodnoty:

- **Jednotné přihlašování (SSO)** – jednotné přihlašování je nakonfigurováno pomocí **singlesignon**. Příslušné hodnoty `Tenant`jsou `Application` `Policy`, `Suppressed`, a .
- **Časový čas relace webové aplikace** – časový čas relace webové aplikace je nastaven s elementem **SessionExpiryType.** Příslušné hodnoty `Absolute` jsou `Rolling`a .
- **Životnost relace webové aplikace** – životnost relace webové aplikace je nastavena pomocí prvku **SessionExpiryInSeconds.** Výchozí hodnota je 86400 sekund (1440 minut).
