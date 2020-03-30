---
title: Definování technického profilu ověření ve vlastní chodnících
titleSuffix: Azure AD B2C
description: Ověřte deklarace identity pomocí ověřovacího technického profilu ve vlastních zásadách ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1eaf159149bb353b1cf0474aad5bc233decddc5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481564"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definování ověřovacího technického profilu ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Technický profil ověření je běžný technický profil z libovolného protokolu, jako je [například Azure Active Directory](active-directory-technical-profile.md) nebo [rozhraní REST API](restful-technical-profile.md). Technický profil ověření vrátí výstupní deklarace identity nebo vrátí stavový kód 4xx HTTP s následujícími daty. Další informace naleznete v [tématu vracející se chybová zpráva](restful-technical-profile.md#returning-error-message)

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Rozsah výstupních deklarací technického profilu ověření je omezen na [vlastní technický profil,](self-asserted-technical-profile.md) který vyvolá technický profil ověření, a jeho technické profily validace. Pokud chcete použít výstupní deklarace v dalším kroku orchestrace, přidejte výstupní deklarace do samoobslužného technického profilu, který vyvolá technický profil ověření.

Ověření technické profily jsou prováděny v pořadí, které se zobrazí v **ValidationTechnicalProfiles** element. V technickém profilu ověření můžete nakonfigurovat, zda by mělo pokračovat provádění jakýchkoli následných technických profilů ověření, pokud technický profil ověření vyvolá chybu nebo je úspěšný.

Technický profil ověření lze podmíněně provést na základě předběžných podmínek definovaných v prvku **ValidationTechnicalProfile.** Můžete například zkontrolovat, zda existuje konkrétní deklarace nebo zda je deklarace rovná nebo není zadané hodnotě.

Vlastní uplatněný technický profil může definovat technický profil ověření, který se použije pro ověření některých nebo všech jeho výstupních deklarací. Všechna vstupní tvrzení referenčního technického profilu musí být uvedena ve výstupních tvrzeních referenčního technického profilu validace.

> [!NOTE]
> Pouze samozvané technické profily mohou používat validační technické profily. Pokud potřebujete ověřit výstupní deklarace identity z neuplatněných technických profilů, zvažte použití dalšího kroku orchestrace v cestě uživatele, abyste vyhověli technickému profilu, který má na starosti ověření.

## <a name="validationtechnicalprofiles"></a>ValidaceTechnickéProfily

Prvek **ValidationTechnicalProfiles** obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ValidaceTechnicalProfile | 1:n | Technický profil, který se použije pro ověření některých nebo všech výstupních tvrzení referenčního technického profilu. |

Prvek **ValidationTechnicalProfile** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| ReferenceId | Ano | Identifikátor technického profilu, který je již definován v zásadách nebo nadřazených zásadách. |
|ContinueOnError|Ne| Označuje, zda by mělo validace všech následných technických profilů validace pokračovat, pokud tento technický profil validace vyvolá chybu. Možné `true` hodnoty: `false` nebo (výchozí, zpracování dalších ověřovacích profilů se zastaví a vrátí se chyba). |
|ContinueOnSuccess | Ne | Označuje, zda má ověření všech následných ověřovacích profilů pokračovat, pokud je tento technický profil ověření úspěšný. Možné `true` hodnoty: `false`nebo . Výchozí hodnota `true`je , což znamená, že zpracování dalších ověřovacích profilů bude pokračovat. |

Prvek **ValidationTechnicalProfile** obsahuje následující prvek:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Předpoklady | 0:1 | Seznam předpokladů, které musí být splněny pro ověření technického profilu provést. |

Prvek **Condition condition** obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| `Type` | Ano | Typ kontroly nebo dotazu, který má být pro předběžnou podmínku nastaven. Buď `ClaimsExist` je zadán, aby bylo zajištěno, že akce by měly být `ClaimEquals` provedeny, pokud zadaný deklarace existují v aktuální sadě deklarací uživatele, nebo je určen, že akce by měly být provedeny, pokud existuje zadaná deklarace a jeho hodnota je rovna zadané hodnotě. |
| `ExecuteActionsIf` | Ano | Označuje, zda by měly být provedeny akce v rámci podmínky, pokud je test pravdivý nebo nepravdivý. |

Prvek **Condition obsahuje** následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Hodnota | 1:n | Data, která je použita v kontrole. Pokud je `ClaimsExist`typ této kontroly , toto pole určuje ClaimTypeReferenceId pro dotaz. Pokud je `ClaimEquals`typ kontroly , toto pole určuje ClaimTypeReferenceId pro dotaz. Zatímco jiný prvek hodnoty obsahuje hodnotu, která má být kontrolována.|
| Akce | 1:1 | Akce, která by měla být provedena, pokud je splněna kontrola předběžnépodmínky v rámci kroku orchestrace. Hodnota **akce** je nastavena `SkipThisValidationTechnicalProfile`na . Určuje, že přidružený technický profil ověření by neměl být proveden. |

### <a name="example"></a>Příklad

Následující příklad používá tyto technické profily ověření:

1. První technický profil ověření kontroluje pověření uživatele a nepokračuje, pokud dojde k chybě, jako je například neplatné uživatelské jméno nebo chybné heslo.
2. Další ověření technický profil, neprovede, pokud deklarace userType neexistuje, nebo pokud `Partner`je hodnota userType . Technický profil ověření se pokusí přečíst profil uživatele z interní databáze zákazníků a pokračovat, pokud dojde k chybě, jako je například služba ROZHRANÍ REST API není k dispozici, nebo jakékoli vnitřní chyby.
3. Poslední ověření technický profil, neprovede, pokud deklarace userType neexistuje, nebo pokud je `Customer`hodnota userType . Technický profil ověření se pokusí přečíst profil uživatele z databáze interního partnera a pokračuje, pokud dojde k chybě, jako je například služba ROZHRANÍ REST API není k dispozici, nebo jakékoli vnitřní chyby.

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
