---
title: Konfigurace požadavků na složitost hesla
titleSuffix: Azure AD B2C
description: Jak nakonfigurovat požadavky na složitost hesel dodaných spotřebiteli ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c5ef550af0c7e19531ea19093ea937880f7dcf14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185637"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurace požadavků na složitost hesel ve službě Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) podporuje změnu požadavků na složitost pro hesla dodaná koncovým uživatelem při vytváření účtu. Ve výchozím nastavení Azure AD `Strong` B2C používá hesla. Azure AD B2C také podporuje možnosti konfigurace pro řízení složitosti hesel, které zákazníci mohou používat.

## <a name="password-rule-enforcement"></a>Vynucení pravidel hesla

Během registrace nebo resetování hesla musí koncový uživatel zadat heslo, které splňuje pravidla složitosti. Pravidla složitosti hesla jsou vynucena na tok uživatele. Je možné mít jeden tok uživatele vyžadují čtyřmístný pin během registrace, zatímco jiný tok uživatele vyžaduje řetězec osm znaků během registrace. Můžete například použít tok uživatele s jinou složitosthesla pro dospělé než pro děti.

Složitost hesla není nikdy vynucena během přihlášení. Uživatelé jsou nikdy vyzváni během přihlášení ke změně hesla, protože nesplňuje aktuální požadavek na složitost.

Složitost hesla lze nakonfigurovat v následujících typech toků uživatelů:

- Tok registrace nebo přihlášení uživatele
- Tok uživatele pro resetování hesla

Pokud používáte vlastní zásady, můžete ([nakonfigurovat složitost hesla ve vlastní chod ).](custom-policy-password-complexity.md)

## <a name="configure-password-complexity"></a>Konfigurace složitosti hesla

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
3. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady).**
2. Vyberte tok uživatele a klepněte na **příkaz Vlastnosti**.
3. V části **Složitost hesla**změňte složitost hesla pro tok tohoto uživatele na **Jednoduché**, **Silné**nebo **Vlastní**.

### <a name="comparison-chart"></a>Srovnávací graf

| Složitost | Popis |
| --- | --- |
| Jednoduchý | Heslo, které má alespoň 8 až 64 znaků. |
| Silné | Heslo, které má alespoň 8 až 64 znaků. Vyžaduje 3 ze 4 malých, velkých, velkých, čísel nebo symbolů. |
| Vlastní | Tato možnost poskytuje největší kontrolu nad pravidly složitosti hesla.  Umožňuje konfiguraci vlastní délky.  Umožňuje také přijímat hesla pouze pro čísla (piny). |

## <a name="custom-options"></a>Vlastní možnosti

### <a name="character-set"></a>Znaková sada

Umožňuje přijímat pouze číslice (špendlíky) nebo úplnou znakovou sadu.

- **Čísla** povolují pouze číslice (0-9) při zadávání hesla.
- **Vše** umožňuje libovolné písmeno, číslo nebo symbol.

### <a name="length"></a>Délka

Umožňuje řídit požadavky na délku hesla.

- **Minimální délka** musí být alespoň 4.
- **Maximální délka** musí být větší nebo rovna minimální délce a maximálně 64 znaků.

### <a name="character-classes"></a>Třídy znaků

Umožňuje řídit různé typy znaků použité v hesle.

- **2 ze 4: Malý znak, velká písmena, číslo (0-9), symbol zajišťuje,** že heslo obsahuje alespoň dva typy znaků. Například číslo a znak s malou písmena.
- **3 ze 4: Malý znak, velká písmena, číslo (0-9), symbol zajišťuje,** že heslo obsahuje alespoň tři typy znaků. Například číslo, znak s velkým písmenem a znak velkých písmen.
- **4 ze 4: Malý znak, velká písmena, číslo (0-9), symbol zajišťuje,** že heslo obsahuje vše pro typy znaků.

    > [!NOTE]
    > Vyžadování **4 ze 4** může mít za následek frustraci koncového uživatele. Některé studie ukázaly, že tento požadavek nezlepšuje entropie hesla. Viz [Pravidla pro hesla NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)
