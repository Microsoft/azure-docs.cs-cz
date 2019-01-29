---
title: BuildingBlocks – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte element BuildingBlocks vlastní zásady v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: afa064232c10d3e84e9c301d44b82faae4904253
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152973"
---
# <a name="buildingblocks"></a>BuildingBlocks

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**BuildingBlocks** prvek přidán uvnitř [TrustFrameworkPolicy](trustframeworkpolicy.md) elementu.

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
 </BuildingBlocks>
```

**BuildingBlocks** prvek obsahuje následující prvky, které musí být zadán v pořadí určeném:

- [ClaimsSchema](claimsschema.md) – definuje typy deklarací identity, které může být odkazováno jako součást této zásady. Schéma deklarací identity je místo, kde deklarace typů deklarací identity. Typ deklarace identity se podobně jako na proměnnou v řadě jazyků prostřednictvím kódu programu. Můžete použít typ deklarace identity pro shromažďování dat od uživatele vaší aplikace, přijímat deklarace identity od zprostředkovatelů sociálních identit, odesílat a přijímat data z vlastního rozhraní API REST nebo ukládání interních datových používat vaše vlastní zásada vypadat. 

- [Predikáty a PredicateValidationsInput](predicates.md) – umožňuje provádět ověření procesu k zajištění, že se zadá jenom správně vytvořená data do deklarace identity.
 
- [ClaimsTransformations](claimstransformations.md) -obsahuje seznam transformace deklarací, které lze použít ve svojí zásadě.  Transformace deklarací identity převede jednu deklaraci identity do jiné. V transformaci deklarací identity jako například zadáte metodu transformace: 
    - Změna velikosti písmen deklarace řetězec k zadanému. Například změna řetězec z malých na velká písmena.
    - Porovnání dvou deklarací identity nebo deklaraci identity true určující odpovídající deklarace identity, v opačném případě vrací false.
    - Vytvoření deklarace identity řetězec ze zadaného parametru v zásadách.
    - Vytváří se náhodný řetězec za použití generátor náhodných čísel.
    - Formátování deklaraci identity podle poskytnutý řetězec formátu. Tato transformace používá C# `String.Format` metody.

- [ContentDefinitions](contentdefinitions.md) – obsahuje adresy URL pro HTML5, šablony, používat na vaší cestě uživatele. Ve vlastních zásadách definuje definici obsahu stránky HTML5 identifikátor URI, který slouží ke konkrétnímu kroku v cestě uživatele. Pro příklad, resetování hesla přihlášení nebo registraci nebo chybové stránky. Vzhled a chování můžete upravit tak, že přepíšete LoadUri pro soubor HTML5. Nebo můžete vytvořit nové definice obsahu podle vašich potřeb. Tento element může obsahovat odkaz lokalizované prostředky pomocí ID lokalizace.

- [Lokalizace](localization.md) – umožňuje zajistit podporu více jazyků. Podpora lokalizace v zásadách umožňuje nastavit seznam podporovaných jazyků v zásadách a vyberte výchozí jazyk. Jazykově specifické řetězce a kolekce jsou také podporovány.


