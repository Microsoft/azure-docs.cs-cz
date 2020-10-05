---
title: Gramatika pravidla deklarace identity Azure Attestation
description: Podrobnosti o deklaracích zásad Azure Attestation a pravidlech deklarací identity.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91285414"
---
# <a name="claim-and-claim-rules"></a>Deklarace identity a pravidla deklarace identity

Pokud chcete pochopit gramatiku pravidel deklarací identity, musíte nejdřív pochopit deklarace identity zásad ověření identity.

## <a name="claim"></a>Deklarovat

Deklarace identity je sada vlastností seskupená dohromady, aby poskytovala relevantní informace. Pro ověření identity Azure obsahuje deklarace identity následující vlastnosti:

- **typ**: řetězcová hodnota, která představuje typ deklarace.
- **hodnota**: logická hodnota, celé číslo nebo hodnota řetězce, která představuje hodnotu deklarace identity.
- **ValueType**: datový typ informací uložených ve vlastnosti Value. Podporované typy jsou řetězec, celé číslo a logická hodnota. Pokud není definován, výchozí hodnota bude "String".
- **Vystavitel**: informace týkající se vystavitele deklarace identity. Vystavitel bude jeden z následujících typů:
  - **AttestationService**: pro autora zásad jsou k dispozici určité deklarace identity, které může autor zásad ověřování využít k tomu, aby provedl příslušné zásady.
  - **AttestationPolicy**: zásady (definované správcem) můžou přidávat deklarace identity na příchozí legitimaci během zpracování. Vystavitel v tomto případě je nastaven na "AttestationPolicy".
  - **CustomClaim**: ověření identity (klienta) může také přidat další deklarace identity k legitimaci ověřování. Vystavitel v tomto případě je nastaven na "CustomClaim".

Pokud není definován. Výchozí hodnota bude "CustomClaim".

## <a name="claim-rule"></a>Pravidlo deklarace identity

Sadu příchozích deklarací používá modul zásad k výpočtu výsledku ověření identity. Pravidlo deklarace identity představuje sadu podmínek, které slouží k ověření příchozích deklarací identity a k provedení definované akce.

```
Conditions list => Action (Claim)
```

Vyhodnocení pravidla deklarace identity Azure se skládá z následujících kroků:

- Pokud není seznam podmínek k dispozici, proveďte akci se zadanou deklarací identity. 
- V opačném případě vyhodnoťte podmínky ze seznamu podmínky.
- Pokud se seznam podmínky vyhodnotí jako false, zastavte. V opačném případě pokračujte.

Podmínky v pravidle deklarací identity se používají k určení, zda je nutné provést akci. Seznam podmínek je posloupnost podmínek, které jsou odděleny operátorem "&&".

Seznam podmínek je strukturovaný jako:

```
Condition && Condition && ...
```

Podmínka je strukturována takto:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

Seznam podmínky se skládá z jednotlivých podmínek u různých vlastností deklarace identity. Podmínka může mít volitelný identifikátor, který se dá použít k odkazování na deklaraci identity/s, která splní podmínku. Tento odkaz se dá použít v ostatních podmínkách nebo v akci stejného pravidla.

Například

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Níže jsou uvedené operátory, které lze použít ke kontrole podmínek:

| Typy | Podporované operace |
|--|--|
| Integer | = = (Equals), \! = (není rovno), <= (menší než nebo rovno), < (menší než), >= (větší než nebo rovno), > (je větší než). |
| Řetězec | = = (je rovno), \! = (není rovno) |
| Logická hodnota | = = (je rovno), \! = (není rovno) |

Vyhodnocení seznamu podmínek:

- Přítomnost operátoru "&&" znamená, že seznam podmínek je vyhodnocen jako pravdivý pouze v případě, že jsou všechny podmínky ze seznamu vyhodnocovány na hodnotu true.
- Podmínka představuje kritéria filtrování sady deklarací. Podmínkou je, že je vyhodnocena jako true, pokud je nalezena alespoň jedna deklarace identity, která splňuje podmínku.
- Deklarace identity se říká, že splňuje kritéria filtrování reprezentované podmínkou, pokud každá z jejích vlastností splňuje odpovídající podmínky vlastností deklarace identity přítomné v podmínce.  

Sada akcí, které jsou povoleny v zásadě, je popsána níže.

| Operace akce | Popis | Oddíly zásad, na které se vztahují |
|--|--|--|
| Povolit () | Příchozí sadu deklarací lze použít k výpočtu **issuancerules**. Nepřijímá žádnou deklaraci identity jako parametr. | **autorizačních pravidel** |
| Deny () | Příchozí sada deklarací by se neměla používat k výpočtu **issuancerules** , že nepřijímá žádnou deklaraci identity jako parametr. | **autorizačních pravidel** |
| Přidat (deklarace identity) | Přidá deklaraci identity do příchozí sady deklarací. Pro následující pravidla deklarace identity budou k dispozici všechny deklarace identity přidané do příchozí sady deklarací. |**autorizačních pravidel**, **issuancerules** |
| problém (deklarace identity) | Přidá deklaraci identity do příchozí a odchozí sady deklarací identity. | **issuancerules** |
| issueproperty (deklarace identity) | Přidá deklaraci identity do sady deklarací příchozích a vlastností. | **issuancerules**

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Nastavení ověření Azure pomocí PowerShellu](quickstart-powershell.md)

