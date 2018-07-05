---
title: Správa jednotného přihlašování a token přizpůsobení pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Další informace o správě jednotného přihlašování a token přizpůsobení pomocí vlastních zásad.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 811fb8b2de59c9d324ab4acb8b0f51b4cec80aee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441793"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Správa jednotného přihlašování a token přizpůsobení pomocí vlastních zásad
Pomocí vlastních zásad poskytuje stejnou kontrolu nad token, relace a jednotné přihlašování – konfigurace (SSO) pomocí předdefinovaných zásad.  Každé nastavení, zjistěte, co naleznete v dokumentaci [tady](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Konfigurace tokenu životnosti a deklarace identity
Chcete-li změnit nastavení na vaše životností tokenů, musíte přidat `<ClaimsProviders>` element v souboru předávající strana zásady měly týkat.  `<ClaimsProviders>` Element je podřízeným prvkem `<TrustFrameworkPolicy>`.  Uvnitř budete muset poskytnout informace, které má vliv na vaše životností tokenů.  XML vypadá jako v tomto příkladu:

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

**Přístup k životností tokenů** -životnost tokenu přístupu lze změnit pomocí úpravy hodnoty uvnitř `<Item>` klíčem = "token_lifetime_secs" v řádu sekund.  Výchozí hodnota v předdefinované je 3 600 sekund (60 minut).

**Životnost tokenu ID** – ID dobu životnosti tokenu lze změnit pomocí úpravy hodnoty uvnitř `<Item>` klíčem = "id_token_lifetime_secs" v řádu sekund.  Výchozí hodnota v předdefinované je 3 600 sekund (60 minut).

**Doba života obnovovacího tokenu** – aktualizace dobu životnosti tokenu lze změnit pomocí úpravy hodnoty uvnitř `<Item>` klíčem = "refresh_token_lifetime_secs" v řádu sekund.  Výchozí hodnota v předdefinované je 1209600 sekund (14 dnů).

**Token doba života posuvného okna obnovovacího** – Pokud chcete nastavit doba života posuvného okna obnovovací token, upravte hodnotu uvnitř `<Item>` klíčem = "rolling_refresh_token_lifetime_secs" v řádu sekund.  Výchozí hodnota v předdefinované je 7776000 (90 dnů).  Pokud nechcete, aby k vynucení doba života posuvného okna, nahraďte tento řádek:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Deklarace identity vystavitele (iss)** – Pokud chcete změnit deklarace identity vystavitele (iss), upravte hodnotu uvnitř `<Item>` klíčem = "IssuanceClaimPattern".  Příslušné hodnoty jsou `AuthorityAndTenantGuid` a `AuthorityWithTfp`.

**Nastavení deklarace identity představující ID zásady** – možnosti pro nastavení této hodnoty jsou TFP (zásady důvěryhodnosti framework) a služby ACR (authentication kontextu odkaz).  
Doporučujeme nastavit to TFP, ujistěte se, k tomu, `<Item>` s Key = "AuthenticationContextReferenceClaimPattern" existuje a má hodnotu `None`.
Ve vaší `<OutputClaims>` položky, přidejte tento element:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Pro službu ACR, odeberte `<Item>` klíčem = "AuthenticationContextReferenceClaimPattern".

**Deklarace identity subjektu (sub)** – tato možnost je nastavena na výchozí ID objektu, pokud chcete přepnout na `Not Supported`, postupujte takto:

Nahraďte tento řádek 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
Tento řádek:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Chování relace a jednotné přihlašování

Chcete-li změnit chování relace a konfigurace jednotného přihlašování, budete muset přidat `<UserJourneyBehaviors>` prvek uvnitř `<RelyingParty>` element.  `<UserJourneyBehaviors>` Prvek musí následovat bezprostředně `<DefaultUserJourney>`.  Uvnitř vaší `<UserJourneyBehavors>` prvek by měl vypadat takto:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Jednotné přihlašování (SSO) konfigurace** – Pokud chcete změnit jediné konfiguraci přihlašování, budete muset upravit hodnotu `<SingleSignOn>`.  Příslušné hodnoty jsou `Tenant`, `Application`, `Policy` a `Disabled`. 

**Webová aplikace životnost relace (minuty)** – Pokud chcete změnit životnost relace webové aplikace potřebujete změnit hodnotu `<SessionExpiryInSeconds>` elementu.  Výchozí hodnota v integrovaných zásad je 86 400 sekund (1 440 minut).

**Časový limit relace webové aplikace** – Pokud chcete změnit časový limit webové aplikace relace je třeba hodnotu změnit `<SessionExpiryType>`.  Příslušné hodnoty jsou `Absolute` a `Rolling`.
