---
title: BuildingBlocks
titleSuffix: Azure AD B2C
description: Zadejte element BuildingBlocks vlastní zásady ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/10/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cf9c333af48e1c148dcd20ccf5ce91b284bd728b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189867"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Element **BuildingBlocks** je přidán uvnitř elementu [TrustFrameworkPolicy.](trustframeworkpolicy.md)

```XML
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

- [ClaimsSchema](claimsschema.md) - Definuje typy deklarací, které lze odkazovat jako součást zásady. Schéma deklarací identity je místo, kde deklarujete typy deklarací. Typ deklarace je podobný proměnné v mnoha programových jazycích. Pomocí typu deklarace identity můžete shromažďovat data od uživatele vaší aplikace, přijímat deklarace od poskytovatelů sociální identity, odesílat a přijímat data z vlastního rozhraní REST API nebo ukládat jakákoli interní data používaná vašimi vlastními zásadami.

- [Predikáty a PredicateValidationsInput](predicates.md) - Umožňuje provést proces ověření zajistit, že pouze správně vytvořená data jsou zadána do deklarace.

- [ClaimsTransformations](claimstransformations.md) – obsahuje seznam deklarací identity transformace, které lze použít ve vaší zásady.  Transformace deklarací převede jednu deklaraci na jinou. V transformaci deklarací identity zadáte metodu transformace, například:
  - Změna případu deklarace řetězce na zadanou. Například změna řetězce z malých písmen na velká.
  - Porovnání dvou deklarací identity a vrácení deklarace identity s hodnotou true označující, že deklarace identity se shodují, jinak nepravdivě.
  - Vytvoření deklarace řetězce z zadaný parametr v zásadě.
  - Vytvoření náhodného řetězce pomocí generátoru náhodných čísel.
  - Formátování deklarace podle zadaný formátovací řetězec. Tato transformace používá `String.Format` metodu C#.

- InputValidation - Tento prvek umožňuje provádět logické agregace, které jsou podobné *a a* *nebo .*

- [ContentDefinitions](contentdefinitions.md) – obsahuje adresy URL pro šablony HTML5, které se mají použít na vaší cestě uživatele. Ve vlastní chodnících definuje definice obsahu identifikátor URI stránky HTML5, který se používá pro zadaný krok v cestě uživatele. Například přihlášení nebo přihlášení, resetování hesla nebo chybové stránky. Vzhled a chování můžete upravit přepsáním identifikátoru LoadUri pro soubor HTML5. Nebo můžete vytvořit nové definice obsahu podle svých potřeb. Tento prvek může obsahovat odkaz na lokalizované prostředky pomocí ID lokalizace.

- [Lokalizace](localization.md) – umožňuje podporovat více jazyků. Podpora lokalizace v zásadách umožňuje nastavit seznam podporovaných jazyků v zásadách a vybrat výchozí jazyk. Řetězce a kolekce specifické pro jazyk jsou také podporovány.

- [DisplayControls](display-controls.md) - Definuje ovládací prvky, které mají být zobrazeny na stránce. Ovládací prvky zobrazení mají speciální funkce a pracují s technickými profily ověření back-endu. Ovládací prvky zobrazení jsou aktuálně ve **verzi Preview**.
