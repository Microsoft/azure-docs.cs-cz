---
title: Složitost hesla – Azure Active Directory B2C | Microsoft Docs
description: Jak nakonfigurovat požadavky na složitost pro hesla dodaná příjemci v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 642621e2e04d3e96dc6886b1bdb28c161560b28e
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065458"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurace požadavků na složitost pro hesla v Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) podporuje změnu požadavků na složitost hesel zadaných koncovým uživatelem při vytváření účtu. Ve výchozím nastavení Azure AD B2C používá `Strong` hesla. Azure AD B2C podporuje také možnosti konfigurace pro kontrolu složitosti hesel, která zákazníci můžou používat.

## <a name="password-rule-enforcement"></a>Vynucení pravidla hesla

Při registraci nebo resetování hesla musí koncový uživatel dodat heslo, které splňuje pravidla složitosti. Pravidla složitosti hesla jsou vynutila pro jednotlivé uživatelské toky. Je možné, aby jeden uživatelský tok při registraci vyžadoval čtyřmístný PIN kód, zatímco jiný tok uživatele během registrace vyžaduje osm znaků řetězce. Například můžete použít tok uživatele s odlišnou složitostí hesla pro dospělé než pro děti.

Složitost hesla se během přihlašování nikdy vynutila. Uživatelům se během přihlašování nikdy nevyzve ke změně hesla, protože nesplňuje aktuální požadavek na složitost.

Složitost hesla se dá nakonfigurovat v následujících typech uživatelských toků:

- Tok uživatelů registrace nebo přihlašování
- Tok uživatele pro resetování hesla

Pokud používáte vlastní zásady, můžete ([nakonfigurovat složitost hesla ve vlastních zásadách](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů**.
2. Vyberte tok uživatele a klikněte na **vlastnosti**.
3. V části **složitost hesla**změňte složitost hesla pro tento tok uživatele na **jednoduché**, **silné**nebo **vlastní**.

### <a name="comparison-chart"></a>Srovnávací graf

| Složitost | Popis |
| --- | --- |
| Jednoduchý | Heslo, které má alespoň 8 až 64 znaků. |
| Silné | Heslo, které má alespoň 8 až 64 znaků. Vyžaduje 3 ze 4 malých a velkých písmen, číslic a symbolů. |
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

- **2 ze 4: Malý znak, velké písmeno, číslo (0-9), symbol** zajistí, že heslo obsahuje alespoň dva typy znaků. Například číslo a malý znak.
- **3 ze 4: Malý znak, velké písmeno, číslo (0-9), symbol** zajistí, že heslo obsahuje alespoň dva typy znaků. Například číslo, malý znak a znak velkého písmene.
- **4 ze 4: Malý znak, velké písmeno, číslo (0-9), symbol** zajišťuje, že heslo obsahuje všechny typy znaků.

    > [!NOTE]
    > Výsledkem **4 ze 4** může být frustrace koncového uživatele. Některé studie ukázaly, že tento požadavek nevylepšuje entropii hesla. Viz [pokyny](https://pages.nist.gov/800-63-3/sp800-63b.html#appA) k heslům pro NIST.
