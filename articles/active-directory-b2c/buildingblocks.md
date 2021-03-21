---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Zadejte element BuildingBlocks vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a42c1d06051c283f0e911c4cd166884ddd060f45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633270"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **BuildingBlocks** je přidán uvnitř elementu [TrustFrameworkPolicy](trustframeworkpolicy.md) .

```xml
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">

  <BuildingBlocks>
    <ClaimsSchema>
      ...
    </ClaimsSchema>
    <Predicates>
    ...
    </Predicates>
    <PredicateValidations>
    ...
    </PredicateValidations>
    <ClaimsTransformations>
      ...
    </ClaimsTransformations>
    <ContentDefinitions>
      ...
    </ContentDefinitions>
    <Localization>
      ...
    </Localization>
    <DisplayControls>
      ...
    </DisplayControls>
 </BuildingBlocks>
```

Element **BuildingBlocks** obsahuje následující prvky, které musí být zadány v definovaném pořadí:

- [ClaimsSchema](claimsschema.md) – definuje typy deklarací, na které se dá odkazovat v rámci zásad. Schéma deklarací identity je místo, kde deklarujete typy deklarací. Typ deklarace identity je podobný proměnné v mnoha programových jazycích. Typ deklarace identity můžete použít ke shromažďování dat od uživatele vaší aplikace, přijímání deklarací identity od zprostředkovatelů sociálních identit, posílání a přijímání dat z vlastních REST API nebo ukládání všech vnitřních dat používaných vašimi vlastními zásadami.

- [Predikáty a PredicateValidationsInput](predicates.md) – umožňují provést proces ověřování, aby bylo zajištěno, že do deklarace identity budou vložena pouze správně vytvořená data.

- [ClaimsTransformations](claimstransformations.md) – obsahuje seznam transformací deklarací identity, které se dají použít v zásadách.  Transformace deklarací převede jednu deklaraci identity na jinou. V transformaci deklarací identity zadáte metodu transformace, například:
  - Změna velikosti řetězce deklarace identity na zadanou hodnotu. Například změna řetězce z malých písmen na velká.
  - Porovnání dvou deklarací identity a vrácení deklarace identity s hodnotou true značí, že deklarace identity se shodují, jinak false.
  - Vytváří se deklarace řetězce ze zadaného parametru v zásadách.
  - Vytvoření náhodného řetězce pomocí generátoru náhodných čísel.
  - Formátování deklarace identity podle poskytnutého formátovacího řetězce. Tato transformace používá metodu jazyka C# `String.Format` .

- InputValidation – tento prvek umožňuje provádění logických agregací, které jsou podobné *a* a *nebo*.

- [ContentDefinitions](contentdefinitions.md) – obsahuje adresy URL pro šablony HTML5, které se mají použít při cestě uživatele. V vlastní zásadě definice obsahu definuje identifikátor URI stránky HTML5, který se používá pro zadaný krok v cestě uživatele. Například přihlášení nebo registrace, resetování hesla nebo chybové stránky. Vzhled a chování můžete upravit přepsáním LoadUri pro soubor HTML5. Případně můžete vytvořit nové definice obsahu podle svých potřeb. Tento element může obsahovat odkaz na lokalizované prostředky pomocí ID lokalizace.

- [Lokalizace](localization.md) – umožňuje podporu více jazyků. Podpora lokalizace v zásadách umožňuje nastavit seznam podporovaných jazyků v zásadách a vybrat výchozí jazyk. Podporují se taky řetězce a kolekce specifické pro jazyk.

- [DisplayControls](display-controls.md) – definuje ovládací prvky, které se mají zobrazit na stránce. Ovládací prvky zobrazení mají speciální funkce a pracují s technickými profily back-endu pro ověřování. 
