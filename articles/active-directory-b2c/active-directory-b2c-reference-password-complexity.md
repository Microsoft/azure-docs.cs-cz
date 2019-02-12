---
title: Složitost hesla – Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak konfigurovat požadavky na složitost hesel poskytnutých uživatelům v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2c6f3d88aae99c419b2507f421cc4dfebb2c022b
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100197"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurovat požadavky na složitost hesel v Azure Active Directory B2C

Azure Active Directory (Azure AD) B2C podporuje měnící se požadavky na složitost hesel poskytnutých koncového uživatele při vytváření účtu. Ve výchozím nastavení, Azure AD B2C používá `Strong` hesla. Azure AD B2C podporuje také možnosti konfigurace pro řízení složitosti hesla, které můžou zákazníci využívat.

## <a name="password-rule-enforcement"></a>Vynucení pravidel heslo

Při registraci nebo resetování hesla, koncový uživatel musí zadat heslo, které splňuje pravidla složitosti. Každý tok uživatele se vynucují pravidla složitosti hesla. Je možné mít jeden tok uživatele vyžadují čtyřmístný PIN kód během registrace nějakou jinou tok uživatele vyžaduje řetězec osm znaků během registrace. Můžete například použít tok uživatele s jiným heslem složitost pro dospělé než pro děti.

Složitost hesla nikdy vynucuje během přihlašování. Uživatelé nikdy vyzváni během přihlášení změnit své heslo, protože nesplňuje požadavek na aktuální složitost.

Složitost hesla lze nakonfigurovat v následujících typech toků uživatele:

- Tok uživatele. registrace nebo přihlašování
- Tok uživatele resetování hesla

Pokud používáte vlastní zásady, můžete si ([nakonfigurujte složitost hesla ve vlastních zásadách](active-directory-b2c-reference-password-complexity-custom.md)).

## <a name="configure-password-complexity"></a>Nakonfigurujte složitost hesla

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů**.
2. Vyberte tok uživatele a klikněte na tlačítko **vlastnosti**.
3. V části **složitost hesla**, složitost hesla pro tohoto uživatele toku můžete změnit **jednoduché**, **silné**, nebo **vlastní**.

### <a name="comparison-chart"></a>Graf porovnání

| Složitost | Popis |
| --- | --- |
| Jednoduchý | Heslo, které je nejméně 8 až 64 znaků. |
| Silné | Heslo, které je nejméně 8 až 64 znaků. Vyžaduje 3 ze 4 malá písmena, velká písmena, číslice nebo symboly. |
| Vlastní | Tato možnost poskytuje většinu kontrolu nad pravidlům složitosti hesla.  To umožňuje konfigurovat vlastní délku.  Umožňuje také přijímat hesla jenom číslo (PIN). |

## <a name="custom-options"></a>Vlastní možnosti

### <a name="character-set"></a>Znaková sada

Můžete tak, aby přijímal pouze číslice (PIN) nebo celé znakové sady.

- **Pouze čísla** umožňuje číslic pouze (0-9) při zadávání hesla.
- **Všechny** umožňuje libovolné písmenem, číslicí nebo symbolem.

### <a name="length"></a>Délka

Umožňuje řídit požadavky na délku hesla.

- **Minimální délka** musí být aspoň na 4.
- **Maximální délka** musí být větší nebo rovna hodnotě minimální délku a může být maximálně 64 znaků.

### <a name="character-classes"></a>Třídy znaků

Umožňuje řídit typy různých znaků hesla.

- **2 z 4: Malé písmeno, velké písmeno, číslici (0-9), Symbol** zajistí, heslo obsahuje alespoň dva typy znaků. Například čísla a znak malého písmene.
- **3 ze 4: Malé písmeno, velké písmeno, číslici (0-9), Symbol** zajistí, heslo obsahuje alespoň dva typy znaků. Například: číslo, malé písmeno a znak velkého písmene.
- **4 ze 4: Malé písmeno, velké písmeno, číslici (0-9), Symbol** zajišťuje heslo obsahuje všechny typy znaků.

    > [!NOTE]
    > Vyžadování **4 ze 4** může vést k frustrace koncového uživatele. Několik studií ukázalo, že tento požadavek nezvyšuje heslo entropie. Zobrazit [pokynů NIST heslo](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
