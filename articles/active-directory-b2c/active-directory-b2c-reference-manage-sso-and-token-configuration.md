---
title: Správa jednotného přihlašování a přizpůsobení tokenů pomocí vlastních zásad v Azure Active Directory B2C | Microsoft Docs
description: Přečtěte si o správě jednotného přihlašování a přizpůsobení tokenů pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 36a95b502c13ccf360ba4ac56b4837d41ee487c8
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72296411"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Správa jednotného přihlašování a přizpůsobení tokenů pomocí vlastních zásad v Azure Active Directory B2C

Tento článek poskytuje informace o tom, jak můžete spravovat konfigurace tokenu, relace a jednotného přihlašování pomocí [vlastních zásad](active-directory-b2c-overview-custom.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Životnost tokenů a konfigurace deklarací identity

Pokud chcete změnit nastavení pro životnosti tokenů, přidejte do souboru předávající strany zásad, které chcete ovlivnit, element [ClaimsProviders](claimsproviders.md) .  Element **ClaimsProviders** je podřízeným prvkem elementu [TrustFrameworkPolicy](trustframeworkpolicy.md) .

Vložte element ClaimsProviders mezi element BasePolicy a element RelyingParty souboru předávající strany.

V rámci aplikace budete muset umístit informace, které mají vliv na životnost tokenů. KÓD XML vypadá jako v tomto příkladu:

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

- **Životnosti přístupového tokenu** – hodnota životnosti přístupového tokenu je nastavená pomocí položky metadat **token_lifetime_secs** . Výchozí hodnota je 3600 sekund (60 minut).
- **Doba platnosti tokenu ID** – hodnota životnosti tokenu ID je nastavená pomocí položky metadat **id_token_lifetime_secs** . Výchozí hodnota je 3600 sekund (60 minut).
- **Doba platnosti tokenu** – hodnota životnosti obnovovacího tokenu je nastavená pomocí položky metadat **refresh_token_lifetime_secs** . Výchozí hodnota je 1209600 sekund (14 dní).
- **Interval posuvných oken obnovovacího tokenu** – Pokud byste chtěli nastavit dobu trvání posuvných oken na obnovovací token, nastavte hodnotu položky metadat **rolling_refresh_token_lifetime_secs** . Výchozí hodnota je 7776000 (90 dní). Pokud nechcete vymáhat dobu trvání klouzavého okna, nahraďte položku `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Deklarace vystavitele** – deklarace identity vystavitele (ISS) se nastaví pomocí položky metadat **IssuanceClaimPattern** . Platné hodnoty jsou `AuthorityAndTenantGuid` a `AuthorityWithTfp`.
- **Nastavení deklarace identity představující ID zásady** – možnosti nastavení této hodnoty jsou `TFP` (zásady pro pravidlo důvěryhodnosti) a `ACR` (Referenční dokumentace kontextu ověřování). Doporučená hodnota je `TFP`. Nastavte **AuthenticationContextReferenceClaimPattern** s hodnotou `None`.

    Do elementu **ClaimsSchema** přidejte tento element:

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Do prvku **OutputClaims** přidejte tento element:

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pro ACR odeberte položku **AuthenticationContextReferenceClaimPattern** .

- **Deklarace subjektu (sub)** – Tato možnost je ve výchozím nastavení nastavena na ObjectId, pokud byste chtěli toto nastavení přepnout na `Not Supported`, nahraďte tento řádek:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    s tímto řádkem:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Chování relace a jednotné přihlašování

Chcete-li změnit chování relace a konfigurace jednotného přihlašování, přidejte do elementu [RelyingParty](relyingparty.md) element **UserJourneyBehaviors** .  Element **UserJourneyBehaviors** musí následovat za **DefaultUserJourney**. Uvnitř vašeho elementu **UserJourneyBehavors** by měl vypadat jako v tomto příkladu:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

V předchozím příkladu jsou nakonfigurovány následující hodnoty:

- **Jednotné přihlašování (SSO)** – jednotné přihlašování je nakonfigurované pomocí **SingleSignon**. Platné hodnoty jsou `Tenant`, `Application`, `Policy` a `Suppressed`.
- **Časový limit relace webové aplikace** – časový limit relace webové aplikace je nastaven pomocí elementu **SessionExpiryType** . Platné hodnoty jsou `Absolute` a `Rolling`.
- **Doba života relace webové aplikace** – životnost relace webové aplikace je nastavená pomocí elementu **SessionExpiryInSeconds** . Výchozí hodnota je 86400 sekund (1440 minut).
