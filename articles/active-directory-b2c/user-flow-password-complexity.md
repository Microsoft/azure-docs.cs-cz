---
title: Konfigurace požadavků na složitost hesla
titleSuffix: Azure AD B2C
description: Jak nakonfigurovat požadavky na složitost pro hesla dodaná příjemci v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9c7f0ead3a2f93d7f390106b8e7471d58b709023
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87481559"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurace požadavků na složitost pro hesla v Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) podporuje změnu požadavků na složitost hesel zadaných koncovým uživatelem při vytváření účtu. Ve výchozím nastavení Azure AD B2C používá `Strong` hesla. Azure AD B2C podporuje také možnosti konfigurace pro kontrolu složitosti hesel, která zákazníci můžou používat.

## <a name="password-rule-enforcement"></a>Vynucení pravidla hesla

Při registraci nebo resetování hesla musí koncový uživatel dodat heslo, které splňuje pravidla složitosti. Pravidla složitosti hesla jsou vynutila pro jednotlivé uživatelské toky. Je možné, aby jeden uživatelský tok při registraci vyžadoval čtyřmístný PIN kód, zatímco jiný tok uživatele během registrace vyžaduje osm znaků řetězce. Například můžete použít tok uživatele s odlišnou složitostí hesla pro dospělé než pro děti.

Složitost hesla se během přihlašování nikdy vynutila. Uživatelům se během přihlašování nikdy nevyzve ke změně hesla, protože nesplňuje aktuální požadavek na složitost.

Složitost hesla se dá nakonfigurovat v následujících typech uživatelských toků:

- Tok uživatelů registrace nebo přihlašování
- Tok uživatele pro resetování hesla

Pokud používáte vlastní zásady, můžete ([nakonfigurovat složitost hesla ve vlastních zásadách](custom-policy-password-complexity.md)).

## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů**.
2. Vyberte tok uživatele a klikněte na **vlastnosti**.
3. V části **složitost hesla**změňte složitost hesla pro tento tok uživatele na **jednoduché**, **silné**nebo **vlastní**.

### <a name="comparison-chart"></a>Srovnávací graf

| Složitost | Description |
| --- | --- |
| Jednoduchost | Heslo, které má alespoň 8 až 64 znaků. |
| Silná | Heslo, které má alespoň 8 až 64 znaků. Vyžaduje 3 ze 4 malých a velkých písmen, číslic a symbolů. |
| Vlastní | Tato možnost poskytuje nejvyšší kontrolu nad pravidly složitosti hesla.  Umožňuje nakonfigurovat vlastní délku.  Umožňuje taky přijímat jenom hesla (PIN kód). |

## <a name="custom-options"></a>Vlastní možnosti

### <a name="character-set"></a>Znaková sada

Slouží k přijetí pouze číslic (PIN) nebo plné znakové sady.

- Při zadávání hesla se **čísly jenom** povolují jenom číslice (0-9).
- **Vše** umožňuje libovolné písmeno, číslici nebo symbol.

### <a name="length"></a>Délka

Umožňuje řídit požadavky na délku hesla.

- **Minimální délka** musí být aspoň 4.
- **Maximální délka** musí být větší nebo rovna minimální délce a maximálně může být 64 znaků.

### <a name="character-classes"></a>Třídy znaků

Umožňuje řídit různé typy znaků používané v hesle.

- **2 ze 4: malý znak, velké písmeno, číslo (0-9), symbol zajistí,** že heslo obsahuje alespoň dva typy znaků. Například číslo a malý znak.
- **3 ze 4: malý znak, Velká písmena, číslo (0-9), symbol zajistí,** že heslo obsahuje alespoň tři typy znaků. Například číslo, malý znak a znak velkého písmene.
- **4 ze 4: malá písmena, Velká písmena, číslo (0-9), symbol zajistí,** že heslo obsahuje všechny typy znaků.

    > [!NOTE]
    > Výsledkem **4 ze 4** může být frustrace koncového uživatele. Některé studie ukázaly, že tento požadavek nevylepšuje entropii hesla. Viz [pokyny k heslům pro NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) .
