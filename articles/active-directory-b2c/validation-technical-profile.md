---
title: Technický profil ověření definování ve vlastních zásadách v Azure Active Directory B2C | Dokumentace Microsoftu
description: Technický profil Azure Active Directory definování ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: f08d5732939e853785e2cce1d1e9ed8eea7f9ad7
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543135"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Technický profil ověření definování ve vlastních zásadách pro Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]
 
Technický profil ověření, jako je běžný technický profil z libovolného protokolu [Azure Active Directory](active-directory-technical-profile.md) nebo [rozhraní REST API](restful-technical-profile.md). Technický profil ověření vrátí výstupní deklarace identit nebo vrátí chybovou zprávu protokolu HTTP 409 (konflikt stavový kód odpovědi), s následujícími údaji:

```JSON
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Deklarace identity, které jsou retuned z technického profilu ověření jsou přidána zpět do kontejneru deklarací identity. V další ověření technické profily můžete použít tyto deklarace identit.

Ověření technické profily jsou provedeny v pořadí uvedeném v **ValidationTechnicalProfiles** elementu. Můžete nakonfigurovat v technickém profilu ověření, jestli spuštění jakékoli následné ověření technické profily by měly pokračovat, pokud technický profil ověření vyvolá chybu nebo je úspěšné.  

Technický profil může podmíněně spustit ověřování založené na předběžné podmínky definované v **ValidationTechnicalProfile** elementu. Můžete například zkontrolovat, jestli konkrétní deklarace identity existuje, nebo pokud se deklarace identity rovná nebo není na zadanou hodnotu.

S vlastním potvrzením technický profil může definovat profil technické ověření pro některé nebo všechny jeho výstupní deklarace identit. Všechny mezi vstupními deklaracemi identity odkazovaného technického profilu musí být uvedena v výstupní deklarace identit odkazující technického profilu ověření.


## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

**ValidationTechnicalProfiles** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Technický profil má být použit pro ověřování, některé nebo všechny výstupní deklarace identit odkazující technického profilu. |

**ValidationTechnicalProfile** prvek obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| referenceId | Ano | Identifikátor technického profilu již definována v zásadách nebo nadřazené zásady. |
|ContinueOnError –|Ne| Označující, zda ověření žádné další ověření technické profily by měly pokračovat, pokud tento profil technické validaiton vyvolá chybu. Hodnoty překročila povolený: `true` nebo `false` (výchozí, se zastaví zpracování další ověření profilů a vrátí chyba). 
|ContinueOnSuccess | Ne | Označující, zda ověření jakékoli následné ověření profilů by měly pokračovat, pokud tento profil technické ověření proběhne úspěšně. Hodnoty překročila povolený: `true` nebo `false`. Výchozí hodnota je `true`, což znamená, že zpracování další ověření profily bude pokračovat. |

**ValidationTechnicalProfile** prvek obsahuje následující element:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Předběžné podmínky | 0:1 | Seznam předběžné požadavky, které musí být splněny pro technický profil ověření ke spuštění. |

**Předběžné podmínky** prvek obsahuje následující atribut:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Typ | Ano | Typ kontroly nebo dotaz k provedení pro předpoklad. Buď `ClaimsExist` je zadali, a ověřte, že by měl provést akce, pokud zadané deklarace existuje v aktuální sadě deklarací identity uživatele nebo `ClaimEquals` je zadán, že by měl provést akce, pokud existuje zadaný deklarace identity a jeho hodnota se rovná Zadaná hodnota. |
| ExecuteActionsIf | Ano | Určuje, zda má být provedena akce v předpoklad je-li test true nebo false. | 

**Předběžné podmínky** prvek obsahuje následující prvky:

| Element | Výskyty | Popis |
| ------- | ----------- | ----------- |
| Hodnota | 1: n | Data, která se používá kontrolu. Pokud je typ této kontroly `ClaimsExist`, toto pole určuje ClaimTypeReferenceId dotazu. Pokud je typ kontroly `ClaimEquals`, toto pole určuje ClaimTypeReferenceId dotazu. Zatímco jiný element hodnota obsahuje hodnotu, která se má zkontrolovat.|
| Akce | 1:1 | Akce, která se má vzít při splnění předběžná kontrola v rámci na krok Orchestrace. Hodnota **akce** je nastavena na `SkipThisValidationTechnicalProfile`. Určuje, že by neměl být spouštěn technický profil přidruženého ověřování. | 

### <a name="example"></a>Příklad:

Následující příklad používá tyto technické profily ověření: 

1. První technický profil ověření ověří přihlašovací údaje uživatele a nebude dále, pokud dojde k chybě, jako je například neplatné uživatelské jméno nebo špatné heslo. 
2. Další ověření technický profil, neprovede, pokud deklarace identity userType neexistuje, nebo pokud je hodnota userType `Partner`. Technický profil ověření se pokusí čtení profilu uživatele z databáze interní zákazníky a pokračovat v případě, že dojde k chybě, jako je REST API služby není dostupná, nebo všechny vnitřní chyby.
3. Poslední ověření technický profil, neprovede, pokud userType deklarace identity, nebo pokud je hodnota userType `Customer`. Technický profil ověření se pokusí načíst profil uživatele z interní partnerská databáze a bude pokračovat, pokud dojde k chybě, jako je REST API služby není dostupná, nebo všechny vnitřní chyby.

```XML
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false"  />
    
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














