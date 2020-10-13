---
title: Podjízdy v Azure Active Directory B2C | Microsoft Docs
description: Určuje prvek dílčích cest vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6609dabe9bd507751bd131a4effe24295e2aac04
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91952439"
---
# <a name="subjourneys"></a>Podjízdy

K organizování a zjednodušení toku kroků orchestrace v rámci cesty uživatele lze použít subjízdy. [Cesty uživatelů](userjourneys.md) určují explicitní cesty, pomocí kterých zásada umožňuje aplikaci předávající strany získat požadované deklarace identity pro uživatele. Uživatel se převezme prostřednictvím těchto cest, aby načetl deklarace identity, které se mají předložit předávající straně. Jinými slovy, cesty uživatelů definují obchodní logiku toho, co koncový uživatel projde, jako Azure AD B2C architektura pro prostředí identity zpracuje požadavek. Cesta uživatele je reprezentována jako sekvence orchestrace, která musí následovat po úspěšné transakci. Element [ClaimsExchange](userjourneys.md#claimsexchanges) kroku orchestrace je svázán s jedním [technickým profilem](technical-profiles-overview.md) , který se spustí.

Podcesta je seskupení kroků orchestrace, které je možné vyvolat v jakémkoli bodě na cestě uživatele. Pomocí subcest můžete vytvořit opakovaně použitelné sekvence kroků nebo implementovat větvení, aby lépe představovaly obchodní logiku.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Větvení cest uživatelů

Dílčí cesty se chovají jako [cesty uživatelů](userjourneys.md), protože obě jsou reprezentovány jako sekvence orchestrace, které musí následovat po úspěšné transakci. Cesty uživatelů je možné vyvolat vlastním jménem a vyžadují, aby se SendClaims krok spustil. Dílčí cesty jsou komponenty cest uživatelů a nelze je volat nezávisle a jsou volány vždy z cesty uživatele.

Klíčovou součástí větvení je umožnění lepší zpracování obchodních logik v cestě uživatele. Obvyklé kroky orchestrace jsou seskupené do jednotlivých částí, které se mají vyvolat samostatně. Podcesta může zjednodušit cestu, při které se několik kroků orchestrace spojí dohromady (mají stejné předpoklady). Podcesta je volána pouze z cesty uživatele, neměla by volat jinou cestu.

Existují dva typy subcest:

- **Call** -vrátí řízení volajícímu. Podcesta se spustí a pak se ovládací prvek vrátí do kroku orchestrace, který je aktuálně prováděn v rámci cesty uživatele.
- **Přenos** – přenáší řízení na dílčí cestu (nevratné větvení). Aby bylo možné vracet deklarace zpátky do aplikace předávající strany, musí mít podcestu SendClaims krok.

## <a name="example-scenarios"></a>Příklady scénářů

### <a name="call-subjourney"></a>Rozvolat cestu

Subcestování volání je užitečné v následujících scénářích:

- Omezení stáří: pro věkové rozmezí existuje mnoho sdílených komponent mezi cestami uživatele. Větvení umožňuje kompilovat společné prvky do složek, které lze sdílet.  
- Souhlas rodičů: větvení umožňuje pohodlí v rámci návrhu souhlasu rodičů tím, že nám umožňuje přístup k deklaracím, které uživatel provedl, a může se rozvětvit na cestu uživatele o souhlasu rodičů po zjištění, že uživatel vyžaduje souhlas. 
- Přihlaste se, abyste se přihlásili: Vezměte v úvahu situaci, kdy uživatel už v adresáři existuje, ale možná jste zapomněli, že ve skutečnosti vytvořil účet. V takovém případě může být žádoucí, aby uživatel bez oznámení, že přihlašovací údaje, které zadal, již existovaly, a vynutil, aby uživatel restartoval cestu, kterou tato zásada umožňuje přepnout z toku registrace do toku přihlášení pro tohoto uživatele.  

### <a name="transfer-subjourney"></a>Přenos subcest

Přenosová subcestou je užitečná v následujících scénářích:

- Zobrazuje se stránka blokování.
- Testování a/B: směrováním žádosti na podcestu se provede a vydá token.

## <a name="adding-a-subjourney-element"></a>Přidání elementu dílčí cesty

Následuje příklad `SubJourney` prvku typu `Call` , který vrací řízení zpět na cestu uživatele.

```xml
<SubJourneys>
  <SubJourney Id="ConditionalAccess_Evaluation" Type="Call">
    <OrchestrationSteps>
      <OrchestrationStep Order="1" Type="ClaimsExchange">
       <ClaimsExchanges>
        <ClaimsExchange Id="ConditionalAccessEvaluation" TechnicalProfileReferenceId="ConditionalAccessEvaluation" />
       </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <Preconditions>
          <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
            <Value>conditionalAccessClaimCollection</Value>
            <Action>SkipThisOrchestrationStep</Action>
          </Precondition>
        </Preconditions>
        <ClaimsExchanges>
          <ClaimsExchange Id="GenerateCAClaimFlags" TechnicalProfileReferenceId="GenerateCAClaimFlags" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

Následuje příklad `SubJourney` prvku typu `Transfer` , který vrátí token zpět do aplikace předávající strany.

```xml
<SubJourneys>
  <SubJourney Id="B" Type="Transfer">
    <OrchestrationSteps>
      ...
      <OrchestrationStep Order="5" Type="SendClaims">
    </OrchestrationSteps>
  </SubJourney>
</SubJourneys>
```

### <a name="invoke-a-subjourney-step"></a>Vyvolat krok cesty

`InvokeSubJourney`K provedení podcesty se použije nový krok orchestrace typu. Následuje příklad, který ukazuje všechny prvky spuštění tohoto kroku orchestrace.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Podcesta by neměla volat jinou cestu.

## <a name="components"></a>Komponenty

Pro definování dílčích cest, které zásady podporují, přidejte element **recests** pod element nejvyšší úrovně souboru zásad.

Element **recestes** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Subcesta | 1: n | Podcesta definující všechny konstrukce, které jsou nezbytné pro kompletní tok uživatele. |

Element **recestes** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor podcesty, který může být použit cestou uživatele k odkazování na podcestu v zásadě. Element **SubJourneyReferenceId** elementu [kandidáta](userjourneys.md#journeylist) odkazuje na tento atribut. |
| Type | Ano | Možné hodnoty: `Call` , nebo `Transfer` . Další informace najdete v tématu [větvení cest uživatele](#user-journey-branching) .|

Element dílčí **cesty** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Sekvence orchestrace, která musí následovat po úspěšné transakci. Každá cesta uživatele se skládá z uspořádaného seznamu kroků orchestrace, které se spustí v posloupnosti. Pokud nějaký krok selhává, transakce se nezdařila. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Úplný seznam elementů kroku Orchestration naleznete v tématu [userjourney](userjourneys.md).

## <a name="next-steps"></a>Další kroky

[UserJourneys](userjourneys.md)