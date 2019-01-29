---
title: Složitost hesla v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak konfigurovat požadavky na složitost hesel poskytnutých uživatelům v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: d1106a67d084020b714f0204b9012076817f9a4c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176060"
---
# <a name="azure-ad-b2c-configure-complexity-requirements-for-passwords"></a>Azure AD B2C: Konfigurovat požadavky na složitost hesel

> [!NOTE]
> **Tato funkce je ve verzi public preview.**

Azure Active Directory B2C (Azure AD B2C) podporuje se měnící požadavky na složitost hesel poskytnutých koncového uživatele při vytváření účtu.  Ve výchozím nastavení, Azure AD B2C používá `Strong` hesla.  Azure AD B2C podporuje také možnosti konfigurace pro řízení složitosti hesla, které můžou zákazníci využívat.

## <a name="when-password-rules-are-enforced"></a>Když se vynucují pravidla pro hesla

Při registraci nebo resetování hesla, koncový uživatel musí zadat heslo, které splňuje pravidla složitosti.  Každý tok uživatele se vynucují pravidla složitosti hesla.  Je možné mít jeden tok uživatele vyžadují čtyřmístný PIN kód během registrace nějakou jinou tok uživatele vyžaduje řetězec osm znaků během registrace.  Můžete například použít tok uživatele s jiným heslem složitost pro dospělé než pro děti.

Složitost hesla nikdy vynucuje během přihlašování.  Uživatelé nikdy vyzváni během přihlášení změnit své heslo, protože nesplňuje požadavek na aktuální složitost.

Toto jsou typy toky uživatelů kde lze nastavit složitost hesla:

* Tok uživatele. registrace nebo přihlašování
* Tok uživatele resetování hesla
* Vlastní zásady ([nakonfigurujte složitost hesla ve vlastních zásadách](active-directory-b2c-reference-password-complexity-custom.md))

## <a name="how-to-configure-password-complexity"></a>Jak nakonfigurovat složitost hesla

1. Otevřít **toky uživatelů**.
2. Vyberte tok uživatele a klikněte na tlačítko **vlastnosti**.
3. V části **složitost hesla**, složitost hesla pro tohoto uživatele toku můžete změnit **jednoduché**, **silné**, nebo **vlastní**.

### <a name="comparison-chart"></a>Graf porovnání

| Složitost | Popis |
| --- | --- |
| Jednoduchý | Heslo, které je nejméně 8 až 64 znaků. |
| Silné | Heslo, které je nejméně 8 až 64 znaků. Vyžaduje 3 ze 4 malá písmena, velká písmena, číslice nebo symboly. |
| Vlastní | Tato možnost poskytuje většinu kontrolu nad pravidlům složitosti hesla.  To umožňuje konfigurovat vlastní délku.  Umožňuje také přijímat hesla jenom číslo (PIN). |

## <a name="options-available-under-custom"></a>Možnosti dostupné v rámci vlastní

### <a name="character-set"></a>Znaková sada

Můžete tak, aby přijímal pouze číslice (PIN) nebo celé znakové sady.

* **Pouze čísla** umožňuje číslic pouze (0-9) při zadávání hesla.
* **Všechny** umožňuje libovolné písmenem, číslicí nebo symbolem.

### <a name="length"></a>Délka

Umožňuje řídit požadavky na délku hesla.

* **Minimální délka** musí být aspoň na 4.
* **Maximální délka** musí být větší nebo rovna hodnotě minimální délku a může být maximálně 64 znaků.

### <a name="character-classes"></a>Třídy znaků

Umožňuje řídit typy různých znaků hesla.

* **2 z 4: Malé písmeno, velké písmeno, číslici (0-9), Symbol** zajistí, heslo obsahuje alespoň dva typy znaků. Například čísla a znak malého písmene.
* **3 ze 4: Malé písmeno, velké písmeno, číslici (0-9), Symbol** zajistí, heslo obsahuje alespoň dva typy znaků. Například: číslo, malé písmeno a znak velkého písmene.
* **4 ze 4: Malé písmeno, velké písmeno, číslici (0-9), Symbol** zajišťuje heslo obsahuje všechny typy znaků.

    > [!NOTE]
    > Vyžadování **4 ze 4** může vést k frustrace koncového uživatele. Několik studií ukázalo, že tento požadavek nezvyšuje heslo entropie. Zobrazit [pokynů NIST heslo](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
