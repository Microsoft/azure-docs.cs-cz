---
title: Definování technického profilu ověření ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Ověřte deklarace identity pomocí technického profilu ověření ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: facef1e1288f2a64872efbf37a9a31fa05244a7e
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950795"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu ověření ve Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil ověření je běžný technický profil z libovolného protokolu, například [Azure Active Directory](active-directory-technical-profile.md) nebo [REST API](restful-technical-profile.md). Technický profil ověřování vrátí deklarace výstupů nebo vrátí chybovou zprávu HTTP 409 (kód stavu odpovědi na konflikt) s následujícími daty:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Deklarace identity, které se vrátí z technického profilu ověření, se přidají zpět do kontejneru deklarací identity. Tyto deklarace identity můžete použít v následujících technických profilech ověření.

Technické profily ověření jsou spouštěny v pořadí, v jakém jsou uvedeny v prvku **ValidationTechnicalProfiles** . V technickém profilu ověření můžete nakonfigurovat, jestli se má v případě, že technický profil ověření vyvolá chybu, nebo jestli je úspěšný, provést všechny následné ověřovací technické profily.

Technický profil ověření může být podmíněně proveden na základě předběžných podmínek definovaných v prvku **ValidationTechnicalProfile** . Můžete třeba ověřit, jestli konkrétní deklarace existují, nebo jestli je deklarace identity rovna zadané hodnotě.

Technický profil s vlastním uplatněním může definovat technický profil ověření, který se použije k ověření některých nebo všech jeho výstupních deklarací. Všechny vstupní deklarace odkazovaného technického profilu se musí objevit ve výstupních deklaracích technického profilu odkazujícího ověřování.

> [!NOTE]
> Ověřovací technické profily můžou používat jenom technické profily s vlastním uplatněním. Pokud potřebujete ověřit výstupní deklarace identity z technických profilů bez vlastního vyhodnocení, zvažte použití dalšího kroku orchestrace v cestě uživatele, aby vyhovoval technickému profilu, který se účtuje za ověření.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Element **ValidationTechnicalProfiles** obsahuje následující prvky:

| Element | Výskytů | Popis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Technický profil, který se má použít k ověření některých nebo všech výstupních deklarací odkazujícího technického profilu. |

Element **ValidationTechnicalProfile** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který je už definovaný v zásadách nebo v nadřazené zásadě. |
|ContinueOnError –|Ne| Označuje, zda má ověřování všech následných technických profilů ověřování pokračovat, pokud tento technický profil ověření vyvolá chybu. Možné hodnoty: `true` nebo `false` (výchozí, zpracování dalších profilů ověřování se zastaví a vrátí se chyba). |
|ContinueOnSuccess | Ne | Označuje, zda má ověřování všech následných ověřovacích profilů pokračovat v případě úspěšného ověření tohoto technického profilu. Možné hodnoty: `true` nebo `false`. Výchozí hodnota je `true`, což znamená, že zpracování dalších profilů ověření bude pokračovat. |

Element **ValidationTechnicalProfile** obsahuje následující element:

| Element | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Předběžné podmínky | 0:1 | Seznam předpokladů, které musí být splněny, aby byl technický profil ověření proveden. |

Prvek **předběžné podmínky** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ kontroly nebo dotazu, který má být proveden pro předběžnou podmínku. `ClaimsExist` je zadáno, aby se zajistilo, že akce by se měly provádět v případě, že zadané deklarace v aktuální sadě deklarací identity existují, nebo `ClaimEquals` je určeno, že akce by se měly provést, pokud existuje zadaná deklarace identity a její hodnota je rovna zadané hodnotě. |
| `ExecuteActionsIf` | Ano | Určuje, zda mají být provedeny akce v předběžné podmínce, pokud je test nastaven na hodnotu true nebo false. |

Prvek **podmínky** obsahuje následující prvky:

| Element | Výskytů | Popis |
| ------- | ----------- | ----------- |
| Hodnota | 1: n | Data, která se používají při kontrole. Pokud je typ této kontroly `ClaimsExist`, toto pole určuje ClaimTypeReferenceId k dotazování na. Pokud je typ kontroly `ClaimEquals`, toto pole určuje ClaimTypeReferenceId pro dotaz na. Zatímco jiný element Value obsahuje hodnotu, která má být kontrolována.|
| Akce | 1:1 | Akce, která má být provedena, pokud je splněna podmínka kontroly předběžných podmínek v rámci kroku orchestrace. Hodnota **Akce** je nastavena na `SkipThisValidationTechnicalProfile`. Určuje, že by se neměl spustit přidružený technický profil ověření. |

### <a name="example"></a>Příklad:

Následující příklad používá tyto technické profily ověřování:

1. První technický profil ověření kontroluje přihlašovací údaje uživatele a nepokračuje, pokud dojde k chybě, například neplatné uživatelské jméno nebo špatné heslo.
2. Další technický profil ověření se neprovede, pokud deklarace userType neexistuje nebo pokud je hodnota userType `Partner`. Technický profil ověření se pokusí přečíst profil uživatele z interní databáze zákazníka a pokračovat, pokud dojde k chybě, například služba REST API není k dispozici nebo došlo k vnitřní chybě.
3. Poslední technický profil ověření se neprovede, pokud deklarace userType neexistovala nebo pokud je hodnota userType `Customer`. Technický profil ověření se pokusí přečíst profil uživatele z interní databáze partnerů a pokračuje v případě výskytu chyby, například REST API služba není k dispozici nebo došlo k vnitřní chybě.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
