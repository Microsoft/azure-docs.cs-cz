---
title: Dílčí cesty v Azure Active Directory B2C | Microsoft Docs
description: Určuje element dílčích cest vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 8f037d4283b4b05081ef47e7223495f6e19d460e
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97386863"
---
# <a name="sub-journeys"></a>Dílčí cesty

Dílčí cesty lze použít k organizování a zjednodušení toku kroků orchestrace v rámci cesty uživatele. [Cesty uživatelů](userjourneys.md) určují explicitní cesty, pomocí kterých zásada umožňuje aplikaci předávající strany získat požadované deklarace identity pro uživatele. Uživatel se převezme prostřednictvím těchto cest, aby načetl deklarace identity, které se mají předložit předávající straně. Jinými slovy, cesty uživatelů definují obchodní logiku toho, co koncový uživatel projde, jako Azure AD B2C architektura pro prostředí identity zpracuje požadavek. Cesta uživatele je reprezentována jako sekvence orchestrace, která musí následovat po úspěšné transakci. Element [ClaimsExchange](userjourneys.md#claimsexchanges) kroku orchestrace je svázán s jedním [technickým profilem](technicalprofiles.md) , který se spustí.

Podcesta je seskupení kroků orchestrace, které lze vyvolat kdykoli v rámci cesty uživatele. Pomocí dílčích cest můžete vytvořit opakovaně použitelné sekvence kroků nebo implementovat větvení pro lepší znázornění obchodní logiky.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="user-journey-branching"></a>Větvení cest uživatelů

Dílčí cesty se chovají jako [cesty uživatelů](userjourneys.md), protože obě jsou reprezentovány jako sekvence orchestrace, které musí následovat po úspěšné transakci. Cesty uživatelů je možné vyvolat vlastním jménem a vyžadují, aby se SendClaims krok spustil. Dílčí cesty jsou komponenty cest uživatelů a nelze je volat nezávisle a jsou volány vždy z cesty uživatele.

Klíčovou součástí větvení je umožnění lepší zpracování obchodních logik v cestě uživatele. Obvyklé kroky orchestrace jsou seskupené do jednotlivých částí, které se mají vyvolat samostatně. Dílčí cesta může zjednodušit cestu, při které se několik kroků orchestrace spojuje dohromady (mají stejné podmínky). Podřízená cesta je volána pouze z cesty uživatele, neměla by volat jinou dílčí cestu.

Existují dva typy dílčích cest:

- **Call** -vrátí řízení volajícímu. Dílčí cesta se spustí a pak se ovládací prvek vrátí do kroku orchestrace, který je aktuálně spuštěn v rámci cesty uživatele.
- **Přenos** – přenáší řízení na dílčí cestu (nevratné větvení). Podřízená cesta musí mít SendClaims krok, který vrátí deklarace zpět do aplikace předávající strany.

## <a name="example-scenarios"></a>Příklady scénářů

### <a name="call-sub-journey"></a>Podřízená cesta volání

Podřízená cesta volání je užitečná v následujících situacích:

- Omezení stáří: pro věkové rozmezí existuje mnoho sdílených komponent mezi cestami uživatele. Větvení umožňuje kompilovat společné prvky do složek, které lze sdílet.  
- Souhlas rodičů: větvení umožňuje pohodlí v rámci návrhu souhlasu rodičů tím, že nám umožňuje přístup k deklaracím, které uživatel provedl, a může se rozvětvit na cestu uživatele o souhlasu rodičů po zjištění, že uživatel vyžaduje souhlas. 
- Přihlaste se, abyste se přihlásili: Vezměte v úvahu situaci, kdy uživatel už v adresáři existuje, ale možná jste zapomněli, že ve skutečnosti vytvořil účet. V takovém případě může být žádoucí, aby uživatel bez oznámení, že přihlašovací údaje, které zadal, již existovaly, a vynutil, aby uživatel restartoval cestu, kterou tato zásada umožňuje přepnout z toku registrace do toku přihlášení pro tohoto uživatele.  

### <a name="transfer-sub-journey"></a>Migrace dílčí cesty

Dílčí cestu přenosu je užitečné v následujících scénářích:

- Zobrazuje se stránka blokování.
- Testování a/B při směrování požadavku na dílčí cestu pro provedení a vydání tokenu.

## <a name="adding-a-subjourneys-element"></a>Přidání elementu dílčích cest

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

### <a name="invoke-a-sub-journey-step"></a>Vyvolat krok dílčí cesty

`InvokeSubJourney`K provedení dílčí cesty se používá nový krok orchestrace typu. Následuje příklad, který ukazuje všechny prvky spuštění tohoto kroku orchestrace.

```xml
<OrchestrationStep Order="5" Type="InvokeSubJourney">
  <JourneyList>
    <Candidate SubJourneyReferenceId="ConditionalAccess_Evaluation" />
  </JourneyList>
</OrchestrationStep>
```

> [!NOTE]
> Dílčí cesta by neměla volat jinou podřízenou cestu.

## <a name="components"></a>Komponenty

Pro definování dílčích cest, které zásady podporují, přidejte element **recests** pod element nejvyšší úrovně souboru zásad.

Element **recestes** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Subcesta | 1: n | Dílčí cesta definující všechny konstrukce, které jsou nezbytné pro kompletní tok uživatele. |

Element **recestes** obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| Id | Ano | Identifikátor dílčí cesty, který může být použit cestou uživatele k odkazování na dílčí cestu v zásadě. Element **SubJourneyReferenceId** elementu [kandidáta](userjourneys.md#journeylist) odkazuje na tento atribut. |
| Typ | Ano | Možné hodnoty: `Call` , nebo `Transfer` . Další informace najdete v tématu [větvení cest uživatele](#user-journey-branching) .|

Element dílčí **cesty** obsahuje následující element:

| Prvek | Výskytů | Popis |
| ------- | ----------- | ----------- |
| OrchestrationSteps | 1: n | Sekvence orchestrace, která musí následovat po úspěšné transakci. Každá cesta uživatele se skládá z uspořádaného seznamu kroků orchestrace, které se spustí v posloupnosti. Pokud nějaký krok selhává, transakce se nezdařila. |

## <a name="orchestrationsteps"></a>OrchestrationSteps

Úplný seznam elementů kroku Orchestration naleznete v tématu [userjourney](userjourneys.md).

## <a name="next-steps"></a>Další kroky

Další informace o [userjourney](userjourneys.md)