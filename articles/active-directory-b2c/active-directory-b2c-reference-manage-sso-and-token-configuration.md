---
title: Správa jednotného přihlašování a tokenu přizpůsobení pomocí vlastních zásad | Microsoft Docs
description: Další informace o správě jednotného přihlašování a tokenu přizpůsobení pomocí vlastních zásad.
services: active-directory-b2c
documentationcenter: ''
author: davidmu
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.openlocfilehash: c9eb7f7711a8987945b8aeaee8d6798b7a6b8284
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Správa jednotného přihlašování a tokenu přizpůsobení pomocí vlastních zásad
Pomocí vlastních zásad poskytuje stejné kontrolu nad token, relací a jeden přihlašování konfigurace (SSO) jako prostřednictvím integrovaných zásad.  Další informace o jaké každé nastavení, najdete v dokumentaci [zde](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Token konfigurace životnosti a deklarace identity
Chcete-li změnit nastavení na vaše životnosti tokenu, je nutné přidat `<ClaimsProviders>` element v souboru předávající strany zásady, které chcete mít vliv.  `<ClaimsProviders>` Element je podřízená `<TrustFrameworkPolicy>`.  Uvnitř budete muset uvést informace, které ovlivní vaše životnosti tokenu.  Soubor XML vypadá takto:

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

**Přístup k tokenu životnosti** dobu životnosti tokenu přístupu lze změnit úpravou hodnoty uvnitř `<Item>` klíčem = "token_lifetime_secs" v sekundách.  Výchozí hodnota v předdefinované je 3600 sekund (60 minut).

**Životnost tokenu ID** ID dobu životnosti tokenu lze změnit úpravou hodnoty uvnitř `<Item>` klíčem = "id_token_lifetime_secs" v sekundách.  Výchozí hodnota v předdefinované je 3600 sekund (60 minut).

**Aktualizujte životnost tokenu** aktualizace dobu životnosti tokenu lze změnit úpravou hodnoty uvnitř `<Item>` klíčem = "refresh_token_lifetime_secs" v sekundách.  Výchozí hodnota v předdefinované je 1209600 sekund (14 dnů).

**Aktualizujte životnost tokenu posuvné okno** Pokud chcete nastavit životnost posuvné okno na obnovovací token, upravte hodnotu uvnitř `<Item>` klíčem = "rolling_refresh_token_lifetime_secs" v sekundách.  Výchozí hodnota v předdefinované je 7776000 (90 dnů).  Pokud nechcete enfore klouzavé období platnosti, nahraďte tento řádek:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Deklarace identity vystavitele (iss)** Pokud chcete změnit deklarace vystavitele (iss), upravte hodnotu uvnitř `<Item>` klíčem = "IssuanceClaimPattern".  Použitelné hodnoty jsou `AuthorityAndTenantGuid` a `AuthorityWithTfp`.

**Nastavení deklarace představující ID zásad** možnosti pro nastavení této hodnoty jsou TFP (zásady důvěryhodnosti framework) a ACR (authentication kontextu reference).  
Doporučujeme, abyste tato nastavení na TFP, chcete-li to provést, zkontrolujte `<Item>` se klíč = "AuthenticationContextReferenceClaimPattern" existuje a je hodnota `None`.
Ve vašem `<OutputClaims>` položky, přidejte tento element:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
ACR, odeberte `<Item>` klíčem = "AuthenticationContextReferenceClaimPattern".

**Deklarace identity subjektu (pod)** tato možnost je uvedena do výchozího stavu ObjectID, pokud chcete přepnout na `Not Supported`, postupujte takto:

Nahraďte tento řádek 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
Tento řádek:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Chování relace a jednotné přihlašování
Pokud chcete změnit chování relace a konfigurace jednotného přihlašování, je nutné přidat `<UserJourneyBehaviors>` elementu `<RelyingParty>` elementu.  `<UserJourneyBehaviors>` Element okamžitě postupujte `<DefaultUserJourney>`.  Uvnitř vaší `<UserJourneyBehavors>` element by měla vypadat takto:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Konfigurace jednotného přihlašování (SSO)** Pokud chcete změnit konfiguraci přihlášení, budete muset upravit hodnotu `<SingleSignOn>`.  Použitelné hodnoty jsou `Tenant`, `Application`, `Policy` a `Disabled`. 

**Webovou aplikaci dobu platnosti relace (minuty)** změnit webovou aplikaci dobu platnosti relace, budete muset upravit hodnotu `<SessionExpiryInSeconds>` elementu.  Výchozí hodnota v integrovaných zásad je 86400 sekund (1 440 minut).

**Časový limit relace webové aplikace** změnit časový limit relace webové aplikace, budete muset upravit hodnotu `<SessionExpiryType>`.  Použitelné hodnoty jsou `Absolute` a `Rolling`.
